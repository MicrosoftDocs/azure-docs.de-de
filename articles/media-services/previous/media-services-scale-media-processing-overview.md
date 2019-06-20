---
title: Übersicht über das Skalieren der Medienverarbeitung | Microsoft Docs
description: Dieses Thema bietet eine Übersicht über die Skalierung der Medienverarbeitung mithilfe von Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 25b2eb05a612b18eab1fbf790b119edbe9c4d8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707396"
---
# <a name="scaling-media-processing-overview"></a>Übersicht über das Skalieren der Medienverarbeitung 
Diese Seite bietet eine Übersicht über die Gründe und Vorgehensweise bei der Skalierung der Medienverarbeitung. 

## <a name="overview"></a>Übersicht
Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Medienverarbeitungsaufgaben erfolgen. Sie können zwischen den folgenden reservierten Einheitentypen wählen: **S1**, **S2** oder **S3**. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit **S2** beispielsweise schneller ausgeführt als mit dem Typ **S1**. Weitere Informationen finden Sie unter [Reserved Unit Types](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)(Typen reservierter Einheiten).

Zusätzlich zum Typ reservierter Einheiten können Sie angeben, dass für Ihr Konto reservierte Einheiten bereitgestellt werden sollen. Die Anzahl der bereitgestellten reservierten Einheiten bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, solange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

## <a name="choosing-between-different-reserved-unit-types"></a>Auswählen zwischen verschiedenen Typen reservierter Einheiten
Die folgende Tabelle hilft Ihnen bei der Entscheidung, wenn Sie zwischen verschiedenen Codierungsgeschwindigkeiten wählen müssen. Sie stellt darüber hinaus einige Fälle für Vergleichstests in [einem Video, das Sie herunterladen können](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z), bereit, damit Sie Ihre eigenen Tests ausführen können:

|Art der Anforderungseinheit|Szenario|Beispielergebnisse für das [Video, 7 Min., 1080 Px](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Single-Bitrate-Codierung. <br/>Dateien mit SD-Auflösung oder einer niedrigeren Auflösung, nicht zeitkritisch, niedrige Kosten.|Die Codierung für eine MP4-Datei mit Einzelbitrate und SD-Auflösung unter Verwendung von „H264 Single Bitrate SD 16x9“ dauert ungefähr 7 Minuten.|
| **S2**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Normale Verwendung für SD- und HD-Codierung.|Die Codierung mit der Voreinstellung „H264 Single Bitrate 720p“ dauert etwa 6 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 720p“ dauert etwa 12 Minuten.|
| **S3**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Videos mit Full HD- und 4K-Auflösung. Zeitkritisch, schnellere Codierung|Die Codierung mit der Voreinstellung „H264 Single Bitrate 1080p“ dauert etwa 3 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 1080p“ dauert etwa 8 Minuten.|

## <a name="considerations"></a>Überlegungen
> [!IMPORTANT]
> Prüfen Sie die in diesem Abschnitt beschriebenen Überlegungen.  
> 
> 

* Für die Audioanalyse- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend der Einheitentyp S3 empfohlen.
* Bei Verwendung des gemeinsam genutzten Pools, d.h. ohne reservierte Einheiten, haben die Codierungsaufgaben die gleiche Leistung wie S1-RUs. Allerdings können sich die Aufgaben beliebig lange in der Warteschlange befinden, und es wird immer nur maximal eine Aufgabe ausgeführt.

## <a name="billing"></a>Abrechnung

Die Abrechnung erfolgt nach der Anzahl der Minuten, in denen die reservierten Einheiten für Medien auf Ihrem Konto bereitgestellt werden. Dies erfolgt unabhängig davon, ob in Ihrem Konto Aufträge ausgeführt werden. Eine ausführliche Erläuterung finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="quotas-and-limitations"></a>Kontingente und Einschränkungen
Informationen zu Kontingenten und Einschränkungen sowie zum Öffnen von Supporttickets finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Nächster Schritt
Führen Sie das Skalieren der Medienverarbeitung mit einer dieser Technologien durch: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Informationen zum Abrufen der aktuellen Version des Java SDK sowie zu den ersten Schritten beim Entwickeln mit Java finden Sie unter [Erste Schritte mit dem Java-Client-SDK für Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Wenn Sie das aktuelle PHP SDK für Media Services herunterladen möchten, suchen Sie im [Packagist-Repository](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) nach Version 0.5.7 des Microsoft-/WindowAzure-Pakets.  

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

