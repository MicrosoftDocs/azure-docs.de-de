---
title: Verwenden von Azure Storage für die Sicherung und Wiederherstellung von SQL Server | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SQL Server in Azure Storage sichern. Erläutert die Vorteile der Sicherung von SQL-Datenbanken in Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: b4100800385792557358d3fb6438f52650483f89
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359790"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Verwenden von Azure Storage für die Sicherung und Wiederherstellung von SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ab SQL Server 2012 SP1 CU2 können Sie SQL Server-Sicherungen direkt in Azure Blob Storage schreiben. Mit dieser Funktion können Sie Sicherungs- und Wiederherstellungsvorgänge in Azure Blob Storage und in SQL Server-Datenbanken ausführen. Die Sicherung in der Cloud bietet zahlreiche Vorteile: Verfügbarkeit, unbegrenzter georeplizierter standortexterner Speicher und problemlose Migration von Daten in die und aus der Cloud. Sie können BACKUP- und RESTORE-Anweisungen mithilfe von T-SQL oder SMO aufrufen.

## <a name="overview"></a>Übersicht
In SQL Server 2016 wurden neue Funktionen eingeführt: Sie können die [Dateimomentaufnahme-Sicherung](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) verwenden, um nahezu sofortige Sicherungen und unglaublich schnelle Wiederherstellungen durchzuführen.

In diesem Thema werden die Vorteile von Azure Storage für SQL Server-Sicherungen erläutert. Anschließend werden die notwendigen Komponenten beschrieben. Die Ressourcen am Ende des Artikels bieten exemplarische Vorgehensweisen und zusätzliche Informationen zur Verwendung dieses Diensts mit Ihren SQL Server-Sicherungen.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Vorteile von Azure Blob Storage für SQL Server-Sicherungen
Bei der Sicherung von SQL Server gibt es verschiedene Herausforderungen. Hierzu gehören die Speicherverwaltung, das Risiko eines Speicherausfalls, der Zugriff auf Offsitespeicher und die Konfiguration der Hardware. Viele dieser Herausforderungen lassen sich durch Verwenden von Azure Blob Storage für SQL Server-Sicherungen meistern. Betrachten Sie die folgenden Vorteile:

