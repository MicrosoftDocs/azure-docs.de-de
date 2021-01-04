---
title: Verwenden des Objektspeichers (Blob-Speichers) mit C++ – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von C++ nicht strukturierte Daten (Blobs) in der Cloud mit Azure Blob Storage (Objektspeicher) speichern.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 64069292ea0059216d06bfc41316c2aed7484dd0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011097"
---
# <a name="how-to-use-blob-storage-from-c"></a>Verwenden des Blob-Speichers mit C++

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit Azure Blob Storage demonstriert. Die Beispiele veranschaulichen das Hochladen, Auflisten, Herunterladen und Löschen von Blobs. Die Beispiele sind in C++ geschrieben und greifen auf die [Azure-Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)zurück.
Weitere Informationen zum Blob Storage finden Sie unter [Einführung in Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Diese Anleitung gilt für die Azure Storage-Clientbibliothek für C++ in der Version 1.0.0 und höher. Microsoft empfiehlt die neueste Version der Speicherclientbibliothek für C++, die über [NuGet](https://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/Azure/azure-storage-cpp) verfügbar ist.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Erstellen einer C++-Anwendung
In diesem Handbuch werden Sie Speicherfunktionen verwenden, die innerhalb einer C++-Anwendung ausgeführt werden können.

Dafür müssen Sie die Azure-Speicherclientbibliothek für C++ installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen.

Zum Installieren der Azure-Speicherclientbibliothek für C++ können Sie die folgenden Methoden verwenden:

- **Linux:** Befolgen Sie die Anweisungen auf der Seite [README-Datei zur Azure Storage-Clientbibliothek für C++: Erste Schritte unter Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
- **Windows:** Verwenden Sie unter Windows [vcpkg](https://github.com/microsoft/vcpkg) als Abhängigkeits-Manager. Befolgen Sie zum Initialisieren von vcpkg die Anweisungen im [Schnellstart](https://github.com/microsoft/vcpkg#quick-start). Verwenden Sie dann folgenden Befehl, um die Bibliothek zu installieren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Eine Anleitung zum Erstellen des Quellcodes und zum Exportieren in NuGet finden Sie in der [Infodatei](https://github.com/Azure/azure-storage-cpp#download--install).

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher
Fügen Sie die folgenden "include"-Anweisungen am Anfang der C++-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge
Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der Speicherzugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Speicherkonto als Werte für *AccountName* und *AccountKey* eingegeben werden. Weitere Informationen über Speicherkonten und Zugriffsschlüssel finden Sie unter [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Zum Testen der Anwendung auf Ihrem lokalen Windows-Computer können Sie den [Azurite-Speicheremulator](../common/storage-use-azurite.md) verwenden. Azurite ist ein Dienstprogramm, das die in Azure verfügbaren Blob-, und Tabellen-Dienste auf dem lokalen Entwicklungscomputer simuliert. Im folgenden Beispiel wird dargestellt, wie Sie ein statisches Feld zur Übergabe der Verbindungszeichenfolge an den lokalen Speicheremulator deklarieren:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Unter [Verwenden des Azurite-Emulators für die lokale Azure Storage-Entwicklung](../common/storage-use-azurite.md) finden Sie Informationen zum Starten von Azurite.

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="retrieve-your-storage-account"></a>Abrufen Ihres Speicherkontos
Sie können Ihre Speicherkontoinformationen mit der Klasse **cloud_storage_account** angeben. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicher-Verbindungszeichenfolge die **parse** -Methode.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Rufen Sie als nächstes einen Verweis auf die Klasse **cloud_blob_client** ab, da Sie mit dieser Objekte abrufen können, die im Blob-Speicher gespeicherte Container und Blobs darstellen. Mit dem folgenden Code wird unter Verwendung des oben abgerufenen Speicherkontenobjekts ein **cloud_blob_client**-Objekt erstellt:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Gewusst wie: Erstellen eines Containers
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien (Blobs) im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## <a name="how-to-upload-a-blob-into-a-container"></a>Gewusst wie: Hochladen eines Blobs in einen Container
Azure Blob Storage unterstützt Block- und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **upload_from_stream**-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Sie können wahlweise auch die **upload_from_file**-Methode verwenden, um eine Datei in ein Blockblob hochzuladen.

## <a name="how-to-list-the-blobs-in-a-container"></a>Gewusst wie: Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Anschließend können Sie mit der **list_blobs**-Methode des Containers die darin enthaltenen Blobs und/oder Verzeichnisse abrufen. Um auf den umfassenden Satz an Eigenschaften und Methoden für ein zurückgegebenes **list_blob_item**-Objekt zuzugreifen, müssen Sie die **list_blob_item.as_blob**-Methode aufrufen, um ein **cloud_blob**-Objekt abzurufen, oder die **list_blob.as_directory**-Methode aufrufen, um ein cloud_blob_directory-Objekt abzurufen. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente im Container **my-sample-container** abgerufen und ausgegeben wird:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Weitere Informationen zu Auflistungsvorgängen finden Sie unter [Auflisten von Azure Storage-Ressourcen in C++](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Gewusst wie: Herunterladen von Blobs
Zum Herunterladen von Blobs rufen Sie zunächst einen Blobverweis ab, und rufen Sie anschließend die **download_to_stream**-Methode auf. Im folgenden Beispiel wird die **download_to_stream**-Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternativ können Sie die **download_to_file**-Methode verwenden, um den Inhalt eines Blobs in eine Datei herunterzuladen.
Sie können auch die **download_text**-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Gewusst wie: Löschen von Blobs
Zum Löschen eines Blobs rufen Sie zunächst einen Blobverweis ab, und rufen Sie anschließend die **delete_blob**-Methode für diesen auf.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Blob-Speichern vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr über Azure-Speicher zu erfahren.

- [Verwenden des Warteschlangenspeichers in C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Verwenden des Tabellenspeichers mit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Auflisten von Azure Storage-Ressourcen in C++](../common/storage-c-plus-plus-enumeration.md)
- [Referenz zur Speicherclientbibliothek für C++](https://azure.github.io/azure-storage-cpp)
- [Azure Storage-Dokumentation](https://azure.microsoft.com/documentation/services/storage/)
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](../common/storage-use-azcopy-v10.md)