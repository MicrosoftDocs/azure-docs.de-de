---
title: 'Tutorial: Generieren von Metadaten für Azure-Images'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird beschrieben, wie Sie den Azure-Dienst „Maschinelles Sehen“ in eine Web-App integrieren, um Metadaten für Bilder zu generieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b51234ff121071de27ec8c91425f4a769c8d833e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008785"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Verwenden von „Maschinelles Sehen“ zum Generieren von Bildmetadaten in Azure Storage

In diesem Tutorial wird beschrieben, wie Sie den Azure-Dienst „Maschinelles Sehen“ in eine Web-App integrieren, um Metadaten für hochgeladene Bilder zu generieren. Dies ist für [Digital Asset Management](../overview.md#computer-vision-for-digital-asset-management)-Szenarien (DAM) nützlich, wenn ein Unternehmen z. B. beschreibende Titel oder durchsuchbare Schlüsselwörter für alle seine Bilder schnell generieren möchte.

Einen vollständigen App-Leitfaden finden Sie unter [Azure Storage and Cognitive Services Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) (Lab für Azure Storage und Cognitive Services) auf GitHub. Dieses Tutorial deckt im Wesentlichen Übung 5 des Labs ab. Es kann sein, dass Sie die vollständige Anwendung erstellen möchten, indem Sie jeden einzelnen Schritt ausführen. Falls Sie aber nur wissen möchten, wie Maschinelles Sehen in eine vorhandene Web-App integriert werden kann, sollten Sie hier weiterlesen.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Erstellen einer Maschinelles Sehen-Ressource in Azure
> * Durchführen einer Bildanalyse für Azure Storage-Bilder
> * Anfügen von Metadaten an Azure Storage-Bilder
> * Überprüfen von Bildmetadaten mit Azure Storage-Explorer

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) oder höher mit Installation der Workloads „ASP.NET und Webentwicklung“ und „Azure-Entwicklung“.
- Ein Azure Storage-Konto mit einem für die Bildspeicherung eingerichteten Blobcontainer. (Falls Sie Hilfe bei diesem Schritt benötigen, helfen Ihnen die Informationen unter [Übung 1 des Azure Storage-Labs](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) weiter.)
- Azure Storage-Explorer-Tool (Falls Sie Hilfe zu diesem Schritt benötigen, helfen Ihnen die Informationen unter [Exercise 2 of the Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) (Übung 2 des Azure Storage-Labs) weiter.)
- Eine ASP.NET-Webanwendung mit Zugriff auf Azure Storage (Unter [Exercise 3 of the Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) (Übung 3 des Azure Storage-Labs) ist beschrieben, wie Sie diese App schnell erstellen können.)

## <a name="create-a-computer-vision-resource"></a>Erstellen einer Maschinelles Sehen-Ressource

Sie müssen für Ihr Azure-Konto eine Maschinelles Sehen-Ressource erstellen. Mit dieser Ressource wird der Zugriff auf den Azure-Dienst „Maschinelles Sehen“ verwaltet. 

1. Befolgen Sie die Anweisungen in [Erstellen einer Azure Cognitive Services-Ressource](../../cognitive-services-apis-create-account.md), um eine Ressource für maschinelles Sehen zu erstellen.

