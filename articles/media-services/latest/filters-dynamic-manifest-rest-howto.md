---
title: Erstellen von Filtern mit der Azure Media Services-REST-API | Microsoft Docs
description: In diesem Thema wird erläutert, wie Sie Filter erstellen, mit denen Ihre Kunden bestimmte Abschnitte eines Streams streamen können. Media Services erstellt dynamische Manifeste, um dieses selektive Streaming zu erreichen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 69399513291a47f7109003e825052314f447125a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002323"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Erstellen von Filtern mit der Media Services-REST-API

Bei der Inhaltsbereitstellung für Ihre Kunden (Streaming von Liveereignissen oder Video on Demand) benötigen Ihre Kunden möglicherweise mehr Flexibilität als in der Manifestdatei für das Standardmedienobjekt beschrieben. Azure Media Services ermöglicht es Ihnen, Kontofilter und Medienobjektfilter für Ihre Inhalte zu definieren. Weitere Informationen finden Sie unter [Filter](filters-concept.md) und [dynamische Manifeste](filters-dynamic-manifest-overview.md).

In diesem Thema wird beschrieben, wie Sie einen Filter für ein Video on Demand-Medienobjekt definieren und mithilfe von REST-APIs [Kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) und [Medienobjektfilter](https://docs.microsoft.com/rest/api/media/assetfilters) erstellen. 

> [!NOTE]
> Lesen Sie die Informationen zu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Voraussetzungen 

Für die in diesem Thema beschriebenen Schritte ist Folgendes erforderlich:

- Lesen Sie [Filter und dynamische Manifeste](filters-dynamic-manifest-overview.md).
- [Konfigurieren Sie Postman für Azure Media Services-REST-API-Aufrufe](media-rest-apis-with-postman.md).

    Befolgen Sie dabei unbedingt den letzten Schritt im Thema [Abrufen von Azure AD-Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definieren eines Filters  

Das folgende Beispiel für einen **Anforderungstext** definiert die Titelauswahlbedingungen, die dem Manifest hinzugefügt werden. Dieser Filter bezieht alle EC-3-Audiotitel und alle Videotitel mit Bitraten im Bereich von 0–1000000 ein.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Erstellen von Kontofiltern

Wählen Sie in der heruntergeladenen Sammlung von Postman **Account Filters (Kontofilter)** ->**Create or update an Account Filter (Kontofilter erstellen oder aktualisieren** aus.

Die **PUT**-HTTP-Anforderungsmethode ähnelt der folgenden:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Wählen Sie die Registerkarte **Text** aus, und fügen Sie den JSON-Code ein, den Sie [zuvor definiert haben](#define-a-filter).

Wählen Sie **Senden** aus. 

Der Filter wurde erstellt.

Weitere Informationen finden Sie unter [Account Filters – Create Or Update](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate) (Erstellen oder Aktualisieren von Kontofiltern). Siehe auch [JSON-Beispiele für Filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Erstellen von Medienobjektfiltern  

Wählen Sie in der heruntergeladenen Postman-Sammlung „Media Services v3“ **Medienobjekte**->**Medienobjektfilter erstellen oder aktualisieren** aus.

Die **PUT**-HTTP-Anforderungsmethode ähnelt der folgenden:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Wählen Sie die Registerkarte **Text** aus, und fügen Sie den JSON-Code ein, den Sie [zuvor definiert haben](#define-a-filter).

Wählen Sie **Senden** aus. 

Der Medienobjektfilter wurde erstellt.

Ausführliche Informationen zum Erstellen oder Aktualisieren von Medienobjektfiltern finden Sie unter [Erstellen und Aktualisieren](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Siehe auch [JSON-Beispiele für Filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Nächste Schritte

[Streamen von Videos](stream-files-tutorial-with-rest.md) 
