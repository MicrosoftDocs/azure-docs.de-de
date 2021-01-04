---
title: Tutorial zum Kopieren von Daten über SMB aus Azure Data Box | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Daten über SMB in Ihre Azure Data Box kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ecc6e1e1a543f3190e9f73512ca0b9ae45cc3fe9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335212"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Tutorial: Kopieren von Daten aus Azure Data Box über SMB (Vorschau)

In diesem Tutorial wird beschrieben, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrer Data Box herstellen und Daten von dieser auf einen lokalen Server kopieren. Das Data Box-Gerät enthält die aus Ihrem Azure Storage-Konto exportierten Daten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten aus Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Bestellung für Azure Data Box aufgegeben.
    - Weitere Informationen zu Importbestellungen finden Sie unter [Tutorial: Bestellen von Azure Data Box](data-box-deploy-ordered.md).
    - Weitere Informationen zu Exportbestellungen finden Sie unter [Tutorial: Bestellen von Azure Data Box](data-box-deploy-export-ordered.md).
2. Sie haben Ihre Data Box erhalten, und die Bestellung wird im Portal mit dem Status **Übermittelt** angezeigt.
3. Sie verfügen über einen Hostcomputer, auf den Sie die Daten von Ihrer Data Box kopieren möchten. Für Ihren Hostcomputer müssen die folgenden Bedingungen erfüllt sein:
   * Es muss ein [unterstütztes Betriebssystem](data-box-system-requirements.md) ausgeführt werden.
   * Er muss mit einem Hochgeschwindigkeitsnetzwerk verbunden sein. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, verwenden Sie eine 1-GbE-Datenverbindung, die Geschwindigkeit der Kopiervorgänge wird dadurch jedoch beeinträchtigt.

## <a name="connect-to-data-box"></a>Herstellen einer Verbindung mit der Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Wenn Sie einen Windows Server-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um eine Verbindung mit der Data Box herzustellen.

1. Zunächst müssen Sie sich authentifizieren und eine Sitzung starten. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**. Wählen Sie **Get credentials** (Anmeldeinformationen abrufen) aus, um die Anmeldeinformationen für den Zugriff auf die mit Ihrem Speicherkonto verknüpften Freigaben abzurufen. 

    ![Abrufen der Anmeldeinformationen für Freigaben](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. Kopieren Sie im Dialogfeld „Auf Freigabe zugreifen und Daten kopieren“ den **Benutzernamen** und das **Kennwort** für die Freigabe. Klicken Sie auf **OK**.
    
    ![Abrufen der Anmeldeinformationen für Freigaben, Option „Auf Freigabe zugreifen und Daten kopieren“](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Öffnen Sie ein Befehlsfenster, um über Ihren Hostcomputer auf die Freigaben zuzugreifen, die mit Ihrem Speicherkonto (*exportbvtdataset2* im folgenden Beispiel) verknüpft sind. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Abhängig von Ihrem Datenformat lauten die Freigabepfade wie folgt:
    - Azure-Blockblob: `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure-Seitenblob: `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure-Dateien: `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Geben Sie das Kennwort für die Freigabe ein, wenn Sie dazu aufgefordert werden. Das folgende Beispiel zeigt das Herstellen einer Verbindung mit einer Freigabe über den obigen Befehl.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Drücken Sie WINDOWS-TASTE+R. Geben Sie im Fenster **Ausführen** die `\\<device IP address>` an. Wählen Sie **OK** aus, um den Datei-Explorer zu öffnen.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer, Eingeben der Geräte-ID](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Die Freigaben sollten jetzt als Ordner angezeigt werden.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer, Anzeigen von Freigaben](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Wenn Sie einen Linux-Client verwenden, stellen Sie mit folgendem Code die SMB-Freigabe bereit. Der unten angegebene Parameter „vers“ ist die Version des SMB, den Ihr Linux-Host unterstützt. Geben Sie die entsprechende Version in den folgenden Befehl ein. Von Data Box unterstützte SMB-Versionen finden Sie unter [Unterstützte Dateisysteme für Linux-Clients](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients). 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Kopieren von Daten aus Data Box

Nachdem Sie eine Verbindung mit den Data Box-Freigaben hergestellt haben, kopieren Sie im nächsten Schritt Ihre Daten.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Nachdem Sie eine Verbindung mit der SMB-Freigabe hergestellt haben, beginnen Sie mit dem Kopieren der Daten. Sie können jedes SMB-kompatible Dateikopiertool (beispielsweise Robocopy) verwenden, um Ihre Daten zu kopieren. Mit Robocopy können mehrere Kopieraufträge initiiert werden. 


Weitere Informationen zum Robocopy-Befehl finden Sie unter [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy und einige Beispiele).

Navigieren Sie nach Abschluss des Kopiervorgangs zum **Dashboard**, und überprüfen Sie den belegten Speicherplatz und den freien Speicherplatz auf Ihrem Gerät.

Sie können nun mit dem Versenden Ihrer Data Box an Microsoft fortfahren.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten aus Data Box

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box zurück an Microsoft senden.

> [!div class="nextstepaction"]
> [Zurücksenden der Azure Data Box an Microsoft](./data-box-deploy-export-picked-up.md)