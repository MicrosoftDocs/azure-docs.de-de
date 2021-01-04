---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: bb090c0e40a8376aafb7b7eca479d16d73ce131d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021333"
---
Hier erhalten Sie Informationen zu den ersten Schritten mit der Custom Vision-Clientbibliothek für .NET. Führen Sie diese Schritte aus, um das Paket zu installieren und den Beispielcode zum Erstellen eines Objekterkennungsmodells auszuprobieren. Sie erstellen ein Projekt, fügen Tags hinzu, trainieren das Projekt anhand von Beispielbildern und verwenden die Vorhersageendpunkt-URL des Projekts, um es programmgesteuert zu testen. Verwenden Sie dieses Beispiel als Vorlage für die Erstellung Ihrer eigenen Bilderkennungsanwendung.

> [!NOTE]
> Falls Sie ein Objekterkennungsmodell erstellen und trainieren möchten, _ohne_ Code zu schreiben, sehen Sie sich stattdessen die [browserbasierte Anleitung](../../get-started-build-detector.md) an.

Verwenden Sie die Custom Vision-Clientbibliothek für .NET für folgende Aufgaben:

* Erstellen eines neuen Custom Vision-Projekts
* Hinzufügen von Tags zum Projekt
* Hochladen und Kennzeichnen von Bildern
* Trainieren des Projekts
* Veröffentlichen der aktuellen Iteration
* Testen des Vorhersageendpunkts

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet) | Quellcode der Bibliothek [(Training)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(Vorhersage)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Paket (NuGet) [(Training)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(Vorhersage)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [Beispiele](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Erstellen einer Custom Vision-Ressource"  target="_blank">Custom Vision-Ressource erstellen<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um eine Trainings- und Vorhersageressource erstellen und Ihre Schlüssel und den Endpunkt abrufen zu können. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressourcen, um Ihre Anwendung mit Custom Vision zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Erstellen Sie mit Visual Studio eine neue .NET Core-Anwendung. 

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie nach der Erstellung eines neuen Projekts die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und suchen Sie nach `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` und `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`. Wählen Sie die neueste Version und dann die Option **Installieren** aus. 

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `custom-vision-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Custom Vision-Clientbibliothek für .NET:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Öffnen Sie über das Projektverzeichnis die Datei *program.cs*, und fügen Sie die folgenden `using`-Direktiven hinzu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

Erstellen Sie in der **Main**-Methode der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellten Custom Vision-Ressourcen erfolgreich bereitgestellt wurden, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre **Schlüssel und den Endpunkt** finden Sie auf den entsprechenden Seiten der Ressourcen unter **Ressourcenverwaltung**. Sie benötigen sowohl Ihren Trainings- als auch Ihren Vorhersageschlüssel.
>
> Denken Sie daran, die Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und machen Sie sie niemals öffentlich zugänglich. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Fügen Sie in der **Main**-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Sie führen die Implementierung hierfür zu einem späteren Zeitpunkt durch.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektmodell

|name|Beschreibung|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-dotnet) | Diese Klasse wird für die Erstellung, das Training und die Veröffentlichung Ihrer Modelle verwendet. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)| Diese Klasse wird zum Abfragen Ihrer Modelle für Vorhersagen zur Objekterkennung verwendet.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet-preview)| Mit dieser Klasse wird eine einzelne Objektvorhersage für ein einzelnes Bild definiert. Sie enthält Eigenschaften für die Objekt-ID und den Namen, die Position des Begrenzungsrahmens für das Objekt und eine Zuverlässigkeitsbewertung.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Custom Vision-Clientbibliothek für .NET durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erstellen eines neuen Custom Vision-Projekts](#create-a-new-custom-vision-project)
* [Hinzufügen von Tags zum Projekt](#add-tags-to-the-project)
* [Hochladen und Kennzeichnen von Bildern](#upload-and-tag-images)
* [Trainieren des Projekts](#train-the-project)
* [Veröffentlichen der aktuellen Iteration](#publish-the-current-iteration)
* [Testen des Vorhersageendpunkts](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie in einer neuen Methode Trainings- und Vorhersageclients, indem Sie Ihren Endpunkt und die Schlüssel verwenden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Erstellen eines neuen Custom Vision-Projekts

Die nächste Methode erstellt ein Objekterkennungsprojekt. Das erstellte Projekt wird auf der [Custom Vision-Website](https://customvision.ai/) angezeigt. Informationen zur Angabe weiterer Optionen bei der Erstellung Ihres Projekts finden Sie im Artikel zur Methode [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true). Informationen zur Projekterstellung finden Sie unter [Schnellstart: Informationen zum Erstellen einer Objekterkennung mit Custom Vision](../../get-started-build-detector.md).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Hinzufügen von Tags zum Projekt

Mit dieser Methode werden die Tags definiert, die Sie zum Trainieren des Modells verwenden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Laden Sie zunächst die Beispielbilder für dieses Projekt herunter. Speichern Sie den Inhalt des [Ordners mit den Beispielbildern](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) auf Ihrem lokalen Gerät.

Wenn Sie Bilder in Objekterkennungsprojekten mit Tags versehen, müssen Sie mithilfe normalisierter Koordinaten die Region des jeweiligen markierten Objekts angeben. Im folgenden Code wird jedem der Beispielbilder die entsprechende markierte Region zugeordnet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Falls Sie für Ihre eigenen Projekte nicht über ein Hilfsprogramm zum Klicken und Ziehen verfügen, um die Koordinaten von Regionen zu markieren, können Sie die Webbenutzeroberfläche auf der [Custom Vision-Website](https://www.customvision.ai/) verwenden. In diesem Beispiel sind die Koordinaten bereits vorhanden.

Auf der Grundlage dieser Zuordnungen werden dann die einzelnen Bilder zusammen mit den jeweiligen Regionskoordinaten hochgeladen. Sie können bis zu 64 Bilder in einem Batch hochladen. Unter Umständen müssen Sie den Wert `imagePath` so ändern, dass er auf die richtigen Ordnerspeicherorte verweist.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Sie haben nun alle Beispielbilder hochgeladen und jedes Bild mit einem Tag für Gabel (**fork**) oder Schere (**scissors**) sowie einem zugeordneten Pixelrechteck versehen.

## <a name="train-the-project"></a>Trainieren des Projekts

Mit dieser Methode wird die erste Trainingsiteration im Projekt erstellt. Der Dienst wird so lange abgefragt, bis das Training abgeschlossen ist.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Trainieren mit ausgewählten Tags
>
> Optional können Sie das Training nur mit einer Teilmenge der angewendeten Tags durchführen. Das ist empfehlenswert, wenn bestimmte Tags noch nicht ausreichend angewendet wurden, andere jedoch schon. Verwenden Sie den Parameter *trainingParameters* im [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true)-Aufruf. Erstellen Sie eine [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?preserve-view=true&view=azure-dotnet)-Klasse, und legen Sie deren Eigenschaft **SelectedTags** auf eine Liste der zu verwendenden Tag-IDs fest. Das Modell wird darauf trainiert, nur die Tags in dieser Liste zu erkennen.

## <a name="publish-the-current-iteration"></a>Veröffentlichen der aktuellen Iteration

Mit dieser Methode wird die aktuelle Iteration des Modells zum Abfragen verfügbar gemacht. Sie können den Modellnamen als Referenz zum Senden von Vorhersageanforderungen verwenden. Hierbei ist es erforderlich, dass Sie Ihren eigenen Wert für `predictionResourceId` eingeben. Sie finden die Vorhersageressourcen-ID im Azure-Portal auf der Registerkarte **Übersicht** der Ressource, wo sie als **Abonnement-ID** angegeben ist.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testen des Vorhersageendpunkts

Diese Methode lädt das Testbild, fragt den Modellendpunkt ab und gibt Vorhersagedaten an die Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Ausführen der Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Führen Sie die Anwendung aus, indem Sie oben im IDE-Fenster auf die Schaltfläche **Debuggen** klicken.

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

---

Während der Anwendungsausführung sollte ein Konsolenfenster mit folgender Ausgabe geöffnet werden:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Daraufhin können Sie sich vergewissern, dass das Testbild (unter **Images/Test/** ) ordnungsgemäß gekennzeichnet und die Erkennungsregion korrekt ist. Drücken Sie eine beliebige Taste, um die Anwendung zu beenden.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt die einzelnen Schritte des Objekterkennungsprozesses im Code ausgeführt. In diesem Beispiel wird eine einzelne Trainingsiteration ausgeführt. Zur Verbesserung der Genauigkeit muss ein Modell jedoch häufig mehrmals trainiert und getestet werden. In der folgenden Anleitung wird die Bildklassifizierung behandelt. Die Prinzipien sind jedoch mit denen der Objekterkennung vergleichbar.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](../../test-your-model.md)

* [Was ist Custom Vision?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs).
* [SDK-Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)