* **Benutzerfreundlichkeit:** Das Speichern von Sicherungen in Azure-Blobs ist eine praktische, flexible und einfach zugängliche Option für die Offsitespeicherung. Die Erstellung von Offsitespeicher für SQL Server-Sicherungen erfordert ggf. lediglich das Ändern vorhandener Skripts/Aufträge für die Verwendung der **BACKUP TO URL**-Syntax. Die räumliche Entfernung zwischen Offsitespeicher und Produktionsdatenbank ist in der Regel so groß, dass ein Notfall niemals Auswirkungen auf beide Standorte hat. Die [Georeplikation Ihrer Azure-Blobs](../../../storage/common/storage-redundancy.md) bietet zusätzlichen Schutz bei einem Notfall, der sich auf die gesamte Region auswirkt.
* **Sicherungsarchiv:** Azure Blob Storage bietet eine bessere Alternative als die sonst übliche Archivierung von Sicherungen auf Band. Bandspeichermedien müssen unter gleichzeitiger Berücksichtigung von Sicherheitsvorkehrungen u. U. physisch an einen externen Standort umgelagert werden. Die Speicherung von Sicherungen in Azure Blob Storage stellt eine sofortige, hochverfügbare und dauerhafte Archivierungsoption dar.
* **Verwaltete Hardware:** Bei Azure-Diensten fällt kein Mehraufwand für die Hardwareverwaltung an. Azure-Dienste verwalten die Hardware und stellen geografische Replikation für Redundanz und Schutz vor Hardwareausfällen zur Verfügung.
* **Unbegrenzter Speicherplatz:** Durch eine direkte Sicherung in Azure-Blobs haben Sie Zugriff auf praktisch unbegrenzten Speicher. Die Sicherung in einem virtuellen Azure-Computer dagegen weist Einschränkungen aufgrund der Computergröße auf. Die Anzahl von Datenträgern, die für Sicherungen an einen virtuellen Azure-Computer angefügt werden können, ist begrenzt. Bei einer sehr großen Instanz beträgt die maximale Anzahl 16 Datenträger, während kleinere Instanzen weniger Datenträger unterstützen.
* **Verfügbarkeit der Sicherungen:** Auf in Azure-Blobs gespeicherte Sicherungen kann jederzeit von überall aus zugegriffen werden. So können Sie Daten ganz einfach auf einer SQL Server-Instanz wiederherstellen, ohne Datenbanken anfügen/trennen oder die VHD herunterladen und anfügen zu müssen.
* **Kosten**: Sie zahlen nur für den genutzten Dienst. Die Option kann genauso kosteneffizient wie eine externe Sicherungs-/Archivierungslösung sein. Weitere Informationen finden Sie im [Azure-Preisrechner](https://go.microsoft.com/fwlink/?LinkId=277060 "Preisrechner") sowie in der [Azure-Preisübersicht](https://go.microsoft.com/fwlink/?LinkId=277059 "Artikel zu den Preisen").
* **Speichermomentaufnahmen:** Wenn Datenbankdateien in einem Azure-Blob gespeichert werden und Sie SQL Server 2016 verwenden, können Sie die [Dateimomentaufnahme-Sicherung](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) für nahezu sofortige Sicherungen und extrem schnelle Wiederherstellungen verwenden.

Weitere Details finden Sie unter [Sicherung und Wiederherstellung von SQL Server mit Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Die beiden folgenden Abschnitte bieten eine Einführung in Azure Blob Storage, einschließlich der erforderlichen SQL Server-Komponenten. Es ist wichtig, die Komponenten zu kennen und zu wissen, wie sie interagieren, um Daten in Azure Blob Storage erfolgreich zu sichern und daraus wiederherzustellen.

## <a name="azure-blob-storage-components"></a>Azure Blob Storage-Komponenten
Die folgenden Azure-Komponenten werden bei einer Sicherung in Azure Blob Storage verwendet.

| Komponente | BESCHREIBUNG |
| --- | --- |
| **Speicherkonto** |Das Speicherkonto ist der Ausgangspunkt für alle Speicherdienste. Um auf Azure Blob Storage zuzugreifen, erstellen Sie zunächst ein Azure Storage-Konto. Weitere Informationen zu Azure Blob Storage finden Sie unter [Verwenden von Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Container** |Ein Container stellt einen Satz Blobs als Gruppe bereit und kann eine unbegrenzte Anzahl von Blobs enthalten. Um eine SQL Server-Sicherung in Azure Blob Storage zu speichern, muss mindestens ein Stammcontainer erstellt worden sein. |
| **Blob** |Eine Datei eines beliebigen Typs und beliebiger Größe. Blobs sind über das folgende URL-Format adressierbar: **https://[Speicherkonto].blob.core.windows.net/[Container]/[Blob]** . Weitere Informationen über Seitenblobs finden Sie unter [Grundlegendes zu Block- und Seitenblobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>SQL Server-Komponenten
Die folgenden SQL Server-Komponenten werden bei einer Sicherung in Azure Blob Storage verwendet.

| Komponente | BESCHREIBUNG |
| --- | --- |
| **URL** |Eine URL gibt einen URI (Uniform Resource Identifier) für eine eindeutige Sicherungsdatei an. Die URL dient zur Angabe des Speicherorts und Namens der SQL Server-Sicherungsdatei. Die URL muss auf ein tatsächliches BLOB, nicht nur auf einen Container verweisen. Wenn das BLOB nicht vorhanden ist, wird es erstellt. Das Angeben eines vorhandenen Blobs führt zu einem Fehler bei BACKUP – es sei denn, die Option > WITH FORMAT wird angegeben. Hier sehen Sie ein Beispiel einer URL, die Sie im BACKUP-Befehl angeben können: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]** . HTTPS wird empfohlen, ist jedoch nicht erforderlich. |
| **Credential** |Die Informationen, die zum Herstellen einer Verbindung mit Azure Blob Storage und zur Authentifizieren damit erforderlich sind, werden als Anmeldeinformationen gespeichert. Damit SQL Server Sicherungen in einen Azure-Blob schreiben oder daraus wiederherstellen kann, müssen SQL Server-Anmeldeinformationen erstellt werden. Weitere Informationen finden Sie unter [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql). |

> [!NOTE]
> SQL Server 2016 wurde aktualisiert und unterstützt jetzt Blockblobs. Weitere Informationen finden Sie unter [Tutorial: Verwenden von Microsoft Azure Blob Storage mit SQL Server 2016-Datenbanken](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016).
> 
> 

## <a name="next-steps"></a>Nächste Schritte
1. Erstellen Sie ein Azure-Konto, falls Sie noch keins besitzen. Wenn Sie Azure evaluieren, können Sie die [kostenlose Testversion](https://azure.microsoft.com/free/)verwenden.
2. Arbeiten Sie dann eines der folgenden Tutorials durch, in denen Sie Anleitungen zum Erstellen eines Speicherkontos und zum Durchführen einer Wiederherstellung erhalten.
   
   * **SQL Server 2014**: [Tutorial: Sicherung und Wiederherstellung von SQL Server 2014 in Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)
   * **SQL Server 2016:** [Tutorial: Verwenden von Microsoft Azure Blob Storage mit SQL Server 2016-Datenbanken](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016).
3. Lesen Sie weitere Artikel zu diesem Thema, und beginnen Sie mit [Sicherung und Wiederherstellung von SQL Server mit Microsoft Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Wenn Probleme auftreten, lesen Sie das Thema [SQL Server-URL-Sicherung – bewährte Methoden und Problembehandlung](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).

Weitere Sicherungs- und Wiederherstellungsoptionen für SQL Server finden Sie unter [Sicherung und Wiederherstellung von SQL Server auf Azure Virtual Machines](backup-restore.md).