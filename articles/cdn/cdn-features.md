---
title: Vergleichen von Produktfeatures des Azure Content Delivery Network (CDN) | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Featureunterstützung der Azure CDN-Produkte (Content Delivery Network).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5aa342700c8440e67987cd9d1b45574c02a45f47
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729814"
---
# <a name="compare-azure-cdn-product-features"></a>Vergleichen von Azure CDN-Produktfeatures

Azure Content Delivery Network (CDN) beinhaltet vier Produkte: **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Akamai**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon**. Informationen zur Migration eines Profils vom Typ **Azure CDN Standard von Verizon** zu **Azure CDN Premium von Verizon** finden Sie unter [Migrieren eines Azure CDN-Profils von Verizon Standard zu Verizon Premium](cdn-migrate.md).

In der folgenden Tabelle werden die Features der einzelnen Produkte gegenübergestellt.

| **Leistungsfeatures und -optimierungen** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Beschleunigung dynamischer Websites](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Angeboten über [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Beschleunigung dynamischer Websites – Adaptive Bildkomprimierung](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Beschleunigung dynamischer Websites – Objektvorabruf](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimierung der allgemeinen Webbereitstellung](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** . Wählen Sie diesen Optimierungstyp aus, wenn ihre Dateien im Schnitt kleiner als 10 MB sind.  | **&#x2713;** |  **&#x2713;** |
| [Videostreamingoptimierung](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | über allgemeine Webbereitstellung | **&#x2713;**  | über allgemeine Webbereitstellung |  über allgemeine Webbereitstellung |
| [Optimierung großer Dateien](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | über allgemeine Webbereitstellung | **&#x2713;** . Wählen Sie diesen Optimierungstyp aus, wenn ihre Dateien im Schnitt größer als 10 MB sind.   | über allgemeine Webbereitstellung |  über allgemeine Webbereitstellung |
| Ändern des Optimierungstyps | |**&#x2713;** | | |
| Ursprungsport |Alle TCP-Ports |[Zulässige Ursprungsports](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alle TCP-Ports |Alle TCP-Ports |
| [Globaler Serverlastenausgleich (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Schnelles Löschen](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** . „Alles löschen“ und das Löschen mit Platzhalter werden derzeit vom Azure-CDN von Akamai nicht unterstützt. |**&#x2713;** |**&#x2713;** |
| [Vorabladen von Assets](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Cache-/Headereinstellungen (mit [Zwischenspeicherungsregeln](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Anpassbare, regelbasierte Engine zur Inhaltsübermittlung (mit [Regel-Engine](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Cache-/Headereinstellungen (mit [Regel-Engine](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| URL-Umleitung/-Umschreibung (mit [Regel-Engine](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regeln für mobile Geräte (mit [Regel-Engine](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Zwischenspeicherung von Abfragezeichenfolgen](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dualer IPv4-/IPv6-Stapel | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-Unterstützung](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sicherheit** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Unterstützung von HTTPS mit CDN-Endpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS für benutzerdefinierte Domänen](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , erfordert die Aktivierung der direkten CNAME-Zuordnung |**&#x2713;** |**&#x2713;** |
| [Unterstützung benutzerdefinierter Domänennamen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geofilterung](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenauthentifizierung](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-Schutz](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Bereitstellen eines eigenen Zertifikats](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Analysen und Berichte** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Azure-Diagnoseprotokolle](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Kernberichte aus Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Benutzerdefinierte Berichte aus Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Echtzeitstatistiken](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Edgeknotenleistung](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Warnungen in Echtzeit](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Benutzerfreundlichkeit** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Einfache Integration mit Azure-Diensten wie [Storage](cdn-create-a-storage-account-with-cdn.md), [Web-Apps](cdn-add-to-web-app.md) und [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Verwaltung per [REST-API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oder [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [MIME-Typen für die Komprimierung](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Nur Standard |Konfigurierbar |Konfigurierbar  |Konfigurierbar  |
| Komprimierungscodierungen  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






