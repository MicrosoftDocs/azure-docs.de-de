---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 02e374e94e3d710ff70a89846c2a077f8dad2343
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105670"
---
Dieser Leitfaden enthält Anweisungen und Beispielcode für die ersten Schritte mit der Custom Vision-Clientbibliothek für Node.js und unterstützt Sie beim Erstellen eines Objekterkennungsmodells. Sie erstellen ein Projekt, fügen Tags hinzu, trainieren das Projekt und verwenden die Vorhersageendpunkt-URL des Projekts, um es programmgesteuert zu testen. Verwenden Sie dieses Beispiel als Vorlage für die Erstellung Ihrer eigenen Bilderkennungsanwendung.

> [!NOTE]
> Falls Sie ein Objekterkennungsmodell erstellen und trainieren möchten, _ohne_ Code zu schreiben, sehen Sie sich stattdessen die [browserbasierte Anleitung](../../get-started-build-detector.md) an.

Verwenden Sie die Custom Vision-Clientbibliothek für .NET für folgende Aufgaben:

* Erstellen eines neuen Custom Vision-Projekts
* Hinzufügen von Tags zum Projekt
* Hochladen und Kennzeichnen von Bildern
* Trainieren des Projekts
* Veröffentlichen der aktuellen Iteration
* Testen des Vorhersageendpunkts

