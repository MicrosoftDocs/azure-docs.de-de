---
title: Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Computer | Microsoft Docs
description: Erfahren Sie mehr über das Migrieren einer lokalen Benutzerdatenbank auf SQL Server in einem virtuellen Azure-Computer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8e5a7bfc243fc8c797ffc66b2130756567ddc0fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795773"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer

Es gibt verschiedene Methoden zur Migration einer lokalen SQL Server-Benutzerdatenbank zu SQL Server auf einem virtuellen Azure-Computer. In diesem Artikel werden verschiedene Methoden kurz erläutert und die besten Methoden für unterschiedliche Szenarien empfohlen.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 und SQL Server 2008 R2 nähern sich dem [Ende ihres Supportlebenszyklus](https://www.microsoft.com/sql-server/sql-server-2008) für lokale Instanzen. Um die Unterstützung zu erweitern, können Sie Ihre SQL Server-Instanz entweder zu einer Azure-VM migrieren oder erweiterte Sicherheitsupdates erwerben, um eine lokale Bereitstellung zu verwenden. Weitere Informationen finden Sie unter [Erweitern der Unterstützung für SQL Server 2008 und 2008 R2 mit Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Was sind die primären Migrationsmethoden?
Die primären Migrationsmethoden sind:

* Durchführen eines lokalen Backups mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer
* Durchführen einer Sicherung auf eine URL und Wiederherstellung im virtuellen Azure-Computer aus der URL
* Trennen und anschließendes Kopieren der Daten-und Protokolldateien in Azure Blob Storage, dann Verbinden mit SQL Server in der Azure-VM über URL
* Konvertieren eines lokalen physischen Computers in Hyper-V-VHD, Hochladen in Azure Blob Storage und anschließendes Bereitstellen als neue VM mithilfe des hochgeladenen VHD
* Versenden einer Festplatte mithilfe des Windows-Import/Export-Diensts
* Bei einer lokalen Bereitstellung von AlwaysOn-Verfügbarkeitsgruppen: Erstellen eines Replikats in Azure mithilfe des [Assistenten zum Hinzufügen von Azure-Replikaten](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) und anschließendes Durchführen eines Failovers, bei dem Benutzer auf die Azure-Datenbankinstanz verwiesen werden
* Verwenden der SQL Server- [Transaktionsreplikation](https://msdn.microsoft.com/library/ms151176.aspx) , um die Azure SQL Server-Instanz als Abonnenten zu konfigurieren, und anschließendes Deaktivieren der Replikation, um Benutzer auf die Azure-Datenbankinstanz zu verweisen

> [!TIP]
> Sie können die gleichen Techniken auch verwenden, um Datenbanken zwischen virtuellen SQL Server-Computern in Azure zu verschieben. Es gibt z.B. keine unterstützte Möglichkeit, einen virtuellen Computer mit einem SQL Server-Katalogimage von einer Version bzw. Edition auf eine andere Version bzw. Edition zu aktualisieren. In diesem Fall müssen Sie einen neuen virtuellen SQL Server-Computer mit der neuen Version/Edition erstellen und dann eine der in diesem Artikel erläuterten Migrationstechniken verwenden, um Ihre Datenbanken zu verschieben. 

## <a name="choosing-your-migration-method"></a>Auswählen der Migrationsmethode
Um eine optimale Leistung bei der Datenübertragung zu erzielen, migrieren Sie die Datenbankdateien mithilfe einer komprimierten Sicherungsdatei zum virtuellen Azure-Computer.

Um die Ausfallzeit während des Datenbankmigrationsvorgangs auf ein Mindestmaß zu beschränken, verwenden Sie entweder die AlwaysOn-Option oder die Option zur Transaktionsreplikation.

Falls die oben genannten Methoden nicht verwendet werden können, migrieren Sie die Datenbank manuell. Mit dieser Methode beginnen Sie in der Regel mit einer Datenbanksicherung, kopieren die Datenbanksicherung nach Azure und führen dann eine Wiederherstellung der Datenbank durch. Sie können auch die Datenbankdateien selbst in Azure kopieren und anschließend anfügen. Es gibt mehrere Methoden, mit denen Sie diesen manuellen Prozess zum Migrieren einer Datenbank zu einem virtuellen Azure-Computer ausführen können.

> [!NOTE]
> Wenn Sie aus älteren Versionen von SQL Server auf SQL Server 2014 oder SQL Server 2016 aktualisieren, sollten Sie erwägen, ob Änderungen erforderlich sind. Es wird empfohlen, dass Sie alle Abhängigkeiten von Funktionen, die von der neuen Version von SQL Server nicht unterstützt werden, im Rahmen des Migrationsprojekts behandeln. Weitere Informationen zu den unterstützten Editionen und Szenarios finden Sie unter [Aktualisieren auf SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

In der folgenden Tabelle werden alle primären Migrationsmethoden aufgeführt, und es wird erläutert, wann die Verwendung der jeweiligen Methode am besten geeignet ist.

| Methode | Version der Quelldatenbank | Version der Zieldatenbank | Größeneinschränkung für Sicherung der Quelldatenbank | Notizen |
| --- | --- | --- | --- | --- |
| [Durchführen einer lokalen Sicherung mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer](#backup-and-restore) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Dies ist eine sehr einfache und ausführlich getestete Methode zum Verschieben von Datenbanken auf verschiedene Computer. |
| [Durchführen einer Sicherung auf eine URL und Wiederherstellung auf dem virtuellen Azure-Computer aus der URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 oder höher |SQL Server 2012 SP1 CU2 oder höher |< 12,8 TB für SQL Server 2016, andernfalls < 1 TB | Dies ist eine weitere Möglichkeit, die Sicherungsdatei mithilfe des Azure-Speichers auf den virtuellen Computer zu verschieben. |
| [Trennen und anschließendes Kopieren der Daten- und Protokolldateien in Azure Blob Storage, dann Anschließen an den SQL Server auf dem virtuellen Azure-Computer über URL](#detach-and-attach-from-url) |SQL Server 2005 oder höher |SQL Server 2014 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Verwenden Sie diese Methode, wenn Sie planen, [diese Dateien mithilfe des Azure Blob Storage-Diensts zu speichern](https://msdn.microsoft.com/library/dn385720.aspx) und sie mit SQL Server in einer Azure-VM zu verbinden, insbesondere bei sehr großen Datenbanken. |
| [Konvertieren eines lokalen physischen Computers in Hyper-V-VHD, Hochladen in Azure Blob Storage und anschließendes Bereitstellen als neuen virtuellen Computer mithilfe des hochgeladenen VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Verwenden Sie diese Methode, wenn Sie [Ihre eigene SQL Server-Lizenz mitbringen](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) und eine Datenbank migrieren, die unter einer älteren Version von SQL Server ausgeführt wird oder wenn Sie System- und Benutzerdatenbanken gemeinsam als Teil der Migration von Datenbanken migrieren, die von anderen Benutzer- und/oder Systemdatenbanken abhängig sind. |
| [Versenden einer Festplatte mithilfe des Windows-Import-Export-Diensts](#ship-hard-drive) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Verwenden Sie den [Windows-Import/Export-Dienst](../../../storage/common/storage-import-export-service.md) , wenn das manuelle Kopieren zu langsam ist (etwa bei sehr großen Datenbanken). |
| [Verwenden des Assistenten zum Hinzufügen von Azure-Replikaten](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 oder höher |SQL Server 2012 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Beschränkt Ausfallzeiten auf ein Mindestmaß, Verwendung bei einer lokalen Always On-Bereitstellung |
| [Verwenden der SQL Server-Transaktionsreplikation](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 oder höher |SQL Server 2005 oder höher |[Azure VM-Speichergrenze](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Verwendung zum Beschränken von Ausfallzeiten auf ein Mindestmaß, wenn keine lokale Always On-Bereitstellung vorliegt |

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen
Sichern Sie Ihre Datenbank mit aktivierter Komprimierung, kopieren Sie die Sicherung auf den virtuellen Computer, und stellen Sie dann die Datenbank wieder her. Wenn die Sicherungsdatei größer als 1 TB ist, müssen Sie ein Stripeset erstellen, da die maximale Größe der VM-Datenträger 1 TB beträgt. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1. Führen Sie eine vollständige Datenbanksicherung an einem lokalen Speicherort durch.
2. Erstellen Sie einen virtuellen Computer mit der gewünschten Version von SQL Server, oder laden Sie ihn hoch.
3. Richten Sie die Verbindungen gemäß Ihren Anforderungen ein. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopieren Sie Ihre Sicherungsdateien mithilfe von Remotedesktop, Windows Explorer oder des Kopierbefehls auf den virtuellen Computer.

## <a name="backup-to-url-and-restore"></a>Sichern und Wiederherstellen per URL
Anstelle einer Sicherung in eine lokale Datei können Sie eine [Sicherung über URLs](https://msdn.microsoft.com/library/dn435916.aspx) verwenden und dann eine Wiederherstellung über diese URL auf den virtuellen Computer durchführen. SQL Server 2016 unterstützt Stripesetsicherungen, die aus Leistungsgründen empfohlen werden und erforderlich sind, um die Größenbeschränkung pro Blob zu umgehen. Für sehr große Datenbanken wird die Verwendung des [Windows-Import/Export-Diensts](../../../storage/common/storage-import-export-service.md) empfohlen.

## <a name="detach-and-attach-from-url"></a>Trennen und Anfügen per URL
Trennen Sie Ihre Datenbank und Protokolldateien, und übertragen Sie diese in [Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx). Fügen Sie anschließend die Datenbank über die URL auf Ihrem virtuellen Azure-Computer an. Verwenden Sie diese Methode, wenn die physischen Datenbankdateien in Blob Storage platziert werden sollen. Dies kann bei sehr umfangreichen Datenbanken nützlich sein. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1. Trennen Sie die Datenbankdateien von der lokalen Datenbankinstanz.
2. Kopieren Sie die getrennten Datenbankdateien in Azure Blob Storage mit dem Befehlszeilenprogramm [AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Fügen Sie die Datenbankdateien über die Azure-URL der SQL Server-Instanz in der Azure-VM an.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertieren in eine VM, Hochladen zur URL und Bereitstellen als neue VM
Verwenden Sie diese Methode, um alle System- und Benutzerdatenbanken in einer lokalen SQL Server-Instanz zu einem virtuellen Computer zu migrieren. Verwenden Sie die folgenden allgemeinen Schritte, um eine vollständige SQL Server-Instanz mit dieser manuellen Methode zu migrieren:

1. Konvertieren Sie physische oder virtuelle Computer in Hyper-V-VHDs.
2. Laden Sie VHD-Dateien mithilfe des Cmdlets [Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)in den Azure-Speicher hoch.
3. Stellen Sie einen neuen virtuellen Computer mithilfe der hochgeladenen VHD bereit.

> [!NOTE]
> Um eine gesamte Anwendung zu migrieren, sollten Sie die Verwendung von [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)erwägen.

## <a name="ship-hard-drive"></a>Versenden einer Festplatte
Mithilfe der Methode [Windows Import-Export-Dienst](../../../storage/common/storage-import-export-service.md) können Sie große Mengen von Dateidaten in Azure Blob Storage übertragen, beispielsweise, wenn das Hochladen über das Netzwerk zu kostenintensiv oder nicht machbar ist. Mit diesem Dienst versenden Sie Festplatten mit den Daten an ein Azure-Rechenzentrum, in dem die Daten in Ihr Speicherkonto hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte
Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](virtual-machines-windows-sql-server-iaas-faq.md) weitere Informationen.

Eine Anleitung zum Erstellen eines virtuellen Azure SQL Server-Computers aus einem erfassten Image finden Sie im CSS SQL Server Engineers-Blog unter [Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) (Tipps und Tricks zum „Klonen“ virtueller Azure SQL-Computer aus erfassten Images).

