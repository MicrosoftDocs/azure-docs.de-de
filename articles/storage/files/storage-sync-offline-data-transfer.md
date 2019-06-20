---
title: Migrieren von Daten in die Azure-Dateisynchronisierung mithilfe von Azure Data Box und anderen Methoden
description: Informationen zum Migrieren von Massendaten auf mit der Azure-Dateisynchronisierung kompatible Weise.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700271"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrieren von Massendaten in die Azure-Dateisynchronisierung
Zum Migrieren von Massendaten in die Azure-Dateisynchronisierung gibt es zwei Möglichkeiten:

* **Hochladen Ihrer Dateien mithilfe der Azure-Dateisynchronisierung.** Dies ist die einfachste Methode. Verschieben Sie Ihre Dateien lokal in Windows Server 2012 R2 oder höher, und installieren Sie den Azure-Dateisynchronisierungs-Agent. Sobald die Synchronisierung eingerichtet ist, werden Ihre Dateien vom Server hochladen. (Unsere Kunden erreichen etwa alle zwei Tage eine durchschnittliche Hochladegeschwindigkeit von 1 TiB.) Um sicherzustellen, dass Ihr Server nicht zu viel Bandbreite für Ihr Rechenzentrum beansprucht, richten Sie ggf. einen [Zeitplan für die Bandbreitenbegrenzung](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) ein.
* **Übertragen Sie Ihre Dateien offline.** Wenn Sie nicht über genügend Bandbreite verfügen, können Sie Dateien möglicherweise nicht in angemessener Zeit in Azure hochladen. Die Herausforderung ist die anfängliche Synchronisierung der gesamten Menge von Dateien. Um diese Herausforderung zu meistern, verwenden Sie Offline-Massenmigrationstools wie z.B. die [Azure Data Box-Familie](https://azure.microsoft.com/services/storage/databox). 

In diesem Artikel wird erklärt, wie Sie Dateien offline auf eine Weise migrieren können, die mit der Azure-Dateisynchronisierung kompatibel ist. Befolgen Sie diese Anweisungen, um Dateikonflikte zu vermeiden und Ihre Zugriffssteuerlisten (ACLs) für Dateien und Ordner und Zeitstempel nach Aktivierung der Synchronisierung beizubehalten.

## <a name="online-migration-tools"></a>Onlinemigrationstools
Der Prozess, den wir in diesem Artikel beschreiben, funktioniert nicht nur für Data Box, sondern auch für andere Offlinemigrationstools. Er funktioniert auch für Onlinetools wie AzCopy, Robocopy bzw. Tools und Dienste von Partnern. Unabhängig davon, wie Sie die anfängliche Problematik beim Hochladen in den Griff bekommen, befolgen Sie die Schritte in diesem Artikel, um diese Tools in einer Weise zu verwenden, die mit der Azure-Dateisynchronisierung kompatibel ist.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vorteile der Verwendung eines Tools zur Offlineübertragung von Daten
Es folgen die wesentlichen Vorteile der Verwendung eines Übertragungstools wie Data Box für die Offlinemigration:

- Sie müssen nicht alle Ihre Dateien über das Netzwerk hochladen. Bei großen Namespaces kann dieses Tool für eine erhebliche Einsparung an Netzwerkbandbreite und Zeit sorgen.
- Wenn Sie die Azure-Dateisynchronisierung verwenden, lädt Ihr Liveserver unabhängig davon, welches Übertragungstool Sie einsetzen (Data Box, Azure Import/Export-Dienst usw.), nur die Dateien hoch, die sich ändern, nachdem Sie die Daten in Azure verschoben haben.
- Die Azure-Dateisynchronisierung synchronisiert Ihre Datei- und Ordner-ACLs, auch wenn das Offlinemigrationstool keine ACLs transportiert.
- Bei Verwendung von Azure Data Box und Azure-Dateisynchronisierung treten keine Ausfallzeiten auf. Wenn Sie Data Box zum Übertragen von Daten in Azure verwenden, nutzen Sie die Netzwerkbandbreite effizient und bewahren die Dateitreue. Sie halten Ihren Namespace außerdem auf dem neuesten Stand, indem Sie nur die Dateien hochladen, die sich ändern, nachdem Sie die Daten in Azure verschoben haben.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Voraussetzungen für die Offlinedatenübertragung
Sie sollten die Synchronisierung auf dem zu migrierenden Server nicht aktivieren, bevor Sie die Offlinedatenübertragung abschließen. Weitere Aspekte, die Sie zuerst berücksichtigen sollten:

- Wenn Sie Data Box für die Massenmigration verwenden möchten: Machen Sie sich mit den [Voraussetzungen für die Bereitstellung von Data Box](../../databox/data-box-deploy-ordered.md#prerequisites) vertraut.
- Planen Sie Ihre endgültige Topologie für die Azure-Dateisynchronisierung: [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- Wählen Sie Azure Storage-Konten aus, die die Dateifreigaben enthalten sollen, mit denen Sie die Synchronisierung durchführen möchten. Stellen Sie sicher, dass die Massenmigration zu temporären Stagingdateifreigaben im gleichen Speicherkonto stattfindet. Massenmigration kann nur mithilfe einer endgültigen und einer Stagingfreigabe aktiviert werden, die sich im gleichen Speicherkonto befinden.
- Eine Massenmigration kann nur beim Erstellen einer neuen Synchronisierungsbeziehung mit einem Server genutzt werden. Sie können keine Massenmigration mit einer vorhandenen Synchronisierungsbeziehung aktivieren.


## <a name="process-for-offline-data-transfer"></a>Prozess für die Offlinedatenübertragung
Im Folgenden erfahren Sie, wie Sie die Azure-Dateisynchronisierung so einrichten, dass sie mit Massenmigrationstools wie Azure Data Box kompatibel ist:

![Diagramm der Einrichtung der Azure-Dateisynchronisierung](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Schritt | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Schritt 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Bestellen Sie Ihre Data Box-Datenträger.](../../databox/data-box-deploy-ordered.md) Die Data Box-Familie bietet zur Erfüllung Ihrer Anforderungen [mehrere Produkte](https://azure.microsoft.com/services/storage/databox/data). Wenn Sie Ihre Data Box erhalten, befolgen Sie die zugehörige [Dokumentation zum Kopieren Ihrer Daten](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) in diesen UNC-Pfad auf der Data Box: *\\<Geräte-IP-Adresse\>\<Speicherkontoname_AzFile\>\<Freigabename\>* . *Freigabename* ist hier der Name der Stagingfreigabe. Senden Sie die Data Box zurück an Azure. |
| ![Schritt 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Warten Sie, bis die Dateien in den Azure-Dateifreigaben angezeigt werden, die Sie als temporäre Stagingfreigaben gewählt haben. *Aktivieren Sie nicht die Synchronisierung mit diesen Freigaben.* |
| ![Schritt 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Erstellen Sie eine neue leere Freigabe für jede Dateifreigabe, die Data Box für Sie erstellt hat. Diese neue Freigabe muss sich im gleichen Speicherkonto wie die Data Box-Freigabe befinden. [Erstellen einer Dateifreigabe in Azure Files](storage-how-to-create-file-share.md). |
| ![Schritt 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Erstellen Sie eine Synchronisierungsgruppe](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in einem Speichersynchronisierungsdienst. Verweisen Sie auf die leere Freigabe als Cloudendpunkt. Wiederholen Sie diesen Schritt für jede Data Box-Dateifreigabe. [Richten Sie die Azure-Dateisynchronisierung ein](storage-sync-files-deployment-guide.md). |
| ![Schritt 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Fügen Sie Ihr Liveserververzeichnis als Serverendpunkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint) hinzu. Geben Sie im Prozess an, dass Sie die Dateien in Azure verschoben haben, und verweisen Sie auf die Stagingfreigaben. Sie können das Cloudtiering nach Bedarf aktivieren oder deaktivieren. Verweisen Sie beim Erstellen eines Serverendpunkts für Ihren Liveserver auf die Stagingfreigabe. Aktivieren Sie auf dem Blatt **Serverendpunkt hinzufügen** unter **Offlinedatenübertragung** die Option **Aktiviert**, und wählen Sie dann die Stagingfreigabe aus, die sich im gleichen Speicherkonto wie der Cloudendpunkt befinden muss. Die Liste der verfügbaren Freigaben wird anhand des Speicherkontos und noch nicht synchronisierter Freigaben gefiltert. |

![Screenshot der Azure Portal-Benutzeroberfläche für die Aktivierung der Offlinedatenübertragung während der Erstellung eines neuen Serverendpunkts](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronisieren der Freigabe
Nachdem Sie Ihren Serverendpunkt erstellt haben, beginnt die Synchronisierung. Der Synchronisierungsprozess bestimmt, ob jede Datei auf dem Server auch in der Stagingfreigabe vorhanden ist, in der Data Box die Dateien abgelegt hat. Wenn die Datei dort vorhanden ist, kopiert der Synchronisierungsprozess die Datei aus der Stagingfreigabe, anstatt sie vom Server hochzuladen. Wenn die Datei nicht in der Stagingfreigabe vorhanden oder eine neuere Version auf dem lokalen Server verfügbar ist, lädt der Synchronisierungsprozess die Datei vom lokalen Server hoch.

> [!IMPORTANT]
> Sie können den Massenmigrationsmodus nur während der Erstellung eines Serverendpunkts aktivieren. Nachdem Sie einen Serverendpunkt eingerichtet haben, können Sie keine Massenmigrationsdaten von einem bereits synchronisierenden Server in den Namespace integrieren.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Zugriffssteuerungslisten und Zeitstempel auf Dateien und Ordnern
Die Azure-Dateisynchronisierung stellt sicher, dass ACLs für Dateien und Ordner auch dann vom Liveserver synchronisiert werden, wenn das verwendete Massenmigrationstool ursprünglich keine ACLs transportiert hat. Aus diesem Grund muss die Stagingfreigabe keine ACLs für Dateien und Ordner enthalten. Wenn Sie das Offlinedatenmigrations-Feature während der Erstellung eines neuen Serverendpunkts aktivieren, werden alle Datei-ACLs auf dem Server synchronisiert. Neu erstellte und geänderte Zeitstempel werden ebenfalls synchronisiert.

## <a name="shape-of-the-namespace"></a>Form des Namespaces
Wenn Sie die Synchronisierung aktivieren, bestimmt der Inhalt des Servers die Form des Namespace. Wenn Dateien nach der Data Box-Momentaufnahme und dem Abschluss der Migration vom lokalen Server gelöscht werden, werden diese Dateien nicht in den synchronisierenden Livenamespace verschoben. Sie verbleiben in der Stagingfreigabe, werden aber nicht kopiert. Dies ist notwendig, da die Synchronisierung den Namespace dem Liveserver entsprechend beibehält. Die Data Box-*Momentaufnahme* ist nur eine Stagingbasis für das effiziente Kopieren von Dateien. Sie ist nicht die Autorität für die Form des Livenamespace.

## <a name="cleaning-up-after-bulk-migration"></a>Bereinigung nach der Massenmigration 
Wenn der Server seine anfängliche Synchronisierung des Namespace abgeschlossen hat, verwenden die Massenmigrationsdateien in Data Box die Stagingdateifreigabe. Im Azure-Portal ändert sich auf dem Blatt **Eigenschaften des Serverendpunkts** im Abschnitt **Offlinedatenübertragung** der Status von **In Bearbeitung** in **Abgeschlossen**. 

![Screenshot des Blatts „Eigenschaften des Serverendpunkts“, auf dem sich die Status- und Deaktivierungssteuerelemente für die Offlinedatenübertragung befinden](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nun können Sie die Stagingfreigabe bereinigen, um Kosten zu sparen:

1. Wählen Sie auf dem Blatt **Eigenschaften des Serverendpunkts**, sobald der Status **Abgeschlossen** lautet, **Offlinedatenübertragung deaktivieren** aus.
2. Erwägen Sie, die Stagingfreigabe zu löschen, um Kosten zu sparen. Die Stagingfreigabe enthält wahrscheinlich keine Datei- und Ordner-ACLs, weshalb sie nicht sehr nützlich ist. Erstellen Sie zum Zweck zeitpunktorientierter Sicherungen eine echte [Momentaufnahme der synchronisierenden Azure-Dateifreigabe](storage-snapshots-files.md). Sie können [Azure Backup für Momentaufnahmen einrichten]( ../../backup/backup-azure-files.md), die nach einem Zeitplan erstellt werden.

Deaktivieren Sie den Offline-Datenübertragungsmodus nur, wenn der Status **Abgeschlossen** lautet oder wenn Sie den Prozess aufgrund einer Fehlkonfiguration abbrechen möchten. Wenn Sie den Modus mitten während einer Bereitstellung deaktivieren, werden Dateien auch dann vom Server hochgeladen, wenn Ihre Stagingfreigabe noch verfügbar ist.

> [!IMPORTANT]
> Nach dem Deaktivieren des Offline-Datenübertragungsmodus besteht keine Möglichkeit, ihn erneut zu aktivieren, wenn die Stagingfreigabe von der Massenmigration immer noch verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
