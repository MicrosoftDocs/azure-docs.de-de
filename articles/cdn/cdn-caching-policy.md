---
title: Verwalten von Azure CDN-Zwischenspeicherungsrichtlinien in Azure Media Services | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Azure CDN-Zwischenspeicherungsrichtlinien in Azure Media Services verwalten.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779134"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Verwalten von Azure CDN-Zwischenspeicherungsrichtlinien in Azure Media Services
Azure Media Services bietet HTTP-basiertes adaptives Streaming und progressive Downloads. HTTP-basiertes Streaming ist überaus skalierbar und bietet den Vorteil, dass Dateien auf Proxy- und CDN-Ebene sowie clientseitig zwischengespeichert werden können. Streamingendpunkte bieten allgemeine Streamingfunktionen sowie Konfigurationsoptionen für HTTP-Cacheheader. Streamingendpunkte legen die HTTP-Cachesteuerungsheader "max-age" und "Expires" fest. Weitere Informationen zu HTTP-Cacheheadern finden Sie unter [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standardcachingheader
Standardmäßig wenden Streamingendpunkte Cacheheader zum Zwischenspeichern für 3 Tage auf On-Demand-Streamingdaten (tatsächliche Medienfragmente/-segmente) und Manifeste (Wiedergabelisten) an. Beim Livestreaming wenden Streamingendpunkte Cacheheader zum Zwischenspeichern für 3 Tage auf Daten (tatsächliche Medienfragmente/-segmente) und für 2 Sekunden auf Manifeste (Wiedergabelisten) an. Wenn ein Liveprogramm in ein On-Demand-Programm umgewandelt wird (Livearchiv), werden die Cacheheader für On-Demand-Streaming angewendet.

## <a name="azure-cdn-integration"></a>Azure CDN-Integration
Azure Media Services bietet ein [integriertes CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) für Streamingendpunkte. Cachesteuerungsheader werden auf CDN-aktivierte Streamingendpunkte genauso angewendet wie Streamingendpunkte. Azure CDN verwendet für Streamingendpunkte konfigurierte Cachewerte, um die Lebensdauer der intern zwischengespeicherten Objekte zu definieren, und verwendet diesen Wert, um die Übermittlungscacheheader festzulegen. Bei der Verwendung von CDN-aktivierten Streamingendpunkten empfiehlt es sich nicht, kleine Cachewerte festzulegen. Das Festlegen kleiner Werte senkt die Leistung und mindert die Vorteile von CDN. Es ist nicht zulässig, Cacheheader für CDN-aktivierte Streamingendpunkte auf Werte unterhalb von 600 Sekunden festzulegen.

> [!IMPORTANT]
>Azure Media Services kann vollständig in Azure CDN integriert werden. Mit nur einem Mausklick können Sie alle verfügbaren Azure CDN-Anbieter in Ihren Streamingendpunkt integrieren, einschließlich Standard- und Premium-Produkte. Weitere Informationen dazu finden Sie in [dieser Ankündigung](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Datengebühren für die Übertragung von Daten vom Streamingendpunkt an das CDN fallen nur dann nicht an, wenn das CDN über Streamingendpunkt-APIs oder über den Abschnitt „Streamingendpunkt“ im Azure-Portal aktiviert wird. Bei einer manuellen Integration oder der direkten Erstellung eines CDN-Endpunkts über CDN-APIs oder den Portalabschnitt fallen Datengebühren an.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurieren von Cacheheadern mit Azure Media Services
Sie können das Azure-Portal oder Azure Media Services-APIs verwenden, um Cacheheaderwerte zu konfigurieren.

1. Informationen zum Konfigurieren von Cacheheadern über das Azure-Portal finden Sie unter [Verwalten von Streamingendpunkten](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) im Abschnitt „Konfigurieren des Streamingendpunkts“.
2. Azure Media Services-REST-API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl-Eigenschaften](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Rangfolge der Cachekonfiguration
1. In Azure Media Services konfigurierte Cachewerte überschreiben Standardwerte.
2. Wenn keine manuelle Konfiguration vorhanden ist, gelten die Standardwerte.
3. Cacheheader zum Zwischenspeichern für 2 Sekunden werden unabhängig von der Azure Media- oder Azure Storage-Konfiguration auf Livestreamingmanifeste (Wiedergabelisten) angewendet. Dieser Wert kann nicht überschrieben werden.