---
title: Tutorial zum Kopieren von Daten auf die Azure Data Box über NFS | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe des NFS über die lokale Webbenutzeroberfläche eine Verbindung herstellen und Daten von Ihrem Hostcomputer auf Ihr Azure Data Box-Gerät kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: bbebe3b3f63e6ccbb5f351abfc9ba3b846ca6fbe
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337660"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Tutorial: Kopieren von Daten in eine Azure Data Box über NFS

In diesem Tutorial wird beschrieben, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrem Hostcomputer herstellen und Daten kopieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Einrichten der Azure Data Box](data-box-deploy-set-up.md) ausgeführt.
2. Sie haben Ihre Data Box erhalten, und die Bestellung wird im Portal mit dem Status **Übermittelt** angezeigt.
3. Sie haben einen Hostcomputer mit den Daten, die Sie auf die Data Box kopieren möchten. Für Ihren Hostcomputer müssen die folgenden Bedingungen erfüllt sein:
    - Es muss ein [unterstütztes Betriebssystem](data-box-system-requirements.md) ausgeführt werden.
    - Er muss mit einem Hochgeschwindigkeitsnetzwerk verbunden sein. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, kann eine 1-GbE-Datenverbindung verwendet werden, die Geschwindigkeit der Kopiervorgänge wird dadurch jedoch beeinträchtigt. 

## <a name="connect-to-data-box"></a>Herstellen einer Verbindung mit der Data Box

Je nach ausgewähltem Speicherkonto erstellt Data Box bis zu:
- Drei Freigaben für jedes verknüpfte Speicherkonto für GPv1 und GPv2
- Eine Freigabe für Storage Premium 
- Eine Freigabe für das Blob Storage-Konto 

Unter Blockblob- und Seitenblobfreigaben sind Entitäten der ersten Ebene Container, und Entitäten der zweiten Ebene sind Blobs. Unter Freigaben für Azure Files sind Entitäten erster Ebene Freigaben. Entitäten zweiter Ebene sind Dateien.

In der folgenden Tabelle sind der UNC-Pfad zu den Freigaben auf Ihrer Data Box und die Azure Storage-Pfad-URL aufgeführt, wohin die Daten hochgeladen werden. Die endgültige URL des Azure Storage-Pfads kann aus dem UNC-Freigabepfad abgeleitet werden.
 
| Azure Storage-Typ| Data Box-Freigaben                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-Blockblobs | <li>UNC-Pfad zu den Freigaben: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-Seitenblobs  | <li>UNC-Pfad zu den Freigaben: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-Pfad zu den Freigaben: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Wenn Sie einen Linux-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um Data Box zum Zulassen des Zugriffs auf NFS-Clients zu konfigurieren.

