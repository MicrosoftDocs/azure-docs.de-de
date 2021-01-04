---
title: Entwickeln für Azure Files mit C++ | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie C++-Anwendungen und -Dienste entwickeln, die Azure Files zum Speichern von Dateidaten verwenden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb74ab16e51fbb3a157757353d5743e889f993dd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629698"
---
# <a name="develop-for-azure-files-with-c"></a>Entwickeln für Azure Files mit C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Informationen zu diesem Tutorial

In diesem Tutorial erfahren Sie, wie Sie grundlegende Vorgänge in Azure Files ausführen. Anhand von in C++ geschriebenen Beispielen lernen Sie, wie Sie Freigaben und Verzeichnisse erstellen sowie Dateien hochladen, auflisten und löschen. Wenn Sie noch nicht mit Azure Files vertraut sind, helfen die in den folgenden Abschnitten erläuterten Konzepte, die Beispiele besser zu verstehen.

* Erstellen und Löschen von Azure-Dateifreigaben
* Erstellen und Löschen von Verzeichnissen
* Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe
* Hochladen, Herunterladen und Löschen einer Datei
* Festlegen des Kontingents (maximale Größe) für eine Azure-Dateifreigabe
* Erstellen eines SAS-Schlüssels (Shared Access Signature) für eine Datei, die eine für die Freigabe definierte SAS-Richtlinie verwendet

> [!Note]  
> Da der Zugriff auf Azure Files über SMB möglich ist, können Sie einfache Anwendungen schreiben, die über die standardmäßigen E/A-Klassen und -Funktionen von C++ auf die Azure-Dateifreigabe zugreifen. In diesem Artikel erfahren Sie, wie Sie Anwendungen schreiben, die das Azure Storage C++ SDK verwenden, das über die [Datei-REST-API](/rest/api/storageservices/file-service-rest-api) mit Azure Files kommuniziert.

## <a name="create-a-c-application"></a>Erstellen einer C++-Anwendung

Zum Erstellen der Beispiele müssen Sie die Azure Storage-Clientbibliothek 2.4.0 für C++ installieren. Sie sollten auch ein Azure-Speicherkonto erstellt haben.

Zum Installieren der Azure Storage-Clientbibliothek 2.4.0 für C++ können Sie eine der folgenden Methoden verwenden:

