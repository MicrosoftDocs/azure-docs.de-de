---
title: Einbinden einer Azure-Dateifreigabe über SMB mit macOS | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit macOS eine Azure-Dateifreigabe über SMB einbinden.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f2abbb355513c175329d01b9d5ed8884f2c27ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715954"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Einbinden einer Azure-Dateifreigabe über SMB mit macOS
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können von macOS El Capitan 10.11 oder höher mit dem SMB 3-Protokoll nach Branchenstandard eingebunden werden. In diesem Artikel werden zwei Methoden zum Einbinden einer Azure-Dateifreigabe unter macOS behandelt: über die Finder-Benutzeroberfläche und mithilfe des Terminals.

> [!Note]  
> Vor dem Einbinden einer Azure-Dateifreigabe über SMB sollten Sie die SMB-Paketsignatur deaktivieren. Andernfalls leidet unter Umständen die Leistung beim Zugriff auf die Azure-Dateifreigabe über macOS. Da die SMB-Verbindung verschlüsselt wird, hat dies keine Auswirkungen auf die Verbindungssicherheit. Über das Terminal können Sie die SMB-Paketsignatur mithilfe folgender Befehle deaktivieren, wie unter [Paketsignaturen für SMB 2- und SMB 3-Verbindungen deaktivieren](https://support.apple.com/HT205926) beschrieben:  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Voraussetzungen für das Einbinden von Azure-Dateifreigaben unter macOS
* **Speicherkontoname**: Zum Einbinden einer Azure-Dateifreigabe benötigen Sie den Namen des Speicherkontos.

* **Speicherkontoschlüssel**: Zum Einbinden einer Azure-Dateifreigabe benötigen Sie den primären (oder sekundären) Speicherschlüssel. SAS-Schlüssel können derzeit nicht zum Einbinden verwendet werden.

* **Stellen Sie sicher, Port 445 geöffnet ist**: SMB kommuniziert über den TCP-Port 445. Vergewissern Sie sich, dass die Firewall Ihres Clientcomputers (Mac) den TCP-Port 445 nicht blockiert.

## <a name="mount-an-azure-file-share-via-finder"></a>Einbinden einer Azure-Dateifreigabe über den Finder
1. **Öffnen Sie den Finder:** Der Finder ist unter macOS standardmäßig geöffnet. Wenn Sie den Finder als aktuelle Anwendung auswählen möchten, klicken Sie im Dock auf das macOS-Gesichtssymbol:  
    ![Das macOS-Gesichtssymbol](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Wählen Sie im Menü „Gehe zu“ die Option „Mit Server verbinden“ aus:** Wandeln Sie die beiden umgekehrten Schrägstriche am Anfang (`\\`) unter Verwendung des UNC-Pfads aus den Voraussetzungen in `smb://` und alle anderen umgekehrten Schrägstriche (`\`) in normale Schrägstriche (`/`) um. Ihr Link sollte folgendermaßen aussehen: ![Dialogfeld „Mit Server verbinden“](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Geben Sie als Benutzername und Kennwort den Speicherkontonamen und den Speicherkontoschlüssel ein:** Wenn Sie im Dialogfeld „Mit Server verbinden“ auf „Verbinden“ klicken, werden Sie zur Eingabe eines Benutzernamens und eines Kennworts aufgefordert. (Hier wird automatisch Ihr macOS-Benutzername eingefügt.) Sie können den Namen des Speicherkontos und den Speicherkontoschlüssel optional in Ihrem macOS-Schlüsselbund platzieren.

4. **Verwenden Sie die Azure-Dateifreigabe nach Bedarf:** Nach Angabe des Freigabenamens und des Speicherkontoschlüssels als Benutzername und Kennwort wird die Freigabe eingebunden. Die Freigabe kann wie ein normaler lokaler Ordner oder eine normale Dateifreigabe verwendet werden. So können Sie beispielsweise Dateien per Drag&Drop in der Dateifreigabe platzieren:

    ![Momentaufnahme einer eingebundenen Azure-Dateifreigabe](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Einbinden einer Azure-Dateifreigabe über das Terminal
1. Ersetzen Sie `<storage-account-name>` durch den Namen Ihres Speicherkontos. Geben Sie den Speicherkontoschlüssel als Kennwort an, wenn Sie zur Eingabe eines Kennworts aufgefordert werden. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Verwenden Sie die Azure-Dateifreigabe nach Bedarf:** Die Azure-Dateifreigabe wird an dem Bereitstellungspunkt eingebunden, der durch den vorherigen Befehl angegeben wurde.  

    ![Momentaufnahme der eingebundenen Azure-Dateifreigabe](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files erhalten Sie über diese Links.

* [Auf dem Mac eine Verbindung über die Dateifreigabe herstellen](https://support.apple.com/HT204445)
* [Häufig gestellte Fragen](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Windows)      
* [Troubleshoot Azure File storage problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Linux)    
