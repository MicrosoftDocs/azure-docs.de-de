---
title: 'ML Studio (Classic): Erneutes Trainieren eines klassischen Webdiensts (Azure)'
description: Erfahren Sie, wie Sie ein Modell in Azure Machine Learning Studio (klassisch) erneut trainieren und einen klassischen Webdienst aktualisieren, sodass er das neu trainierte Modell verwendet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 158541d34568b7ea02ea82dbfe90f5801824716f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325788"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Erneutes Trainieren und Bereitstellen eines (klassischen) Studio-Webdiensts

**GILT FÜR:**  ![Grünes Häkchen.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassisch) ![X gibt nein an. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Das erneute Training von Machine Learning-Modellen ist eine Möglichkeit sicherzustellen, dass sie fehlerfrei bleiben und auf den relevantesten verfügbaren Daten basieren. In diesem Artikel werden das erneute Trainieren eines (klassischen) Studio-Webdiensts erläutert. Eine Anleitung zum erneuten Trainieren eines neues (klassischen) Studio-Webdiensts [finden Sie in dieser Anleitung](retrain-machine-learning-model.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits über sowohl ein Experiment zum erneuten Trainieren als auch ein Vorhersageexperiment verfügen. Diese Schritte werden unter [Erneutes Trainieren und Bereitstellen eines Machine Learning-Modells](./retrain-machine-learning-model.md) erläutert. Anstatt jedoch Ihr Machine Learning-Modell als neuen Webdienst bereitzustellen, werden Sie Ihr Vorhersageexperiment als klassischen Webdienst bereitstellen.
     
## <a name="add-a-new-endpoint"></a>Hinzufügen eines neuen Endpunkts

Der von Ihnen bereitgestellte Vorhersagewebdienst enthält einen standardmäßigen Bewertungsendpunkt, der mit den ursprünglichen Trainings- und Bewertungsexperimenten des trainierten Modells synchronisiert wird. Erstellen Sie zum Aktualisieren des Webdiensts mit einem neuen trainierten Modell einen neuen Bewertungsendpunkt.

Es gibt zwei Möglichkeiten zum Hinzufügen eines neuen Endpunkts zu einem Webdienst:

* Programmgesteuert
* Verwenden des Azure Web Services-Portals

> [!NOTE]
> Achten Sie darauf, dass Sie den Endpunkt dem Vorhersagewebdienst hinzufügen und nicht dem Trainingswebdienst. Wenn Sie sowohl einen Trainings- als auch einen Vorhersagewebdienst korrekt bereitgestellt haben, werden zwei separate Webdienste aufgeführt. Der Vorhersagewebdienst sollte mit „[predictive exp.]“ enden.
>

### <a name="programmatically-add-an-endpoint"></a>Programmgesteuertes Hinzufügen eines Endpunkts

Sie können Bewertungsendpunkte mithilfe des Beispielcodes in diesem [GitHub-Repository](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)hinzufügen.

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Hinzufügen eines Endpunkts im Azure Web Services-Portal

1. Klicken Sie in Machine Learning Studio (klassisch) in der linken Navigationsspalte auf „Webdienste“.
1. Klicken Sie unten auf dem Dashboards des Webdiensts auf **Manage endpoints preview** (Endpunktvorschau verwalten).
1. Klicken Sie auf **Hinzufügen**.
1. Geben Sie einen Namen und eine Beschreibung für den neuen Endpunkt ein. Wählen Sie die Protokollierungsstufe aus, und legen Sie fest, ob Beispieldaten aktiviert sind. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Protokollierung für Machine Learning-Webdienste](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualisieren des trainierten Modells des hinzugefügten Endpunkts

### <a name="retrieve-patch-url"></a>Abrufen der PATCH-URL

Um im Webportal die richtige PATCH-URL abzurufen, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure Machine Learning-Webdienstportal](https://services.azureml.net/) an.
1. Klicken Sie oben auf **Webdienste** oder **Classic Web Services** (Klassische Webdienste).
1. Klicken Sie auf den von Ihnen verwendeten Bewertungswebdienst. (wenn Sie den Standardnamen des Webdiensts nicht geändert haben, endet er auf „[Scoring Exp.]“).
1. Klicken Sie auf **+NEU**.
1. Klicken Sie nach dem Hinzufügen des Endpunkts auf dessen Namen.
1. Klicken Sie unter der **Patch** -URL auf **API-Hilfe** , um die Hilfeseite für das Patching zu öffnen.

> [!NOTE]
> Falls Sie den Endpunkt nicht dem Vorhersagewebdienst, sondern dem Trainingswebdienst hinzugefügt haben, tritt beim Klicken auf den Link **Ressource aktualisieren** der folgende Fehler auf: „Sorry, but this feature is not supported or available in this context. This Web Service has no updatable resources. We apologize for the inconvenience and are working on improving this workflow.“ (Dieses Feature wird in diesem Kontext leider nicht unterstützt oder ist nicht verfügbar. Der Webdienst besitzt keine aktualisierbaren Ressourcen. Wir arbeiten bereits an einer Optimierung dieses Workflows.)
>

Die PATCH-Hilfeseite enthält die PATCH-URL, die Sie verwenden müssen, und außerdem Beispielcode, den Sie zum Aufrufen verwenden können.

![Patch-URL](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aktualisieren des Endpunkts

Sie können das trainierte Modell jetzt verwenden, um den zuvor erstellten Bewertungsendpunkt zu aktualisieren.

Der folgende Beispielcode zeigt, wie Sie *BaseLocation* , *RelativeLocation* , *SasBlobToken* und die PATCH-URL zum Aktualisieren des Endpunkts verwenden.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

Der *apiKey* und die *endpointUrl* für den Aufruf werden im Dashboard des Endpunkts angezeigt.

Der Wert des Parameters *Name* in *Resources* muss mit dem Ressourcennamen des gespeicherten trainierten Modells im Vorhersageexperiment übereinstimmen. Gehen Sie wie folgt vor, um den Ressourcennamen abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im linken Menü auf **Machine Learning**.
1. Klicken Sie unter „Name“ auf Ihren Arbeitsbereich und dann auf **Web Services**.
1. Klicken Sie unter „Name“ auf **Census Model [predictive exp.]** .
1. Klicken Sie auf den neuen Endpunkt, den Sie hinzugefügt haben.
1. Klicken Sie im Dashboard des Endpunkts auf **Ressource aktualisieren**.
1. Auf der Dokumentationsseite der API zur Ressourcenaktualisierung für den Webdienst wird der **Ressourcenname** unter **Updatable Resources** angezeigt.

Wenn Ihr SAS-Token abläuft, bevor Sie die Aktualisierung des Endpunkts abgeschlossen haben, müssen Sie einen GET-Befehl mit der Einzelvorgangs-ID ausführen, um ein neues Token abzurufen.

Nach der erfolgreichen Ausführung des Codes sollte der neue Endpunkt nach ungefähr 30 Sekunden mit der Verwendung des neuen trainierten Modells beginnen.

## <a name="next-steps"></a>Nächste Schritte

Um mehr darüber zu erfahren, wie Sie Webdienste verwalten oder mehrere Experimentausführungen verfolgen können, lesen Sie die folgenden Artikel:

* [Erkunden des Web Services-Portals](manage-new-webservice.md)
* [Verwalten von Experimentiterationen](manage-experiment-iterations.md)