---
title: 'Codierung in der Cloud mit Media Services: Azure | Microsoft-Dokumentation'
description: In diesem Thema wird der Codierungsvorgang bei Verwendung von Azure Media Services beschrieben
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761881"
---
# <a name="encoding-with-media-services"></a>Codierung mit Media Services

Der Begriff Codierung beschreibt in Media Services den Prozess der Konvertierung von Dateien, die digitale Videos und/oder Audiodateien enthalten, von einem Format in ein anderes Format. Ziel ist es, (a) die Größe der Dateien zu reduzieren und/oder (b) ein Format zu erstellen, das mit vielen Geräten und Anwendungen kompatibel ist. Dieser Prozess wird auch als Videokomprimierung oder Transcodierung bezeichnet. Weitere Erläuterungen zu diesem Konzept finden Sie unter [Datenkomprimierung](https://en.wikipedia.org/wiki/Data_compression) und [Was bedeutet Codierung und Transcodierung?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx).

Das Übermitteln der Videos an Geräte und Anwendungen erfolgt in der Regel durch [progressiven Download](https://en.wikipedia.org/wiki/Progressive_download) oder über [Adaptive Bitrate Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Zur Bereitstellung per progressivem Download können Sie Azure Media Services zum Konvertieren Ihrer digitalen Mediendatei (Mezzanine) in eine [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)-Datei verwenden, die das Video, das mit dem [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)-Codec codiert wurde, und die Audiodatei, die mit dem [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)-Codec codiert wurde, enthält. Diese MP4-Datei wird in ein Medienobjekt in Ihrem Speicherkonto geschrieben. Sie können die Azure Storage-APIs oder -SDKs verwenden (z. B. [Storage-REST-API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) oder .[NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)), um die Datei direkt herunterzuladen. Wenn Sie das Ausgabemedienobjekt mit einem bestimmten Containernamen im Speicher erstellt haben, verwenden Sie die diesen Speicherort. Andernfalls können Sie Media Services verwenden, um [die Medienobjektcontainer-URLs aufzulisten](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Die Mezzanine-Datei muss mit mehreren Bitraten (hoch bis niedrig) codiert werden, um Inhalte für die Übermittlung durch das Adaptive Bitrate Streaming vorzubereiten. Mit abnehmender Bitrate wird auch die Auflösung des Videos verringert, um eine gleichmäßige Qualitätsminderung zu gewährleisten. Dies führt zu einer sogenannten Codierungsleiter, also einer Tabelle mit Auflösungen und Bitraten. Weitere Informationen hierzu erhalten Sie unter [auto-generated adaptive bitrate ladder (Automatisches Generieren einer Reihe von adaptiven Bitraten)](autogen-bitrate-ladder.md). Sie können Media Services zum Codieren Ihrer Mezzanine-Dateien mit mehreren Bitraten verwenden. Dabei erhalten Sie einige MP4-Dateien und zugeordnete Streaming-Konfigurationsdateien, die in ein Medienobjekt in Ihrem Speicherkonto geschrieben werden. Sie können dann die Funktion [Dynamische Paketerstellung](dynamic-packaging-overview.md) in Media Services verwenden, um das Video über Streamingprotokolle wie [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) oder [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) bereitzustellen. Dazu müssen Sie einen [Streaminglocator](streaming-locators-concept.md) und Streaming-URLs erstellen, die dem unterstützten Protokoll entsprechen. Dieses kann dann je nach Funktionen von Geräten/Anwendungen an diese übergeben werden.

Das folgende Diagramm zeigt den Workflow für bedarfsgesteuerte Codierung mit der dynamischen Paketerstellung.

![Dynamische Paketerstellung](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

In diesem Thema erhalten Sie Anleitungen zum Codieren Ihrer Inhalte mit Media Services v3.

## <a name="transforms-and-jobs"></a>Transformationen und Aufträge

Um mit Media Services v3 codieren zu können, müssen Sie eine [Transformation](https://docs.microsoft.com/rest/api/media/transforms) und einen [Auftrag](https://docs.microsoft.com/rest/api/media/jobs) erstellen. Die Transformation definiert eine Anweisung für die Codierungseinstellungen und -ausgaben. Der Auftrag ist eine Instanz der Anweisung. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transforms-jobs-concept.md).

Bei der Codierung mit Media Services verwenden Sie Voreinstellungen, um dem Encoder mitzuteilen, wie die eingegebenen Mediendateien verarbeitet werden sollen. Beispielsweise können Sie die gewünschte Videoauflösung und/oder die Anzahl der Audiokanäle für den codierten Inhalt angeben. 

Mit einer der empfohlenen integrierten Voreinstellungen basierend auf in der Branche bewährten Vorgehensweisen können Sie schnell einsteigen. Alternativ können Sie eine benutzerdefinierte Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen erstellen. Weitere Informationen finden Sie unter [Codieren mit einer benutzerdefinierten Transformation](customize-encoder-presets-how-to.md). 

Ab Januar 2019 wird bei der Codierung mit Media Encoder Standard zum Erzeugen von MP4-Dateien eine neue MPI-Datei generiert und dem Ausgabeasset hinzugefügt. Diese MPI-Datei dient zum Verbessern der Leistung für die [dynamische Paketerstellung](dynamic-packaging-overview.md) und Streamingszenarios.

> [!NOTE]
> Sie sollten weder die MPI-Datei ändern oder entfernen noch beliebige Abhängigkeiten vom Vorhandensein (oder Nichtvorhandensein) einer solchen Datei in Ihren Dienst integrieren.

## <a name="built-in-presets"></a>Integrierte Voreinstellungen

Media Services unterstützt derzeit die folgenden integrierten Codierungsvoreinstellungen:  

### <a name="builtinstandardencoderpreset-preset"></a>Voreinstellung „BuiltInStandardEncoderPreset“

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) wird zum Festlegen einer integrierten Voreinstellung für die Codierung des Eingabevideos mit dem Standard-Encoder verwendet. 

Die folgenden Voreinstellungen werden derzeit unterstützt:

- **EncoderNamedPreset.AACGoodQualityAudio**: Erzeugt eine einzelne MP4-Datei, die nur mit 192 Kbit/s codierte Stereo-Audiodaten enthält.
- **EncoderNamedPreset.AdaptiveStreaming** (empfohlen). Weitere Informationen finden Sie unter [Automatisches Generieren einer Reihe von Bitraten-/Auflösungspaaren](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: Stellt eine experimentelle Voreinstellung für die inhaltsbezogene Codierung zur Verfügung. Anhand der eingegebenen Inhalte versucht der Dienst, automatisch die optimale Anzahl der Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming zu bestimmen. Die zugrunde liegenden Algorithmen werden im Laufe der Zeit weiter entwickelt. Die Ausgabe enthält MP4-Dateien mit überlappendem Video und Audio. Weitere Informationen finden Sie unter [Experimentelle Voreinstellung für inhaltsbezogene Codierung](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: Erzeugt einen Satz von acht MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 6000 Kbit/s und 400 Kbit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 1080p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: Erzeugt einen Satz von sechs MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 3400 Kbit/s und 400 Kbit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 720p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: Erzeugt einen Satz von fünf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 1600 Kbit/s und 400 Kbit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 480p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec bei 6750 KBit/s und einer Bildhöhe von 1080 Pixeln codiert wird, während das Stereo-Audio mit dem AAC-LC-Codec bei 64 KBit/s codiert wird.
- **EncoderNamedPreset.H264SingleBitrate720p**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec bei 4500 KBit/s und einer Bildhöhe von 720 Pixeln codiert wird, während das Stereo-Audio mit dem AAC-LC-Codec bei 64 KBit/s codiert wird.
- **EncoderNamedPreset.H264SingleBitrateSD**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec bei 2200 KBit/s und einer Bildhöhe von 480 Pixeln codiert wird, während das Stereo-Audio mit dem AAC-LC-Codec bei 64 KBit/s codiert wird.

Die aktuellste Liste der Voreinstellungen finden Sie unter [Integrierte Voreinstellungen zum Codieren von Videos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Informationen zur Verwendung der Voreinstellungen finden Sie unter [Hochladen, Codieren und Streamen von Dateien](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Voreinstellung „StandardEncoderPreset“

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beschreibt die Einstellungen, die beim Codieren des Eingabevideos mit dem Standard-Encoder verwendet werden sollen. Verwenden Sie diese Voreinstellung, wenn Sie Transformationseinstellungen anpassen. 

#### <a name="considerations"></a>Überlegungen

Beim Erstellen von benutzerdefinierten Voreinstellungen gelten die folgenden Überlegungen:

- Alle Werte für Höhe und Breite in AVC-Inhalt müssen ein Vielfaches von 4 sein.
- In Azure Media Services v3 werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen bei unseren v2-APIs. Dort wurden Kilobits pro Sekunde (KBit/s) als Einheit verwendet. Wenn beispielsweise die Bitrate in v2 als 128 (Kilobits/Sekunde) angegeben wurde, würde sie in v3 auf 128.000 (Bits/Sekunde) festgelegt.

#### <a name="examples"></a>Beispiele

Media Services unterstützt die vollständige Anpassung aller Werte in Voreinstellungen zum Erfüllen Ihrer spezifischen Codierungsanforderungen. Beispiele zum Anpassen von Encodervoreinstellungen finden Sie unter:

- [Anpassen von Voreinstellungen mit .NET](customize-encoder-presets-how-to.md)
- [Anpassen von Voreinstellungen mit der CLI](custom-preset-cli-howto.md)
- [Anpassen von Voreinstellungen mit REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Voreinstellungsschemas

In Media Services v3 sind Voreinstellungen stark typisierte Entitäten in der API selbst. Die „Schema“-Definition für diese Objekte finden Sie in [Offene API-Spezifikation (oder Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Sie können die voreingestellten Definitionen (wie **StandardEncoderPreset**) auch in der [REST-API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), im [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) oder eine andere Referenzdokumentation für das Media Services v3 SDK anzeigen.

## <a name="scaling-encoding-in-v3"></a>Skalieren der Codierung in v3

Informationen zum Skalieren der Medienverarbeitung finden Sie unter [Skalieren mit CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Codieren aus einer HTTPS-URL mithilfe von integrierten Voreinstellungen](job-input-from-http-how-to.md)
* [Codieren einer lokalen Datei mithilfe von integrierten Voreinstellungen](job-input-from-local-file-how-to.md)
* [Entwickeln einer benutzerdefinierten Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen](customize-encoder-presets-how-to.md)
* [Hochladen, Codieren und Streamen mit Media Services](stream-files-tutorial-with-api.md)
