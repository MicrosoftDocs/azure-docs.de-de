---
title: Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Computer | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Migrieren einer lokalen Benutzerdatenbank zu SQL Server auf einem virtuellen Azure-Computer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4cd37128893309be5a1e362671b9e28dcc436b1b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356207"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Computer

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Es gibt verschiedene Möglichkeiten zur Migration einer lokalen SQL Server-Benutzerdatenbank zu SQL Server auf einem virtuellen Azure-Computer. In diesem Artikel werden verschiedene Methoden kurz erläutert und die besten Methoden für unterschiedliche Szenarien empfohlen.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 und SQL Server 2008 R2 nähern sich dem [Ende ihres Supportlebenszyklus](https://www.microsoft.com/sql-server/sql-server-2008) für lokale Instanzen. Um die Unterstützung zu erweitern, können Sie Ihre SQL Server-Instanz entweder zu einer Azure-VM migrieren oder erweiterte Sicherheitsupdates erwerben, um eine lokale Bereitstellung zu verwenden. Weitere Informationen finden Sie unter [Erweitern der Unterstützung für SQL Server 2008 und 2008 R2 mit Azure](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Was sind die primären Migrationsmethoden?

Die primären Migrationsmethoden sind:

* Durchführen einer lokalen Sicherung mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer
* Durchführen einer Sicherung über eine URL und anschließende Wiederherstellung auf dem virtuellen Azure-Computer über die URL
* Trennen der Daten- und Protokolldateien, Kopieren dieser Dateien in Azure Blob Storage und anschließendes Verbinden mit SQL Server auf dem virtuellen Azure-Computer über die URL
* Konvertieren des lokalen physischen Computers in Hyper-V-VHD, Hochladen des Computers in Azure Blob Storage und anschließendes Bereitstellen als neue VM mithilfe der hochgeladenen VHD
* Versenden einer Festplatte mithilfe des Windows-Import/Export-Diensts
* Bei einer lokalen Bereitstellung von AlwaysOn-Verfügbarkeitsgruppen: Erstellen eines Replikats in Azure mithilfe des [Assistenten zum Hinzufügen von Azure-Replikaten](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability), Durchführen eines Failovers und Verweisen der Benutzer auf die Azure-Datenbankinstanz
* Verwenden der SQL Server [Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication), um die Azure SQL Server-Instanz als Abonnenten zu konfigurieren, Deaktivieren der Replikation und Verweisen der Benutzer auf die Azure-Datenbankinstanz

> [!TIP]
> Sie können die gleichen Techniken auch verwenden, um Datenbanken zwischen virtuellen SQL Server-Computern in Azure zu verschieben. Es gibt z.B. keine unterstützte Möglichkeit, einen virtuellen Computer mit einem SQL Server-Katalogimage von einer Version bzw. Edition auf eine andere Version bzw. Edition zu aktualisieren. In diesem Fall müssen Sie einen neuen virtuellen SQL Server-Computer mit der neuen Version/Edition erstellen und dann eine der in diesem Artikel erläuterten Migrationstechniken verwenden, um Ihre Datenbanken zu verschieben. 

## <a name="choose-a-migration-method"></a>Auswählen einer Migrationsmethode

Um eine optimale Leistung bei der Datenübertragung zu erzielen, migrieren Sie die Datenbankdateien mithilfe einer komprimierten Sicherungsdatei zum virtuellen Azure-Computer.

Um die Ausfallzeit während des Datenbankmigrationsvorgangs auf ein Mindestmaß zu beschränken, verwenden Sie entweder die AlwaysOn-Option oder die Option zur Transaktionsreplikation.

Falls die oben genannten Methoden nicht verwendet werden können, migrieren Sie die Datenbank manuell. Im Allgemeinen beginnen Sie mit einer Datenbanksicherung, kopieren die Datenbanksicherung anschließend in Azure und führen dann eine Wiederherstellung der Datenbank durch. Sie können auch die Datenbankdateien selbst in Azure kopieren und anschließend anfügen. Es gibt mehrere Methoden, mit denen Sie diesen manuellen Prozess zum Migrieren einer Datenbank zu einem virtuellen Azure-Computer ausführen können.

> [!NOTE]
> Wenn Sie aus älteren Versionen von SQL Server auf SQL Server 2014 oder SQL Server 2016 aktualisieren, sollten Sie erwägen, ob Änderungen erforderlich sind. Es wird empfohlen, dass Sie alle Abhängigkeiten von Funktionen, die von der neuen Version von SQL Server nicht unterstützt werden, im Rahmen des Migrationsprojekts behandeln. Weitere Informationen zu den unterstützten Editionen und Szenarios finden Sie unter [Aktualisieren auf SQL Server](/sql/database-engine/install-windows/upgrade-sql-server).

In der folgenden Tabelle werden alle primären Migrationsmethoden aufgeführt, und es wird erläutert, wann die Verwendung der jeweiligen Methode am besten geeignet ist.

| Methode | Version der Quelldatenbank | Version der Zieldatenbank | Größeneinschränkung für Sicherung der Quelldatenbank | Notizen |
| --- | --- | --- | --- | --- |
| [Durchführen einer lokalen Sicherung mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer](#back-up-and-restore) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](../../../index.yml) | Dies ist eine sehr einfache und ausführlich getestete Methode zum Verschieben von Datenbanken zwischen Computern. |
| [Durchführen einer Sicherung auf eine URL und Wiederherstellung auf dem virtuellen Azure-Computer aus der URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 oder höher | SQL Server 2012 SP1 CU2 oder höher | < 12,8 TB für SQL Server 2016, andernfalls < 1 TB | Dies ist eine weitere Möglichkeit, die Sicherungsdatei mithilfe des Azure-Speichers auf den virtuellen Computer zu verschieben. |
| [Trennen und anschließendes Kopieren der Daten- und Protokolldateien in Azure Blob Storage, dann Anschließen an den SQL Server auf dem virtuellen Azure-Computer über URL](#detach-and-attach-from-a-url) | SQL Server 2005 oder höher |SQL Server 2014 oder höher | [Azure VM-Speichergrenze](../../../index.yml) | Verwenden Sie diese Methode, wenn Sie planen, [diese Dateien mithilfe des Azure Blob Storage-Diensts zu speichern](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) und sie mit SQL Server in einer Azure-VM zu verbinden, insbesondere bei sehr großen Datenbanken. |
| [Konvertieren eines lokalen physischen Computers in Hyper-V-VHD, Hochladen in Azure Blob Storage und anschließendes Bereitstellen als neuen virtuellen Computer mithilfe des hochgeladenen VHD](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](../../../index.yml) |Verwenden Sie diese Methode, wenn Sie [Ihre eigene SQL Server-Lizenz mitbringen](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) und eine Datenbank migrieren, die unter einer älteren Version von SQL Server ausgeführt wird oder wenn Sie System- und Benutzerdatenbanken gemeinsam als Teil der Migration von Datenbanken migrieren, die von anderen Benutzer- und/oder Systemdatenbanken abhängig sind. |
| [Versenden einer Festplatte mithilfe des Windows-Import-Export-Diensts](#ship-a-hard-drive) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](../../../index.yml) |Verwenden Sie den [Windows-Import/Export-Dienst](../../../storage/common/storage-import-export-service.md) , wenn das manuelle Kopieren zu langsam ist (etwa bei sehr großen Datenbanken). |
| [Verwenden des Assistenten zum Hinzufügen von Azure-Replikaten](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 oder höher |SQL Server 2012 oder höher |[Azure VM-Speichergrenze](../../../index.yml) |Beschränkt Ausfallzeiten auf ein Mindestmaß, Verwendung bei einer lokalen Always On-Bereitstellung |
| [Verwenden der SQL Server-Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](../../../index.yml) |Verwendung zum Beschränken von Ausfallzeiten auf ein Mindestmaß, wenn keine lokale Always On-Bereitstellung vorliegt |

## <a name="back-up-and-restore"></a>Sichern und Wiederherstellen

Sichern Sie Ihre Datenbank mit aktivierter Komprimierung, kopieren Sie die Sicherung auf den virtuellen Computer, und stellen Sie dann die Datenbank wieder her. Wenn die Sicherungsdatei größer als 1 TB ist, müssen Sie ein Stripeset erstellen, da die maximale Größe eines VM-Datenträgers 1 TB beträgt. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1. Führen Sie eine vollständige Datenbanksicherung an einem lokalen Speicherort durch.
2. Erstellen Sie einen virtuellen Computer mit der gewünschten Version von SQL Server, oder laden Sie ihn hoch.
3. Richten Sie die Verbindungen gemäß Ihren Anforderungen ein. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Resource Manager)](ways-to-connect-to-sql.md).
4. Kopieren Sie Ihre Sicherungsdateien mithilfe von Remotedesktop, Windows Explorer oder des Kopierbefehls auf den virtuellen Computer.

## <a name="backup-to-url-and-restore-from-url"></a>Sichern und Wiederherstellen über eine URL

Anstelle der Sicherung in eine lokale Datei können Sie eine [Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url) verwenden und dann eine Wiederherstellung über diese URL auf den virtuellen Computer durchführen. SQL Server 2016 unterstützt Stripesetsicherungssätze. Sie werden aus Leistungsgründen empfohlen und sind erforderlich, um die Größenbeschränkungen für Blobs überschreiten zu können. Für sehr große Datenbanken wird die Verwendung des [Windows-Import/Export-Diensts](../../../storage/common/storage-import-export-service.md) empfohlen.

## <a name="detach-and-attach-from-a-url"></a>Trennen und Anfügen über eine URL

Trennen Sie Ihre Datenbank und Protokolldateien, und übertragen Sie diese in [Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Fügen Sie anschließend die Datenbank über die URL auf Ihrem virtuellen Azure-Computer an. Verwenden Sie diese Methode, wenn sich die physischen Datenbankdateien in Blob Storage befinden sollen, was bei sehr großen Datenbanken nützlich ist. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1. Trennen Sie die Datenbankdateien von der lokalen Datenbankinstanz.
2. Kopieren Sie die getrennten Datenbankdateien in Azure Blob Storage mit dem Befehlszeilenprogramm [AZCopy](../../../storage/common/storage-use-azcopy-v10.md).
3. Fügen Sie die Datenbankdateien über die Azure-URL der SQL Server-Instanz in der Azure-VM an.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Konvertieren in eine VM, Hochladen auf eine URL und Bereitstellen als neue VM

Verwenden Sie diese Methode, um alle System- und Benutzerdatenbanken in einer lokalen SQL Server-Instanz zu einem virtuellen Computer zu migrieren. Verwenden Sie die folgenden allgemeinen Schritte, um eine vollständige SQL Server-Instanz mit dieser manuellen Methode zu migrieren:

1. Konvertieren Sie physische oder virtuelle Computer in Hyper-V-VHDs.
2. Laden Sie VHD-Dateien mithilfe des Cmdlets [Add-AzureVHD](/previous-versions/azure/dn495173(v=azure.100))in den Azure-Speicher hoch.
3. Stellen Sie einen neuen virtuellen Computer mithilfe der hochgeladenen VHD bereit.

> [!NOTE]
> Um eine gesamte Anwendung zu migrieren, sollten Sie die Verwendung von [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)erwägen.

## <a name="ship-a-hard-drive"></a>Versenden einer Festplatte

Mithilfe der Methode [Windows Import-Export-Dienst](../../../storage/common/storage-import-export-service.md) können Sie große Mengen von Dateidaten in Azure Blob Storage übertragen, beispielsweise, wenn das Hochladen über das Netzwerk zu kostenintensiv oder nicht machbar ist. Mit diesem Dienst versenden Sie Festplatten mit den Daten an ein Azure-Rechenzentrum, in dem die Daten in Ihr Speicherkonto hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Was ist SQL Server auf virtuellen Azure-Computern? (Windows)](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.

Eine Anleitung zum Erstellen eines SQL Servers auf einem virtuellen Azure-Computers aus einem erfassten Image finden Sie im CSS SQL Server Engineers-Blog unter [Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) (Tipps und Tricks zum „Klonen“ virtueller Azure SQL-Computer aus erfassten Images).