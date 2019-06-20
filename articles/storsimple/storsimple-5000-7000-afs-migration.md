---
title: Migrieren von Daten aus StorSimple Serie 5000/7000 zur Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Beschreibung des Migrierens von Daten aus StorSimple Serie 5000/7000 zur Azure-Dateisynchronisierung (Azure File Sync, AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150735"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrieren von Daten aus der StorSimple-Serie 5000/7000 zur Azure-Dateisynchronisierung

> [!IMPORTANT]
> Ab dem 9. Juli 2019 werden die StorSimple-Serien 5000 und 7000 nicht mehr unterstützt. Es wird empfohlen, dass Kunden der StorSimple-Serie 5000/7000 zu einer der in diesem Dokument beschriebenen Alternativen migrieren.

Die Datenmigration ist der Prozess des Verschiebens von Daten aus einem Speicherort in einen anderen. Dabei muss eine genaue Kopie der auf einem Gerät befindlichen aktuellen Daten einer Organisation auf einem anderen Gerät erstellt werden – vorzugsweise ohne Unterbrechung oder Deaktivierung aktiver Anwendungen – und dann die gesamte Eingabe/Ausgabe-Aktivität (E/A) auf das neue Gerät umgeleitet werden. 

Speichergeräte der StorSimple-Serien 5000 und 7000 erreichen im Juli 2019 ihr das Dienstende. Dies bedeutet, dass Microsoft nach Juli 2019 nicht mehr in der Lage sein wird, die Hardware und Software der StorSimple-Serien 5000 und 7000 zu unterstützen. Kunden, die diese Geräte verwenden, sollten ihre StorSimple-Daten zu anderen Hybridspeicherlösungen in Azure migrieren. Dieser Artikel behandelt die Migration von Daten von einem Gerät der StorSimple-Serie 5000/7000 zur Azure-Dateisynchronisierung (AFS).

## <a name="intended-audience"></a>Zielpublikum

Dieser Artikel richtet sich an IT-Spezialisten (IT) und Wissensarbeiter, die für das Bereitstellen und Verwalten von Geräten der StorSimple-Serie 5000/7000 im Rechenzentrum verantwortlich sind. Für Kunden, die ihre StorSimple-Geräte für Dateiserverworkloads (mit Windows Server) einsetzen, ist dieser Migrationspfad möglicherweise besonders attraktiv. Wenn Sie glauben, dass die Funktionen der Azure-Dateisynchronisierung sich gut für Ihre Organisation eignen, erfahren Sie in diesem Artikel, wie Sie diese Lösungen aus StorSimple verschieben.

## <a name="migration-considerations"></a>Überlegungen zur Migration

Dieser Prozess eignet sich für Kunden, die eine Windows-Dateifreigabe mithilfe eines StorSimple-Volumes zur Speicherung konfiguriert haben. Das Migrieren von Daten aus StorSimple 5000/7000 zur Azure-Dateisynchronisierung beinhaltet das Konvertieren dieses Dateifreigabe-Speicherorts in einen [Serverendpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) und die anschließende Verwendung lokal angefügter Laufwerke als weiteren Endpunkt, der dann zu Ihrem neuen Speicherort wird. 

Bei der Verschiebung zur AFS sollten die folgenden Aspekte beachtet werden:

1. Für Azure Files gilt derzeit eine Einschränkung von 5 TB pro Freigabe. Um diese Einschränkung zu überwinden, kann die Azure-Dateisynchronisierung mit Datenverteilung über mehrere Azure Files-Freigaben verwendet werden. Weitere Informationen finden Sie unter [Planung für eine Azure Files-Bereitstellung](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Diese Migration lädt das gesamte primäre Dataset auf ein lokales Gerät, wenn das Kopieren der Daten von einem lokalen Gerät erfolgt. Stellen Sie sicher, dass Sie über genügend Bandbreite für diese Übertragung verfügen.
3. Dabei werden die bereits erstellten Momentaufnahmen nicht beibehalten. Es werden nur die primären Daten migriert. Der Prozess behält auch nicht die zugeordneten Bandbreitenvorlagen oder Sicherungsrichtlinien bei. [Verwenden Sie Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files), um nach dem Migrieren der Daten zur Azure-Dateifreigabe die Sicherungsrichtlinien einzurichten.
4. StorSimple stellt Erstanbieterhardware bereit. Mit Azure Files/Azure-Dateisynchronisierung verwenden Sie jedoch Ihre eigene lokale Windows Server-Hardware als lokalen Cache. Sie müssen sicherstellen, dass ausreichend Speicherkapazität zur Verfügung steht, um das Dataset Ihrer Wahl lokal zu speichern. Weitere Informationen zu Tiering und dem Einrichten eines Ziels mit erforderlichem freiem Speicherplatz finden Sie unter [Bereitstellen von Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Informieren Sie sich über die [Preise für Azure Files](https://azure.microsoft.com/pricing/details/storage/files/), da sie von StorSimple abweichen. Im Gegensatz zu StorSimple werden Deduplizierung und Komprimierung bei AFS nicht eingesetzt.

## <a name="migration-prerequisites"></a>Voraussetzungen für die Migration

Hier werden die Voraussetzungen für die Migration Ihres Legacygeräts der Serie 5000 oder 7000 zur Azure-Dateisynchronisierung beschrieben. Bevor Sie beginnen, stellen Sie sicher, dass folgende Voraussetzungen erfüllt sind:

- Zugriff auf ein Gerät der StorSimple-Serie 5000/7000, auf dem Version v2.1.1.518 oder höher der Software ausgeführt wird. Frühere Versionen werden nicht unterstützt. In der oberen rechten Ecke der Webbenutzeroberfläche des StorSimple-Geräts sollte die ausgeführte Version der Software angezeigt werden.  
    Wenn Ihr Gerät nicht v2.1.1.518 ausführt, aktualisieren Sie Ihr System auf die erforderliche Mindestversion. Eine ausführliche Anleitung finden Sie unter [Software Patch Upgrade Guide v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518) (Anleitung für Softwarepatchupgrade auf v2.1.1.518).
- Suchen Sie nach aktiven Sicherungsaufträgen, die auf dem Quellgerät ausgeführt werden. Dies umfasst auch die Aufträge auf dem Host mit der Konsole für den StorSimple-Datenschutz. Warten Sie, bis die aktuellen Aufträge abgeschlossen sind. 
- Zugriff auf einen Windows Server-Host, der mit Ihrem StorSimple-Gerät der Serie 5000/7000 verbunden ist. Der Host muss – wie in [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) beschrieben – eine unterstützte Version von Windows Server ausführen.
- StorSimple-Volumes werden auf dem Host eingebunden und enthalten Dateifreigaben.
- Der Host verfügt über ausreichend lokalen Speicher, um Ihre lokal zwischengespeicherten Daten zu speichern.
- Zugriff auf Besitzerebene auf das Azure-Abonnement, das Sie zum Bereitstellen der Azure-Dateisynchronisierung verwenden. Beim Erstellen eines Cloudendpunkts für Ihre Synchronisierungsgruppe können Probleme auftreten, wenn Sie nicht über Berechtigungen auf Besitzer- oder Administratorebene verfügen.
- Zugriff auf ein [Speicherkonto vom Typ „Allgemein v2“](https://docs.microsoft.com/azure/storage/common/storage-account-overview) mit einer Azure-Dateifreigabe, mit der eine Synchronisierung durchgeführt werden soll. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - [Erstellen einer Dateifreigabe über das Azure-Portal](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migrationsprozess

Das Migrieren von Daten aus StorSimple 5000/7000 zu AFS ist ein zweistufiger Prozess:
1.  Replizieren der Daten vom lokalen Dateiserver, wo die StorSimple-Volumes in eine Azure Files-Freigabe eingebunden sind.  Die Replikation erfolgt über einen AFS-Agent, den Sie installieren.
2.  Trennen Sie das StorSimple-Geräte. Die lokalen Datenträger fungieren dann als lokaler Cache.

### <a name="migration-steps"></a>Schritte bei der Migration

Führen Sie die folgenden Schritte aus, um die auf StorSimple-Volumes konfigurierte Windows-Dateifreigabe zu einer Freigabe der Azure-Dateisynchronisierung zu migrieren. 
1.  Führen Sie diese Schritte auf dem gleichen Windows Server-Host aus, auf dem die StorSimple-Volumes eingebunden sind, oder verwenden Sie ein anderes System. 
    - [Vorbereiten von Windows Server für die Verwendung mit der Azure-Dateisynchronisierung](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installieren des Azure-Dateisynchronisierungs-Agents](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Bereitstellen des Speichersynchronisierungsdiensts](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrieren eines Windows-Servers beim Speichersynchronisierungsdienst](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Erstellen einer Synchronisierungsgruppe und eines Cloudendpunkts](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint) Synchronisierungsgruppen müssen für jede Windows-Dateifreigabe erstellt werden, die vom Host migriert werden muss.
    - [Erstellen eines Serverendpunkts](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Geben Sie den Pfad als den Pfad des StorSimple-Volumes an, das Ihre Dateifreigabedaten enthält. Wenn das StorSimple-Volume z.B. Laufwerk `J` ist, und Ihre Daten befinden sich in `J:/<myafsshare>`, dann fügen Sie diesen Pfad als Serverendpunkt hinzu. Behalten Sie **Tiering** als **Deaktiviert** bei.
2.  Warten Sie, bis die Dateiserversynchronisierung abgeschlossen ist. Stellen Sie bei jedem Server in einer bestimmten Synchronisierungsgruppe Folgendes sicher:
    - Die Zeitstempel für „Letzter Synchronisierungsversuch“ für Uploads und Downloads sind aktuell.
    - Der Status ist sowohl für Uploads als auch für Downloads grün.
    - **Synchronisierungsaktivität** zeigt nur sehr wenige oder keine Dateien an, die synchronisiert werden müssen.
    - **Dateien ohne Synchronisierung** ist für Uploads und Downloads „0“.
    Weitere Informationen dazu, wann die Serversynchronisierung abgeschlossen ist, finden Sie unter [Wie erkenne ich, ob meine Server miteinander synchronisiert sind?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Die Synchronisierung kann je nach Datenmenge und Bandbreite mehrere Stunden bis Tage dauern. Nachdem die Synchronisierung abgeschlossen ist, sind all Ihre Daten sicher in der Azure-Dateifreigabe gespeichert. 
3.  Wechseln Sie zu den Freigaben auf den StorSimple-Volumes. Wählen Sie eine Freigabe aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Eigenschaften** aus. Beachten Sie die Freigabeberechtigungen unter **Sicherheit**. Diese Berechtigungen müssen in einem späteren Schritt manuell auf die neue Freigabe angewendet werden.
4.  Die nächsten Schritte unterscheiden sich je nachdem, ob Sie den gleichen Windows Server-Host oder einen anderen verwenden.

    Überspringen Sie diesen Schritt, und fahren Sie mit dem nächsten Schritt fort, wenn Sie einen anderen Windows Server-Host verwenden. Wenn Sie den gleichen Windows-Dateiserver für AFS verwenden, treten jetzt einige Minuten Ausfallzeit auf. 
    - **Ausfallzeit beginnt**: Löschen Sie den Serverendpunkt, den Sie in *Schritt 1F* erstellt haben. 
    - Erstellen Sie einen neuen Serverendpunkt mit dem Pfad, in den die zu speichernden Daten geleitet werden sollen.
    - Sobald der Serverendpunkt als „Fehlerfrei“ angezeigt wird (dies kann einige Minuten dauern), sehen Sie die Daten an diesem neuen Speicherort. Sie können jetzt Ihren Windows Server-Host dazu konfigurieren, von diesem neuen Standort aus Dateien bereitzustellen. - **Ausfallzeit endet**.
5.  Wenn Sie einen anderen Windows-Dateiserver zur Azure-Dateisynchronisierung verwenden, treten keine Ausfallzeiten auf. 
    - Fügen Sie einen anderen Serverendpunkt mit dem Pfad des lokalen Speichers hinzu, den Sie für die Verwendung als Cache statt des StorSimple-Geräts vorbereitet haben. 
    - In einigen Minuten werden die Dateien auf dem neuen Server angezeigt. Sie können den Wechsel von Ihrem StorSimple-Gerät zu diesem neuen Speicherort auf dem Host zu einem beliebigen Zeitpunkt durchführen.

    > [!TIP] 
    > Erwägen Sie, diese neue Dateifreigabe mit dem gleichen Namen und Pfad zu konfigurieren wie diejenige, die durch sie ersetzt wird, um Unterbrechungen zu minimieren. Wenn Sie DFS-N verwenden, müssen Sie möglicherweise Änderungen in der Konfiguration vornehmen.
6.  Konfigurieren Sie die Freigabeberechtigungen wie in *Schritt 3* angemerkt neu.

Wenn während der Datenmigration Probleme auftreten, [wenden Sie sich an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Nächste Schritte

Wenn AFS nicht die richtige Lösung für Sie ist, informieren Sie sich über das [Migrieren von Daten von einem Gerät der Serie StorSimple 5000/7000 zu einem Gerät der Serie 8000](storsimple-8000-migrate-from-5000-7000.md).

