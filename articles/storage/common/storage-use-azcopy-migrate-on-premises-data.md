---
title: 'Tutorial: Migrieren von lokalen Daten nach Azure Storage mithilfe von AzCopy | Microsoft-Dokumentation'
description: In diesem Tutorial verwenden Sie AzCopy, um Daten zu bzw. aus Blob-, Tabellen- und Dateiinhalten zu migrieren oder in diese Inhalte bzw. aus diesen Inhalten zu kopieren. Migrieren Sie mühelos Daten aus Ihrem lokalen Speicher nach Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 5c10edc4f11aad23801045011b67592b6cc537e4
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912137"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Tutorial: Migrieren von lokalen Daten zum Cloudspeicher mithilfe von AzCopy

AzCopy ist ein Befehlszeilentool zum Kopieren von Daten in oder aus Azure Blob Storage, Azure Files und Azure Table Storage mittels einfacher Befehle. Die Befehle sind für optimale Leistung konzipiert. Mit AzCopy können Sie Daten entweder zwischen einem Dateisystem und einem Speicherkonto oder zwischen Speicherkonten kopieren. AzCopy kann zum Kopieren von Daten aus lokalen Daten in ein Speicherkonto verwendet werden.

Laden Sie die für Ihr Betriebssystem geeignete Version von AzCopy herunter:

* [AzCopy unter Linux](storage-use-azcopy-linux.md) wurde mit dem .NET Core Framework erstellt. Als Ziel sind Linux-Plattformen festgelegt, die Befehlszeilenoptionen im POSIX-Format bieten. 
* [AzCopy unter Windows](storage-use-azcopy.md) wurde mit dem .NET Framework erstellt. Dieses bietet Befehlszeilenoptionen im Windows-Format. 
 