1. Kehren Sie dann zum Menü für Ihre Ressourcengruppe zurück, und klicken Sie auf das Abonnement der Maschinelles Sehen-API, das Sie gerade erstellt haben. Kopieren Sie die URL unter **Endpunkt** an einen Ort, an dem Sie gleich schnell darauf zugreifen können. Klicken Sie anschließend auf **Zugriffsschlüssel anzeigen**.

    ![Azure-Portalseite mit Hervorhebung der Endpunkt-URL und des Links für Zugriffsschlüssel](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Kopieren Sie im nächsten Fenster den Wert von **SCHLÜSSEL 1** in die Zwischenablage.

    ![Dialogfeld „Schlüssel verwalten“ mit Hervorhebung der Schaltfläche „Kopieren“](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Hinzufügen von Anmeldeinformationen für Maschinelles Sehen

Als Nächstes fügen Sie Ihrer App die erforderlichen Anmeldeinformationen hinzu, damit der Zugriff auf Maschinelles Sehen-Ressourcen möglich ist.

Öffnen Sie Ihre ASP.NET-Webanwendung in Visual Studio, und navigieren Sie zur Datei **Web.config** im Stamm des Projekts. Fügen Sie dem Abschnitt `<appSettings>` der Datei die folgenden Anweisungen hinzu, und ersetzen Sie dabei `VISION_KEY` durch den Schlüssel, den Sie im vorherigen Schritt kopiert haben, und `VISION_ENDPOINT` durch die URL, die Sie im Schritt davor gespeichert haben.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Klicken Sie im Projektmappen-Explorer dann mit der rechten Maustaste auf das Projekt, und verwenden Sie den Befehl **NuGet-Pakete verwalten**, um das Paket **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** zu installieren. Dieses Paket enthält die Typen, die zum Aufrufen der Maschinelles Sehen-API benötigt werden.

## <a name="add-metadata-generation-code"></a>Hinzufügen des Codes zum Generieren von Metadaten

Als Nächstes fügen Sie den Code hinzu, der den Dienst „Maschinelles Sehen“ nutzt, um Metadaten für Bilder zu erstellen. Diese Schritte gelten für die ASP.NET-App im Lab, aber Sie können sie auch an Ihre eigene App anpassen. An diesem Punkt ist es wichtig, dass Sie über eine ASP.NET-Webanwendung verfügen, mit der Bilder in einen Azure Storage-Container hochgeladen und Bilder daraus gelesen und dann in der Ansicht angezeigt werden können. Falls Sie nicht sicher sind, wie Sie vorgehen sollen, hilft Ihnen [Übung 3 des Azure Storage-Labs](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) weiter. 

1. Öffnen Sie die Datei *HomeController.cs* im Ordner **Controllers** des Projekts, und fügen Sie oben in der Datei die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Fahren Sie dann mit der **Upload**-Methode fort. Hierbei werden Bilder konvertiert und in den Blobspeicher hochgeladen. Fügen Sie den folgenden Code direkt nach dem Block hinzu, der mit `// Generate a thumbnail` beginnt (oder am Ende Ihres Prozesses für die Erstellung von Bildblobs). In diesem Code wird für das Blob, in dem das Bild (`photo`) enthalten ist, Maschinelles Sehen verwendet, um eine Beschreibung für das Bild zu generieren. Mit der Maschinelles Sehen-API wird auch eine Liste mit Schlüsselwörtern generiert, die auf das Bild angewendet werden. Die generierte Beschreibung und die Schlüsselwörter werden in den Metadaten des Blobs gespeichert, damit sie später abgerufen werden können.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Navigieren Sie als Nächstes in derselben Datei zur **Index**-Methode. Mit dieser Methode werden die gespeicherten Bildblobs im betreffenden Blobcontainer enumeriert (als **IListBlobItem**-Instanzen) und an die Anwendungsansicht übergeben. Ersetzen Sie den `foreach`-Block in dieser Methode durch den folgenden Code. Mit diesem Code wird **CloudBlockBlob.FetchAttributes** aufgerufen, um die angefügten Metadaten für jedes Blob abzurufen. Die per Computer generierte Beschreibung (`caption`) wird aus den Metadaten extrahiert und dem **BlobInfo**-Objekt hinzugefügt, das dann an die Ansicht übergeben wird.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testen der App

Speichern Sie Ihre Änderungen in Visual Studio, und drücken Sie **STRG+F5**, um die Anwendung in Ihrem Browser zu starten. Verwenden Sie die App zum Hochladen einiger Bilder – entweder aus dem Ordner „photos“ in den Ressourcen des Labs oder aus Ihrem eigenen Ordner. Wenn Sie den Mauszeiger in der Ansicht auf eines der Bilder bewegen, sollte ein QuickInfo-Fenster mit dem per Computer generierten Beschriftungstext für das Bild angezeigt werden.

![Per Computer generierter Beschriftungstext](../Images/thumbnail-with-tooltip.png)

Verwenden Sie zum Anzeigen aller angefügten Metadaten den Azure Storage-Explorer, um den Speichercontainer einzublenden, den Sie für Bilder nutzen. Klicken Sie mit der rechten Maustaste auf die Blobs im Container, und wählen Sie **Eigenschaften**. Im Dialogfeld wird eine Liste mit Schlüssel-Wert-Paaren angezeigt. Die per Computer generierte Bildbeschreibung wird unter dem Element „Caption“ (Beschriftung) gespeichert, und die Schlüsselwörter für die Suche unter „Tag0“, „Tag1“ usw. Klicken Sie auf **Abbrechen**, wenn Sie fertig sind, um das Dialogfeld zu schließen.

![Dialogfenster mit Image-Eigenschaften und aufgeführten Metadatentags](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit Ihrer Web-App weiterarbeiten möchten, helfen Ihnen die Informationen im Abschnitt [Nächste Schritte](#next-steps) weiter. Wenn Sie nicht planen, diese Anwendung weiter zu nutzen, sollten Sie alle App-spezifischen Ressourcen löschen. Zum Löschen von Ressourcen können Sie die Ressourcengruppe löschen, die Ihr Azure Storage-Abonnement und die Maschinelles Sehen-Ressource enthält. Das Speicherkonto, die dafür hochgeladenen Blobs und die App Service-Ressource, die zum Herstellen der Verbindung mit der ASP.NET-Web-App benötigt werden, werden entfernt. 

Öffnen Sie zum Löschen der Ressourcengruppe im Portal die Registerkarte **Ressourcengruppe**, navigieren Sie zu der Ressourcengruppe, die Sie für dieses Projekt verwendet haben, und klicken Sie oben in der Ansicht auf **Ressourcengruppe löschen**. Sie werden aufgefordert, den Namen der Ressourcengruppe einzugeben, um zu bestätigen, dass Sie den Löschvorgang durchführen möchten. Der Grund ist, dass eine Ressourcengruppe nach dem Löschen nicht wiederhergestellt werden kann.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie den Azure-Dienst „Maschinelles Sehen“ in einer vorhandenen Web-App eingerichtet, um beim Hochladen automatisch Beschriftungstext und Schlüsselwörter für Blobbilder zu generieren. Fahren Sie mit Übung 6 des Azure Storage-Labs fort, um zu erfahren, wie Sie Ihrer Web-App Suchfunktionalität hinzufügen. Hierbei werden die Schlüsselwörter für die Suche verwendet, die vom Dienst Maschinelles Sehen generiert wurden.

> [!div class="nextstepaction"]
> [Add search to your app](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6) (Hinzufügen der Suchfunktion zu Ihrer App)
