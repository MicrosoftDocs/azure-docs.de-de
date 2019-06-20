---
title: Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (ASP.NET Core) | Microsoft-Dokumentation
description: Erfahren Sie etwas über die ersten Schritte mit Azure Blob Storage in einem ASP.NET Core-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
documentationcenter: ''
author: camsoper
manager: wpickett
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: f8de7f7a263a087abb16ed48d05b2cae9834b4ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110505"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage ist ein Dienst, bei dem unstrukturierte Daten in der Cloud als Objekte oder Blobs gespeichert werden. In Blob Storage können alle Arten von Text- oder Binärdaten gespeichert werden, z. B. ein Dokument, eine Mediendatei oder ein Installer einer Anwendung. Der Blobspeicher wird auch als Objektspeicher bezeichnet. Weitere Informationen zum Blob Storage finden Sie unter [Einführung in Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

In diesem Tutorial wird gezeigt, wie Sie ASP.NET Core-Code für einige häufige Szenarien schreiben, die Blobspeicher verwenden. Zu den Szenarien zählen das Erstellen eines Blobcontainers sowie das Hochladen, Auflisten, Herunterladen und Löschen von Blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Einrichten der Entwicklungsumgebung

Dieser Abschnitt führt Sie durch das Einrichten der Entwicklungsumgebung. Dies umfasst das Erstellen einer ASP.NET MVC-App (Model-View-Controller), das Hinzufügen einer Verbindung mit verbundenen Diensten, das Hinzufügen eines Controllers und das Angeben der erforderlichen Namespace-Direktiven.

### <a name="create-an-aspnet-mvc-app-project"></a>Erstellen einer ASP.NET MVC-App

1. Öffnen Sie Visual Studio.

1. Wählen Sie im Hauptmenü **Datei** > **Neu** > **Projekt** aus.

