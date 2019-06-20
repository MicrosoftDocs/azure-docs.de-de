---
title: Definieren von Filtern in Azure Media Services
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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225412"
---
# <a name="filters"></a>Filter

Bei der Inhaltsbereitstellung für Ihre Kunden (Livestreaming von Ereignissen oder Video on Demand) benötigen Ihre Kunden möglicherweise mehr Flexibilität als in der Manifestdatei für das Standardmedienobjekt beschrieben. Azure Media Services bietet [dynamische Manifeste](filters-dynamic-manifest-overview.md), die auf vordefinierten Filtern basieren. 

Filter sind serverseitige Regeln, die Ihren Kunden Folgendes ermöglichen: 

- Wiedergabe von einzelnen Abschnitten eines Videos (anstelle des gesamten Videos) Beispiel:
  - Reduzieren des Manifests, um einen Subclip eines Liveereignisses anzuzeigen („Filtern von Subclips“), oder
  - Kürzen des Starts eines Videos ("Kürzen eines Videos").
- Ausschließliche Bereitstellung der angegebenen Wiedergaben und/oder Sprachspuren, die vom Gerät für die Inhaltswiedergabe unterstützt werden („Filtern der Wiedergabe“) 
- Anpassen des Präsentationsfensters (DVR), um eine begrenzte Größe des DVR-Fensters im Player anzugeben („Anpassen des Präsentationsfensters“).

Media Services ermöglicht es Ihnen, **Kontofilter** und **Medienobjektfilter** für Ihre Inhalte zu erstellen. Darüber hinaus können Sie Ihre zuvor erstellten Filter einem **Streaminglocator** zuordnen.

## <a name="defining-filters"></a>Definieren von Filtern

Es gibt zwei Arten von Filtern: 

