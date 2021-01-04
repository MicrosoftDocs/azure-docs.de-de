---
title: 'Schnellstart: Azure Blob Storage-Bibliothek v12: Xamarin'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Version 12 der Azure Blob Storage-Clientbibliothek mit Xamarin verwenden, um einen Container und ein Blob in Blobspeicher (Objektspeicher) zu erstellen. Danach erfahren Sie, wie Sie das Blob auf Ihr mobiles Gerät herunterladen und alle Blobs in einem Container auflisten.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 641117414c855a044d3ed55004b6e6ea15b259ac
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004737"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Schnellstart: Azure Blob Storage-Clientbibliothek v12 mit Xamarin

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Blob Storage-Clientbibliothek v12 mit Xamarin. Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Führen Sie die Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Blobspeicher ist für die Speicherung großer Mengen unstrukturierter Daten optimiert.

Verwenden Sie die Azure Blob Storage-Clientbibliothek v12 mit Xamarin für Folgendes:

* Erstellen eines Containers
* Hochladen eines Blobs in Azure Storage
* Auflisten aller Blobs in einem Container
* Herunterladen des Blobs auf Ihr Gerät
* Löschen eines Containers

Referenzlinks:

* [API-Referenzdokumentation](/dotnet/api/azure.storage.blobs)
* [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Beispiel](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
* Azure Storage-Konto – [Erstellen eines Speicherkontos](../common/storage-account-create.md)
* Visual Studio mit installierter [Workload für die mobile Entwicklung für .NET](/xamarin/get-started/installation/?pivots=windows) oder [Visual Studio für Mac](/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Einrichten
    
In diesem Abschnitt erfahren Sie, wie Sie ein Projekt für die Verwendung mit der Azure Blob Storage-Clientbibliothek v12 mit Xamarin vorbereiten.
    
### <a name="create-the-project"></a>Erstellen des Projekts

1. Öffnen Sie Visual Studio, und erstellen Sie eine leere Forms-App.
1. Nennen Sie sie wie folgt: BlobQuickstartV12

### <a name="install-the-package"></a>Installieren des Pakets

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Lösung, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Suchen Sie nach **Azure.Storage.Blobs**, und installieren Sie für alle Projekte in Ihrer Lösung die neueste stabile Version.

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Führen Sie im Verzeichnis **BlobQuickstartV12** die folgenden Schritte aus:

1. Öffnen Sie die Datei *MainPage.xaml* in Ihrem Editor.
1. Ersetzen Sie alles zwischen `<ContentPage></ContentPage>` durch Folgendes:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten). Blob Storage bietet drei Typen von Ressourcen:

* Das Speicherkonto
* Einen Container im Speicherkonto
* Ein Blob im Container

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Blob Storage-Architektur](./media/storage-blobs-introduction/blob1.png)

Verwenden Sie die folgenden .NET-Klassen zur Interaktion mit folgenden Ressourcen:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) Die `BlobServiceClient`-Klasse ermöglicht Ihnen, Azure Storage-Ressourcen und Blobcontainer zu bearbeiten.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): Die `BlobContainerClient`-Klasse ermöglicht Ihnen, Azure Storage-Container und deren Blobs zu bearbeiten.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): Die `BlobClient`-Klasse ermöglicht Ihnen, Azure Storage-Blobs zu bearbeiten.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) Die `BlobDownloadInfo`-Klasse stellt die Eigenschaften und den Inhalt dar, die beim Herunterladen eines Blobs zurückgegeben werden.

## <a name="code-examples"></a>Codebeispiele

In den Beispielcodeausschnitten wird gezeigt, wie folgende Aufgaben mit der Azure Blob Storage-Clientbibliothek für .NET in einer App vom Typ „Xamarin.Forms“ ausgeführt werden:

