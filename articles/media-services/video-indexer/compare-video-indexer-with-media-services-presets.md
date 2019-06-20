---
title: Vergleich der Voreinstellungen von Video Indexer und Azure Media Services v3 | Microsoft-Dokumentation
description: In diesem Thema werden die Voreinstellungen von Video Indexer und Azure Media Services v3 verglichen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 275178998948e357a6a72fbe5d0b3c9c01485a3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800172"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Vergleich der Voreinstellungen von Azure Media Services v3 und Video Indexer 

In diesem Artikel werden die Funktionen von **Video Indexer-APIs** und **Media Services v3-APIs** verglichen. 

Derzeit gibt es eine Überschneidung bei den Features der [APIs von Video Indexer](https://api-portal.videoindexer.ai/) und der [APIs von Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). In der folgenden Tabelle finden Sie die aktuelle Richtlinie zum Verständnis der Unterschiede und Gemeinsamkeiten. 

## <a name="compare"></a>Vergleichen

|Feature|Video Indexer-APIs |Voreinstellungen für das Video- und Audioanalysetool<br/>in APIs von Azure Media Services v3|
|---|---|---|
|Medienerkenntnisse|[Erweitert](video-indexer-output-json-v2.md) |[Grundlagen](../latest/intelligence-concept.md)|
|Erfahrungen|Die vollständige Liste der unterstützten Features finden Sie hier: <br/> [Übersicht](video-indexer-overview.md)|Gibt nur Videoerkenntnisse zurück.|
|Abrechnung|[Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Compliance|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci) und [HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) zertifiziert. Die neuesten Updates finden Sie unter [Status der aktuellen Zertifizierungen von Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services ist mit vielen Zertifizierungen konform. Lesen Sie [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf), und suchen Sie nach „Media Services“, um zu prüfen, ob es mit einem bestimmten Zertifikat konform ist.|
|Kostenlose Testversion|USA (Ost)|Nicht verfügbar|
|Regionale Verfügbarkeit|„USA, Osten 2“, „USA, Süden-Mitte“, „USA, Westen 2“, „Europa, Norden“, „Europa, Westen“, „Asien, Südosten“, „Asien, Osten“ und „Australien, Osten“.  Die neuesten Updates finden Sie auf der Seite [Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).|Siehe [Azure-Status](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nächste Schritte

[What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))

[Media Services v3 – Übersicht](../latest/media-services-overview.md)
