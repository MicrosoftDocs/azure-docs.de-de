---
Titel: Livetranskription: Azure Media Services description: Erfahren Sie mehr zur Azure Media Services-Livetranskription.  
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: ne ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel

---

# <a name="live-transcription-preview"></a>Livetranskription (Preview)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Service übermittelt Video, Audio und Text in unterschiedlichen Protokollen. Wenn Sie Ihren Livestream mittels MPEG-DASH oder HLS/CMAF veröffentlichen, stellt unser Dienst neben den Video- und Audiospuren auch den transkribierten Text im IMSC1.1-kompatiblen TTML-Format bereit. Die Übermittlung ist in MPEG-4 Part 30-Fragmente (ISO/IEC 14496-30) gepackt. Bei Verwendung der Zustellung über HLS/TS wird Text als VTT-Blöcke übermittelt.

Bei aktivierter Livetranskription werden zusätzliche Gebühren fällig. Überprüfen Sie die Preisinformationen im Abschnitt „Livevideo“ auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

In diesem Artikel wird beschrieben, wie Sie Livetranskription aktivieren, wenn Sie ein Liveereignis mit Azure Media Services streamen. Bevor Sie fortfahren, stellen Sie sicher, dass Sie mit der Verwendung von Media Services v3-REST-APIs vertraut sind (weitere Informationen finden Sie in [diesem Tutorial](stream-files-tutorial-with-rest.md)). Sie sollten außerdem mit dem Konzept des [Livestreamings](live-streaming-overview.md) vertraut sein. Es wird empfohlen, das Tutorial [Livestreaming mit Media Services](stream-live-tutorial-with-api.md) durchzuarbeiten.

## <a name="live-transcription-preview-regions-and-languages"></a>Regionen und Sprachen der Livetranskriptions-Vorschauversion

Livetranskription ist in den folgenden Regionen verfügbar:

- Asien, Südosten
- Europa, Westen
- Nordeuropa
- East US
- USA (Mitte)
- USA Süd Mitte
- USA, Westen 2
- Brasilien Süd

Dies ist die Liste der verfügbaren Sprachen, die transkribiert werden können. Verwenden Sie dazu den Sprachcode in der API.

| Sprache | Sprachcode |
| -------- | ------------- |
| Katalanisch  | ca-ES |
| Dänisch (Dänemark) | da-DK |
| Deutsch (Deutschland) | de-DE |
| Englisch (Australien) | en-AU |
| Englisch (Kanada) | en-CA |
| Walisisch (Großbritannien) | en-GB |
| Englisch (Indien) | en-IN |
| Englisch (Neuseeland) | en-NZ |
| Englisch (USA) | de-DE |
| Spanisch (Spanien) | es-ES |
| Spanisch (Mexiko) | es-MX |
| Finnisch (Finnland) | fi-FI |
| Französisch (Kanada) | fr-CA |
| Französisch (Frankreich) | fr-FR |
| Italienisch (Italien) | it-IT |
| Niederländisch (Niederlande) | nl-NL |
| Portugiesisch (Brasilien) | pt-BR |
| Portugiesisch (Portugal) | pt-PT |
| Schwedisch (Schweden) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Erstellen des Liveereignisses mit Livetranskription

Zum Erstellen eines Liveereignisses mit aktivierter Transkription senden Sie den PUT-Vorgang mit der API-Version 2019-05-01-preview, beispielsweise:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Der Vorgang weist den folgenden Funktionskörper auf (wobei ein Pass-Through-Liveereignis mit RTMP als Erfassungsprotokoll erstellt wird). Beachten Sie das Hinzufügen einer transcriptions-Eigenschaft.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Starten oder Beenden der Transkription nach dem Start des Liveereignisses

Sie können die Livetranskription starten und beenden, während sich das Liveereignis im Ausführungsstatus befindet. Weitere Informationen zum Starten und Beenden von Liveereignissen finden Sie im Abschnitt zu den Vorgängen mit langer Ausführungsdauer unter [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md#long-running-operations).

Um Livetranskriptionen zu aktivieren oder die Transkriptionssprache zu aktualisieren, patchen Sie das Livereignis so, dass es eine Eigenschaft „transcriptions“ enthält. Um Livetranskriptionen zu deaktivieren, entfernen Sie die „transcriptions“-Eigenschaft aus dem Objekt des Liveereignisses.  

> [!NOTE]
> Das **mehrmalige** Aktivieren oder Deaktivieren der Transkription während des Liveereignisses wird nicht unterstützt.

Dies ist der Beispielaufruf zum Aktivieren von Livetranskriptionen.

PATCH: ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Übermittlung und Wiedergabe der Transkription

Lesen Sie den Artikel [Dynamische Paketerstellung – Übersicht](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery), worin beschrieben wird, wie unser Dienst dynamische Paketerstellung verwendet, um Video, Audio und Text in unterschiedlichen Protokollen zu übermitteln. Wenn Sie Ihren Livestream mittels MPEG-DASH oder HLS/CMAF veröffentlichen, stellt unser Dienst neben den Video- und Audiospuren auch den transkribierten Text im IMSC1.1-kompatiblen TTML-Format bereit. Diese Übermittlung ist in MPEG-4 Part 30-Fragmente (ISO/IEC 14496-30) gepackt. Bei Übermittlung über HLS/TS wird Text in Form von VTT-Blöcken übermittelt. Sie können einen Webplayer wie [Azure Media Player](use-azure-media-player.md) verwenden, um den Stream wiederzugeben.  

> [!NOTE]
> Wenn Sie Azure Media Player verwenden, verwenden Sie die Version 2.3.3 oder höher.

## <a name="known-issues"></a>Bekannte Probleme

In der Vorschauphase liegen die folgenden bekannten Probleme bei der Livetranskription vor:

- Apps müssen die Vorschau-APIs verwenden, die in der [Media Services v3 OpenAPI-Spezifikation](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) beschrieben sind.
- Der DRM-Schutz (Digital Rights Management, Verwaltung digitaler Rechte) gilt nicht für die Textspur, es lediglich die AES-Umschlagverschlüsselung möglich.

## <a name="next-steps"></a>Nächste Schritte

* [Media Services: Übersicht](media-services-overview.md)
