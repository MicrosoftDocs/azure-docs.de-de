---
title: Verwenden der CLI zum Erstellen von Filtern mit Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie mithilfe der CLI Filter in Media Services erstellen können.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c6007b66e31996db5c6b043219470968a7b05031
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114684"
---
# <a name="creating-filters-with-cli"></a>Erstellen von Filtern mit der CLI 

Bei der Inhaltsbereitstellung für Ihre Kunden (Streaming von Liveereignissen oder Video on Demand) benötigen Ihre Kunden möglicherweise mehr Flexibilität als in der Manifestdatei für die Standardmediendatei beschrieben. Azure Media Services ermöglicht es Ihnen, Kontofilter und Medienobjektfilter für Ihre Inhalte zu definieren. 

Eine ausführliche Beschreibung dieser Funktion und der Szenarien, in denen sie verwendet wird, finden Sie unter [Dynamische Manifeste](filters-dynamic-manifest-overview.md) und [Filter](filters-concept.md).

In diesem Thema werden das Konfigurieren eines Filters für ein Video on Demand-Medienobjekt und die Verwendung der Befehlszeilenschnittstelle für Media Services v3 zum Erstellen von [Kontofiltern](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) und [Filtern für Medienobjekte](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) gezeigt. 

> [!NOTE]
> Lesen Sie die Informationen zu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Voraussetzungen 

- [Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md) Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 

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

Der folgende [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)-Befehl erstellt einen Kontofilter mit [zuvor definierten](#define-a-filter) Titelfilter-Auswahloptionen. 

Mit diesem Befehl können Sie einen optionalen `--tracks`-Parameter übergeben, der JSON-Code für die ausgewählten Spuren enthält.  Verwenden Sie „@{file}“, um den JSON-Code aus einer Datei zu laden. Wenn Sie die Azure CLI lokal verwenden, geben Sie den gesamten Dateipfad an:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Siehe auch [JSON-Beispiele für Filter](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Erstellen eines Medienobjektfilters

Der folgende [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)-Befehl erstellt einen Medienobjektfilter mit [zuvor definierten](#define-a-filter) Titelfilter-Auswahloptionen. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Siehe auch [JSON-Beispiele für Filter](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

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

Sobald Sie Filter definiert haben, können Ihre Kunden diese in der Streaming-URL verwenden. Auf Streamingprotokolle mit adaptiver Bitrate können Filter angewandt werden: Apple HTTP Live Streaming (HLS), MPEG-DASH und Smooth Streaming.

Die folgende Tabelle zeigt einige Beispiele für URLs mit Filtern:

|Protocol|Beispiel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Nächster Schritt

[Streamen von Videos](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Weitere Informationen

[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
