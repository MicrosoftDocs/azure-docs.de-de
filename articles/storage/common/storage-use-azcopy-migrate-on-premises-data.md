---
title: 'Tutorial: Migrieren von lokalen Daten nach Azure Storage mit AzCopy | Microsoft-Dokumentation'
description: In diesem Tutorial verwenden Sie AzCopy, um Daten zu bzw. aus Blob-, Tabellen- und Dateiinhalten zu migrieren oder in diese Inhalte bzw. aus diesen Inhalten zu kopieren. Migrieren Sie mühelos Daten aus Ihrem lokalen Speicher nach Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: eddee58d70cf621bd6c82d54fe75434f4e596d9e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498165"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrieren von lokalen Daten zu Cloudspeicher mit AzCopy

AzCopy ist ein Befehlszeilentool zum Kopieren von Daten in oder aus Azure Blob Storage, Azure Files und Azure Table Storage mittels einfacher Befehle. Die Befehle sind für optimale Leistung konzipiert. Mit AzCopy können Sie Daten entweder zwischen einem Dateisystem und einem Speicherkonto oder zwischen Speicherkonten kopieren. AzCopy kann zum Kopieren von Daten aus lokalen Daten in ein Speicherkonto verwendet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein Speicherkonto. 
> * Verwenden Sie AzCopy, um all Ihre Daten hochzuladen.
> * Ändern Sie die Daten für Testzwecke.
> * Erstellen Sie einen geplanten Task oder Cron-Auftrag, um neue Dateien für den Upload zu identifizieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Laden Sie für dieses Tutorial die neueste Version von AzCopy herunter. Informationen finden Sie unter [Übertragen von Daten mit AzCopy v10](storage-use-azcopy-v10.md).

Unter Windows ist [Schtasks](/windows/win32/taskschd/schtasks) erforderlich, da das Tool in diesem Tutorial zum Planen einer Aufgabe verwendet wird. Linux-Benutzer verwenden stattdessen den Befehl „crontab“.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Erstellen eines Containers

Im ersten Schritt wird ein Container erstellt, da Blobs immer in einen Container hochgeladen werden müssen. Container dienen zum Organisieren von Blobgruppen und sind somit vergleichbar mit Ordnern für Dateien auf Ihrem Computer.

Führen Sie folgende Schritte durch, um einen Container zu erstellen:

1. Wählen Sie auf der Hauptseite die Schaltfläche **Speicherkonten** und dann das von Ihnen erstellte Speicherkonto aus.
2. Klicken Sie unter **Dienste** auf **Blobs** und dann auf **Container**.

   ![Der Screenshot zeigt die Containererstellung](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamen müssen mit einem Buchstaben oder einer Zahl beginnen. Sie dürfen nur Buchstaben, Zahlen und den Bindestrich (-) enthalten. Weitere Benennungsregeln für Blobs und Container finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

## <a name="download-azcopy"></a>Herunterladen von AzCopy

Laden Sie die ausführbare Datei für AzCopy V10 herunter:

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (ZIP)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (TAR)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP)

Speichern Sie die AzCopy-Datei auf Ihrem Computer. Fügen Sie den Speicherort der Datei der Systempfadvariablen hinzu, damit Sie von einem beliebigen Ordner auf Ihrem Computer auf diese ausführbare Datei verweisen können.

## <a name="authenticate-with-azure-ad"></a>Authentifizierung über Azure AD

Weisen Sie Ihrer Identität zunächst die Rolle [Mitwirkender an Storage-Blobdaten](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) zu. Weitere Informationen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](./storage-auth-aad-rbac-portal.md).

Öffnen Sie anschließend eine Eingabeaufforderung, geben Sie den folgenden Befehl ein, und drücken Sie die EINGABETASTE.

```azcopy
azcopy login
```

Dieser Befehl gibt einen Authentifizierungscode und die URL einer Website zurück. Öffnen Sie die Website, geben Sie den Code ein, und wählen Sie dann die Schaltfläche **Weiter** aus.

![Der Screenshot zeigt die Aufforderung zur Anmeldung.](media/storage-use-azcopy-v10/azcopy-login.png)

Daraufhin wird ein Anmeldefenster geöffnet. Melden Sie sich in diesem Fenster mit Ihren Azure-Kontoanmeldeinformationen bei Ihrem Azure-Konto an. Wenn Sie sich erfolgreich angemeldet haben, können Sie das Browserfenster schließen und mit der Verwendung von AzCopy beginnen.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Hochladen des Inhalts eines Ordners in Blobspeicher

Mit AzCopy können Sie unter [Windows](./storage-use-azcopy-v10.md) oder [Linux](./storage-use-azcopy-v10.md) alle Dateien in einem Ordner in Blob Storage hochladen. Um alle Blobs in einem Ordner hochzuladen, geben Sie den folgenden AzCopy-Befehl aus:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Ersetzen Sie den Platzhalter `<local-folder-path>` durch den Pfad zu einem Ordner, der Dateien enthält (beispielsweise `C:\myFolder` oder `/mnt/myFolder`).

