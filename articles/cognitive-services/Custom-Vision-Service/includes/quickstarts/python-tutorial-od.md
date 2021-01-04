---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: b35b383eb93b0c451fcb98c66538a4335480a649
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021376"
---
Hier erhalten Sie Informationen zu den ersten Schritten mit der Custom Vision-Clientbibliothek für Python. Führen Sie diese Schritte aus, um das Paket zu installieren und den Beispielcode zum Erstellen eines Objekterkennungsmodells auszuprobieren. Sie erstellen ein Projekt, fügen Tags hinzu, trainieren das Projekt und verwenden die Vorhersageendpunkt-URL des Projekts, um es programmgesteuert zu testen. Verwenden Sie dieses Beispiel als Vorlage für die Erstellung Ihrer eigenen Bilderkennungsanwendung.

> [!NOTE]
> Falls Sie ein Objekterkennungsmodell erstellen und trainieren möchten, _ohne_ Code zu schreiben, sehen Sie sich stattdessen die [browserbasierte Anleitung](../../get-started-build-detector.md) an.

Verwenden Sie die Custom Vision-Clientbibliothek für Python für folgende Aufgaben:

* Erstellen eines neuen Custom Vision-Projekts
* Hinzufügen von Tags zum Projekt
* Hochladen und Kennzeichnen von Bildern
* Trainieren des Projekts
* Veröffentlichen der aktuellen Iteration
* Testen des Vorhersageendpunkts

