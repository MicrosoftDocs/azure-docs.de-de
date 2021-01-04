---
title: Abrufen einer Medienprozessorinstanz mithilfe von REST | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2020
ms.author: inhenkel
ms.openlocfilehash: e1ac2a2408e16c3f8057c49c69c354ebb1e377c7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348490"
---
# <a name="how-to-get-a-media-processor-instance"></a>Vorgehensweise zum Abrufen einer Medienprozessorinstanz

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)


## <a name="overview"></a>Übersicht

Medienprozessoren sind für bestimmte Video- oder Audioverarbeitungsaufgaben wie Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Alle an Media Services übermittelten Aufgaben benötigen einen Medienprozessor zum Codieren, Verschlüsseln oder Konvertieren der Video- oder Audioinhalte.

## <a name="azure-media-processors"></a>Azure-Medienprozessoren

Das folgende Thema enthält Listen von Medienprozessoren:

* [Codieren von Medienprozessoren](scenarios-and-availability.md)
* [Analytics-Medienprozessoren](scenarios-and-availability.md)

>[!NOTE]
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Abrufen eines Medienprozessors

Der folgende REST-Aufruf zeigt, wie Sie eine Medienprozessorinstanz nach Namen (in diesem Fall **Media Encoder Standard** ) abrufen. 

Anforderung:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Antwort:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessorinstanz zu erstellen, und können mit dem Artikel [Codieren von Medienobjekten](media-services-rest-get-started.md) fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Media Encoder Standard codieren können.

