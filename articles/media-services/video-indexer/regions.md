---
title: 'Regionen, in dem Video Indexer verfügbar ist: Azure'
titlesuffix: Azure Media Services
description: Dieser Artikel befasst sich mit den Azure-Regionen, in denen Video Indexer verfügbar ist.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 404aaf91c0cb30df0a83353ef7397987ec3f8e80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799412"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-Regionen, in denen Video Indexer vorhanden ist

Video Indexer-APIs enthalten einen Parameter **location**, den Sie auf die Azure-Region festlegen sollten, an die der Aufruf weitergeleitet werden soll. Dabei muss es sich um eine [Azure-Region handeln, in der Video Indexer verfügbar ist](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Standorte

Dem Parameter **location** muss der Codename der Azure-Region als Wert zugewiesen werden. Wenn Sie Video Indexer im Vorschaumodus verwenden, sollten Sie *„trial“* als Wert verwenden. Andernfalls können Sie die folgende Zeile in der [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ausführen, um den Codenamen der Azure-Region abzurufen, in der sich Ihr Konto befindet und a die Ihr Aufruf weitergeleitet werden soll:

```bash
az account list-locations
```

Nach dem Ausführen der oben gezeigten Zeile erhalten Sie eine Liste mit allen Azure-Regionen. Navigieren Sie zu der Azure-Region, die den *displayName* aufweist, nach dem Sie suchen, und verwenden ihren Wert *name* für den Parameter **location**.

Verwenden Sie für die Azure-Region „USA, Westen 2“ (unten angezeigt) beispielsweise „westus2“ als Wert für den Parameter **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Nächste Schritte

- [Anpassen des Sprachmodells mit APIs](customize-language-model-with-api.md)
- [Anpassen des Markenmodells mit APIs](customize-brands-model-with-api.md)
- [Anpassen des Personenmodells mit APIs](customize-person-model-with-api.md)
