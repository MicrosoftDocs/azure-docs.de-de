---
title: Media Services-Architekturen
titleSuffix: Azure Media Services
description: In diesem Artikel werden Architekturen für Media Services beschrieben.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: c14dbe4253696e01e99111a24319e455c1d551e9
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533994"
---
# <a name="media-services-architectures"></a>Media Services-Architekturen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Livestreaming von digitalen Medien

Mit einer Livestreaminglösung können Sie Videos in Echtzeit erfassen und in Echtzeit an Kunden übertragen, z. B. als Onlinestreaming von Interviews, Konferenzen und Sportveranstaltungen. In dieser Lösung wird das Video von einer Videokamera erfasst und an einen Eingabeendpunkt des Kanals gesendet. Der Kanal empfängt den Liveeingabestream und stellt ihn zum Streamen über einen Streamingendpunkt für einen Webbrowser oder eine mobile App zur Verfügung. Zudem bietet der Kanal einen Vorschau-Überwachungsendpunkt bereit, über den eine Vorschau des Streams angezeigt und dieser geprüft werden kann, bevor er weiter verarbeitet und übermittelt wird. Der Kanal kann den verarbeiteten Inhalt auch für ein späteres Streaming (Video-on-Demand) aufzeichnen und speichern.

Diese Lösung basiert auf den folgenden verwalteten Azure-Diensten: Media Services und Content Delivery Network. Diese Dienste werden in einer Hochverfügbarkeitsumgebung ausgeführt, gepatcht und unterstützt, sodass Sie sich auf die Lösung selbst konzentrieren können und nicht auf die Umgebung, in der sie ausgeführt werden, konzentrieren müssen.

Weitere Informationen finden Sie im Azure Architecture Center unter [Livestreaming von digitalen Medien](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream).

## <a name="video-on-demand-digital-media"></a>Digitale Video on Demand-Medien

Eine einfache Video-on-Demand-Lösung, die Ihnen die Möglichkeit bietet, aufgezeichnete Videoinhalte wie Filme, Nachrichten-, Sportbeiträge, Schulungsvideos und Tutorials für den Kundensupport an alle videofähigen Endgeräte, mobilen Anwendungen oder Desktopbrowser zu streamen. Videodateien werden in Azure Blob Storage hochgeladen, in ein Standardformat mit mehreren Bitraten codiert und dann über alle wichtigen Streamingprotokolle mit adaptiver Bitrate (HLS, MPEG-DASH, Smooth) an den Azure Media Player-Client verteilt.

Diese Lösung basiert auf den folgenden verwalteten Azure-Diensten: Blob Storage, Content Delivery Network und Azure Media Player. Diese Dienste werden in einer Hochverfügbarkeitsumgebung ausgeführt, gepatcht und unterstützt, sodass Sie sich auf die Lösung selbst konzentrieren können und nicht auf die Umgebung, in der sie ausgeführt werden, konzentrieren müssen.

Weitere Informationen finden Sie im Azure Architecture Center unter [Digitale Video on Demand-Medien](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video).

## <a name="gridwich-media-processing-system"></a>Gridwich-Medienverarbeitungssystem

Das Gridwich-System umfasst Best Practices für die Verarbeitung und Veröffentlichung von Medienobjekten in Azure. Obwohl das Gridwich-System medienspezifisch ist, kann das Framework für Nachrichtenverarbeitung und Ereignisse auf jeden beliebigen zustandslosen Ereignisverarbeitungsworkflow angewendet werden.

Weitere Informationen finden Sie im Azure Architecture Center unter [Gridwich-Medienverarbeitungssystem](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture).

## <a name="next-steps"></a>Nächste Schritte

> [Azure Media Services – Übersicht](media-services-overview.md)