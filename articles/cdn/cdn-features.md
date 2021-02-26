---
title: Vergleichen von Produktfeatures des Azure Content Delivery Network (CDN)
description: Hier finden Sie Informationen zur Featureunterstützung der Azure CDN-Produkte (Content Delivery Network).
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3a794d8915c820abecd530911f05ac649e5e538c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379912"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Wie sehen die Vergleiche zwischen Azure CDN-Produktfeatures aus?

Azure Content Delivery Network (CDN) beinhaltet vier Produkte: 

* **Azure CDN Standard von Microsoft**
* **Azure CDN Standard von Akamai**
* **Azure CDN Standard von Verizon**
* **Azure CDN Premium von Verizon** 

In der folgenden Tabelle werden die Features der einzelnen Produkte gegenübergestellt.

| **Leistungsfeatures und -optimierungen** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Beschleunigung dynamischer Websites](./cdn-dynamic-site-acceleration.md)  | Angeboten über [Azure Front Door Service](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Beschleunigung dynamischer Websites – Adaptive Bildkomprimierung](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Beschleunigung dynamischer Websites – Objektvorabruf](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimierung der allgemeinen Webbereitstellung](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** . Wählen Sie diesen Optimierungstyp aus, wenn ihre Dateien im Schnitt kleiner als 10 MB sind.  | **&#x2713;** |  **&#x2713;** |
| [Videostreamingoptimierung](./cdn-media-streaming-optimization.md)  | über allgemeine Webbereitstellung | **&#x2713;**  | über allgemeine Webbereitstellung |  über allgemeine Webbereitstellung |
| [Optimierung großer Dateien](./cdn-large-file-optimization.md)  | über allgemeine Webbereitstellung | **&#x2713;** . Wählen Sie diesen Optimierungstyp aus, wenn ihre Dateien im Schnitt größer als 10 MB sind.   | über allgemeine Webbereitstellung |  über allgemeine Webbereitstellung |
| Ändern des Optimierungstyps | |**&#x2713;** | | |
| Ursprungsport |Alle TCP-Ports |[Zulässige Ursprungsports](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alle TCP-Ports |Alle TCP-Ports |
| [Globaler Serverlastenausgleich (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Schnelles Löschen](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** . „Alles löschen“ und das Löschen mit Platzhalter werden derzeit vom Azure-CDN von Akamai nicht unterstützt. |**&#x2713;** |**&#x2713;** |
| [Vorabladen von Assets](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Cache-/Headereinstellungen (mit [Zwischenspeicherungsregeln](cdn-caching-rules.md))  |**&#x2713;** mit [Standard-Regel-Engine](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Anpassbare, regelbasierte Engine zur Inhaltsübermittlung |**&#x2713;** mit [Standard-Regel-Engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** mit [Regel-Engine](./cdn-verizon-premium-rules-engine.md) |
| Cache-/Headereinstellungen  |**&#x2713;** mit [Standard-Regel-Engine](cdn-standard-rules-engine.md) | | |**&#x2713;** mit [Premium-Regel-Engine](./cdn-verizon-premium-rules-engine.md) |
| URL-Umleitung/-Rewrite |**&#x2713;** mit [Standard-Regel-Engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** mit [Premium-Regel-Engine](./cdn-verizon-premium-rules-engine.md) |
| Regeln für mobile Geräte  |**&#x2713;** mit [Standard-Regel-Engine](cdn-standard-rules-engine.md) | | |**&#x2713;** mit [Premium-Regel-Engine](./cdn-verizon-premium-rules-engine.md) |
| [Zwischenspeicherung von Abfragezeichenfolgen](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dualer IPv4-/IPv6-Stapel | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-Unterstützung](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Security** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Unterstützung von HTTPS mit CDN-Endpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS für benutzerdefinierte Domänen](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , erfordert die Aktivierung der direkten CNAME-Zuordnung |**&#x2713;** |**&#x2713;** |
| [Unterstützung benutzerdefinierter Domänennamen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geofilterung](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenauthentifizierung](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-Schutz](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Bereitstellen eines eigenen Zertifikats](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Unterstützte TLS-Versionen | TLS 1.2, TLS 1.0/1.1 ([konfigurierbar](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters)) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
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
| Einfache Integration mit Azure-Diensten wie [Storage](cdn-create-a-storage-account-with-cdn.md), [Web-Apps](cdn-add-to-web-app.md) und [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Verwaltung per [REST-API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oder [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [MIME-Typen für die Komprimierung](./cdn-improve-performance.md)  |Nur Standard |Konfigurierbar |Konfigurierbar  |Konfigurierbar  |
| Komprimierungscodierungen  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## <a name="migration"></a>Migration

Informationen zur Migration eines Profils vom Typ **Azure CDN Standard von Verizon** zu **Azure CDN Premium von Verizon** finden Sie unter [Migrieren eines Azure CDN-Profils von Verizon Standard zu Verizon Premium](cdn-migrate.md). 

> [!NOTE]
> Es gibt eine Upgrademöglichkeit von Verizon Standard auf Verizon Premium, zwischen anderen Produkten ist jedoch derzeit keine Konvertierung möglich.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure CDN](cdn-overview.md).