Referenzdokumentation [(Training)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(Vorhersage)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Quellcode der Bibliothek [(Training)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(Vorhersage)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Paket (npm) [(Training)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(Vorhersage)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [Beispiele](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Erstellen einer Custom Vision-Ressource"  target="_blank">Custom Vision-Ressource erstellen<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um eine Trainings- und Vorhersageressource erstellen und Ihre Schlüssel und den Endpunkt abrufen zu können. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressourcen, um Ihre Anwendung mit Custom Vision zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Zum Schreiben einer Bildanalyseanwendung mit Custom Vision für Node.js benötigen Sie die Custom Vision NPM-Pakete. Führen Sie den folgenden Befehl in PowerShell aus, um sie zu installieren:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

Erstellen Sie eine Datei namens `index.js`, und importieren Sie die folgenden Bibliotheken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für Azure-Endpunkt und -Schlüssel Ihrer Ressource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellten Custom Vision-Ressourcen erfolgreich bereitgestellt wurden, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre **Schlüssel und den Endpunkt** finden Sie auf den entsprechenden Seiten der Ressourcen unter **Ressourcenverwaltung**. Sie müssen zusammen mit dem Endpunkt der Trainingsressourcen auch Ihre Trainings- und Vorhersageschlüssel abrufen.
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../../cognitive-services/cognitive-services-security.md).

Fügen Sie außerdem Felder für Ihren Projektnamen und einen Timeoutparameter für asynchrone Aufrufe hinzu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Objektmodell

|name|BESCHREIBUNG|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Diese Klasse wird für die Erstellung, das Training und die Veröffentlichung Ihrer Modelle verwendet. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Diese Klasse wird zum Abfragen Ihrer Modelle für Vorhersagen zur Objekterkennung verwendet.|
|[Vorhersage](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Mit dieser Schnittstelle wird eine einzelne Vorhersage für ein einzelnes Bild definiert. Sie enthält Eigenschaften für die Objekt-ID und den Namen sowie eine Zuverlässigkeitsbewertung.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Custom Vision-Clientbibliothek für JavaScript durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erstellen eines neuen Custom Vision-Projekts](#create-a-new-custom-vision-project)
* [Hinzufügen von Tags zum Projekt](#add-tags-to-the-project)
* [Hochladen und Kennzeichnen von Bildern](#upload-and-tag-images)
* [Trainieren des Projekts](#train-the-project)
* [Veröffentlichen der aktuellen Iteration](#publish-the-current-iteration)
* [Testen des Vorhersageendpunkts](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie Clientobjekte mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein **ApiKeyCredentials**-Objekt mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient)- und ein [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)-Objekt zu erstellen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Hinzufügen einer Hilfsfunktion

Fügen Sie die folgende Funktion hinzu, um mehrere asynchrone Aufrufe ausführen zu können. Sie verwenden sie später.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Erstellen eines neuen Custom Vision-Projekts

Starten Sie eine neue Funktion, die alle Custom Vision-Funktionsaufrufe enthält. Fügen Sie den folgenden Code hinzu, um ein neues Custom Vision Service-Projekt zu erstellen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Laden Sie zunächst die Beispielbilder für dieses Projekt herunter. Speichern Sie den Inhalt des [Ordners mit den Beispielbildern](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) auf Ihrem lokalen Gerät.

> [!NOTE]
> Benötigen Sie für Ihr Training ein größeres Spektrum von Bildern? Mit Trove, einem Microsoft Garage-Projekt, können Sie Bilder zu Trainingszwecken sammeln und erwerben. Die gesammelten Bilder können Sie dann herunterladen und wie gewohnt in Ihr Custom Vision-Projekt importieren. Weitere Informationen finden Sie unter [Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3).

Um dem Projekt die Beispielbilder hinzuzufügen, fügen Sie nach der Erstellung der Kategorien den folgenden Code ein. Dieser Code lädt das Bild mit dem entsprechenden Tag hoch. Wenn Sie Bilder in Objekterkennungsprojekten mit Tags versehen, müssen Sie mithilfe normalisierter Koordinaten die Region des jeweiligen markierten Objekts angeben. Die Bereiche für dieses Tutorial werden inline mit dem Code hartcodiert. Die Regionen geben den Begrenzungsrahmen in normalisierten Koordinaten an, und die Koordinaten werden in der folgenden Reihenfolge angegeben: links, oben, Breite, Höhe. Sie können bis zu 64 Bilder in einem Batch hochladen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Sie müssen den Pfad zu den Bildern (`sampleDataRoot`) gemäß dem Ort ändern, an den Sie das Repository mit den Cognitive Services-Beispielen für das Python SDK heruntergeladen haben.

> [!NOTE]
> Falls Sie über kein Hilfsprogramm zum Klicken und Ziehen verfügen, um die Koordinaten von Regionen zu markieren, können Sie die Webbenutzeroberfläche unter [Customvision.ai](https://www.customvision.ai/) verwenden. In diesem Beispiel sind die Koordinaten bereits vorhanden.


## <a name="train-the-project"></a>Trainieren des Projekts

Mit diesem Code wird die erste Iteration des Vorhersagemodells erstellt. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Veröffentlichen der aktuellen Iteration

Mit diesem Code wird die trainierte Iteration für den Vorhersageendpunkt veröffentlicht. Der Name der veröffentlichten Iteration kann zum Senden von Vorhersageanforderungen verwendet werden. Eine Iteration ist erst im Vorhersageendpunkt verfügbar, wenn sie veröffentlicht wurde.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testen des Vorhersageendpunkts

Um ein Bild an den Vorhersageendpunkt zu senden und die Vorhersage abzurufen, fügen Sie Ihrer Funktion den folgenden Code hinzu: 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Schließen Sie anschließend die Custom Vision-Funktion, und rufen Sie sie auf:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```shell
node index.js
```

Die Ausgabe der Anwendung sollte in der Konsole angezeigt werden. Daraufhin können Sie sich vergewissern, dass das Testbild (unter **<sampleDataRoot>/Test/** ) ordnungsgemäß gekennzeichnet und die Erkennungsregion korrekt ist. Sie können auch zur [Custom Vision-Website](https://customvision.ai) zurückgehen und den aktuellen Status Ihres neu erstellten Projekts ansehen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt die einzelnen Schritte des Objekterkennungsprozesses im Code ausgeführt. In diesem Beispiel wird eine einzelne Trainingsiteration ausgeführt. Zur Verbesserung der Genauigkeit muss ein Modell jedoch häufig mehrmals trainiert und getestet werden. In der folgenden Anleitung wird die Bildklassifizierung behandelt. Die Prinzipien sind jedoch mit denen der Objekterkennung vergleichbar.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](../../test-your-model.md)

* [Was ist Custom Vision?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js).
* [SDK-Referenzdokumentation (Training)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK-Referenzdokumentation (Vorhersage)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
