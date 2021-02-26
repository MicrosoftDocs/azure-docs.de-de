---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 43a903f47cf001cbb220b8d7c18dd8fda161a61f
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105999"
---
Erste Schritte mit der Custom Vision-REST-API. Führen Sie diese Schritte aus, um die API aufzurufen und ein Bildklassifizierungsmodell zu erstellen. Sie erstellen ein Projekt, fügen Tags hinzu, trainieren das Projekt und verwenden die Vorhersageendpunkt-URL des Projekts, um es programmgesteuert zu testen. Verwenden Sie dieses Beispiel als Vorlage für die Erstellung Ihrer eigenen Bilderkennungsanwendung.

> [!NOTE]
> Am einfachsten ist es, für Custom Vision ein Clientbibliothek-SDK oder die [browserbasierte Vorgehensweise](../../get-started-build-detector.md) zu nutzen.

Verwenden Sie die Custom Vision-Clientbibliothek für .NET für folgende Aufgaben:

* Erstellen eines neuen Custom Vision-Projekts
* Hinzufügen von Tags zum Projekt
* Hochladen und Kennzeichnen von Bildern
* Trainieren des Projekts
* Veröffentlichen der aktuellen Iteration
* Testen des Vorhersageendpunkts

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Erstellen einer Custom Vision-Ressource"  target="_blank">Custom Vision-Ressource erstellen<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um eine Trainings- und Vorhersageressource erstellen und Ihre Schlüssel und den Endpunkt abrufen zu können. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressourcen, um Ihre Anwendung mit Custom Vision zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="create-a-new-custom-vision-project"></a>Erstellen eines neuen Custom Vision-Projekts