* **Linux:** Befolgen Sie die Anweisungen auf der Seite [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) (in englischer Sprache).
* **Windows:** Klicken Sie in Visual Studio auf **Extras &gt; NuGet-Paket-Manager &gt; Paket-Manager-Konsole**. Geben Sie im Fenster der [NuGet-Paket-Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**:
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Einrichten der Anwendung für die Verwendung von Azure Files

Fügen Sie die folgenden „include“-Anweisungen an den Stellen am Anfang der C++-Quelldatei hinzu, an denen Sie Azure Files bearbeiten möchten:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Zur Verwendung des Dateidiensts müssen Sie eine Verbindung mit Ihrem Azure-Speicherkonto herstellen. Im ersten Schritt muss eine Verbindungszeichenfolge konfiguriert werden, mit der die Verbindung mit dem Speicherkonto hergestellt wird. Definieren Sie dazu eine statische Variable.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Herstellen einer Verbindung mit einem Azure-Speicherkonto

Sie können Ihre Speicherkontoinformationen mit der Klasse **cloud_storage_account** angeben. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicher-Verbindungszeichenfolge die **parse** -Methode.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

Alle Dateien und Verzeichnisse in einer Azure-Dateifreigabe befinden sich in einem Container mit dem Namen **Freigabe**. Das Speicherkonto kann über so viele Freigaben verfügen, wie es die Kapazität des Kontos zulässt. Für den Zugriff auf eine Freigabe und ihren Inhalt muss ein Azure Files-Client verwendet werden.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Mit dem Azure Files-Client können Sie dann einen Verweis auf die Freigabe abrufen.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Um die Freigabe zu erstellen, verwenden Sie die **create_if_not_exists**-Methode des **cloud_file_share**-Objekts.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

An diesem Punkt enthält **share** einen Verweis auf eine Freigabe mit dem Namen **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Löschen einer Azure-Dateifreigabe

Das Löschen einer Freigabe erfolgt durch Aufrufen der **delete_if_exists** -Methode für ein cloud_file_share-Objekt. Hier ein Beispielcode dazu.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Sie können den Speicher organisieren, indem Sie Dateien in Unterverzeichnissen ablegen, anstatt alle Dateien im Stammverzeichnis zu speichern. Mit Azure Files können Sie so viele Verzeichnisse erstellen wie für Ihr Konto zulässig. Der unten stehende Code erstellt ein Verzeichnis namens **my-sample-directory** im Stammverzeichnis sowie ein Unterverzeichnis namens **my-sample-subdirectory**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Löschen eines Verzeichnisses

Das Löschen eines Verzeichnisses ist eine einfache Angelegenheit. Dabei ist jedoch zu beachten, dass Verzeichnisse, die noch Dateien oder andere Verzeichnisse enthalten, nicht gelöscht werden können.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Auflisten von Dateien und Verzeichnissen in einer Azure-Dateifreigabe

Eine Liste von Dateien und Verzeichnissen in einer Freigabe kann einfach durch Aufrufen von **list_files_and_directories** in einem **cloud_file_directory**-Verweis abgerufen werden. Um auf den umfassenden Satz an Eigenschaften und Methoden für ein zurückgegebenes **list_file_and_directory_item**-Objekt zuzugreifen, müssen Sie die **list_file_and_directory_item.as_file**-Methode aufrufen, um ein **cloud_file**-Objekt abzurufen, oder Sie müssen die **list_file_and_directory_item.as_directory**-Methode aufrufen, um ein **cloud_file_directory**-Objekt abzurufen.

Der folgende Code zeigt, wie Sie den URI der einzelnen Elemente im Stammverzeichnis der Freigabe abrufen und ausgeben.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Hochladen einer Datei

Eine Azure-Dateifreigabe enthält mindestens ein Stammverzeichnis, in dem Dateien gespeichert werden können. In diesem Abschnitt erfahren Sie, wie Sie eine Datei vom lokalen Speicher in das Stammverzeichnis einer Freigabe hochladen.

Im ersten Schritt beim Hochladen einer Datei wird ein Verweis auf das Verzeichnis abgerufen, in dem sie gespeichert werden soll. Rufen Sie dazu die **get_root_directory_reference**-Methode des Freigabeobjekts auf.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Nachdem Sie nun einen Verweis auf das Stammverzeichnis der Freigabe erstellt haben, können Sie eine Datei in dieses Verzeichnis hochladen. Dieses Beispiel lädt Daten aus einer Datei, aus Text und aus einem Datenstrom hoch.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Herunterladen einer Datei

Zum Herunterladen von Dateien rufen Sie zuerst einen Dateiverweis ab und dann die **download_to_stream**-Methode auf, um den Dateiinhalt in ein Datenstromobjekt zu übertragen, das Sie dann dauerhaft in einer lokalen Datei speichern können. Alternativ können Sie die **download_to_file**-Methode verwenden, um den Inhalt einer Datei in eine lokale Datei herunterzuladen. Sie können die **download_text**-Methode verwenden, um den Inhalt einer Datei als Textzeichenfolge herunterzuladen.

Das folgende Beispiel verwendet die Methoden **download_to_stream** und **download_text**, um das Herunterladen der Dateien zu veranschaulichen, die in den vorherigen Abschnitten erstellt wurden.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Löschen von Dateien

Ein weiterer häufiger Azure Files-Vorgang ist das Löschen von Dateien. Der folgende Code löscht eine Datei namens „my-sample-file-3“, die im Stammverzeichnis gespeichert ist.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Festlegen des Kontingents (maximale Größe) für eine Azure-Dateifreigabe

Sie können das Kontingent (also die maximale Größe) für eine Dateifreigabe in Gigabytes festlegen. Sie können auch überprüfen, wie viele Daten sich aktuell auf der Freigabe befinden.

Durch Festlegen des Kontingents für eine Freigabe können Sie die Gesamtgröße der Dateien einschränken, die in der Freigabe gespeichert werden. Überschreitet die Gesamtgröße der Dateien in der Freigabe das für die Freigabe festgelegte Kontingent, können die Clients weder die Größe von vorhandenen Dateien ändern noch neue Dateien erstellen – es sei denn, diese sind leer.

Das folgende Beispiel zeigt, wie Sie die aktuelle Nutzung einer Freigabe überprüfen und das Kontingent für die Freigabe festlegen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generieren einer SAS für eine Datei oder Dateifreigabe

Sie können eine Shared Access Signature (SAS) für eine Dateifreigabe oder eine einzelne Datei generieren. Sie können auch eine SAS-Richtlinie für eine Dateifreigabe erstellen, um Shared Access Signatures zu verwalten. Erstellen einer SAS-Richtlinie wird empfohlen, weil sie eine Möglichkeit bietet, die SAS zu widerrufen, wenn diese gefährdet sein sollte.

Im folgenden Beispiel wird eine SAS-Richtlinie für eine Freigabe erstellt und die Richtlinie dann dazu verwendet, die Einschränkungen für eine SAS für eine Datei in der Freigabe bereitzustellen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Storage finden Sie in den folgenden Ressourcen:

* [Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp)
* [Azure Storage-Dateidienstbeispiele in C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage-Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage-Dokumentation](https://azure.microsoft.com/documentation/services/storage/)