---
title: Häufig gestellte Fragen zu Azure Media Services | Microsoft-Dokumentation
description: Häufig gestellte Fragen (FAQs)
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3164445dc28a2436d8492902d865414da8d75702
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463905"
---
# <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen behandelt, die in der Benutzercommunity von Azure Media Services (AMS) aufgekommen sind.

## <a name="general-ams-faqs"></a>Allgemeine häufig gestellte Fragen zu AMS

F: Wie funktioniert das Streaming auf Apple iOS-Geräten?

A: Fügen Sie dem Abschnitt „/Manifest“ der URL den Pfad „(format=m3u8-aapl)“ hinzu, um den Ursprungsserver des Streamings anzuweisen, HLS-Inhalte für die Nutzung auf nativen Apple iOS-Geräten zurückzugeben. (Details hierzu finden Sie unter [Übermitteln von Inhalten](media-services-deliver-content-overview.md).)

F: Wie wird die Indizierung skaliert?

A: Die reservierten Einheiten für Codierungs- und Indizierungsaufgaben sind identisch. Folgen Sie den Anweisungen unter [Skalieren von für die Codierung reservierten Einheiten](media-services-scale-media-processing-overview.md). **Hinweis** : Die Indizierungsleistung für Typen reservierter Einheiten ist nicht herabgesetzt.

F: Ich habe ein Video hochgeladen, codiert und veröffentlicht. Was könnte die Ursache dafür sein, dass das Video nicht wiedergegeben wird, wenn ich versuche, es zu streamen?

A: Einer der häufigsten Gründe ist, dass der Streamingendpunkt, von dem Sie die Wiedergabe versuchen, nicht den Status **Wird ausgeführt** aufweist.  

F: Ist ein Zusammensetzen bei einem Livestream möglich?

A: Das Zusammensetzen (Compositing) von Livestreams wird derzeit nicht in Azure Media Services angeboten, Sie müssen dies daher im Vorfeld auf Ihrem Computer durchführen.

F: Kann ich das Azure CDN für das Livestreaming verwenden?

A: Media Services unterstützt die Integration im Azure CDN. (Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-portal-manage-streaming-endpoints.md).)  Sie können Livestreaming mit CDN verwenden. Azure Media Services ermöglicht die Smooth Streaming-, HLS- und MPEG-DASH-Ausgabe. Alle diese Formate verwenden HTTP zum Übertragen von Daten und bieten damit die Vorteile der HTTP-Zwischenspeicherung. Beim Livestreaming werden die eigentlichen Video- oder Audiodaten in Fragmente aufgeteilt, da dann einzeln im CDN zwischengespeichert werden. Die einzigen zu aktualisierenden Daten sind die Manifestdaten. CDN aktualisiert die Manifestdaten regelmäßig.

F: Unterstützt Azure Media Services das Speichern von Bildern?

A: Wenn Sie nur JPEG- oder PNG-Bilder speichern möchten, sollten Sie diese in Azure Blob Storage belassen. Es ergeben sich keine Vorteile daraus, sie in Ihrem Media Services-Konto zu speichern, außer Sie möchten sie zusammen mit Ihren Video- oder Audio-Medienobjekten aufbewahren. Dies gilt auch, wenn Sie die Bilder als Overlays im Video-Encoder verwenden möchten. Media Encoder Standard unterstützt die Überlagerung von Videos mit Bildern, und daher werden JPEG und PNG auch als unterstützte Eingabeformate aufgelistet. Weitere Informationen finden Sie unter [Erstellen von Überlagerungen](media-services-advanced-encoding-with-mes.md#overlay).

F: Wie kann ich Medienobjekte von einem Media Services-Konto in ein anderes kopieren?

A: Um Medienobjekte unter Verwendung von .NET von einem Media Services-Konto in ein anderes zu kopieren, verwenden Sie die Erweiterungsmethode [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354), die im Repository [Azure Media Services .NET SDK-Erweiterungen](https://github.com/Azure/azure-sdk-for-media-services-extensions/) verfügbar ist. Weitere Informationen finden Sie in [diesem](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) Forumsthread.

F: Welche Zeichen werden in Dateinamen bei der Arbeit mit AMS unterstützt?

A: Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der IAssetFile.Name-Eigenschaft (z. B. http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Aus diesem Grund ist die Prozentkodierung nicht zulässig. Der Wert der **Name**-Eigenschaft darf keines der folgenden [für die Prozentcodierung reservierten Zeichen](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) enthalten: ! * '();:@&=+$,/?%#[]". Darüber hinaus darf „.“ nur einmal für die Dateinamenerweiterung vorhanden sein.

F: Wie wird eine Verbindung mithilfe von REST hergestellt?

A: Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

F: Wie kann ich ein Video während des Codierungsvorgangs drehen?

A: Der [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) unterstützt Drehungen in den Winkeln 90/180/270 Grad. Das Standardverhalten ist „Auto“. Dabei wird versucht, die Rotationsmetadaten in der eingehenden MP4-/MOV-Datei zu erkennen und auszugleichen. Schließen Sie das folgende **Sources**-Element in einer der [hier](media-services-mes-presets-overview.md) definierten JSON-Voreinstellungen ein:

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
