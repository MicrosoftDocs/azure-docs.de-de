---
title: Erstellen von Filtern mit dem Azure Media Services .NET SDK
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
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494295"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Erstellen von Filtern mit dem Media Services .NET SDK

Bei der Inhaltsbereitstellung für Ihre Kunden (Streaming von Liveereignissen oder Video on Demand) benötigen Ihre Kunden möglicherweise mehr Flexibilität als in der Manifestdatei für das Standardmedienobjekt beschrieben. Azure Media Services ermöglicht es Ihnen, Kontofilter und Medienobjektfilter für Ihre Inhalte zu definieren. 

Eine ausführliche Beschreibung dieses Features und der Szenarien, in denen es verwendet wird, finden Sie unter [Dynamische Manifeste](filters-dynamic-manifest-overview.md) und [Filter](filters-concept.md).

In diesem Thema wird beschrieben, wie Sie mit dem Media Services .NET SDK einen Filter für ein Video on Demand-Medienobjekt sowie [Kontofilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) und [Medienobjektfilter](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet) erstellen. 

> [!NOTE]
> Lesen Sie die Informationen zu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Voraussetzungen 

- Lesen Sie [Filter und dynamische Manifeste](filters-dynamic-manifest-overview.md).
- [Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md) Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 
- Beschaffen Sie die Informationen, die für den [Zugriff auf APIs](access-api-cli-how-to.md) benötigt werden.
- Lesen Sie [Hochladen, Codieren und Streamen mit Azure Media Services](stream-files-tutorial-with-api.md), um sich über den [Einstieg in die Verwendung des .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet) zu informieren.

## <a name="define-a-filter"></a>Definieren eines Filters  

In .NET konfigurieren Sie die Spurauswahl mit den Klassen [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) und [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

Der folgende Code definiert einen Filter, der alle EC-3-Audiotitel und alle Videotitel mit Bitraten im Bereich von 0–1000000 einbezieht.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Erstellen von Kontofiltern

Der folgende Code zeigt, wie Sie .NET zum Erstellen eines Kontofilters verwenden, der die [oben definierte](#define-a-filter) Spurauswahl einschließt. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Erstellen von Medienobjektfiltern

Der folgende Code zeigt, wie Sie .NET zum Erstellen eines Medienobjektfilters verwenden, der die [oben definierte](#define-a-filter) Spurauswahl einschließt. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Zuordnen von Filtern mit Streaminglocator

Sie können eine Liste von Medienobjekt- oder Kontenfiltern angeben, die für Ihren Streaminglocator gelten würden. Der [dynamische Paketerstellungs-Manager (Streamingendpunkt)](dynamic-packaging-overview.md) wendet diese Liste der Filter zusammen mit den Filtern an, die Ihr Client in der URL angibt. Diese Kombination generiert ein [dynamisches Manifest](filters-dynamic-manifest-overview.md), das auf Filtern in den URL und Filtern basiert, die Sie im Streaminglocator angeben. Es wird empfohlen, dieses Feature zu verwenden, wenn Sie Filter anwenden, aber nicht die Filternamen in der URL verfügbar machen möchten.

Der folgende C#-Code zeigt, wie ein Streaminglocator erstellt und `StreamingLocator.Filters` angegeben wird. Dies ist eine optionale Eigenschaft, die ein `IList<string>`-Objekt mit Filternamen annimmt.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Streamen unter Verwendung von Filtern

Sobald Sie Filter definiert haben, können Ihre Kunden diese in der Streaming-URL verwenden. Auf Streamingprotokolle mit adaptiver Bitrate können Filter angewandt werden: Apple HTTP Live Streaming (HLS), MPEG-DASH und Smooth Streaming.

Die folgende Tabelle zeigt einige Beispiele für URLs mit Filtern:

|Protocol|Beispiel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nächste Schritte

[Streamen von Videos](stream-files-tutorial-with-api.md) 


