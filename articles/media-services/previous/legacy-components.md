---
title: Legacy-Komponenten von Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema werden die Legacy-Komponenten von Azure Media Services behandelt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: d85cbeb99264b5b730fe585fd39f658e6448467f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515762"
---
# <a name="azure-media-services-legacy-components"></a>Legacy-Komponenten von Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Im Laufe der Zeit verbessern wir Media Service-Komponenten und nehmen Legacy-Komponenten außer Betrieb. Dieser Artikel unterstützt Sie bei der Migration Ihrer Anwendung von einer Legacy- zu einer aktuellen Komponente.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Deaktivierungspläne für Legacy-Komponenten und Hinweise zur Migration

Die Medienprozessoren *Windows Azure Media Encoder* (WAME) und *Azure Media Encoder* (AME) sind veraltet.

* [Migrieren von Windows Azure Media Encoder zu Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrieren von Azure Media Encoder zu Media Encoder Standard](migrate-azure-media-encoder.md)

Die folgenden Media Analytics-Medienprozessoren sind entweder veraltet oder gelten bald als veraltet:

  
 
| **Medienprozessorname** | **Datum der Deaktivierung** | **Zusätzliche Hinweise** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1\. Januar 2020 | Dieser Medienprozessor wird durch den [AudioAnalyzerPreset-Standardmodus von Media Services v3](../latest/analyzing-video-audio-files-concept.md) ersetzt. Weitere Informationen finden Sie unter [Migration von Azure Media Indexer 2 zu Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1\. März 2023 | Dieser Medienprozessor wird durch den [AudioAnalyzerPreset-Standardmodus von Media Services v3](../latest/analyzing-video-audio-files-concept.md) ersetzt. Weitere Informationen finden Sie unter [Migration von Azure Media Indexer 2 zu Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Bewegungserkennung | 1\. Juni 2020|Derzeit keine Austauschpläne. |
| Videozusammenfassung |1\. Juni 2020|Derzeit keine Austauschpläne.|
| Optische Zeichenerkennung in Video | 1\. Juni 2020 |Dieser Medienprozessor wird durch den [Azure Media Services Video Indexer](../video-indexer/index.yml) ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyzing-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleichen der Voreinstellungen von Azure Media Services v3 und Video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Face Detector | 1\. Juni 2020 | Dieser Medienprozessor wird durch den [Azure Media Services Video Indexer](../video-indexer/index.yml) ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyzing-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleichen der Voreinstellungen von Azure Media Services v3 und Video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1\. Juni 2020 |Dieser Medienprozessor wird durch den [Azure Media Services Video Indexer](../video-indexer/index.yml) ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyzing-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleichen der Voreinstellungen von Azure Media Services v3 und Video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Nächste Schritte

[Hinweise zur Migration von Media Services v2 zu v3](../latest/migrate-from-v2-to-v3.md)