* Ersetzen Sie den Platzhalter `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalter `<container-name>` durch den Namen des erstellten Containers.

Um die Inhalte des angegebenen Verzeichnisses rekursiv in Blob Storage hochzuladen, geben Sie die Option `--recursive` an. Beim Ausführen von AzCopy mit dieser Option werden auch alle Unterordner und die darin enthaltenen Dateien hochgeladen.

## <a name="upload-modified-files-to-blob-storage"></a>Hochladen geänderter Dateien in Blob Storage

Mit AzCopy können Sie Dateien basierend auf dem Zeitpunkt der letzten Änderung hochladen. 

Um dies zu testen, ändern Sie Dateien im Quellverzeichnis oder erstellen neue Dateien für Testzwecke. Verwenden Sie anschließend den AzCopy-Befehl `sync`.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Ersetzen Sie den Platzhalter `<local-folder-path>` durch den Pfad zu einem Ordner, der Dateien enthält (beispielsweise `C:\myFolder` oder `/mnt/myFolder`).

* Ersetzen Sie den Platzhalter `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalter `<container-name>` durch den Namen des erstellten Containers.

Weitere Informationen zum Befehl `sync` finden Sie unter [Transfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md#synchronize-files) (Übertragen von Daten mit AzCopy und Blob Storage).

## <a name="create-a-scheduled-task"></a>Erstellen einer geplanten Aufgabe

Sie können einen geplanten Task oder Cron-Auftrag, der ein AzCopy-Befehlsskript ausführt, erstellen. Das Skript identifiziert neue lokale Daten und lädt diese in einem bestimmten Zeitintervall in den Cloudspeicher hoch.

Kopieren Sie den AzCopy-Befehl in einen Text-Editor. Aktualisieren Sie die Parameterwerte des AzCopy-Befehls mit den entsprechenden Werten. Speichern Sie die Datei als `script.sh` (Linux) oder `script.bat` (Windows) für AzCopy. 

In diesem Beispielen wird davon ausgegangen, dass der Ordnername `myFolder`, der Speicherkontoname `mystorageaccount` und der Containername `mycontainer` lautet.

> [!NOTE]
> Im Linux-Beispiel wird ein SAS-Token angefügt. Sie müssen in Ihrem Befehl ein Token angeben. Die aktuelle Version von AzCopy V10 unterstützt die Azure AD-Autorisierung in Cron-Aufträgen nicht.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

In diesem Tutorial wird [SCHTASKS](/windows/win32/taskschd/schtasks) verwendet, um einen geplanten Task unter Windows zu erstellen. Der [Crontab](http://crontab.org/)-Befehl wird verwendet, um einen Cron-Auftrag unter Linux zu erstellen.

 **SCHTASKS** ermöglicht Administratoren das Erstellen, Löschen, Abfragen, Ändern, Ausführen und Beenden von geplanten Tasks auf einem lokalen oder Remotecomputer. Mithilfe von **Cron** können Linux- und UNIX-Benutzer Befehle oder Skripts zu einem bestimmten Datum und einer bestimmten Uhrzeit mithilfe von [Cron-Ausdrücken](https://en.wikipedia.org/wiki/Cron#CRON_expression) ausführen.

# <a name="linux"></a>[Linux](#tab/linux)

Um einem Cron-Auftrag unter Linux zu erstellen, geben Sie den folgenden Befehl auf einem Terminalserver ein:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Durch Angabe des Cron-Ausdrucks `*/5 * * * *` im Befehl wird angegeben, dass das Shell-Skript `script.sh` alle fünf Minuten ausgeführt werden soll. Sie können das Skript für die Ausführung zu einem bestimmten Zeitpunkt planen, d.h. täglich, monatlich oder jährlich. Weitere Informationen zum Festlegen von Datum und Uhrzeit für die Ausführung eines Auftrags finden Sie unter [Cron-Ausdrücke](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Um einen geplanten Task unter Windows zu erstellen, geben Sie den folgenden Befehl an einer Eingabeaufforderung oder in PowerShell ein:

In diesem Beispiel wird davon ausgegangen, dass sich das Skript auf dem Stammlaufwerk Ihres Computers befindet. Sie können Ihr Skript jedoch an einem Speicherort Ihrer Wahl ablegen.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Im Befehl wird Folgendes verwendet:
- Der Parameter `/SC` zum Festlegen eines Minutenplans
- Der Parameter `/MO` zum Angeben eines Intervalls von fünf Minuten
- Der Parameter `/TN` zum Festlegen des Tasknamens
- Der Parameter `/TR` zum Festlegen des Pfads zur Datei `script.bat`

Weitere Informationen zum Erstellen eines geplanten Tasks unter Windows finden Sie unter [SCHTASKS](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Um zu überprüfen, ob der geplante Task bzw. Cron-Auftrag ordnungsgemäß ausgeführt wird, erstellen Sie neue Dateien in Ihrem Verzeichnis `myFolder`. Warten Sie fünf Minuten, um sicherzustellen, dass die neuen Dateien in Ihr Speicherkonto hochgeladen wurden. Navigieren Sie zu Ihrem Protokollverzeichnis, um Ausgabeprotokolle des geplanten Tasks oder Cron-Auftrags anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Möglichkeiten zum Verschieben von lokalen Daten in Azure Storage und umgekehrt finden Sie unter folgendem Link:

* [Verschieben von Daten in und aus Azure Storage](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)  

Weitere Informationen zu AzCopy finden Sie in den folgenden Artikeln:

* [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)

* [Übertragen von Daten mit AzCopy und Blob Storage](storage-use-azcopy-blobs.md)

* [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

* [Übertragen von Daten mit AzCopy und Amazon S3-Buckets](storage-use-azcopy-s3.md)
 
* [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)