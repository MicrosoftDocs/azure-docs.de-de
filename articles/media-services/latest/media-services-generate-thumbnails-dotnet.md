---
title: Generieren von Miniaturansichten mithilfe von Media Encoder Standard mit .NET
description: In diesem Artikel sehen Sie, wie Sie mithilfe von .NET ein Objekt codieren und gleichzeitig Vorschauminiaturen mithilfe des Media Encoder Standards generieren können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 551574685ec9a2f9eb1ef569fbec7c9a104353ff
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955867"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Generieren von Vorschauminiaturen mithilfe des Media Encoder Standards mit .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Sie können Media Encoder Standard verwenden, um aus den Eingangsvideodaten eine oder mehrere Miniaturansichten im Bilddateiformat [JPEG](https://en.wikipedia.org/wiki/JPEG) oder [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) zu generieren.

## <a name="recommended-reading-and-practice"></a>Empfohlene Literatur und Praxis

Es wird empfohlen, dass Sie sich mit benutzerdefinierten Transformationen vertraut machen, indem Sie den Artikel [Codieren mit einer benutzerdefinierten Transformation – .NET](customize-encoder-presets-how-to.md) lesen.

## <a name="transform-code-example"></a>Transformieren eines Codebeispiels

Im folgenden Codebeispiel wird nur eine Vorschauminiatur erstellt.  Sie sollten die folgenden Parameter festlegen:

- **start:** Die Stelle im Eingabevideo, ab der Vorschauminiaturen erstellt werden sollen. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT05S, damit ab fünf Sekunden gestartet wird, oder die Nummer eines Frames, z. B. 10, damit beim zehnten Frame gestartet wird, oder einen relativen Wert zur Dauer des Streams, z. B. 10 %, damit bei zehn Prozent der Gesamtdauer des Streams gestartet wird. Außerdem wird ein Makro namens {Best} unterstützt, das den Encoder anweist, die beste Vorschauminiatur aus den ersten Sekunden des Videos auszuwählen. Dabei wird unabhängig von anderen Einstellungen für „step“ und „range“ nur eine Vorschauminiatur erstellt. Der Standardwert des Makros lautet {Best}.
- **step:** Das Intervall, mit dem Vorschauminiaturen erstellt werden. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT05S, damit ein Bild alle fünf Sekunden erstellt wird, oder die Nummer eines Frames, z. B. 30 für ein Bild alle 30 Frames, oder einen relativen Wert für die Gesamtdauer des Streams, z. B. 10 %, damit ein Bild zu den Zeitpunkten erstellt wird, die jeweils immer zehn Prozent der Gesamtdauer des Streams entsprechen. Der step-Wert wirkt sich auf die erste Vorschauminiatur aus, die erstellt wird. Dabei handelt es sich möglicherweise jedoch nicht um die, die zur vordefinierten Startzeit der Transformation angegeben wurde. Das liegt am Encoder, der versucht, die beste Vorschauminiatur zwischen Startzeit und Schrittposition (bei der Startzeit beginnend) als erste Ausgabe auszuwählen. Der Standardwert beträgt 10 %, d. h., die erste erstellte Vorschauminiatur kann sich weit von dem zur Startzeit angegebenen entfernt befindet, wenn der Stream eine lange Dauer aufweist. Wählen Sie einen sinnvollen step-Wert aus, wenn zu erwarten ist, dass sich die erste Vorschauminiatur in der Nähe der Startzeit befindet, oder legen Sie den range-Wert auf 1 fest, wenn zur Startzeit nur eine Vorschauminiatur erforderlich ist.
- **range:** Die relative Position zur Transformation der vordefinierten Startzeit im Eingabevideo, bei der die Erzeugung von Vorschauminiaturen beendet werden soll. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT5M30S, damit ab fünf Minuten und 30 Sekunden nach Startzeit angehalten wird, oder die Nummer eines Frames, z. B. 300, damit beim dreihundertsten Frame ab dem Frame zur Startzeit angehalten wird. Wenn dieser Wert 1 ist, bedeutet dies, dass nur eine Vorschauminiatur zur Startzeit erzeugt wird, oder ein relativer Wert zur Streamdauer, z. B. 50 %, um bei der Hälfte der Streamdauer ab Startzeit anzuhalten. Der Standardwert ist 100 %. Dies bedeutet, dass am Ende des Streams angehalten werden soll.
- **layers:** Eine Sammlung von Ausgabebildebenen, die vom Encoder erzeugt werden sollen.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Nächste Schritte
[Generieren von Vorschauminiaturen mit REST](media-services-generate-thumbnails-rest.md)
