---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 2/17/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: d34cfd31f9648ce6f401c3936a0a28be4729f8e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101705473"
---
<!--Create a thumbnail sprites REST-->

Der folgende Azure-REST-Befehl erstellt eine Transformation für Miniaturbild-Sprites. Ersetzen Sie die Werte `subscriptionID`, `resourceGroup` und `accountName` durch aktuell verwendete Werte. 

Weisen Sie Ihrer Transformation einen Namen zu, indem Sie `transformName` festlegen.

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version={{api-version}}

```

## <a name="body"></a>Text

```json
{
    "properties": {
    "description": "Basic Transform using the Standard Encoder, JpgImage, JpgLayer, and JpgFormat ",
    "outputs": [{
        "onError": "StopProcessingJob",
        "relativePriority": "Normal",
        "preset": {
            "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
            "Codecs": [{
                "@odata.type": "#Microsoft.Media.JpgImage",
                "start": "1",
                "step": "5%",
                "range": "100%",
                "spriteColumn": 10,
                "layers": [{
                    "@odata.type" : "#Microsoft.Media.JpgLayer",
                    "width": "20%",
                    "height": "20%",
                    "quality" : 90
                }]
                }],
            "formats":[{
                "@odata.type" : "#Microsoft.Media.JpgFormat",
                "filenamePattern":"ThumbnailSprite-{Basename}-{Index}{Extension}"
                }]
            }
        }]
    }
}
```

## <a name="sample-response"></a>Beispiel für eine Antwort
```json
{
    "name": "thumbnailsprites",
    "id": "/subscriptions/<your subscription ID>/resourceGroups/<your resource group name/providers/Microsoft.Media/mediaservices/<your account name>/transforms/thumbnailsprites",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2021-02-17T21:56:06.6213416Z",
        "description": "Basic Transform using the Standard Encoder, JpgImage, JpgLayer, and JpgFormat ",
        "lastModified": "2021-02-17T21:57:17.3241984Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.JpgImage",
                            "stretchMode": "AutoSize",
                            "syncMode": "Auto",
                            "start": "1",
                            "step": "5%",
                            "range": "100%",
                            "layers": [
                                {
                                    "width": "20%",
                                    "height": "20%",
                                    "quality": 90
                                }
                            ],
                            "spriteColumn": 10
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.JpgFormat",
                            "filenamePattern": "ThumbnailSprite-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}
```