* [Erstellen von Variablen auf Klassenebene](#create-class-level-variables)
* [Container erstellen](#create-a-container)
* [Hochladen von Blobs in einen Container](#upload-blobs-to-a-container)
* [Auflisten der Blobs in einem Container](#list-the-blobs-in-a-container)
* [Herunterladen von Blobs](#download-blobs)
* [Löschen eines Containers](#delete-a-container)

### <a name="create-class-level-variables"></a>Erstellen von Variablen auf Klassenebene

Im Code weiter unten werden mehrere Variablen auf der Klassenebene deklariert. Diese werden im weiteren Verlauf des Beispiels für die Kommunikation mit Azure Blob Storage benötigt.

Sie werden zusätzlich zur Verbindungszeichenfolge für das Speicherkonto verwendet, die im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) festgelegt wurde.

Fügen Sie in der Datei *MainPage.xaml.cs* den folgenden Code als Variablen auf Klassenebene hinzu:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Erstellen eines Containers

Legen Sie einen Namen für den neuen Container fest. Der folgende Code hängt einen GUID-Wert an den Containernamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zum Benennen von Containern und Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Erstellen Sie eine Instanz der [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient)-Klasse. Rufen Sie dann die [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)-Methode auf, um den Container in Ihrem Speicherkonto zu erstellen.

Fügen Sie der Datei *MainPage.xaml.cs* den folgenden Code hinzu:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Hochladen von Blobs in einen Container

Der folgende Codeausschnitt führt folgende Aktionen durch:

1. Erstellen eines Speicherstreams (`MemoryStream`) mit Text
1. Hochladen des Texts in ein Blob durch Aufrufen der Funktion [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) der Klasse [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient), wobei sowohl der Dateiname als auch der Speicherstream (`MemoryStream`) mit dem Text übergeben werden. Mit dieser Methode wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, sofern es bereits vorhanden ist.

Fügen Sie der Datei *MainPage.xaml.cs* den folgenden Code hinzu:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Listen Sie die Blobs im Container auf, indem Sie die [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)-Methode aufrufen. In diesem Fall wurde dem Container nur ein Blob hinzugefügt, sodass beim Auflisten auch nur ein Blob zurückgegeben wird.

Fügen Sie der Datei *MainPage.xaml.cs* den folgenden Code hinzu:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie das zuvor erstellte Blob herunter, indem Sie die [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync)-Methode aufrufen. Im Beispielcode wird die `Stream`-Darstellung des Blobs zunächst in einen Speicherstream (`MemoryStream`) und dann in einen Streamreader (`StreamReader`) kopiert, damit der Text angezeigt werden kann.

Fügen Sie der Datei *MainPage.xaml.cs* den folgenden Code hinzu:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Löschen eines Containers

Im folgenden Code werden die von der App erstellten Ressourcen bereinigt, indem der gesamte Container mithilfe von [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) gelöscht wird.

Die App fordert vor dem Löschen von Blob und Container eine Bestätigung an. Dies ist eine gute Möglichkeit, um zu überprüfen, ob die Ressourcen richtig erstellt wurden, bevor sie gelöscht werden.

Fügen Sie der Datei *MainPage.xaml.cs* den folgenden Code hinzu:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Ausführen des Codes

Beim Start der App wird der Container zunächst wie dargestellt erstellt. Zum Hochladen, Auflisten und Herunterladen der Blobs sowie zum Löschen des Containers muss dann auf die Schaltflächen geklickt werden.

Drücken Sie unter Windows F5, um die App auszuführen. Drücken Sie auf einem Mac BEFEHLSTASTE+EINGABETASTE, um die App auszuführen.

Von der App wird nach jedem Vorgang etwas auf dem Bildschirm ausgegeben. Die Ausgabe der App sieht etwa wie im folgenden Beispiel aus:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Vergewissern Sie sich, dass die Ausgabe des Blobinhalts auf dem Bildschirm dem hochgeladenen Wert entspricht, bevor Sie mit der Bereinigung beginnen.

Bestätigen Sie nach Überprüfung der Werte die Aufforderung, um den Container zu löschen und die Demo abzuschließen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie Blobs unter Verwendung der Azure Blob Storage-Clientbibliothek v12 mit Xamarin hochladen, herunterladen und auflisten.

Weitere Beispiel-Apps für Blob Storage finden Sie unter:

> [!div class="nextstepaction"]
> [Xamarin-Beispiel für das Azure Blob Storage SDK v12](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Tutorials, Beispiele, Schnellstartanleitungen und weiteres Dokumentationsmaterial finden Sie unter [Entwicklung mobiler Anwendungen mit Microsoft](/azure/mobile-apps).
* Weitere Informationen zu Xamarin finden Sie unter [Erste Schritte mit Xamarin](/xamarin/get-started/).