[Referenzdokumentation](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [Paket (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [Beispiele](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Erstellen einer Custom Vision-Ressource"  target="_blank">Custom Vision-Ressource erstellen<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um eine Trainings- und Vorhersageressource erstellen und Ihre Schlüssel und den Endpunkt abrufen zu können. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressourcen, um Ihre Anwendung mit Custom Vision zu verbinden. Die Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Zum Schreiben einer Bildanalyseanwendung mit Custom Vision für Python benötigen Sie die Clientbibliothek für Custom Vision. Führen Sie nach der Installation von Python den folgenden Befehl in PowerShell oder in einem Konsolenfenster aus:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Datei, und importieren Sie die folgenden Bibliotheken:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für den Azure-Endpunkt und die Abonnementschlüssel Ihrer Ressource.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellten Custom Vision-Ressourcen erfolgreich bereitgestellt wurden, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre **Schlüssel und den Endpunkt** finden Sie auf den entsprechenden Seiten der Ressourcen unter **Ressourcenverwaltung**. Sie benötigen sowohl Ihren Trainings- als auch Ihren Vorhersageschlüssel.
>
> Sie finden den Wert der Vorhersageressourcen-ID auf der Registerkarte **Übersicht** der Ressource, wo sie als **Abonnement-ID** angegeben ist.
>
> Denken Sie daran, die Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und machen Sie sie niemals öffentlich zugänglich. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../../cognitive-services/cognitive-services-security.md).

## <a name="object-model"></a>Objektmodell

|name|Beschreibung|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Diese Klasse wird für die Erstellung, das Training und die Veröffentlichung Ihrer Modelle verwendet. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Diese Klasse wird zum Abfragen Ihrer Modelle für Vorhersagen zur Objekterkennung verwendet.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Mit dieser Klasse wird eine einzelne Objektvorhersage für ein einzelnes Bild definiert. Sie enthält Eigenschaften für die Objekt-ID und den Namen, die Position des Begrenzungsrahmens für das Objekt und eine Zuverlässigkeitsbewertung.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Custom Vision-Clientbibliothek für Python durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erstellen eines neuen Custom Vision-Projekts](#create-a-new-custom-vision-project)
* [Hinzufügen von Tags zum Projekt](#add-tags-to-the-project)
* [Hochladen und Kennzeichnen von Bildern](#upload-and-tag-images)
* [Trainieren des Projekts](#train-the-project)
* [Veröffentlichen der aktuellen Iteration](#publish-the-current-iteration)
* [Testen des Vorhersageendpunkts](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie Trainings- und Vorhersageclients mit dem Endpunkt und den Schlüsseln. Erstellen Sie **ApiKeyServiceClientCredentials**-Objekte mit Ihren Schlüsseln, und verwenden Sie sie mit Ihrem Endpunkt, um ein [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python)- und ein [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)-Objekt zu erstellen.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Erstellen eines neuen Custom Vision-Projekts

Fügen Sie Ihrem Skript den folgenden Code hinzu, um ein neues Custom Vision Service-Projekt zu erstellen. 

Informationen zur Angabe weiterer Optionen bei der Erstellung Ihres Projekts finden Sie im Artikel zur Methode [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true). Informationen zur Projekterstellung finden Sie unter [Schnellstart: Informationen zum Erstellen einer Objekterkennung mit Custom Vision](../../get-started-build-detector.md).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Hinzufügen von Tags zum Projekt

Fügen Sie zum Erstellen von Objekttags in Ihrem Projekt den folgenden Code hinzu:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Laden Sie zunächst die Beispielbilder für dieses Projekt herunter. Speichern Sie den Inhalt des [Ordners mit den Beispielbildern](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) auf Ihrem lokalen Gerät.

Wenn Sie Bilder in Objekterkennungsprojekten mit Tags versehen, müssen Sie mithilfe normalisierter Koordinaten die Region des jeweiligen markierten Objekts angeben. Im folgenden Code wird jedem der Beispielbilder die entsprechende markierte Region zugeordnet. Die Regionen geben den Begrenzungsrahmen in normalisierten Koordinaten an, und die Koordinaten werden in der folgenden Reihenfolge angegeben: links, oben, Breite, Höhe.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Falls Sie über kein Hilfsprogramm zum Klicken und Ziehen verfügen, um die Koordinaten von Regionen zu markieren, können Sie die Webbenutzeroberfläche unter [Customvision.ai](https://www.customvision.ai/) verwenden. In diesem Beispiel sind die Koordinaten bereits vorhanden.

Laden Sie dann auf der Grundlage dieser Zuordnungen die einzelnen Bilder zusammen mit den jeweiligen Regionskoordinaten hoch. (Sie können bis zu 64 Bilder in einem Batch hochladen.) Fügen Sie den folgenden Code hinzu.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Sie müssen den Pfad zu den Bildern gemäß dem Ort ändern, an den Sie das Repository mit den Cognitive Services-Beispielen für das Python SDK heruntergeladen haben.

## <a name="train-the-project"></a>Trainieren des Projekts

Mit diesem Code wird die erste Iteration des Vorhersagemodells erstellt. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Trainieren mit ausgewählten Tags
>
> Optional können Sie das Training nur mit einer Teilmenge der angewendeten Tags durchführen. Das ist empfehlenswert, wenn bestimmte Tags noch nicht ausreichend angewendet wurden, andere jedoch schon. Legen Sie im Aufruf **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** den optionalen Parameter *selected_tags* auf die Liste der zu verwendenden Tag-IDs fest. Das Modell wird darauf trainiert, nur die Tags in dieser Liste zu erkennen.

## <a name="publish-the-current-iteration"></a>Veröffentlichen der aktuellen Iteration

Eine Iteration ist erst im Vorhersageendpunkt verfügbar, wenn sie veröffentlicht wurde. Mit dem folgenden Code wird die aktuelle Iteration des Modells zum Abfragen verfügbar gemacht: 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testen des Vorhersageendpunkts

Um ein Bild an den Vorhersageendpunkt zu senden und die Vorhersage abzurufen, fügen Sie am Ende der Datei den folgenden Code hinzu:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie *CustomVisionQuickstart.py* aus.

```powershell
python CustomVisionQuickstart.py
```

Die Ausgabe der Anwendung sollte in der Konsole angezeigt werden. Daraufhin können Sie sich vergewissern, dass das Testbild (unter **<base_image_location>/images/Test**) ordnungsgemäß gekennzeichnet und die Erkennungsregion korrekt ist. Sie können auch zur [Custom Vision-Website](https://customvision.ai) zurückgehen und den aktuellen Status Ihres neu erstellten Projekts ansehen.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt die einzelnen Schritte des Objekterkennungsprozesses im Code ausgeführt. In diesem Beispiel wird eine einzelne Trainingsiteration ausgeführt. Zur Verbesserung der Genauigkeit muss ein Modell jedoch häufig mehrmals trainiert und getestet werden. In der folgenden Anleitung wird die Bildklassifizierung behandelt. Die Prinzipien sind jedoch mit denen der Objekterkennung vergleichbar.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](../../test-your-model.md)

* [Was ist Custom Vision?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py).
* [SDK-Referenzdokumentation](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)