* [Kontofilter](https://docs.microsoft.com/rest/api/media/accountfilters) (global): Diese Filter können auf jedes Medienobjekt im Azure Media Services-Konto angewendet werden und haben die Lebensdauer des Kontos.
* [Medienobjektfilter](https://docs.microsoft.com/rest/api/media/assetfilters) (lokal): Sie können nur auf ein Medienobjekt angewendet werden, dem der Filter bei der Erstellung zugeordnet wurde, und haben die Lebensdauer des Medienobjekts. 

Die Typen **Kontofilter** und **Medienobjektfilter** besitzen exakt dieselben Eigenschaften zum Definieren/Beschreiben des jeweiligen Filters. Außer beim Erstellen des **Medienobjektfilters** müssen Sie den Namen des Medienobjekts angeben, dem Sie den Filter zuordnen möchten.

Abhängig vom Szenario entscheiden Sie, welcher Filtertyp (Medienobjektfilter oder Kontofilter) besser geeignet ist. Kontofilter eignen sich für Geräteprofile (Wiedergabefilterung), Medienobjektfilter hingegen können zum Kürzen eines bestimmten Medienobjekts eingesetzt werden.

Sie verwenden die folgenden Eigenschaften zum Beschreiben der Filter. 

|NAME|BESCHREIBUNG|
|---|---|
|firstQuality|Die erste Qualitätsstufe der Bitrate für den Filter.|
|presentationTimeRange|Der Bereich der Präsentationszeit. Diese Eigenschaft wird zum Filtern der Start-/Endpunkte des Manifests, der Länge des Präsentationsfensters und der Livestartposition verwendet. <br/>Weitere Informationen finden Sie unter [PresentationTimeRange](#presentationtimerange).|
|tracks|Die Bedingungen zur Spurauswahl. Weitere Informationen finden Sie unter [tracks](#tracks).|

### <a name="presentationtimerange"></a>presentationTimeRange

Verwenden Sie diese Eigenschaft mit **Medienobjektfiltern**. Es wird nicht empfohlen, die Eigenschaft mit **Kontofiltern** festzulegen.

|NAME|BESCHREIBUNG|
|---|---|
|**endTimestamp**|Gilt für Video on Demand (VoD).<br/>Bei einer Livestreamingpräsentation wird diese Eigenschaft stillschweigend ignoriert und angewendet, wenn die Präsentation endet und der Stream zu VoD wird.<br/>Dies ist ein Long-Wert, der einen absoluten Endpunkt der Präsentation darstellt und auf den nächsten GOP-Start gerundet wird. Die Einheit ist die Zeitskala, daher würde ein „endTimestamp“ von 1800000000 einer Zeit von drei Minuten entsprechen.<br/>Verwenden Sie „startTimestamp“ und „endTimestamp“, um die Fragmente in der Wiedergabeliste (das Manifest) zu kürzen.<br/>Wenn Sie z. B. „startTimestamp=40000000“ und „endTimestamp=100000000“ mit der voreingestellten Zeitskala verwenden, wird eine Wiedergabeliste generiert, die Fragmente aus einer Zeitspanne von vier bis zehn Sekunden der VoD-Präsentation enthält. Wenn ein Fragment die Grenze überschreitet, wird das gesamte Fragment in das Manifest aufgenommen.|
|**forceEndTimestamp**|Gilt nur für Livestreaming.<br/>Gibt an, ob die Eigenschaft „endTimestamp“ vorhanden sein muss. Bei „true“ muss „endTimestamp“ muss angegeben werden, da ansonsten ein ungültiger Anforderungscode zurückgegeben wird.<br/>Zulässige Werte: false, true.|
|**liveBackoffDuration**|Gilt nur für Livestreaming.<br/> Dieser Wert definiert die letzte Liveposition, die ein Client durchsuchen kann.<br/>Mit dieser Eigenschaft können Sie die Livewiedergabeposition verzögern und einen serverseitigen Puffer für Spieler erstellen.<br/>Die Einheit für diese Eigenschaft ist die Zeitskala (siehe unten).<br/>Der maximal zulässige Wert für „LiveBackoffDuration“ beträgt 300 Sekunden (3000000000).<br/>Ein Wert von 2000000000 bedeutet z. B., dass der letzte verfügbare Inhalt 20 Sekunden gegenüber der tatsächlichen Liveedge verzögert ist.|
|**presentationWindowDuration**|Gilt nur für Livestreaming.<br/>Verwenden Sie „presentationWindowDuration“, um ein gleitendes Fenster mit Fragmenten auf die Wiedergabeliste anzuwenden.<br/>Die Einheit für diese Eigenschaft ist die Zeitskala (siehe unten).<br/>Legen Sie beispielsweise „presentationWindowDuration=1200000000“ fest, um ein gleitendes Fenster von 2 Minuten anzuwenden. Medien, die in einem 2-Minuten-Zeitraum um die Liveposition liegen, werden in die Wiedergabeliste aufgenommen. Wenn ein Fragment die Grenze überschreitet, wird das gesamte Fragment in die Wiedergabeliste aufgenommen. Die Mindestdauer für das Präsentationsfenster beträgt 60 Sekunden.|
|**startTimestamp**|Gilt für Video on Demand (VoD) oder Livestreaming.<br/>Dies ist ein Long-Wert, der einen absoluten Startpunkt des Streams darstellt. Er wird auf den nächstgelegenen GOP-Start gerundet. Die Einheit ist die Zeitskala, daher würde ein „startTimestamp“ von 150000000 einer Zeit von 15 Sekunden entsprechen.<br/>Verwenden Sie „startTimestamp“ und „endTimestamp“, um die Fragmente in der Wiedergabeliste (das Manifest) zu kürzen.<br/>Wenn Sie z. B. „startTimestamp=40000000“ und „endTimestamp=100000000“ mit der voreingestellten Zeitskala verwenden, wird eine Wiedergabeliste generiert, die Fragmente aus einer Zeitspanne von vier bis zehn Sekunden der VoD-Präsentation enthält. Wenn ein Fragment die Grenze überschreitet, wird das gesamte Fragment in das Manifest aufgenommen.|
|**timescale**|Gilt für alle Zeitstempel und Zeiträume in einem Präsentationszeitbereich, der als Anzahl der Inkremente in einer Sekunde angegeben wird.<br/>Der Standardwert ist 10000000 – zehn Millionen Inkremente in einer Sekunde, wobei jedes Inkrement 100 Nanosekunden lang sein würde.<br/>Wenn Sie z. B. einen Startzeitstempel auf 30 Sekunden einstellen möchten, würden Sie bei Verwendung der Standardzeitskala einen Wert von 300000000 verwenden.|

### <a name="tracks"></a>Spuren

Sie geben eine Liste von Eigenschaftsbedingungen für die Filterung nach Spuren an (FilterTrackPropertyConditions), anhand derer die Spuren Ihres Streams (Livestreaming oder Video on Demand) in das dynamisch erstellte Manifest aufgenommen werden sollen. Die Filter werden durch einen logischen **AND**- und **OR**-Vorgang kombiniert.

Spureigenschaftsbedingungen für die Filterung beschreiben Spurtypen, Werte (siehe nachstehende Tabelle) und Vorgänge („Equal“, „NotEqual“). 

|NAME|BESCHREIBUNG|
|---|---|
|**Bitrate**|Verwenden Sie die Bitrate der Spur zur Filterung.<br/><br/>Der empfohlene Wert ist ein Bereich aus Bitraten in Bits pro Sekunde. Beispiel: 0-2427000.<br/><br/>Hinweis: Sie können einen bestimmten Wert für die Bitrate angeben, z.B. 250000 (Bits pro Sekunde), dieser Ansatz wird jedoch nicht empfohlen, weil die exakten Bitraten je nach Medienobjekt unterschiedlich sein können.|
|**FourCC**|Verwenden Sie den FourCC-Wert der Spur für die Filterung.<br/><br/>Der Wert ist das erste Element des Codecs-Formats, wie angegeben in [RFC 6381](https://tools.ietf.org/html/rfc6381). Aktuell werden die folgenden Codecs unterstützt: <br/>Für Video: avc1, hev1, hvc1<br/>Für Audio: mp4a, ec-3<br/><br/>Um die FourCC-Werte für die Spuren in einem Medienobjekt zu bestimmen, müssen Sie die Manifestdatei abrufen und untersuchen.|
|**Sprache**|Verwenden Sie die Sprache der Spur zur Filterung.<br/><br/>Der Wert ist das Tag einer Sprache, die Sie einschließen möchten, wie angegeben in RFC 5646. Beispiel: „en“.|
|**Name**|Verwenden Sie den Namen der Spur zur Filterung.|
|**Typ**|Verwenden Sie den Typ der Spur zur Filterung.<br/><br/>Die folgenden Werte sind zulässig: video, audio, text|

### <a name="example"></a>Beispiel

Das folgende Beispiel definiert einen Livestreamingfilter: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Zuordnen von Filtern mit Streaminglocator

Sie können eine Liste von [Medienobjekt- oder Kontofiltern](filters-concept.md) für Ihren [Streaminglocator](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body) angeben. Der [dynamische Paketerstellungs-Manager](dynamic-packaging-overview.md) wendet diese Liste der Filter zusammen mit den Filtern an, die Ihr Client in der URL angibt. Diese Kombination generiert ein [dynamisches Manifest](filters-dynamic-manifest-overview.md), das auf Filtern in den URL und Filtern basiert, die Sie im Streaminglocator angeben. 

Hierzu folgende Beispiele:

* [Zuordnen von Filtern mit Streaminglocator: .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Zuordnen von Filtern mit Streaminglocator: CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualisieren von Filtern
 
**Streaminglocators** können nicht aktualisiert werden, während Filter aktualisierbar sind. 

Es wird nicht empfohlen, die Definition von Filtern zu aktualisieren, die einem aktiv veröffentlichten **Streaminglocator** zugeordnet sind, insbesondere wenn CDN aktiviert ist. Streamingserver und CDNs können interne Caches besitzen, die dazu führen können, dass veraltete zwischengespeicherte Daten zurückgegeben werden. 

Wenn die Filterdefinition geändert werden muss, erwägen Sie, einen neuen Filter zu erstellen und diesen der **Streaminglocator**-URL hinzuzufügen oder einen neuen **Streaminglocator** zu veröffentlichen, der direkt auf den Filter verweist.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten Informationen zum programmgesteuerten Erstellen von Filtern.  

- [Erstellen von Filtern mit REST-APIs](filters-dynamic-manifest-rest-howto.md)
- [Erstellen von Filtern mit .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Erstellen von Filtern mit der CLI](filters-dynamic-manifest-cli-howto.md)

