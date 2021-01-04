---
title: 'Schnellstart: Clientbibliothek für Azure Blob Storage: Ruby'
description: Hier erfahren Sie, wie Sie ein Speicherkonto und einen Container in Azure Blob Storage erstellen. Außerdem erfahren Sie, wie Sie die Speicherclientbibliothek für Ruby verwenden, um ein Blob zu erstellen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781095"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Schnellstart: Azure Blob Storage-Clientbibliothek für Ruby

Hier erfahren Sie, wie Sie mithilfe von Ruby Blobs erstellen, herunterladen und in einem Container in Microsoft Azure Blob Storage auflisten.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Stellen Sie sicher, dass die folgenden zusätzlichen Komponenten installiert sind:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Azure Storage-Bibliothek für Ruby](https://github.com/azure/azure-storage-ruby) mit dem [RubyGem-Paket](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die in diesem Schnellstart verwendete [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) ist eine einfache Ruby-Anwendung.

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. Mit dem folgenden Befehl wird das Repository auf Ihrem lokalen Computer geklont:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Navigieren Sie zum Ordner *storage-blobs-ruby-quickstart*, und öffnen Sie die Datei *example.rb* in Ihrem Code-Editor.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Geben Sie Ihren Speicherkontonamen und Ihren Kontoschlüssel an, um eine Instanz vom Typ [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) für Ihre Anwendung zu erstellen.

Durch den folgenden Code in der Datei *example.rb* wird ein neues Objekt vom Typ [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) instanziiert. Ersetzen Sie die Werte *accountname* und *accountkey* mit Ihrem Kontonamen und dem Kontoschlüssel.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

In dem Beispiel wird ein Container in Blob Storage erstellt. Außerdem wird ein neues Blob in dem Container erstellt, die Blobs im Container werden aufgelistet, und das Blob wird in eine lokale Datei heruntergeladen.

Führen Sie das Beispiel aus. Hier sehen Sie eine Beispielausgabe der Anwendungsausführung:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Wenn Sie zum Fortsetzen die EINGABETASTE drücken, werden der Speichercontainer und die lokale Datei durch das Beispielprogramm gelöscht. Überprüfen Sie den Ordner *Dokumente* auf die heruntergeladene Datei, bevor Sie den Vorgang fortsetzen.

Die Dateien in Ihrem Speicherkonto können auch mithilfe des [Azure Storage-Explorers](https://storageexplorer.com) angezeigt werden. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht.

Drücken Sie nach der Überprüfung der Dateien die EINGABETASTE, um die Testdateien zu löschen und die Demo zu beenden. Öffnen Sie die Datei *example.rb*, um den Code anzuzeigen.

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Als Nächstes wird der Beispielcode Schritt für Schritt erläutert, damit Sie verstehen, wie er funktioniert.

### <a name="get-references-to-the-storage-objects"></a>Abrufen von Verweisen auf die Speicherobjekte

Zunächst müssen Instanzen der Objekte erstellt werden, die für den Zugriff auf und die Verwaltung von Blob Storage verwendet werden. Diese Objekte bauen aufeinander auf. Jedes wird vom jeweils nächsten Objekt in der Liste verwendet.

- Erstellen Sie eine Instanz des [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService)-Elements von Azure Storage, um die Anmeldeinformationen für die Verbindung einzurichten.
- Erstellen Sie das Objekt [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container), das den Container darstellt, auf den Sie zugreifen. Container werden zum Organisieren der Blobs verwendet, so wie Sie auf Ihrem Computer Ordner zum Organisieren von Dateien verwenden.

Wenn Sie über das Containerobjekt verfügen, können Sie ein Blob vom Typ [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) erstellen, das auf ein bestimmtes Blob verweist, für das Sie sich interessieren. Verwenden Sie das Objekt [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block), um Blobs zu erstellen, herunterzuladen und zu kopieren.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Der folgende Beispielcode bewirkt Folgendes:

- Erstellen eines neuen Containers
- Festlegen von Berechtigungen für den Container, um öffentliche Blobs zu erhalten. Der Name des Containers ist eine Kombination aus *quickstartblobs* und einer angefügten eindeutigen ID.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Erstellen eines Blobs im Container

Von Blob Storage werden Block-, Anfüge- und Seitenblobs unterstützt. Rufen Sie zum Erstellen eines Blobs die Methode [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) auf, und übergeben Sie dabei die Daten für das Blob.

Im folgenden Beispiel wird ein Blob namens *QuickStart_* mit einer eindeutigen ID und der Dateierweiterung *.txt* im zuvor erstellten Container erstellt:

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Blockblobs können eine Größe von bis zu 4,7 TB haben und beliebige Daten umfassen – von Arbeitsblättern bis hin zu großen Videodateien. Seitenblobs werden in erster Linie für die VHD-Dateien verwendet, die virtuellen IaaS-Computern zugrunde liegen. Anfügeblobs werden üblicherweise zur Protokollierung genutzt und können beispielsweise verwendet werden, wenn Sie etwas in eine Datei schreiben und nach und nach weitere Informationen hinzufügen möchten.

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs im Container

Rufen Sie eine Liste der Dateien im Container mithilfe der Methode „[list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method)“ ab. Der folgende Code ruft die Liste der Blobs ab und zeigt anschließend deren Namen an:

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Herunterladen eines Blobs

Verwenden Sie die Methode [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method), um ein Blob auf Ihren lokalen Datenträger herunterzuladen. Durch den folgenden Code wird das in einem vorherigen Abschnitt erstellte Blob heruntergeladen:

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn ein Blob nicht mehr benötigt wird, verwenden Sie [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method), um es zu entfernen. Mithilfe der Methode [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) können Sie einen gesamten Container löschen. Beim Löschen eines Containers werden auch alle im Container gespeicherten Blobs gelöscht.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Ressourcen für die Entwicklung von Ruby-Anwendungen mit Blobs

Weitere Ressourcen für die Ruby-Entwicklung finden Sie hier:

- Zeigen Sie den [Quellcode der Ruby-Clientbibliothek](https://github.com/Azure/azure-storage-ruby) für Azure Storage auf GitHub an, und laden Sie ihn herunter.
- Sehen Sie sich [Azure-Beispiele](/samples/browse/?products=azure&languages=ruby) an, die unter Verwendung der Ruby-Clientbibliothek geschrieben wurden.
- [Beispiel: Erste Schritte mit Azure Storage in Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mithilfe von Ruby Dateien zwischen Azure Blob Storage und einem lokalen Datenträger übertragen. Weitere Informationen zur Verwendung von Blob Storage finden Sie in der Speicherkontoübersicht.

> [!div class="nextstepaction"]
> [Speicherkontoübersicht](../common/storage-account-overview.md)

Weitere Informationen zum Storage-Explorer und zu Blobs finden Sie unter [Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
