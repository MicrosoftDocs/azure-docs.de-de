---
title: Erstellen eines Medienprozessors mithilfe des Azure Media Services-SDK für .NET| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463836"
---
# <a name="how-to-get-a-media-processor-instance"></a>Gewusst wie: Abrufen einer Medienprozessorinstanz
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Übersicht
Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

## <a name="azure-media-processors"></a>Azure-Medienprozessoren 

Das folgende Thema enthält Listen von Medienprozessoren:

* [Codieren von Medienprozessoren](scenarios-and-availability.md#encoding-media-processors)
* [Analytics-Medienprozessoren](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Abrufen von Medienprozessoren

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Das folgende Codebeispiel setzt eine Variable auf Modulebene mit dem Namen **_context** voraus, die Zugriff auf den Serverkontext bietet. Weitere Informationen finden Sie im Abschnitt [Programmgesteuertes Herstellen einer Verbindung mit Media Services](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessorinstanz zu erstellen, und können mit dem Thema [Codieren von Medienobjekten](media-services-dotnet-encode-with-media-encoder-standard.md) fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Media Encoder Standard codieren können.

