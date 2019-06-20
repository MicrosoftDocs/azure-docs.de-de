---
title: Entwickeln für Azure Files mit Python | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Python-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Dateidaten verwenden.
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ecb3ef82196c3b6febd44850b47f467ba37facc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701597"
---
# <a name="develop-for-azure-files-with-python"></a>Entwickeln für Azure Files mit Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Dieses Tutorial veranschaulicht die grundlegende Verwendung von Python bei der Entwicklung von Anwendungen oder Diensten, die Azure Files zum Speichern von Dateidaten verwenden. In diesem Tutorial erstellen wir eine einfache Konsolenanwendung und zeigen Ihnen, wie Sie grundlegende Aktionen mit Python und Azure Files ausführen:

* Erstellen von Azure-Dateifreigaben
* Erstellen von Verzeichnissen
* Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe
* Hochladen, Herunterladen und Löschen einer Datei

> [!Note]  
> Da auf Azure Files über das SMB-Protokoll zugegriffen werden kann, können Sie unter Verwendung der standardmäßigen Python-E/A-Klassen und -Funktionen einfache Anwendungen mit Zugriff auf die Azure-Dateifreigabe schreiben. In diesem Artikel erfahren Sie, wie Sie Anwendungen schreiben, die das Azure Storage Python SDK verwenden, das über die [Azure Files-REST-API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) mit Azure Files kommuniziert.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Herunterladen und Installieren des Azure Storage SDK für Python

Das [Azure Storage SDK für Python](https://github.com/azure/azure-storage-python) erfordert Python 2.7, 3.3, 3.4, 3.5 oder 3.6.
 
## <a name="install-via-pypi"></a>Installation über PyPI

Geben Sie für die Installation über Python Package Index (PyPI) folgenden Befehl ein:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Wenn Sie ein Upgrade aus dem Azure Storage SDK für Python Version 0.36 oder früher vornehmen, deinstallieren Sie das ältere SDK mit `pip uninstall azure-storage`, bevor Sie das neueste Paket installieren.

Informationen zu alternativen Installationsmethoden finden Sie im [Azure Storage SDK für Python auf GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Anzeigen der Beispielanwendung
Um eine Beispielanwendung anzuzeigen und auszuführen, die zeigt, wie Python mit Azure Files verwendet wird, finden Sie unter [Azure Storage: Erste Schritte mit Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Um die Beispielanwendung auszuführen, stellen Sie sicher, dass Sie die `azure-storage-file`- und `azure-storage-common`-Pakete beide installiert haben.

## <a name="set-up-your-application-to-use-azure-files"></a>Einrichten der Anwendung für die Verwendung von Azure Files
Fügen Sie am Anfang jeder Python-Quelldatei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Einrichten der Verbindung mit Azure Files 
Das `FileService`-Objekt ermöglicht das Arbeiten mit Freigaben, Verzeichnissen und Dateien. Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein `FileService`-Objekt erstellt. Ersetzen Sie `<myaccount>` und `<mykey>` durch Ihren Kontonamen und Schlüssel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
Im folgenden Codebeispiel können Sie die Freigabe mithilfe eines `FileService`-Objekts erstellen, falls nicht vorhanden.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses
Sie können zudem den Speicher organisieren, indem Sie Dateien in Unterverzeichnissen ablegen, anstatt alle Dateien im Stammverzeichnis zu speichern. Mit Azure Files können Sie so viele Verzeichnisse erstellen wie für Ihr Konto zulässig. Mit dem folgenden Code wird ein Unterverzeichnis mit dem Namen **sampledir** im Stammverzeichnis erstellt.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe
Verwenden Sie zum Auflisten der Dateien und Verzeichnisse in einer Freigabe die Methode **list\_directories\_and\_files**. Diese Methode gibt einen Generator zurück. Der folgende Code gibt den **Namen** der einzelnen Dateien und Verzeichnisse in einer Freigabe an der Konsole aus.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Hochladen einer Datei 
Eine Azure-Dateifreigabe enthält mindestens ein Stammverzeichnis, in dem Dateien gespeichert werden können. In diesem Abschnitt erfahren Sie, wie Sie eine Datei vom lokalen Speicher in das Stammverzeichnis einer Freigabe hochladen.

Um eine Datei zu erstellen und Daten hochzuladen, verwenden die Methode `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` oder `create_file_from_text`. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

`create_file_from_path` lädt den Inhalt einer Datei aus dem angegebenen Pfad, `create_file_from_stream` den Inhalt einer Datei aus einer bereits geöffneten Datei/einem bereits geöffneten Stream hoch. `create_file_from_bytes` lädt ein Byte-Array, `create_file_from_text` den angegebenen Textwert wie vorgegeben codiert hoch (Standardcodierung ist UTF-8).

Das folgende Beispiel lädt den Inhalt der Datei **sunset.png** in die Datei **myfile** hoch.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Herunterladen einer Datei
Verwenden Sie zum Herunterladen von Daten aus einer Datei `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` oder `get_file_to_text`. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Das folgende Beispiel verwendet `get_file_to_path`, um den Inhalt der Datei **myfile** herunterzuladen und in der Datei **out-sunset.png** zu speichern.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Löschen von Dateien
Um eine Datei zu löschen, rufen Sie `delete_file` auf.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Erstellen einer Freigabemomentaufnahme
Sie können eine Zeitpunktkopie Ihrer gesamten Dateifreigabe erstellen.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Erstellen einer Freigabemomentaufnahme mit Metadaten**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Auflisten von Freigaben und Momentaufnahmen 
Sie können alle Momentaufnahmen für eine bestimmte Freigabe auflisten.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Durchsuchen einer Freigabemomentaufnahme
Sie können den Inhalt der einzelnen Freigabemomentaufnahmen durchsuchen, um Dateien und Verzeichnisse von diesem Zeitpunkt abzurufen.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Abrufen einer Datei aus einer Freigabemomentaufnahme
Sie können eine Datei aus einer Freigabemomentaufnahme für Ihr Wiederherstellungsszenario herunterladen.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Löschen einer einzelnen Freigabemomentaufnahme  
Sie können eine einzelne Freigabemomentaufnahme löschen.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Löschen einer Freigabe bei vorhandenen Freigabemomentaufnahmen
Eine Freigabe, die Momentaufnahmen enthält, kann erst gelöscht werden, nachdem alle Momentaufnahmen gelöscht wurden.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit dem Bearbeiten von Azure Files mit Python vertraut gemacht haben, folgen Sie diesen Links, um mehr zu erfahren.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage-SDK für Python](https://github.com/Azure/azure-storage-python)