Sie verwenden einen Befehl der folgenden Art, um ein Bildklassifizierungsprojekt zu erstellen. Das erstellte Projekt wird auf der [Custom Vision-Website](https://customvision.ai/) angezeigt.


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Kopieren Sie den Befehl in einen Text-Editor, und nehmen Sie folgende Änderungen vor:

* Ersetzen Sie `{subscription key}` durch Ihren gültigen Abonnementschlüssel für die Gesichtserkennungs-API.
* Ersetzen Sie `{endpoint}` durch den Endpunkt, der Ihrem Abonnementschlüssel entspricht.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Ersetzen Sie `{name}` durch den Namen Ihres Projekts.
* Optional können Sie auch andere URL-Parameter festlegen, um zu konfigurieren, welche Art von Modell für Ihr Projekt verwendet werden soll. Informationen zu den Optionen finden Sie unter [CreateProject-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae).

Sie erhalten eine JSON-Antwort der folgenden Art: Speichern Sie den `"id"`-Wert Ihres Projekts an einem temporären Speicherort.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Hinzufügen von Tags zum Projekt

Verwenden Sie den folgenden Befehl, um die Tags zu definieren, die Sie zum Trainieren des Modells verwenden möchten.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Fügen Sie auch hier Ihren eigenen Schlüssel und die Endpunkt-URL ein.
* Ersetzen Sie `{projectId}` durch Ihre eigene Projekt-ID.
* Ersetzen Sie `{name}` durch den Namen des Tags, das Sie verwenden möchten.

Wiederholen Sie diesen Vorgang für alle Tags, die in Ihrem Projekt verwendet werden sollen. Fügen Sie die Tags `"Hemlock"` und `"Japanese Cherry"` hinzu, falls Sie die bereitgestellten Beispielbilder nutzen.

Sie erhalten eine JSON-Antwort der folgenden Art: Speichern Sie den `"id"`-Wert jedes Tags an einem temporären Speicherort.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Hochladen und Kennzeichnen von Bildern

Laden Sie als Nächstes die Beispielbilder für dieses Projekt herunter. Speichern Sie den Inhalt des [Ordners mit den Beispielbildern](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) auf Ihrem lokalen Gerät.

> [!NOTE]
> Benötigen Sie für Ihr Training ein größeres Spektrum von Bildern? Mit Trove, einem Microsoft Garage-Projekt, können Sie Bilder zu Trainingszwecken sammeln und erwerben. Die gesammelten Bilder können Sie dann herunterladen und wie gewohnt in Ihr Custom Vision-Projekt importieren. Weitere Informationen finden Sie unter [Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3).

Verwenden Sie den folgenden Befehl, um die Bilder hochzuladen und die Tags anzuwenden: einmal für die Bilder vom Typ „Hemlock“ (Hemlocktanne) und dann separat für die Bilder vom Typ „Japanese Cherry“ (Japanische Zierkirsche). Weitere Optionen finden Sie unter der API zum Erstellen von Bildern aus Daten ([Create Images From Data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5)).

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Fügen Sie auch hier Ihren eigenen Schlüssel und die Endpunkt-URL ein.
* Ersetzen Sie `{projectId}` durch Ihre eigene Projekt-ID.
* Ersetzen Sie `{tagArray}` durch die ID eines Tags.
* Fügen Sie in den Text der Anforderung dann die Binärdaten der Bilder ein, die Sie mit Tags versehen möchten.

## <a name="train-the-project"></a>Trainieren des Projekts

Bei dieser Methode wird das Modell anhand der mit Tags versehenen Bilder trainiert, die Sie hochgeladen haben, und eine ID für die aktuelle Projektiteration zurückgegeben.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Fügen Sie auch hier Ihren eigenen Schlüssel und die Endpunkt-URL ein.
* Ersetzen Sie `{projectId}` durch Ihre eigene Projekt-ID.
* Ersetzen Sie `{tagArray}` durch die ID eines Tags.
* Fügen Sie in den Text der Anforderung dann die Binärdaten der Bilder ein, die Sie mit Tags versehen möchten.
* Optional können Sie auch andere URL-Parameter verwenden. Informationen zu den Optionen finden Sie unter der API für das Trainieren von Projekten ([Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1)).

> [!TIP]
> Trainieren mit ausgewählten Tags
>
> Optional können Sie das Training nur mit einer Teilmenge der angewendeten Tags durchführen. Das ist empfehlenswert, wenn bestimmte Tags noch nicht ausreichend angewendet wurden, andere jedoch schon. Fügen Sie den optionalen JSON-Inhalt dem Text Ihrer Anforderung hinzu. Fügen Sie die IDs der Tags, die Sie verwenden möchten, in das Array `"selectedTags"` ein.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

Die JSON-Antwort enthält Informationen zu Ihrem trainierten Projekt, z. B. die Iterations-ID (`"id"`). Speichern Sie diesen Wert für den nächsten Schritt.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Veröffentlichen der aktuellen Iteration

Mit dieser Methode wird die aktuelle Iteration des Modells zum Abfragen verfügbar gemacht. Sie nutzen den zurückgegebenen Modellnamen als Referenz zum Senden von Vorhersageanforderungen. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Fügen Sie auch hier Ihren eigenen Schlüssel und die Endpunkt-URL ein.
* Ersetzen Sie `{projectId}` durch Ihre eigene Projekt-ID.
* Ersetzen Sie `{iterationId}` durch die ID, die im vorherigen Schritt zurückgegeben wurde.
* Ersetzen Sie `{publishedName}` durch den Namen, den Sie Ihrem Vorhersagemodell zuweisen möchten.
* Ersetzen Sie `{predictionId}` durch Ihre eigene Vorhersageressourcen-ID. Sie finden diese ID im Azure-Portal auf der Registerkarte **Übersicht** der Vorhersageressource. Sie ist als **Abonnement-ID** angegeben.
* Optional können Sie auch andere URL-Parameter verwenden. Weitere Informationen finden Sie unter der API zum Veröffentlichen der Iteration ([Publish Iteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5)).

## <a name="test-the-prediction-endpoint"></a>Testen des Vorhersageendpunkts

Verwenden Sie abschließend diesen Befehl, um Ihr trainiertes Modell zu testen, indem Sie ein neues Bild für die Klassifizierung mit Tags hochladen. Sie können das Bild im Ordner „Test“ der zuvor heruntergeladenen Beispieldateien verwenden.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Fügen Sie auch hier Ihren eigenen Schlüssel und die Endpunkt-URL ein.
* Ersetzen Sie `{projectId}` durch Ihre eigene Projekt-ID.
* Ersetzen Sie `{publishedName}` durch den Namen, den Sie im vorherigen Schritt verwendet haben.
* Fügen Sie dem Anforderungstext die Binärdaten Ihres lokalen Bilds hinzu.
* Optional können Sie auch andere URL-Parameter verwenden. Weitere Informationen finden Sie unter der API zum Klassifizieren des Bilds ([Classify Image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)).

In der zurückgegebenen JSON-Antwort sind die einzelnen Tags, die vom Modell auf das Bild angewendet wurden, und die zugehörigen Wahrscheinlichkeitsergebnisse aufgelistet. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt alle Schritte des Bildklassifizierungsprozesses per REST-API ausgeführt. In diesem Beispiel wird eine einzelne Trainingsiteration ausgeführt. Zur Verbesserung der Genauigkeit muss ein Modell jedoch häufig mehrmals trainiert und getestet werden.

> [!div class="nextstepaction"]
> [Testen und erneutes Trainieren eines Modells mit Custom Vision Service](../../test-your-model.md)

* [Was ist Custom Vision?](../../overview.md)
* [API-Referenzdokumentation (Training)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API-Referenzdokumentation (Vorhersage)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
