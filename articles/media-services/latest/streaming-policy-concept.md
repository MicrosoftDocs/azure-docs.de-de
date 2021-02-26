---
title: Streamingrichtlinien in Azure Media Services
description: In diesem Artikel wird erläutert, was Streamingrichtlinien sind und wie sie in Azure Media Services verwendet werden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 2873486b95f0bb4a9a27e2e050cc6f6d5473a44e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898171"
---
# <a name="streaming-policies"></a>Streamingrichtlinien

In Azure Media Services v3 können Sie mithilfe von [Streamingrichtlinien](/rest/api/media/streamingpolicies) Streamingprotokolle und Verschlüsselungsoptionen für Ihre [Streaminglocator](streaming-locators-concept.md) definieren. Media Services v3 bietet einige vordefinierte Streamingrichtlinien, die Sie direkt für Tests oder in der Produktion verwenden können. 

Die folgenden vordefinierten Streamingrichtlinien sind aktuell verfügbar:<br/>
* „Predefined_DownloadOnly“
* „Predefined_ClearStreamingOnly“
* „Predefined_DownloadAndClearStreaming“
* „Predefined_ClearKey“
* „Predefined_MultiDrmCencStreaming“ 
* „Predefined_MultiDrmStreaming“

Die folgenden „Entscheidungsstruktur“ hilft Ihnen bei der Auswahl einer vordefinierten Streamingrichtlinie für Ihr Szenario.

> [!IMPORTANT]
> * Eigenschaften von **Streamingrichtlinien** vom Datetime-Typ liegen immer im UTC-Format vor.
> * Sie sollten eine begrenzte Sammlung von Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Optionen benötigt werden. Weitere Informationen finden Sie unter [Kontingente und Grenzwerte](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Entscheidungsstruktur

Klicken Sie auf Bild, um es in voller Größe anzeigen.  

[![Diagramm mit einer Entscheidungsstruktur, die Sie bei der Auswahl einer vordefinierten Streamingrichtlinie für Ihr Szenario unterstützen soll.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Werden Ihre Inhalte verschlüsselt, müssen Sie eine [Richtlinie für Inhaltsschlüssel](content-key-policy-concept.md) erstellen. Zum Streamen oder Herunterladen in Klartext ist die **Richtlinie für Inhaltsschlüssel** nicht erforderlich. 

Wenn Sie besondere Anforderungen haben (wenn Sie z. B. verschiedene Protokolle angeben möchten oder einen benutzerdefinierten Schlüsselbereitstellungsdienst oder eine klare Audiospur verwenden müssen), können Sie eine benutzerdefinierte Streamingrichtlinie [erstellen](/rest/api/media/streamingpolicies/create). 

## <a name="get-a-streaming-policy-definition"></a>Abrufen einer Streamingrichtliniendefinition  

Wenn Sie die Definition einer Streamingrichtlinie anzeigen möchten, verwenden Sie [Get](/rest/api/media/streamingpolicies/get) und geben den Namen der Richtlinie an. Beispiel:

### <a name="rest"></a>REST

Anforderung:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Antwort:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filterung, Sortierung, Paging

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Media Services-Entitäten](entities-overview.md).

## <a name="next-steps"></a>Nächste Schritte

* [Streamen einer Datei](stream-files-dotnet-quickstart.md)
* [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](protect-with-aes128.md)
* [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](protect-with-drm.md)
