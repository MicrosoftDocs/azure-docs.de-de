---
title: Migrieren von Azure Media Services V2 zu V3 | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Änderungen, die in Azure Media Services V3 eingeführt wurden, und zeigt Unterschiede zwischen den beiden Versionen. Dieser Artikel bietet außerdem Hilfestellung bei der Migration von Media Services v2 zu v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, streamen, übertragen, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0cdf4699f5dc00087845ee6ca0d24ad6493c320b
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426871"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Hinweise zur Migration von Media Services v2 zu v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Sie können eine Benachrichtigung erhalten, wann auf dieser Seite Updates vorhanden sind, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` kopieren und in Ihren RSS-Feedreader einfügen.

Dieser Artikel bietet Hilfestellung bei der Migration von Media Services v2 zu v3.

Wenn Sie derzeit über einen Videodienst verfügen, der auf Basis von der [älteren APIs von Media Services v2](../previous/media-services-overview.md) entwickelt wurden, überprüfen Sie vor der Migration zu den v3-APIs die folgenden Richtlinien und Überlegungen. Die v3-API bietet zahlreiche Vorteile und neue Features, die die Entwicklungsumgebung und Funktionen von Media Services verbessern. Wie im Abschnitt [Bekannte Probleme](#known-issues) dieses Artikels erläutert, gibt es auch einige Einschränkungen aufgrund von Änderungen zwischen den API-Versionen. Diese Seite wird laufend aktualisiert, da das Media Services-Team die v3-APIs weiterhin verbessert und die Diskrepanz zwischen den Versionen behebt. 

## <a name="prerequisites"></a>Voraussetzungen

* Informieren Sie sich über [Media Services v2 und v3](media-services-v2-vs-v3.md).
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Vorteile von Media Services v3
  
### <a name="api-is-more-approachable"></a>Besser zugängliche API

*  v3 basiert auf einer vereinheitlichten API-Oberfläche, die sowohl Verwaltungs- als auch Betriebsfunktionen auf der Basis von Azure Resource Manager bereitstellt. Azure Resource Manager-Vorlagen können zum Erstellen und Bereitstellen von Transformationen, Streamingendpunkten, Liveereignissen und mehr verwendet werden.
* Dokument [OpenAPI-Spezifikation (früher als Swagger bezeichnet)](https://aka.ms/ams-v3-rest-sdk).
    Macht das Schema für alle Dienstkomponenten verfügbar, einschließlich dateibasierter Codierung.
* SDKs verfügbar für [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [Go](https://aka.ms/ams-v3-go-ref) und Ruby.
* [Azure CLI](/cli/azure/ams)-Integration für einfache Skriptunterstützung.

### <a name="new-features"></a>Neue Funktionen

* Für dateibasierte Auftragsverarbeitung können Sie als Eingabe eine HTTP(S)-URL verwenden.<br/>Sie müssen noch keine Inhalte in Azure gespeichert haben und müssen auch keine Objekte erstellen.
* Führt das Konzept von [Transformationen](transforms-jobs-concept.md) für dateibasierte Auftragsverarbeitung ein. Eine Transformation kann verwendet werden, um wiederverwendbare Konfigurationen und Azure Resource Manager-Vorlagen zu erstellen, und um Verarbeitungseinstellungen zwischen mehreren Kunden oder Mandanten zu isolieren.
* Ein Medienobjekt kann mehrere [Streaminglocators](streaming-locators-concept.md) mit verschiedenen Einstellungen für die [dynamische Paketerstellung](dynamic-packaging-overview.md) und dynamische Verschlüsselung haben.
* Der [Inhaltsschutz](content-key-policy-concept.md) unterstützt Features mit mehreren Schlüsseln.
* Wenn Media Services zum Transcodieren eines Beitragsfeeds mit Einzelbitrate in einen Ausgabestream mit mehreren Bitraten verwendet wird, können Sie Liveereignisse streamen, die bis zu 24 Stunden lang sind.
* Neue Unterstützung für Livestreaming mit niedriger Latenz für Liveereignisse. Weitere Informationen finden Sie unter [Latenz](live-event-latency.md).
* Die Vorschau von Liveereignissen unterstützt die [dynamische Paketerstellung](dynamic-packaging-overview.md) und dynamische Verschlüsselung. Dadurch wird in der Vorschau der Inhaltsschutz sowie auch die Paketerstellung für DASH und HLS ermöglicht.
* Die Liveausgabe ist einfacher zu verwenden als die Programmentität in den v2-APIs. 
* Verbesserte RTMP-Unterstützung (höhere Stabilität und bessere Unterstützung für Quellcodierer).
* Sichere RTMPS-Erfassung.<br/>Bei der Erstellung eines Liveereignisses erhalten Sie vier Erfassungs-URLs. Die vier Erfassungs-URLs sind nahezu identisch und verfügen über das gleiche Streamingtoken (AppId). Nur der Portnummernteil unterscheidet sich. Zwei der URLs dienen als primäre URL und Backup-URL für RTMPS.   
* Sie verfügen über rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) für Ihre Entitäten. 

## <a name="known-issues"></a>Bekannte Probleme

*  Derzeit können Sie das [Azure-Portal](https://portal.azure.com/) für Folgendes verwenden:

    * Verwalten von Media Services v3-[Liveereignissen](live-events-outputs-concept.md) 
    * Anzeigen (nicht verwalten) von [Medienobjekten](assets-concept.md) der Version 3 
    * [Abrufen von Informationen über den Zugriff auf APIs](./access-api-howto.md) 

    Verwenden Sie für alle anderen Verwaltungsaufgaben (etwa für [Transformationen und Aufträge](transforms-jobs-concept.md) und [Inhaltsschutz](content-protection-overview.md)) die [REST-API](/rest/api/media/), die [CLI](/cli/azure/ams) oder eins der unterstützten [SDKs](media-services-apis-overview.md#sdks).
* Sie müssen in Ihrem Konto reservierte Einheiten für Medien (Media Reserved Units, MRUs) bereitstellen, um die Parallelität und Leistung Ihrer Aufträge zu steuern. Das gilt insbesondere für Aufträge mit Video- oder Audioanalyse. Weitere Informationen finden Sie unter [Übersicht über das Skalieren der Medienverarbeitung](../previous/media-services-scale-media-processing-overview.md). Die MRUs können per [CLI 2.0 für Media Services v3](media-reserved-units-cli-how-to.md), über das [Azure-Portal](../previous/media-services-portal-scale-media-processing.md) oder mithilfe der [v2-APIs](../previous/media-services-dotnet-encoding-units.md) verwaltet werden. MRUs müssen unabhängig davon bereitgestellt werden, ob Sie Media Services v2- oder v3-APIs verwenden.
* Mit der v3-API erstellte Media Services-Entitäten können von der v2-API nicht verwaltet werden.  
* Nicht alle Entitäten in der V2-API werden automatisch in der V3-API angezeigt.  Im Folgenden sind Beispiele für Entitäten in den beiden Versionen aufgeführt, die nicht kompatibel sind:  
    * In v2 erstellte Aufträge und Aufgaben werden in v3 nicht angezeigt, da sie keiner Transformation zugeordnet sind. Es wird empfohlen, zu v3-Transformationen und -Aufträge zu wechseln. Während des Wechsels müssen die aktiven v2-Aufträge für einen relativ kurzen Zeitraum überwacht werden.
    * Mit v2 erstellte Kanäle und Programme (die Liveereignissen und Liveausgaben in v3 zugeordnet sind) können nicht mehr mit v3 verwaltet werden. Es wird empfohlen, zu einem günstigen Zeitpunkt (beim Beenden des Kanals) zu v3-Liveereignissen und -Liveausgaben zu wechseln.<br/>Derzeit können Sie kontinuierlich ausgeführte Kanäle nicht migrieren.  

> [!NOTE]
> Diese Seite wird laufend aktualisiert, da das Media Services-Team die v3-APIs weiterhin verbessert und die Diskrepanz zwischen den Versionen behebt.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET](stream-files-dotnet-quickstart.md)