1. Wählen Sie im Dialogfeld **Neues Projekt** **Web** > **ASP.NET Core-Webanwendung** > **AspNetCoreStorage** aus. Wählen Sie dann **OK**aus.

    ![Screenshot: Visual Studio-Dialogfeld „Neues Projekt“](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung** **.NET Core** > **ASP.NET Core 2.0** > **Webanwendung (Model-View-Controller)** aus. Wählen Sie dann **OK**aus.

    ![Screenshot: Dialogfeld „Neue ASP.NET Core-Webanwendung“](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Herstellen einer Verbindung mit einem Azure-Speicherkonto über verbundene Dienste

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.

2. Wählen Sie im Kontextmenü **Hinzufügen** > **Verbundener Dienst** aus.

1. Wählen Sie im Dialogfeld **Verbundene Dienste** die Option **Cloudspeicher mit Azure Storage** und dann **Konfigurieren** aus.

    ![Screenshot: Dialogfeld „Verbundene Dienste“](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Wählen Sie im Dialogfeld **Azure Storage** das Azure-Speicherkonto aus, das für dieses Tutorial verwendet werden soll. Um ein neues Azure-Speicherkonto zu erstellen, wählen Sie **Neues Azure-Speicherkonto erstellen** aus, und füllen Sie das Formular aus. Wählen Sie **Hinzufügen** aus, nachdem Sie entweder ein vorhandenes Speicherkonto ausgewählt oder ein neues Speicherkonto erstellt haben. Visual Studio installiert das NuGet-Paket für Azure Storage und eine Speicherverbindungszeichenfolge in **appsettings.json**.

> [!TIP]
> Informationen zum Erstellen eines Speicherkontos mit dem [Azure-Portal](https://portal.azure.com) finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-quickstart-create-account.md).
>
> Sie können ein Azure-Speicherkonto auch mit [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md) oder [Azure Cloud Shell](../cloud-shell/overview.md) erstellen.


### <a name="create-an-mvc-controller"></a>Erstellen eines MVC-Controllers 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller**.

2. Wählen Sie im Kontextmenü **Hinzufügen** > **Controller** aus.

    ![Screenshot des Projektmappen-Explorers](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC 5 Controller - Leer** und dann **Hinzufügen** aus.

    ![Screenshot des Dialogfelds „Gerüst hinzufügen“](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Benennen Sie den Controller im Dialogfeld **Leeren MVC-Controller hinzufügen** mit *BlobsController*, und wählen Sie **Hinzufügen** aus.

    ![Screenshot des Dialogfelds „Leeren MVC-Controller hinzufügen“](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Fügen Sie der Datei `BlobsController.cs` die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Herstellen einer Verbindung mit einem Speicherkonto und Abrufen eines Containerverweises

Ein Blobcontainer ist eine geschachtelte Hierarchie von Blobs und Ordnern. Für die übrigen Schritte in diesem Dokument ist ein Verweis auf einen Blobcontainer erforderlich, daher muss der Code in einer eigenen Methode platziert werden, um wiederverwendet werden zu können.

Mit den folgenden Schritten wird eine Methode erstellt, um mithilfe der Verbindungszeichenfolge in **appsettings.json** eine Verbindung mit dem Speicherkonto herzustellen. Durch diese Schritte wird außerdem ein Verweis auf einen Container erstellt. Die Einstellung der Verbindungszeichenfolge in **appsettings.json** wird im Format `<storageaccountname>_AzureStorageConnectionString` benannt. 

1. Öffnen Sie die Datei `BlobsController.cs` .

1. Fügen Sie eine Methode mit dem Namen **GetCloudBlobContainer** hinzu, die ein **CloudBlobContainer**-Element zurückgibt. Ersetzen Sie `<storageaccountname>_AzureStorageConnectionString` durch den tatsächlichen Namen des Schlüssels in **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> *test-blob-container* ist zwar noch nicht vorhanden, trotzdem wird mit diesem Code ein Verweis darauf erstellt. Dies dient dazu, dass der Container mit der im nächsten Schritt dargestellten Methode `CreateIfNotExists` erstellt werden kann.

## <a name="create-a-blob-container"></a>Erstellen eines Blobcontainers

Die folgenden Schritte veranschaulichen, wie Sie einen Blobcontainer erstellen:

1. Fügen Sie eine Methode namens `CreateBlobContainer` hinzu, die `ActionResult` zurückgibt.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie ein `CloudBlobContainer`-Objekt ab, das einen Verweis auf den gewünschten Blobcontainernamen darstellt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Rufen Sie die `CloudBlobContainer.CreateIfNotExists`-Methode auf, um den Container zu erstellen, wenn er noch nicht vorhanden ist. Die `CloudBlobContainer.CreateIfNotExists`-Methode gibt **true** zurück, wenn der Container nicht vorhanden war und erfolgreich erstellt wurde. Andernfalls gibt die Methode **false** zurück.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aktualisieren Sie `ViewBag` mit dem Namen des Blobcontainers.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Der folgende Code zeigt die abgeschlossene `CreateBlobContainer`-Methode:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Ansichten**.

2. Wählen Sie im Kontextmenü **Hinzufügen** > **Neuer Ordner** aus. Nennen Sie den neuen Ordner *Blobs*. 

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, und klicken Sie mit der rechten Maustaste auf **Blobs**.

4. Wählen Sie im Kontextmenü **Hinzufügen** > **Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **CreateBlobContainer** ein, und wählen Sie **Hinzufügen** aus.

1. Öffnen Sie `CreateBlobContainer.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**, und öffnen Sie `_Layout.cshtml`.

1. Suchen Sie nach der unsortierten Liste, die wie folgt aussieht: `<ul class="nav navbar-nav">`.  Fügen Sie nach dem letzten `<li>`-Element in der Liste den folgenden HTML-Code ein, um ein weiteres Navigationsmenüelement hinzuzufügen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Blobcontainer erstellen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Screenshot vom Erstellen eines Blobcontainers](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Wie bereits erwähnt, gibt die `CloudBlobContainer.CreateIfNotExists`-Methode nur dann **true** zurück, wenn der Container nicht vorhanden ist und erstellt wird. Aus diesem Grund gibt die Methode **false** zurück, wenn die App bei vorhandenem Container ausgeführt wird.

## <a name="upload-a-blob-into-a-blob-container"></a>Hochladen eines Blobs in einen Blobcontainer

Wenn der [Blobcontainer erstellt wurde](#create-a-blob-container), können Sie Dateien in diesen Container hochladen. Dieser Abschnitt führt Sie durch die Schritte zum Hochladen einer lokalen Datei in einen Blobcontainer. Die Schritte setzen voraus, dass ein Blobcontainer namens *test-blob-container* vorhanden ist. 

1. Öffnen Sie die Datei `BlobsController.cs` .

1. Fügen Sie eine Methode namens `UploadBlob` hinzu, die eine Zeichenfolge zurückgibt.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Rufen Sie in der `UploadBlob`-Methode ein `CloudBlobContainer`-Objekt ab, das einen Verweis auf den gewünschten Blobcontainernamen darstellt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure-Speicher unterstützt verschiedene Blobtypen. In diesem Tutorial werden Block-BLOBs verwendet. Rufen Sie die `CloudBlobContainer.GetBlockBlobReference`-Methode auf, um einen Verweis auf einen Blockblob abzurufen.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Der Blobname ist Teil der URL, die zum Abrufen eines Blobs verwendet wird. Er kann eine beliebige Zeichenfolge (einschließlich des Dateinamens) sein.

1. Sobald ein Blobverweis vorhanden ist, können Sie jeden Datenstrom in diesen hochladen, indem Sie die `UploadFromStream`-Methode des Blobverweisobjekts aufrufen. Die `UploadFromStream`-Methode erstellt das Blob, falls es nicht vorhanden ist, oder überschreibt es, falls es vorhanden ist. (Ändern Sie *&lt;file-to-upload>* in einen vollqualifizierten Pfad zu einer Datei, die hochgeladen werden soll.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Das folgende Beispiel zeigt die abgeschlossene `UploadBlob`-Methode (mit einem vollqualifizierten Pfad für die hochzuladende Datei):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten `<li>`-Element in der Liste den folgenden HTML-Code ein, um ein weiteres Navigationsmenüelement hinzuzufügen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Blob hochladen**. Das Wort *success!* sollte angezeigt werden.
    
    ![Screenshot der Erfolgsüberprüfung](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Auflisten von Blobs in einem Blobcontainer

In diesem Abschnitt wird das Auflisten von Blobs in einem Blobcontainer veranschaulicht. Der Beispielcode verweist auf den *test-blob-container*, der im Abschnitt [Erstellen eines Blobcontainers](#create-a-blob-container) erstellt wurde.

1. Öffnen Sie die Datei `BlobsController.cs` .

1. Fügen Sie eine Methode namens `ListBlobs` hinzu, die `ActionResult` zurückgibt.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Rufen Sie in der `ListBlobs`-Methode ein `CloudBlobContainer`-Objekt ab, das einen Verweis auf den Blobcontainer darstellt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Verwenden Sie zum Auflisten der Blobs in einem Blobcontainer die Methode `CloudBlobContainer.ListBlobsSegmentedAsync`. Die `CloudBlobContainer.ListBlobsSegmentedAsync` Methode gibt ein `BlobResultSegment` zurück. Dieses enthält `IListBlobItem`-Objekte, die in `CloudBlockBlob`-, `CloudPageBlob`- oder `CloudBlobDirectory`-Objekte umgewandelt werden können. Der folgende Codeausschnitt listet alle Blobs im Blobcontainer auf. Jedes Blob wird auf Grundlage seines Typs in das entsprechende Objekt umgewandelt. Sein Name (oder URI im Fall eines `CloudBlobDirectory`) wird einer Liste hinzugefügt.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    Der folgende Code zeigt die abgeschlossene `ListBlobs`-Methode:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, und klicken Sie mit der rechten Maustaste auf **Blobs**.

2. Wählen Sie im Kontextmenü **Hinzufügen** > **Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen `ListBlobs` ein, und wählen Sie **Hinzufügen** aus.

1. Öffnen Sie `ListBlobs.cshtml`, und ersetzen Sie den Inhalt durch den folgenden Code:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten `<li>`-Element in der Liste den folgenden HTML-Code ein, um ein weiteres Navigationsmenüelement hinzuzufügen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Blobs auflisten** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Screenshot der Auflistung von Blobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Herunterladen von Blobs

Dieser Abschnitt veranschaulicht das Herunterladen eines Blobs. Sie können es entweder dauerhaft im lokalen Speicher aufbewahren oder seine Inhalte in eine Zeichenfolge auslesen. Der Beispielcode verweist auf den *test-blob-container*, der im Abschnitt [Erstellen eines Blobcontainers](#create-a-blob-container) erstellt wurde.

1. Öffnen Sie die Datei `BlobsController.cs` .

1. Fügen Sie eine Methode namens `DownloadBlob` hinzu, die eine Zeichenfolge zurückgibt.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Rufen Sie in der `DownloadBlob`-Methode ein `CloudBlobContainer`-Objekt ab, das einen Verweis auf den Blobcontainer darstellt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Rufen Sie ein Blobverweisobjekt durch Aufrufen der `CloudBlobContainer.GetBlockBlobReference`-Methode ab. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Verwenden Sie zum Herunterladen eines Blobs die `CloudBlockBlob.DownloadToStream`-Methode. Der folgende Code überträgt die Inhalte eines Blobs in ein Datenstromobjekt. Dieses Objekt wird dann dauerhaft in einer lokalen Datei gespeichert. (Ändern Sie *&lt;local-file-name>* in den vollqualifizierten Dateinamen, der angibt, wohin der Blob heruntergeladen werden soll.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Das folgende Beispiel zeigt die abgeschlossene `ListBlobs`-Methode (mit einem vollqualifizierten Pfad für die lokale Datei, die erstellt wird):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten `<li>`-Element in der Liste den folgenden HTML-Code ein, um ein weiteres Navigationsmenüelement hinzuzufügen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Blob herunterladen** zum Herunterladen des Blobs. Das im `CloudBlobContainer.GetBlockBlobReference`-Methodenaufruf angegebene Blob wird an den Speicherort heruntergeladen, den Sie im Aufruf der `File.OpenWrite`-Methode angegeben haben. Der Text *success!* sollte im Browser angezeigt werden. 

## <a name="delete-blobs"></a>Löschen von Blobs

Die folgenden Schritte veranschaulichen, wie Sie ein Blob löschen:

1. Öffnen Sie die Datei `BlobsController.cs` .

1. Fügen Sie eine Methode namens `DeleteBlob` hinzu, die eine Zeichenfolge zurückgibt.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Rufen Sie in der `DeleteBlob`-Methode ein `CloudBlobContainer`-Objekt ab, das einen Verweis auf den Blobcontainer darstellt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Rufen Sie ein Blobverweisobjekt durch Aufrufen der `CloudBlobContainer.GetBlockBlobReference`-Methode ab. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Verwenden Sie zum Löschen eines Blobs die Methode `Delete`.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Die abgeschlossene `DeleteBlob`-Methode sollte wie folgt aussehen:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** > **Freigegeben**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten `<li>`-Element in der Liste den folgenden HTML-Code ein, um ein weiteres Navigationsmenüelement hinzuzufügen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Blob löschen** zum Löschen des Blobs, das Sie im Aufruf der `CloudBlobContainer.GetBlockBlobReference`-Methode angegeben haben. Der Text *success!* sollte im Browser angezeigt werden. Wählen Sie im Browser die Schaltfläche **Zurück** aus, und klicken Sie dann auf **Blobs auflisten**, um zu bestätigen, dass sich das Blob nicht mehr im Container befindet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Blobs in Azure Storage mithilfe von ASP.NET Core speichern, auflisten und abrufen. Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.

  * [Erste Schritte mit Tabellenspeicher und verbundenen Visual Studio-Diensten (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Erste Schritte mit Azure-Warteschlangenspeicher und verbundenen Visual Studio-Diensten](vs-storage-aspnet-getting-started-queues.md)
