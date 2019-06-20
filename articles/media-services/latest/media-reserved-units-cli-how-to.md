---
title: Verwenden der CLI zum Skalieren reservierter Einheiten für Medien – Azure | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie die CLI zum Skalieren der Medienverarbeitung mit Azure Media Services verwenden.
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
ms.date: 04/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b1a79f2798fc98fd7361c47788c79e329e2cb827
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65556185"
---
# <a name="scaling-media-processing"></a>Skalieren der Medienverarbeitung

Mit Azure Media Services können Sie die Medienverarbeitung in Ihrem Konto skalieren, indem Sie reservierte Einheiten für Medien (Media Reserved Units, MRUs) verwalten. MRUs bestimmen die Geschwindigkeit, mit der Medienverarbeitungsaufgaben verarbeitet werden. Sie können zwischen den folgenden reservierten Einheitentypen wählen: **S1**, **S2** oder **S3**. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit **S2** beispielsweise schneller ausgeführt als mit dem Typ **S1**. 

Zusätzlich zum Typ reservierter Einheiten können Sie angeben, dass für Ihr Konto reservierte Einheiten bereitgestellt werden sollen. Die Anzahl der bereitgestellten reservierten Einheiten bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, solange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

## <a name="choosing-between-different-reserved-unit-types"></a>Auswählen zwischen verschiedenen Typen reservierter Einheiten

Die folgende Tabelle hilft Ihnen bei der Entscheidung, wenn Sie zwischen verschiedenen Codierungsgeschwindigkeiten wählen müssen. Sie stellt darüber hinaus einige Fälle für Vergleichstests in [einem Video, das Sie herunterladen können](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z), bereit, damit Sie Ihre eigenen Tests ausführen können:

|Art der Anforderungseinheit|Szenario|Beispielergebnisse für das [Video, 7 Min., 1080 Px](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Single-Bitrate-Codierung. <br/>Dateien mit SD-Auflösung oder einer niedrigeren Auflösung, nicht zeitkritisch, niedrige Kosten.|Die Codierung für eine MP4-Datei mit Einzelbitrate und SD-Auflösung unter Verwendung von „H264 Single Bitrate SD 16x9“ dauert ungefähr 7 Minuten.|
| **S2**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Normale Verwendung für SD- und HD-Codierung.|Die Codierung mit der Voreinstellung „H264 Single Bitrate 720p“ dauert etwa 6 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 720p“ dauert etwa 12 Minuten.|
| **S3**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Videos mit Full HD- und 4K-Auflösung. Zeitkritisch, schnellere Codierung|Die Codierung mit der Voreinstellung „H264 Single Bitrate 1080p“ dauert etwa 3 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 1080p“ dauert etwa 8 Minuten.|

## <a name="considerations"></a>Überlegungen

* Für die Audioanalyse- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend der Einheitentyp S3 empfohlen.
* Bei Verwendung des gemeinsam genutzten Pools, d.h. ohne reservierte Einheiten, haben die Codierungsaufgaben die gleiche Leistung wie S1-RUs. Allerdings können sich die Aufgaben beliebig lange in der Warteschlange befinden, und es wird immer nur maximal eine Aufgabe ausgeführt.

Im Rest des Artikels wird veranschaulicht, wie Sie [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) zum Skalieren von MRUs verwenden.

> [!NOTE]
> Für die Audioanalyse- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend empfohlen, Ihr Konto mit zehn S3-MRUs bereitzustellen. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket, falls Sie mehr als zehn S3-MRUs benötigen.
>
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von anderen v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="prerequisites"></a>Voraussetzungen 

[Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skalieren von reservierten Einheiten für Medien mit der CLI

Führen Sie den Befehl `mru` aus.

Mit dem folgenden Befehl [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) werden reservierte Einheiten für Medien im Konto „amsaccount“ festgelegt, indem die Parameter **count** und **type** verwendet werden.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Abrechnung

Die Abrechnung erfolgt nach der Anzahl der Minuten, in denen die reservierten Einheiten für Medien auf Ihrem Konto bereitgestellt werden. Dies erfolgt unabhängig davon, ob in Ihrem Konto Aufträge ausgeführt werden. Eine ausführliche Erläuterung finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Nächster Schritt

[Analysieren von Videos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Weitere Informationen

* [Kontingente und Einschränkungen](limits-quotas-constraints.md)
* [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
