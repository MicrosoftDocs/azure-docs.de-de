---
title: Benutzerdefinierter Skill der Formularerkennung (C#)
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie einen benutzerdefinierten Skill der Formularerkennung mit C# und Visual Studio erstellen.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 58f1c2621165a7074c04752832c6560b2fd3e423
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011964"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Beispiel: Erstellen eines benutzerdefinierten Skills der Formularerkennung

In diesem Beispiel für ein Azure Cognitive Search-Skillset lernen Sie, wie Sie mit C# und Visual Studio einen benutzerdefinierten Skill der Formularerkennung erstellen. Die Formularerkennung analysiert Dokumente und extrahiert Schlüssel-Wert-Paare und Tabellendaten. Durch das Umschließen der Formularerkennung durch die [Schnittstelle für benutzerdefinierte Skills](cognitive-search-custom-skill-interface.md) können Sie diese Funktion als Schritt in einer End-to-End-Anreicherungspipeline hinzufügen. Die Pipeline kann dann die Dokumente laden und andere Transformationen durchführen.

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (beliebige Edition)
- Mindestens fünf Formulare des gleichen Typs. Sie können die Beispieldaten verwenden, die mit dieser Anleitung bereitgestellt werden.

## <a name="create-a-form-recognizer-resource"></a>Erstellen einer Formularerkennungsressource

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Trainieren Ihres Modells

Sie müssen ein Formularerkennungsmodell mit Ihren Eingabeformularen trainieren, bevor Sie diesen Skill verwenden können. Befolgen Sie den [cURL-Schnellstart](../cognitive-services/form-recognizer/quickstarts/curl-train-extract.md), um zu erfahren, wie Sie ein Modell trainieren. Sie können die in diesem Schnellstart bereitgestellten Beispielformulare oder Ihre eigenen Daten verwenden. Nachdem das Modell trainiert wurde, kopieren Sie seinen ID-Wert an einen sicheren Speicherort.

## <a name="set-up-the-custom-skill"></a>Einrichten des benutzerdefinierten Skills

In diesem Tutorial wird das Projekt [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) im GitHub-Repository [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) verwendet. Klonen Sie dieses Repository auf Ihrem lokalen Computer, und navigieren Sie zu **Vision/AnalyzeForm/** , um auf das Projekt zuzugreifen. Öffnen Sie dann _AnalyzeForm.csproj_ in Visual Studio. Dieses Projekt erstellt eine Azure Functions-Ressource, die die [Schnittstelle für benutzerdefinierte Skills](cognitive-search-custom-skill-interface.md) erfüllt und für die Azure Cognitive Search-Anreicherung verwendet werden kann. Es nimmt als Eingabe Formulardokumente entgegen und gibt die angegebenen Schlüssel-Wert-Paare (als Text) aus.

Fügen Sie als Erstes Umgebungsvariablen auf Projektebene hinzu. Suchen Sie im linken Bereich das Projekt **AnalyzeForm**, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Eigenschaften** aus. Klicken Sie im Fenster **Eigenschaften** auf die Registerkarte **Debuggen**, und suchen Sie nach dem Feld **Umgebungsvariablen**. Klicken Sie auf **Hinzufügen**, um die folgenden Variablen hinzuzufügen:
* `FORMS_RECOGNIZER_ENDPOINT_URL` mit Ihrer Endpunkt-URL als Wert.
* `FORMS_RECOGNIZER_API_KEY` mit Ihrem Abonnementschlüssel als Wert.
* `FORMS_RECOGNIZER_MODEL_ID` mit der ID des trainierten Modells als Wert.
* `FORMS_RECOGNIZER_RETRY_DELAY` mit dem Wert „1000“. Dieser Wert ist die Zeit in Millisekunden, die das Programm vor dem erneuten Übermitteln der Abfrage wartet.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` mit dem Wert „100“. Dieser Wert gibt an, wie oft das Programm den Dienst abfragt, während es versucht, eine erfolgreiche Antwort zu erhalten.

Öffnen Sie als Nächstes _AnalyzeForm.cs_, und suchen Sie die Variable `fieldMappings`, die auf die Datei *field-mappings.json* verweist. Diese Datei (und die darauf verweisende Variable) definiert die Liste der Schlüssel, die Sie aus Ihren Formularen extrahieren möchten, mit einer benutzerdefinierten Bezeichnung für jeden Schlüssel. Der Wert `{ "Address:", "address" }, { "Invoice For:", "recipient" }` bedeutet beispielsweise, dass das Skript nur die Werte für die erkannten Felder `Address:` und `Invoice For:` speichert und diese Werte als `"address"` bzw. `"recipient"` bezeichnet.

Beachten Sie abschließend die Variable `contentType`. Dieses Skript führt das angegebene Formularerkennungsmodell für Remotedokumente aus, auf die durch die URL verwiesen wird. Der Inhaltstyp ist somit `application/json`. Wenn Sie lokale Dateien analysieren möchten, indem Sie ihre Bytestreams in die HTTP-Anforderungen einschließen, müssen Sie den `contentType` in den entsprechenden [MIME-Typ](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) für Ihre Datei ändern.

## <a name="test-the-function-from-visual-studio"></a>Testen der Funktion aus Visual Studio

Nachdem Sie das Projekt bearbeitet haben, speichern Sie es, und legen Sie das Projekt **AnalyzeForm** als Startprojekt in Visual Studio fest (sofern es nicht bereits festgelegt ist). Drücken Sie dann **F5**, um die Funktion in Ihrer lokalen Umgebung auszuführen. Verwenden Sie einen REST-Dienst wie [Postman](https://www.postman.com/), um die Funktion aufzurufen.

### <a name="http-request"></a>HTTP-Anforderung

Sie führen zum Aufrufen der Funktion die folgende Anforderung aus.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Anforderungstext

Beginnen Sie mit der Anforderungstextvorlage weiter unten.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Hier müssen Sie die URL eines Formulars angeben, das denselben Typ aufweist wie die Formulare, mit denen Sie trainiert haben. Zu Testzwecken können Sie eines Ihrer Trainingsformulare verwenden. Wenn Sie den cURL-Schnellstart befolgt haben, befinden sich Ihre Formulare in einem Azure Blob Storage-Konto. Öffnen Sie Azure Storage-Explorer, suchen Sie eine Formulardatei, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Shared Access Signature abrufen** aus. Im nächsten Dialogfeld werden eine URL und ein SAS-Token bereitgestellt. Geben Sie diese Zeichenfolgen in die Felder `"formUrl"` und `"formSasToken"` Ihres Anforderungstexts ein.

> [!div class="mx-imgBorder"]
> ![Azure Storage-Explorer mit ausgewähltem PDF-Dokument](media/cognitive-search-skill-form/form-sas.png)

Wenn Sie ein Remotedokument analysieren möchten, das sich nicht in Azure Blob Storage befindet, fügen Sie dessen URL in das Feld `"formUrl"` ein, und lassen Sie das Feld `"formSasToken"` leer.

> [!NOTE]
> Wenn der Skill in ein Skillset integriert ist, werden die URL und das Token von Cognitive Search bereitgestellt.

### <a name="response"></a>Antwort

Die Antwort sollte in etwa wie hier dargestellt aussehen:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Veröffentlichen der Funktion in Azure

Wenn Sie mit dem Funktionsverhalten zufrieden sind, können Sie sie veröffentlichen.

1. Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus. Wählen Sie **Neu erstellen** > **Veröffentlichen**.
1. Wenn Sie Visual Studio noch nicht mit Ihrem Azure-Konto verbunden haben, wählen Sie **Konto hinzufügen...** aus.
1. Befolgen Sie die Anweisungen auf dem Bildschirm. Geben Sie einen eindeutigen Namen für Ihren App-Dienst, das Azure-Abonnement, die Ressourcengruppe, den Hostingplan und das gewünschte Speicherkonto an. Sie können eine neue Ressourcengruppe, einen neuen Hostingplan und ein neues Speicherkonto erstellen, wenn Sie noch nicht über diese verfügen. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.
1. Beachten Sie die Website-URL nach Abschluss der Bereitstellung. Diese URL ist die Adresse der Funktions-App in Azure. Speichern Sie sie an einem temporären Speicherort.
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Ressourcengruppe, und suchen Sie nach der von Ihnen veröffentlichten Funktion `AnalyzeForm`. Im Abschnitt **Verwalten** sollten Sie Hostschlüssel angezeigt werden. Kopieren Sie den *Standardhostschlüssel*, und speichern Sie ihn an einem temporären Speicherort.

## <a name="connect-to-your-pipeline"></a>Herstellen einer Verbindung mit Ihrer Pipeline

Um diesen Skill in einer Cognitive Search-Pipeline zu nutzen, müssen Sie Ihrem Skillset eine Skilldefinition hinzufügen. Der folgende JSON-Block ist ein Beispiel für eine Skilldefinition. (Sie sollten die Eingaben und Ausgaben entsprechend Ihrem Szenario und Ihrer Skillsetumgebung aktualisieren.) Ersetzen Sie `AzureFunctionEndpointUrl` durch Ihre Funktions-URL und `AzureFunctionDefaultHostKey` durch Ihren Hostschlüssel.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie einen benutzerdefinierten Skill über den Azure-Formularerkennungsdienst erstellt. Weitere Informationen zu benutzerdefinierten Skills finden Sie in den folgenden Ressourcen. 

* [Azure Search Power Skills: ein Repository benutzerdefinierter Skills](https://github.com/Azure-Samples/azure-search-power-skills)
* [Hinzufügen einer benutzerdefinierten Qualifikation zu einer KI-Anreicherungspipeline](cognitive-search-custom-skill-interface.md)
* [Definieren einer Fähigkeitengruppe](cognitive-search-defining-skillset.md)
* [Erstellen eines Skillsets (REST)](/rest/api/searchservice/create-skillset)
* [Zuordnen angereicherter Felder](cognitive-search-output-field-mapping.md)