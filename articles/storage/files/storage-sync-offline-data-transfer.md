---
title: Migrieren von Daten zur Azure-Dateisynchronisierung mit Azure Data Box
description: Hier erfahren Sie, wie Sie Massendaten offline und auf eine Weise migrieren, die mit der Azure-Dateisynchronisierung kompatibel ist. Vermeiden Sie Dateikonflikte, und bewahren Sie Datei- und Ordner-ACLs sowie Zeitstempel nach dem Aktivieren der Synchronisierung.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017626"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrieren von Massendaten zur Azure-Dateisynchronisierung mit Azure Data Box
Zum Migrieren von Massendaten in die Azure-Dateisynchronisierung gibt es zwei Möglichkeiten:

* **Hochladen Ihrer Dateien mithilfe der Azure-Dateisynchronisierung.** Dies ist die einfachste Methode. Verschieben Sie Ihre Dateien lokal in Windows Server 2012 R2 oder höher, und installieren Sie den Azure-Dateisynchronisierungs-Agent. Sobald die Synchronisierung eingerichtet ist, werden Ihre Dateien vom Server hochladen. (Unsere Kunden erreichen etwa alle zwei Tage eine durchschnittliche Hochladegeschwindigkeit von 1 TiB.) Um sicherzustellen, dass Ihr Server nicht zu viel Bandbreite für Ihr Rechenzentrum beansprucht, richten Sie ggf. einen [Zeitplan für die Bandbreitenbegrenzung](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) ein.
* **Übertragen Sie Ihre Dateien offline.** Wenn Sie nicht über genügend Bandbreite verfügen, können Sie Dateien möglicherweise nicht in angemessener Zeit in Azure hochladen. Die Herausforderung ist die anfängliche Synchronisierung der gesamten Menge von Dateien. Um diese Herausforderung zu meistern, verwenden Sie Offline-Massenmigrationstools wie z.B. die [Azure Data Box-Familie](https://azure.microsoft.com/services/storage/databox). 

In diesem Artikel wird erklärt, wie Sie Dateien offline auf eine Weise migrieren können, die mit der Azure-Dateisynchronisierung kompatibel ist. Befolgen Sie diese Anweisungen, um Dateikonflikte zu vermeiden und Ihre Zugriffssteuerlisten (ACLs) für Dateien und Ordner und Zeitstempel nach Aktivierung der Synchronisierung beizubehalten.

## <a name="migration-tools"></a>Migrationstools
Der Prozess, den wir in diesem Artikel beschreiben, funktioniert nicht nur für Data Box, sondern auch für andere Offlinemigrationstools. Er funktioniert auch für Tools wie AzCopy oder Robocopy oder Partnertools und -dienste, die direkt über das Internet funktionieren. Um die anfängliche Herausforderung für das Hochladen zu meistern, befolgen Sie die Schritte in diesem Artikel, um diese Tools in einer Weise zu verwenden, die mit der Azure-Dateisynchronisierung kompatibel ist.

In einigen Fällen müssen Sie von einem Windows-Server zu einem anderen Windows-Server wechseln, bevor Sie die Azure-Dateisynchronisierung übernehmen. Der [Speichermigrationsdienst](/windows-server/storage/storage-migration-service/overview) (Storage Migration Service, SMS) kann dabei helfen. Egal, ob Sie zu einer Serverbetriebssystemversion migrieren müssen, die von Azure-Dateisynchronisierung unterstützt wird (Windows Server 2012R2 und höher), oder einfach migrieren müssen, weil Sie ein neues System für Azure-Dateisynchronisierung kaufen, SMS bietet zahlreiche Features und Vorteile, die Ihnen helfen, die jeweilige Migration reibungslos zu erledigen.

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
| ![Schritt 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Bestellen Sie Ihre Data Box-Datenträger.](../../databox/data-box-deploy-ordered.md) Die Data Box-Familie bietet zur Erfüllung Ihrer Anforderungen [mehrere Produkte](https://azure.microsoft.com/services/storage/databox/data). Wenn Sie Ihre Data Box erhalten, befolgen Sie die zugehörige [Dokumentation zum Kopieren Ihrer Daten](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) in den folgenden UNC-Pfad auf der Data Box: *\\<DeviceIPAddress\>\<StorageAccountName_AzFile\>\<ShareName\>* . *Freigabename* ist hier der Name der Stagingfreigabe. Senden Sie die Data Box zurück an Azure. |
| ![Schritt 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Warten Sie, bis die Dateien in den Azure-Dateifreigaben angezeigt werden, die Sie als temporäre Stagingfreigaben gewählt haben. *Aktivieren Sie nicht die Synchronisierung mit diesen Freigaben.* |
| ![Schritt 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Erstellen Sie eine neue leere Freigabe für jede Dateifreigabe, die Data Box für Sie erstellt hat. Diese neue Freigabe muss sich im gleichen Speicherkonto wie die Data Box-Freigabe befinden. [Erstellen einer Dateifreigabe in Azure Files](storage-how-to-create-file-share.md).</li><li>[Erstellen Sie eine Synchronisierungsgruppe](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in einem Speichersynchronisierungsdienst. Verweisen Sie auf die leere Freigabe als Cloudendpunkt. Wiederholen Sie diesen Schritt für jede Data Box-Dateifreigabe. [Richten Sie die Azure-Dateisynchronisierung ein](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Schritt 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Fügen Sie Ihr Liveserververzeichnis als Serverendpunkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint) hinzu. Geben Sie im Prozess an, dass Sie die Dateien in Azure verschoben haben, und verweisen Sie auf die Stagingfreigaben. Sie können das Cloudtiering nach Bedarf aktivieren oder deaktivieren. Verweisen Sie beim Erstellen eines Serverendpunkts für Ihren Liveserver auf die Stagingfreigabe. Aktivieren Sie auf dem Blatt **Serverendpunkt hinzufügen** unter **Offlinedatenübertragung** die Option **Aktiviert**, und wählen Sie dann die Stagingfreigabe aus, die sich im gleichen Speicherkonto wie der Cloudendpunkt befinden muss. Die Liste der verfügbaren Freigaben wird anhand des Speicherkontos und noch nicht synchronisierter Freigaben gefiltert. Der Screenshot unter dieser Tabelle zeigt, wie Sie während der Erstellung des Serverendpunkts im Azure-Portal auf die Data Box-Freigabe verweisen. |
| ![Schritt 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Nachdem Sie im vorherigen Schritt den Serverendpunkt hinzugefügt haben, werden die Daten automatisch von der richtigen Quelle übertragen. Im Abschnitt [Synchronisieren der Freigabe](#syncing-the-share) wird erläutert, wann Daten aus der Data Box-Freigabe oder vom Windows-Server übertragen werden. |
| |

![Screenshot der Azure Portal-Benutzeroberfläche für die Aktivierung der Offlinedatenübertragung während der Erstellung eines neuen Serverendpunkts](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronisieren der Freigabe
Nachdem Sie den Serverendpunkt erstellt haben, beginnt die Synchronisierung. Der Synchronisierungsprozess bestimmt, ob jede Datei auf dem Server auch in der Stagingfreigabe vorhanden ist, in der Data Box die Dateien abgelegt hat. Wenn die Datei dort vorhanden ist, kopiert der Synchronisierungsprozess die Datei aus der Stagingfreigabe, anstatt sie vom Server hochzuladen. Wenn die Datei nicht in der Stagingfreigabe vorhanden oder eine neuere Version auf dem lokalen Server verfügbar ist, lädt der Synchronisierungsprozess die Datei vom lokalen Server hoch.

Beim Synchronisieren der Freigabe werden alle fehlenden Dateiattribute, Berechtigungen oder Zeitstempel aus den Dateivarianten auf dem lokalen Server zusammengeführt und mit ihren Dateientsprechungen aus der Data Box-Freigabe kombiniert. Dadurch wird sichergestellt, dass jede Datei und jeder Ordner mit der bestmöglichen Dateigenauigkeit in die Azure-Dateifreigabe gelangt.

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
2. Erwägen Sie, die Stagingfreigabe zu löschen, um Kosten zu sparen. Die Stagingfreigabe enthält wahrscheinlich keine Datei- und Ordner-ACLs, weshalb sie wahrscheinlich nicht nützlich ist. Erstellen Sie zum Zweck zeitpunktorientierter Sicherungen eine echte [Momentaufnahme der synchronisierenden Azure-Dateifreigabe](storage-snapshots-files.md). Sie können [Azure Backup für Momentaufnahmen einrichten]( ../../backup/backup-afs.md), die nach einem Zeitplan erstellt werden.

Deaktivieren Sie den Offline-Datenübertragungsmodus nur, wenn der Status **Abgeschlossen** lautet oder wenn Sie den Prozess aufgrund einer Fehlkonfiguration abbrechen möchten. Wenn Sie den Modus mitten während einer Bereitstellung deaktivieren, werden Dateien auch dann vom Server hochgeladen, wenn Ihre Stagingfreigabe noch verfügbar ist.

> [!IMPORTANT]
> Nach dem Deaktivieren des Offline-Datenübertragungsmodus besteht keine Möglichkeit, ihn erneut zu aktivieren, wenn die Stagingfreigabe von der Massenmigration immer noch verfügbar ist.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure-Dateisynchronisierung und vorab per Seeding hinzugefügte Dateien in der Cloud

Falls Sie außer Data Box noch eine andere Methode verwendet haben, um ein Seeding für Dateien in einer Azure-Dateifreigabe durchzuführen (beispielweise AzCopy, RoboCopy, eine Cloudsicherung oder eine andere Methode), sollten Sie trotzdem den in diesem Artikel beschriebenen [Prozess für die Offlinedatenübertragung](#process-for-offline-data-transfer) ausführen. Ignorieren Sie dabei lediglich Data Box als die Methode, mit der Ihre Dateien in die Cloud gelangen. Achten Sie jedoch unbedingt darauf, dass Sie dem Prozess für das Seeding der Dateien in einer *Stagingfreigabe* (und nicht in der endgültigen, mit der Azure-Dateisynchronisierung verbundenen Freigabe) folgen.

> [!WARNING]
> **Folgen Sie dem Prozess für das Seeding von Dateien in einer Stagingfreigabe** (und nicht in der endgültigen, mit der Azure-Dateisynchronisierung verbundenen Freigabe). Andernfalls können Dateikonflikte entstehen (beide Dateiversionen werden gespeichert), und es kann passieren, dass Dateien, die auf dem Liveserver gelöscht wurden, wiederhergestellt werden, wenn sie in Ihrem älteren, per Seeding hinzugefügten Dateisatz noch vorhanden sind. Darüber hinaus werden Ordneränderungen zusammengeführt, was die Trennung des Namespace nach einem solchen Fehler äußerst schwierig macht.

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)