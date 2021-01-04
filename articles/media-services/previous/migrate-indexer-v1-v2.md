---
title: Migrieren von Indexer v1 und v2 zu Azure Media Services Video Indexer | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie die Migration von Azure Media Indexer v1 und v2 zu Azure Media Services Video Indexer erfolgt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 330bffebb870635fd473e88a8eadb300eed40b9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012134"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrieren von Media Indexer und Media Indexer 2 zu Video Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Es wird empfohlen, dass Kunden von Indexer v1 und Indexer v2 auf den [Media Services v3 AudioAnalyzerPreset Basic-Modus](../latest/analyzing-video-audio-files-concept.md) umsteigen. Die Medienprozessoren [Azure Media Indexer](media-services-index-content.md) und [Azure Media Indexer 2 Preview](./legacy-components.md) werden außer Betrieb genommen. Die Daten zur Einstellung finden Sie unter dem Thema [Legacykomponenten](legacy-components.md).

Azure Media Services Video Indexer baut auf Azure Media Analytics, Azure Cognitive Search, Cognitive Services (beispielsweise Gesichtserkennungs-API, Microsoft Translator, Maschinelles Sehen-API und benutzerdefinierter Spracherkennungsdienst) auf. Sie ermöglicht Ihnen, mithilfe der Video- und Audiomodelle von Video Indexer Erkenntnisse aus Ihren Videos zu gewinnen. Informationen zu den Szenarien, in denen Video Indexer verwendet werden kann, zu den gebotenen Funktionen und zum Einstieg finden Sie unter [Video- und Audiomodelle von Video Indexer](../video-indexer/video-indexer-overview.md). 

Sie können Erkenntnisse aus Ihren Video- und Audiodateien ziehen, indem Sie die [Azure Media Services v3-Analyse-Einstellungen](../latest/analyzing-video-audio-files-concept.md) oder direkt die [Video Indexer-APIs](https://api-portal.videoindexer.ai/) verwenden. Derzeit gibt es eine Überschneidung bei den Features der APIs von Video Indexer und der APIs von Media Services v3.

> [!NOTE]
> Informationen zu den Unterschieden zwischen Video Indexer im Vergleich mit den Voreinstellungen des Media Services-Analysetools finden Sie im [Vergleichsdokument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

In diesem Artikel werden die Schritte zum Migrieren von Azure Media Indexer und Azure Media Indexer 2 zu Azure Media Services Video Indexer erläutert.  

## <a name="migration-options"></a>Migrationsoptionen

|Wenn Sie dies benötigen  |und anschließend |
|---|---|
|eine Lösung, die eine Spracherkennungs-Transkription für beliebige Mediendatei Formate in einem Untertitel-Dateiformat bereitstellt: VTT, SRT oder TTLL<br/>sowie zusätzliche Erkenntnisse über die Audiospur, wie etwa: Schlüsselwörter, Schließen auf Themen, akustische Ereignisse, Sprecherdiarisierung, Extrahierung von Entitäten und Übersetzung| aktualisieren Sie Ihre Anwendungen mithilfe der Video Indexer v2 REST-API oder der Einstellung des Azure Media Services v3 Audio Analyzers für die Verwendung der Funktionen des Azure Video Indexers.|
|Spracherkennungsfunktionen| verwenden Sie direkt die Cognitive Services Speech-API.|  

## <a name="getting-started-with-video-indexer"></a>Erste Schritte mit Video Indexer

Der folgende Abschnitt enthält Links zum Thema: [Wie kann ich mich mit Video Indexer vertraut machen?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Erste Schritte mit Media Services v3-APIs

Mit der Azure Media Services (AMS) v3-API können Sie Inhalte aus Video- und Audiodateien mit den [Azure Media Services v3-Analyse-Einstellungen](../latest/analyzing-video-audio-files-concept.md) extrahieren.

**AudioAnalyzerPreset** ermöglicht Ihnen, mehrere Audioinformationen aus einer Audio- oder Videodatei zu extrahieren. Die Ausgabe beinhaltet eine VTT- oder TTML-Datei für das Audiotranskript und eine JSON-Datei (mit allen zusätzlichen Audio-Erkenntnissen). Zu den Audio-Erkenntnissen gehören Schlüsselwörter, Sprecherindizierung und Sprachempfindungsanalyse. AudioAnalyzerPreset unterstützt außerdem für bestimmte Sprachen die Spracherkennung. Ausführliche Informationen finden Sie unter [Transformationen](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Erste Schritte

Informationen zu den ersten Schritten finden Sie unter:

* [Tutorial](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-Beispiele: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) oder [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-Beispiele: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) oder [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Erste Schritte mit den Cognitive Services-Spracherkennungsdiensten

[Azure Cognitive Services](../../cognitive-services/index.yml) bietet einen Spracherkennungsdienst, der Audiodatenströme in Echtzeit in Text umwandelt, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. Sie können die Spracherkennung verenden, um [Ihr eigenes Akustikmodell, Sprachmodell oder Aussprachemodell anzupassen](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Weitere Informationen finden Sie unter [Cognitive Services-Spracherkennung](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Der Spracherkennungsdienst akzeptiert keine Videodateiformate und auch nur [bestimmte Audioformate](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Weitere Informationen zum Spracherkennungsdienst und Ihren ersten Schritten damit finden Sie unter [Was ist die Spracherkennung?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Bekannte Unterschiede zu veralteten Diensten

Sie werden feststellen, dass Video Indexer, Azure Media Services v3 AudioAnalyzerPreset und die Cognitive Services-Spracherkennungsdienste zuverlässiger sind und Ausgaben höherer Qualität erzeugen als die eingestellten Prozessoren Azure Media Indexer 1 und Azure Media Indexer 2.  

Dies sind einige der bekannten Unterschiede:

* Die Cognitive Services-Spracherkennungsdienste unterstützen keine Extrahierung von Schlüsselwörtern. Jedoch bieten sowohl Video Indexer als auch Media Services v3 AudioAnalyzerPreset eine stabilere Menge von Schlüsselwörtern im JSON-Dateiformat.

## <a name="support"></a>Support

Sie können ein Supportticket erstellen, indem Sie zu [Neue Supportanfrage ](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigieren.

## <a name="next-steps"></a>Nächste Schritte

* [Legacy-Komponenten](legacy-components.md)
* [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/media-services/#encoding)