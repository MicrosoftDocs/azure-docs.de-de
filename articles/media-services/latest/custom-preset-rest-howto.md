---
title: 'Codieren mit einer benutzerdefinierten Transformation: REST'
description: In diesem Thema wird gezeigt, wie mit Azure Media Services v3 eine benutzerdefinierte Transformation mithilfe von REST codiert wird.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2c0416e2787bc69465e18a71f6b9912f7571c24d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956088"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Codieren mit einer benutzerdefinierten Transformation: REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bei der Codierung mit Azure Media Services können Sie mit einer der empfohlenen integrierten Voreinstellungen, basierend auf in der Branche bewährten Vorgehensweisen, schnell einsteigen, wie im [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über REST](stream-files-tutorial-with-rest.md#create-a-transform) gezeigt. Sie können auch eine benutzerdefinierte Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen entwickeln.

## <a name="considerations"></a>Überlegungen

Beim Erstellen von benutzerdefinierten Voreinstellungen gelten die folgenden Überlegungen:

* Alle Werte für Höhe und Breite in AVC-Inhalt müssen ein Vielfaches von 4 sein.
* In Azure Media Services v3 werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen bei unseren v2-APIs. Dort wurden Kilobits pro Sekunde (KBit/s) als Einheit verwendet. Wenn beispielsweise die Bitrate in v2 als 128 (Kilobits/Sekunde) angegeben wurde, würde sie in v3 auf 128.000 (Bits/Sekunde) festgelegt.

## <a name="prerequisites"></a>Voraussetzungen 

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) <br/>Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos. 
- [Konfigurieren Sie Postman für Azure Media Services-REST-API-Aufrufe](media-rest-apis-with-postman.md).<br/>Befolgen Sie dabei unbedingt den letzten Schritt im Thema [Abrufen von Azure AD-Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definieren einer benutzerdefinierten Voreinstellung

Im folgenden Beispiel wird der Anforderungstext einer neuen Transformation definiert. Es werden mehrere Ausgaben definiert, die bei Verwendung dieser Transformation generiert werden sollen. 

In diesem Beispiel wird zuerst eine AacAudio-Ebene für die Audiocodierung hinzugefügt, sowie zwei H264Video-Ebenen für die Videocodierung. In den Videoebenen weisen wir Bezeichnungen zu, damit sie in den Ausgabedateinamen verwendet werden können. Als Nächstes soll die Ausgabe auch Miniaturansichten enthalten. Im Beispiel unten geben wir Bilder im PNG-Format, die mit 50% der Auflösung vom Videoeingang generiert wurden, und mit drei Zeitstempeln an – {25%, 50%, 75%} der Länge des Videoeingangs. Schließlich geben wir das Format für die Ausgabedateien an – eine für Video+Audio und eine andere für die Miniaturansichten. Weil wir mehrere H264-Ebenen haben, müssen wir Makros verwenden, die eindeutige Namen pro Ebene generieren. Wir können entweder ein `{Label}`- oder ein `{Bitrate}`-Makro verwenden. Im Beispiel wird erstere Option verwendet.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Erstellen einer neuen Transformation  

In diesem Beispiel wird eine **Transformation** erstellt, die auf der benutzerdefinierten Voreinstellung basiert, die zuvor festgelegt wurde. Beim Erstellen einer Transformation sollten Sie zunächst mit der Methode [Get](/rest/api/media/transforms/get) überprüfen, ob bereits eine Transformation vorhanden ist. Wenn eine Transformation vorhanden ist, verwenden Sie diese. 

Wählen Sie in der heruntergeladenen Sammlung von Postman **Transforms and Jobs** (Transformationen und Aufträge)->**Create or update Transform** (Transformation erstellen oder aktualisieren) aus.

Die **PUT**-HTTP-Anforderungsmethode ähnelt der folgenden:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Wählen Sie die Registerkarte **Text** aus, und fügen Sie den Text mit dem JSON-Code ein, den Sie [zuvor definiert haben](#define-a-custom-preset). Damit die Transformation in Media Services auf das angegebene Video oder die angegebene Audiodatei angewendet wird, müssen Sie unter dieser Transformation einen Auftrag übermitteln.

Wählen Sie **Senden** aus. 

Damit die Transformation in Media Services auf das angegebene Video oder die angegebene Audiodatei angewendet wird, müssen Sie unter dieser Transformation einen Auftrag übermitteln. Ein umfassendes Beispiel, in dem gezeigt wird, wie ein Auftrag unter einer Transformation übermittelt wird, finden Sie unter [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure Media Services API (Azure Media Services-API)](/rest/api/media/).
