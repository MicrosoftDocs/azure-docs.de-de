---
title: Erstellen von Subclips für ein Video beim Codieren mit Media Services
description: In diesem Artikel wird beschrieben, wie Sie beim Codieren mit Azure Media Services mithilfe des .NET SDK Subclips für ein Video erstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a9408adfe33fc71707532f3bf5646f3727fd7dda
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955053"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Erstellen von Subclips für ein Video beim Codieren mit Media Services: .NET

Sie können mithilfe eines [Auftrags](/rest/api/media/jobs) Videos beim Codieren zuschneiden oder Subclips für diese erstellen. Dies funktioniert mit jeder [Transformation](/rest/api/media/transforms), die entweder mit den [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)-Voreinstellungen oder mit den [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)-Voreinstellungen erstellt wird.

Mithilfe des folgenden C#-Beispiels wird ein Auftrag erstellt, der ein Video in ein Objekt zuschneidet, sobald er einen Codierungsauftrag übermittelt. 

## <a name="prerequisites"></a>Voraussetzungen

Für die in diesem Thema beschriebenen Schritte ist Folgendes erforderlich:

- [Erstellen eines Azure Media Services-Kontos](./create-account-howto.md)
- Erstellen Sie eine Transformation sowie ein Eingabe- und ein Ausgabeobjekt. Informationen zum Erstellen einer Transformation sowie von Eingabe- und Ausgabeobjekten finden Sie im Tutorial [Hochladen, Codieren und Streamen von Videos](stream-files-tutorial-with-api.md).
- Lesen Sie sich den Artikel [Encoding concept (Codierungskonzept)](encoding-concept.md) durch.

## <a name="example"></a>Beispiel

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Nächste Schritte

[Codieren mit einer benutzerdefinierten Transformation](customize-encoder-presets-how-to.md) 