1. Geben Sie die IP-Adressen der zulässigen Clients an, die auf die Freigabe zugreifen können. Wechseln Sie in der lokalen Webbenutzeroberfläche zur Seite **Verbindung herstellen und Daten kopieren**. Klicken Sie unter **NFS-Einstellungen** auf **NFS-Clientzugriff**. 

    ![Konfigurieren des NFS-Clientzugriffs](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Geben Sie die IP-Adresse des NFS-Clients an, und klicken Sie auf **Hinzufügen**. Sie können den Zugriff für mehrere NFS-Clients konfigurieren, indem Sie diesen Schritt wiederholen. Klicken Sie auf **OK**.

    ![Konfigurieren der IP-Adresse eines NFS-Clients](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Stellen Sie sicher, dass auf dem Linux-Hostcomputer eine [unterstützte Version](data-box-system-requirements.md) des NFS-Clients installiert ist. Verwenden Sie die jeweilige Version für Ihre Linux-Distribution. 

3. Führen Sie nach der Installation des NFS-Clients den folgenden Befehl aus, um die NFS-Freigabe auf Ihrem Data Box-Gerät einzubinden:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Das folgende Beispiel zeigt, wie Sie über NFS eine Verbindung mit einer Data Box-Freigabe herstellen. Die IP-Adresse des Data Box-Geräts lautet `10.161.23.130`, die Freigabe `Mystoracct_Blob` wird auf der Ubuntu-VM eingebunden, und der Bereitstellungspunkt ist `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Für Mac-Clients müssen Sie eine zusätzliche Option wie folgt hinzufügen: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner**. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.

## <a name="copy-data-to-data-box"></a>Kopieren von Daten auf die Data Box

Nachdem Sie eine Verbindung mit den Data Box-Freigaben hergestellt haben, kopieren Sie im nächsten Schritt Ihre Daten. Bevor Sie mit dem Kopieren der Daten beginnen, sollten Sie folgende Aspekte beachten:

* Stellen Sie sicher, dass Sie die Daten in Freigaben kopieren, die das richtige Datenformat aufweisen. Kopieren Sie beispielsweise die Blockblobdaten in die Freigabe für Blockblobs. Kopieren Sie VHDs in Seitenblobs. Wenn das Datenformat nicht mit dem entsprechenden Freigabetyp übereinstimmt, tritt später beim Hochladen der Daten in Azure ein Fehler auf.
*  Stellen Sie beim Kopieren von Daten sicher, dass bei der Datengröße die Grenzwerte eingehalten werden, die im Artikel [Größenbeschränkungen für das Azure-Speicherkonto](data-box-limits.md#azure-storage-account-size-limits) beschrieben sind.
* Falls von Data Box hochgeladene Daten gleichzeitig von anderen Anwendungen außerhalb von Data Box hochgeladen werden, kann dies zu Fehlern bei Uploadaufträgen und zu Datenbeschädigungen führen.
* Es wird empfohlen, SMB und NFS nicht gleichzeitig zu verwenden und Daten nicht an dasselbe Endziel in Azure zu kopieren. In solchen Fällen kann das endgültige Ergebnis nicht bestimmt werden.
* **Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner**. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.
* Beim Einlesen von groß- und kleinschreibungsabhängigen Verzeichnis- und Dateinamen aus einer NFS-Freigabe für NFS auf Data Box:
  * Die Groß-/Kleinschreibung im Namen wird beibehalten.
  * Bei den Dateien wird die Groß-/Kleinschreibung nicht berücksichtigt.

    Wenn Sie beispielsweise `SampleFile.txt` und `Samplefile.Txt` kopieren, bleiben die Groß-/Kleinbuchstaben im Namen erhalten, wenn Sie ihn nach Data Box kopieren, aber die zweite Datei überschreibt die erste, da diese als die gleiche Datei betrachtet wird.

> [!IMPORTANT]
> Bevor Sie bestätigen können, dass Data Box Ihre Daten nach Azure Storage übertragen hat, müssen Sie sicherstellen, dass Sie über eine Kopie der Quelldaten verfügen.

Wenn Sie einen Linux-Hostcomputer verwenden, verwenden Sie ein Kopierhilfsprogramm wie Robocopy. Einige der verfügbaren Alternativen in Linux sind [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) oder [Ultracopier](https://ultracopier.first-world.info/).  

Der `cp`-Befehl ist eine der besten Optionen zum Kopieren eines Verzeichnisses. Weitere Informationen zur Verwendung finden Sie auf den [Handbuchseiten zum cp-Befehl](http://man7.org/linux/man-pages/man1/cp.1.html).

Befolgen Sie die nachstehenden Richtlinien, wenn Sie die rsync-Option für einen Multithread-Kopiervorgang verwenden:

* Installieren Sie je nach Dateisystem, das Ihr Linux-Client verwendet, das **CIFS Utils**- oder **NFS Utils**-Paket.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installieren Sie **Rsync** und **Parallel** (variiert abhängig von der verteilten Linux-Version).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Erstellen Sie einen Bereitstellungspunkt.

    `sudo mkdir /mnt/databox`

* Binden Sie das Volume ein.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Spiegeln Sie die Verzeichnisstruktur des Ordners.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Kopieren Sie Dateien.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     Hierbei gibt „j“ die Anzahl von Parallelisierungen und „X“ die Anzahl paralleler Kopien an.

     Wir empfehlen, mit 16 parallelen Kopien zu beginnen und die Anzahl von Threads je nach verfügbaren Ressourcen zu erhöhen.

> [!IMPORTANT]
> Folgende Linux-Dateitypen werden nicht unterstützt: symbolische Verknüpfungen, Zeichendateien, Blockdateien, Sockets und Pipes. Diese Dateitypen führen zu Fehlern bei der **Versandvorbereitung**.

Falls während des Kopiervorgangs Fehler auftreten, wird eine Benachrichtigung angezeigt.

![Herunterladen und Anzeigen von Fehlern beim Verbinden und Kopieren](media/data-box-deploy-copy-data/view-errors-1.png)

Wählen Sie **Problemliste herunterladen** aus.

![Herunterladen der Problemliste bei einem Kopierfehler](media/data-box-deploy-copy-data/view-errors-2.png)

Öffnen Sie die Liste, um die Details des Fehlers anzuzeigen, und wählen Sie die Lösungs-URL aus, um die empfohlene Lösung anzuzeigen.

![Probleme in einer Problemliste für Kopierfehler](media/data-box-deploy-copy-data/view-errors-3.png)

Weitere Informationen finden Sie unter [Anzeigen von Fehlerprotokollen beim Kopieren von Daten auf die Data Box](data-box-logs.md#view-error-log-during-data-copy). Eine detaillierte Liste von Fehlern beim Datenkopiervorgang finden Sie unter [Behandeln von Problemen bei der Data Box](data-box-troubleshoot.md).

Um die Datenintegrität zu gewährleisten, wird inline eine Prüfsumme berechnet, während die Daten kopiert werden. Überprüfen Sie nach Abschluss des Kopiervorgangs den belegten Speicherplatz und den freien Speicherplatz auf Ihrem Gerät.

   ![Überprüfen des freien und belegten Speicherplatzes im Dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box zurück an Microsoft senden.

> [!div class="nextstepaction"]
> [Zurücksenden der Azure Data Box an Microsoft](./data-box-deploy-picked-up.md)
