---
title: Codieren von Videos mit dem Standard-Encoder in Media Services
description: In diesem Thema wird gezeigt, wie Sie in Media Services mit dem Standard-Encoder ein Eingabevideo mithilfe einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren auf Basis der eingegebenen Auflösung und Bitrate codieren können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5b973d17e10f3dbb75f5208d9003b4f8118b37c7
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98891402"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codieren mit einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Übersicht

In diesem Artikel wird gezeigt, wie Sie in Media Services mit dem Standard-Encoder ein Eingabevideo in eine automatisch generierte Reihe von Bitraten-/Auflösungspaaren auf Basis der eingegebenen Auflösung und Bitrate codieren können. Diese Voreinstellung des Encoders übersteigt in keinem Fall die eingegebene Auflösung und Bitrate. Ist die Eingabe beispielsweise 720p bei 3 MBit/s, bleibt die Ausgabe bestenfalls bei 720p, und es beginnt mit niedrigeren Raten als 3 MBit/s.

### <a name="encoding-for-streaming"></a>Codierung für Streaming

Wenn Sie die Voreinstellung **AdaptiveStreaming** beim **Transformieren** verwenden, erhalten Sie eine Ausgabe, die für die Bereitstellung über Streamingprotokolle wie HLS und DASH geeignet ist. Bei Verwendung dieser Voreinstellung legt der Dienst intelligent fest, wie viele Videoebenen mit welcher Bitrate und Auflösung erzeugt werden sollen. Die Ausgabe enthält MP4-Dateien, in denen mit AAC codierte Audio- und mit H.264 codierte Videodaten sich nicht überlappen.

Ein Beispiel für die Verwendung dieser Voreinstellung finden Sie unter [Streamen einer Datei](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Output

Dieser Abschnitt zeigt drei Beispiele von Ausgabevideoebenen, die vom Media Services-Encoder als Ergebnis der Codierung mit der Voreinstellung **Adaptives Streaming** erzeugt wurden. In allen Fällen enthält die Ausgabe eine reine MP4-Audiodatei, die mit 128 KBit/s codiertes Audio in Stereo enthält.

### <a name="example-1"></a>Beispiel 1
Die Quelle mit der Höhe „1080“ und der Bildrate „29,970“ produziert 6 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Beispiel 2
Die Quelle mit der Höhe „720“ und der Bildrate „23,970“ produziert 5 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Beispiel 3
Die Quelle mit der Höhe „360“ und der Bildrate „29,970“ produziert 3 Videoebenen:

|Ebene|Höhe|Breite|Bitrate (KBit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Streamen einer Datei](stream-files-dotnet-quickstart.md)
