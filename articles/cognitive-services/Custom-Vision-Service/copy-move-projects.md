---
title: Kopieren und Verschieben von Custom Vision-Projekten
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Ihre Custom Vision-Projekte mit den APIs „ExportProject“ und „ImportProject“ kopieren und verschieben.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 548d936538a909da10796d0377f119826582c420
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616058"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Kopieren und Verschieben von Custom Vision-Projekten

Nachdem Sie ein Custom Vision-Projekt erstellt und trainiert haben, sollten Sie das Projekt in eine andere Ressource kopieren. Beispielsweise können Sie ein Projekt aus einer Entwicklungsumgebung in eine Produktionsumgebung verschieben oder ein Projekt in einem Konto in einer anderen Azure-Region sichern, um die Datensicherheit zu erhöhen.

Die APIs **[ExportProjekt](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** und **[ImportProjekt](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** ermöglichen dies, indem sie Projekte von einem Custom Vision-Konto in andere kopieren können. Der vorliegende Leitfaden zeigt, wie Sie diese REST-APIs mit cURL verwenden. Sie können auch einen HTTP-Anforderungsdienst wie Postman verwenden, um die Anforderungen auszugeben.

## <a name="business-scenarios"></a>Geschäftsszenarien

Wenn Ihre App oder Ihr Unternehmen von der Verwendung eines Custom Vision-Projekts abhängt, empfehlen wir Ihnen, Ihr Modell in ein anderes Custom Vision-Konto zu kopieren, das sich in einer anderen Region befindet. Im Falle eines regionalen Ausfalls können Sie dann auf Ihr Projekt in der Region zugreifen, in die es kopiert wurde.

##  <a name="prerequisites"></a>Voraussetzungen

- Zwei Azure Custom Vision-Ressourcen. Wenn Sie diese nicht haben, wechseln Sie zum Azure-Portal und [erstellen Sie eine neue Custom Vision-Ressource](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Die Trainingsschlüssel und Endpunkt-URLs Ihrer Custom Vision-Ressourcen. Sie finden diese Werte im Azure-Portal auf der Registerkarte **Übersicht** für die Ressource.
- Ein erstelltes Custom Vision-Projekt. Anweisungen dazu finden Sie unter [Erstellen einer Klassifizierung](./getting-started-build-a-classifier.md).

## <a name="process-overview"></a>Übersicht über den Prozess

Der Vorgang zum Kopieren eines Projekts umfasst die folgenden Schritte:

1. Zuerst erhalten Sie die ID des Projekts in dem Quellkonto, das Sie kopieren möchten.
1. Dann rufen Sie die **ExportProjekt**-API mithilfe der Projekt-ID und des Trainingsschlüssels Ihres Quellkontos auf. Sie erhalten eine temporäre Tokenzeichenfolge.
1. Dann rufen Sie die **ImportProjekt**-API mithilfe der Tokenzeichenfolge und des Trainingsschlüssels Ihres Zielkontos auf. Das Projekt wird dann unter Ihrem Zielkonto aufgeführt.

## <a name="get-the-project-id"></a>Abrufen der Projekt-ID

Rufen Sie zunächst **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** auf, um eine Liste der vorhandenen Custom Vision-Projekte und deren IDs anzuzeigen. Verwenden Sie den Trainingsschlüssel und den Endpunkt Ihres Quellkontos.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Sie erhalten eine `200\OK`-Antwort mit einer Liste von Projekten und zugehörigen Metadaten im Text. Der `"id"`-Wert ist die Zeichenfolge, die für die nächsten Schritte kopiert werden soll.

```json
[
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
]
```

## <a name="export-the-project"></a>Exportieren des Projekts

Rufen Sie **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** mithilfe der Projekt-ID und Ihres Quelltrainingsschlüssels und -endpunkts auf.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Sie erhalten eine `200/OK`-Antwort mit Metadaten zum exportierten Projekt und eine Verweiszeichenfolge `"token"`. Kopieren Sie den Wert des Tokens.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importieren des Projekts

Rufen Sie **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** mithilfe Ihres Zieltrainingsschlüssels und -endpunkts zusammen mit dem Verweistoken auf. Sie können Ihrem Projekt auch einen Namen im neuen Konto zuordnen.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Sie erhalten eine `200/OK`-Antwort mit Metadaten zu Ihrem neu importierten Projekt.

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

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Sie ein Projekt zwischen Custom Vision-Ressourcen kopieren und verschieben. Sehen Sie sich als Nächstes die API-Referenzdokumentation an, um sich über weitere Möglichkeiten bei Verwendung von Custom Vision zu informieren.
* [Referenzdokumentation zur Rest-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)