In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein Speicherkonto. 
> * Verwenden Sie AzCopy, um all Ihre Daten hochzuladen.
> * Ändern Sie die Daten für Testzwecke.
> * Erstellen Sie einen geplanten Task oder Cron-Auftrag, um neue Dateien für den Upload zu identifizieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial zu absolvieren, laden Sie die neueste Version von AzCopy unter [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) oder [Windows](https://aka.ms/downloadazcopy) herunter.

Unter Windows ist [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) erforderlich, da das Tool in diesem Tutorial zum Planen einer Aufgabe verwendet wird. Linux-Benutzer verwenden stattdessen den Befehl „crontab“.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Erstellen eines Containers

Im ersten Schritt wird ein Container erstellt, da Blobs immer in einen Container hochgeladen werden müssen. Container dienen zum Organisieren von Blobgruppen und sind somit vergleichbar mit Ordnern für Dateien auf Ihrem Computer. 

Führen Sie folgende Schritte durch, um einen Container zu erstellen:

1. Wählen Sie auf der Hauptseite die Schaltfläche **Speicherkonten** und dann das von Ihnen erstellte Speicherkonto aus.
2. Klicken Sie unter **Dienste** auf **Blobs** und dann auf **Container**. 

   ![Erstellen eines Containers](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamen müssen mit einem Buchstaben oder einer Zahl beginnen. Sie dürfen nur Buchstaben, Zahlen und den Bindestrich (-) enthalten. Weitere Benennungsregeln für Blobs und Container finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Hochladen des Inhalts eines Ordners in Blobspeicher

Mit AzCopy können Sie unter [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) oder [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) alle Dateien in einem Ordner in Blob Storage hochladen. Um alle Blobs in einem Ordner hochzuladen, geben Sie den folgenden AzCopy-Befehl aus:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Ersetzen Sie `<key>` und `key` durch Ihren Kontoschlüssel. Im Azure-Portal können Sie den Kontoschlüssel unter **Einstellungen** im Abschnitt **Zugriffsschlüssel** abrufen. Wählen Sie einen Schlüssel aus, und fügen Sie ihn in den AzCopy-Befehl ein. Wenn der angegebene Zielcontainer nicht vorhanden ist, wird er von AzCopy erstellt und die Datei anschließend in den Container hochgeladen. Aktualisieren Sie den Quellpfad zu Ihrem Datenverzeichnis, und ersetzen Sie **myaccount** in der Ziel-URL durch den Namen Ihres Speicherkontos.

Um die Inhalte des angegebenen Verzeichnisses rekursiv in Blob Storage hochzuladen, legen Sie die Option `--recursive` (Linux) oder `/S` (Windows) fest. Beim Ausführen von AzCopy mit einer dieser Optionen werden auch alle Unterordner und die darin enthaltenen Dateien hochgeladen.

## <a name="upload-modified-files-to-blob-storage"></a>Hochladen geänderter Dateien in Blob Storage

Mit AzCopy können Sie [Dateien basierend auf dem Zeitpunkt der letzten Änderung hochladen](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features). Um dies zu testen, ändern Sie Dateien im Quellverzeichnis oder erstellen neue Dateien für Testzwecke. Um ausschließlich aktualisierte oder neue Dateien hochzuladen, fügen Sie den Parameter `--exclude-older` (Linux) oder `/XO` (Windows) zum AzCopy-Befehl hinzu.

Falls Sie nur Quellressourcen kopieren möchten, die im Ziel nicht vorhanden sind, können Sie beide Parameter – `--exclude-older` und `--exclude-newer` (Linux) oder `/XO` und `/XN` (Windows) – im AzCopy-Befehl angeben. AzCopy lädt nur die aktualisierten Daten basierend auf dem Zeitstempel hoch.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task"></a>Erstellen einer geplanten Aufgabe

Sie können einen geplanten Task oder Cron-Auftrag, der ein AzCopy-Befehlsskript ausführt, erstellen. Das Skript identifiziert neue lokale Daten und lädt diese in einem bestimmten Zeitintervall in den Cloudspeicher hoch.

Kopieren Sie den AzCopy-Befehl in einen Text-Editor. Aktualisieren Sie die Parameterwerte des AzCopy-Befehls mit den entsprechenden Werten. Speichern Sie die Datei als `script.sh` (Linux) oder `script.bat` (Windows) für AzCopy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy wird mit der Verboseoption `--verbose` (Linux) oder `/V` (Windows) ausgeführt. Die Ausgabe wird in eine Protokolldatei umgeleitet.

In diesem Tutorial wird [SCHTASKS](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) verwendet, um einen geplanten Task unter Windows zu erstellen. Der [Crontab](http://crontab.org/)-Befehl wird verwendet, um einen Cron-Auftrag unter Linux zu erstellen.
 **SCHTASKS** ermöglicht Administratoren das Erstellen, Löschen, Abfragen, Ändern, Ausführen und Beenden von geplanten Tasks auf einem lokalen oder Remotecomputer. Mithilfe von **Cron** können Linux- und UNIX-Benutzer Befehle oder Skripts zu einem bestimmten Datum und einer bestimmten Uhrzeit mithilfe von [Cron-Ausdrücken](https://en.wikipedia.org/wiki/Cron#CRON_expression) ausführen.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Um einem Cron-Auftrag unter Linux zu erstellen, geben Sie den folgenden Befehl auf einem Terminalserver ein:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Durch Angabe des Cron-Ausdrucks `*/5 * * * *` im Befehl wird angegeben, dass das Shell-Skript `script.sh` alle fünf Minuten ausgeführt werden soll. Sie können das Skript für die Ausführung zu einem bestimmten Zeitpunkt planen, d.h. täglich, monatlich oder jährlich. Weitere Informationen zum Festlegen von Datum und Uhrzeit für die Ausführung eines Auftrags finden Sie unter [Cron-Ausdrücke](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Um einen geplanten Task unter Windows zu erstellen, geben Sie den folgenden Befehl an einer Eingabeaufforderung oder in PowerShell ein:

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Im Befehl wird Folgendes verwendet:
- Der Parameter `/SC` zum Festlegen eines Minutenplans
- Der Parameter `/MO` zum Angeben eines Intervalls von fünf Minuten
- Der Parameter `/TN` zum Festlegen des Tasknamens
- Der Parameter `/TR` zum Festlegen des Pfads zur Datei `script.bat`

Weitere Informationen zum Erstellen eines geplanten Tasks unter Windows finden Sie unter [SCHTASKS](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Um zu überprüfen, ob der geplante Task bzw. Cron-Auftrag ordnungsgemäß ausgeführt wird, erstellen Sie neue Dateien in Ihrem Verzeichnis `myfolder`. Warten Sie fünf Minuten, um sicherzustellen, dass die neuen Dateien in Ihr Speicherkonto hochgeladen wurden. Navigieren Sie zu Ihrem Protokollverzeichnis, um Ausgabeprotokolle des geplanten Tasks oder Cron-Auftrags anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Möglichkeiten zum Verschieben von lokalen Daten in Azure Storage und umgekehrt finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Verschieben von Daten in und aus Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)  

Weitere Informationen speziell zu AzCopy finden Sie unter dem jeweiligen Link für Ihr Betriebssystem:

> [!div class="nextstepaction"]
> [Übertragen von Daten mit AzCopy unter Windows](storage-use-azcopy.md)
> [Übertragen von Daten mit AzCopy unter Linux](storage-use-azcopy-linux.md)
