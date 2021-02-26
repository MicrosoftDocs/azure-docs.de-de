---
title: Verwenden der CLI zum Erstellen von Filtern mit Azure Media Services
description: In diesem Artikel wird gezeigt, wie Sie mithilfe der CLI Filter in Azure Media Services v3 erstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 75df01ff65a0910dab140942b520978cd7728775
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897576"
---
# <a name="creating-filters-with-cli"></a>Erstellen von Filtern mit der CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bei der Inhaltsbereitstellung für Ihre Kunden (Streaming von Liveereignissen oder Video on Demand) benötigen Ihre Kunden möglicherweise mehr Flexibilität als in der Manifestdatei für die Standardmediendatei beschrieben. Azure Media Services ermöglicht es Ihnen, Kontofilter und Medienobjektfilter für Ihre Inhalte zu definieren. 

Eine ausführliche Beschreibung dieses Features und der Szenarien, in denen es verwendet wird, finden Sie unter [Dynamische Manifeste](filters-dynamic-manifest-overview.md) und [Filter](filters-concept.md).

In diesem Thema werden das Konfigurieren eines Filters für ein Video on Demand-Medienobjekt und die Verwendung der Befehlszeilenschnittstelle für Media Services v3 zum Erstellen von [Kontofiltern](/cli/azure/ams/account-filter?view=azure-cli-latest) und [Filtern für Medienobjekte](/cli/azure/ams/asset-filter?view=azure-cli-latest) gezeigt. 

> [!NOTE]
> Lesen Sie die Informationen zu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Voraussetzungen 

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definieren eines Filters 

Das folgende Beispiel definiert die Titelauswahlbedingungen, die dem endgültigen Manifest hinzugefügt werden. Dieser Filter bezieht alle EC-3-Audiotitel und alle Videotitel mit Bitraten im Bereich von 0–1000000 ein.

> [!TIP]
> Wenn Sie planen, **Filter** in REST zu definieren, beachten Sie, dass Sie das JSON-Wrapperobjekt „Properties“ einschließen müssen.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Erstellen von Kontofiltern

Der folgende [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest)-Befehl erstellt einen Kontofilter mit [zuvor definierten](#define-a-filter) Titelfilter-Auswahloptionen. 

Mit diesem Befehl können Sie einen optionalen `--tracks`-Parameter übergeben, der JSON-Code für die ausgewählten Spuren enthält.  Verwenden Sie „@{file}“, um den JSON-Code aus einer Datei zu laden. Wenn Sie die Azure CLI lokal verwenden, geben Sie den gesamten Dateipfad an:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Siehe auch [JSON-Beispiele für Filter](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Erstellen von Medienobjektfiltern

Der folgende [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest)-Befehl erstellt einen Medienobjektfilter mit [zuvor definierten](#define-a-filter) Titelfilter-Auswahloptionen. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Siehe auch [JSON-Beispiele für Filter](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Zuordnen von Filtern mit Streaminglocator

Sie können eine Liste von Medienobjekt- oder Kontenfiltern angeben, die für Ihren Streaminglocator gelten würden. Der [dynamische Paketerstellungs-Manager (Streamingendpunkt)](dynamic-packaging-overview.md) wendet diese Liste der Filter zusammen mit den Filtern an, die Ihr Client in der URL angibt. Diese Kombination generiert ein [dynamisches Manifest](filters-dynamic-manifest-overview.md), das auf Filtern in den URL und Filtern basiert, die Sie im Streaminglocator angeben. Es wird empfohlen, dieses Feature zu verwenden, wenn Sie Filter anwenden, aber nicht die Filternamen in der URL verfügbar machen möchten.

Der folgende CLI-Code zeigt, wie ein Streaminglocator erstellt und `filters` angegeben wird. Dies ist eine optionale Eigenschaft, die eine durch Leerzeichen getrennte Liste von Objektfilternamen und/oder Kontofilternamen annimmt.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Streamen unter Verwendung von Filtern

Sobald Sie Filter definiert haben, können Ihre Kunden diese in der Streaming-URL verwenden. Die Filter können auf Streaminprotokolle mit adaptiver Bitrate angewendet werden: Apple HTTP Live Streaming (HLS), MPEG-DASH und Smooth Streaming.

Die folgende Tabelle zeigt einige Beispiele für URLs mit Filtern:

|Protocol|Beispiel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Nächster Schritt

[Streamen von Videos](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Weitere Informationen

[Azure-Befehlszeilenschnittstelle](/cli/azure/ams?view=azure-cli-latest)
