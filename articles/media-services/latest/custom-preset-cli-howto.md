---
title: Codieren der benutzerdefinierten Transformation mit Media Services v3 CLI – Azure | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie mit Azure Media Services v3 eine benutzerdefinierte Transformation mithilfe der Befehlszeilenschnittstelle codiert wird.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762027"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Codieren mit einer benutzerdefinierten Transformation: CLI

Bei der Codierung mit Azure Media Services können Sie mit einer der empfohlenen integrierten Voreinstellungen, basierend auf in der Branche bewährten Vorgehensweisen, schnell einsteigen, wie im Schnellstart [Streamingdateien](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) gezeigt. Sie können auch eine benutzerdefinierte Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen entwickeln.

## <a name="considerations"></a>Überlegungen

Beim Erstellen von benutzerdefinierten Voreinstellungen gelten die folgenden Überlegungen:

* Alle Werte für Höhe und Breite in AVC-Inhalt müssen ein Vielfaches von 4 sein.
* In Azure Media Services v3 werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen bei unseren v2-APIs. Dort wurden Kilobits pro Sekunde (KBit/s) als Einheit verwendet. Wenn beispielsweise die Bitrate in v2 als 128 (Kilobits/Sekunde) angegeben wurde, würde sie in v3 auf 128.000 (Bits/Sekunde) festgelegt.

## <a name="prerequisites"></a>Voraussetzungen 

[Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md) <br/>Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definieren einer benutzerdefinierten Voreinstellung

Im folgenden Beispiel wird der Anforderungstext einer neuen Transformation definiert. Es werden mehrere Ausgaben definiert, die bei Verwendung dieser Transformation generiert werden sollen. 

In diesem Beispiel wird zuerst eine AacAudio-Ebene für die Audiocodierung hinzugefügt, sowie zwei H264Video-Ebenen für die Videocodierung. In den Videoebenen weisen wir Bezeichnungen zu, damit sie in den Ausgabedateinamen verwendet werden können. Als Nächstes soll die Ausgabe auch Miniaturansichten enthalten. Im Beispiel unten geben wir Bilder im PNG-Format, die mit 50% der Auflösung vom Videoeingang generiert wurden, und mit drei Zeitstempeln an – {25%, 50%, 75%} der Länge des Videoeingangs. Schließlich geben wir das Format für die Ausgabedateien an – eine für Video+Audio und eine andere für die Miniaturansichten. Weil wir mehrere H264-Ebenen haben, müssen wir Makros verwenden, die eindeutige Namen pro Ebene generieren. Wir können entweder ein `{Label}`- oder ein `{Bitrate}`-Makro verwenden. Im Beispiel wird erstere Option verwendet.

Diese Transformation wird in einer Datei gespeichert. In diesem Beispiel erhält die Datei die Bezeichnung `customPreset.json`. 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}

```

## <a name="create-a-new-transform"></a>Erstellen einer neuen Transformation  

In diesem Beispiel wird eine **Transformation** erstellt, die auf der benutzerdefinierten Voreinstellung basiert, die zuvor festgelegt wurde. Beim Erstellen einer Transformation sollten Sie zunächst prüfen, ob bereits eine vorhanden ist. Wenn eine Transformation vorhanden ist, verwenden Sie diese. Der folgende `show`-Befehl gibt die Transformation `customTransformName` zurück, falls sie vorhanden ist:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Der folgende CLI-Befehl erstellt die Transformation basierend auf der benutzerdefinierten Voreinstellung (die zuvor definiert wurde). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Damit die Transformation in Media Services auf das angegebene Video oder die angegebene Audiodatei angewendet wird, müssen Sie unter dieser Transformation einen Auftrag übermitteln. Ein umfassendes Beispiel, in dem gezeigt wird, wie ein Auftrag unter einer Transformation übermittelt wird, finden Sie unter [Schnellstart: Streamen von Videodateien: CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Weitere Informationen

[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
