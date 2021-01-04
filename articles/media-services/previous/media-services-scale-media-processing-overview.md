---
title: Reservierte Einheiten für Medien – Übersicht | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Skalierung der Medienverarbeitung mithilfe von Azure Media Services.
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
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 5353cf841663d70f2fce6662964358b7828610d8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020894"
---
# <a name="media-reserved-units"></a>Reservierte Einheiten für Medien

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Mit Azure Media Services können Sie die Medienverarbeitung skalieren, indem Sie reservierte Einheiten für Medien (Media Reserved Units, MRUs) verwalten. Eine MRU bietet zusätzliche Rechenkapazität, die für die Codierung von Medien erforderlich ist. Die Anzahl der MRUs bestimmt, mit welcher Geschwindigkeit die Medientasks verarbeitet werden und wie viele Medientasks gleichzeitig in einem Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf MRUs verfügt und Tasks verarbeitet werden müssen, können fünf Medientasks gleichzeitig ausgeführt werden. Die restlichen Tasks werden in eine Warteschlange eingereiht und können nacheinander für die Verarbeitung aufgerufen werden, wenn ein aktiver Task abgeschlossen wird. Für jede von Ihnen bereitgestellte MRU wird zwar Kapazität reserviert, es wird jedoch keine dedizierte Ressource bereitgestellt. In Zeiten mit extrem hoher Nachfrage wird die Verarbeitung aller MRUs möglicherweise nicht umgehend gestartet.

## <a name="choosing-between-different-reserved-unit-types"></a>Auswählen zwischen verschiedenen Typen reservierter Einheiten

Die folgende Tabelle hilft Ihnen bei der Entscheidung, wenn Sie zwischen verschiedenen Codierungsgeschwindigkeiten wählen müssen.  Sie zeigt die Dauer der Codierung für ein 7-minütiges Video mit 1080p in Abhängigkeit von der verwendeten MRU an.

|Art der Anforderungseinheit|Szenario|Beispielergebnisse für das Video, 7 Min., 1080 Px |
|---|---|---|
| **S1**|Single-Bitrate-Codierung. <br/>Dateien mit SD-Auflösung oder einer niedrigeren Auflösung, nicht zeitkritisch, niedrige Kosten.|Die Codierung für eine MP4-Datei mit Einzelbitrate und SD-Auflösung unter Verwendung von „H264 Single Bitrate SD 16x9“ dauert ungefähr 7 Minuten.|
| **S2**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Normale Verwendung für SD- und HD-Codierung.|Die Codierung mit der Voreinstellung „H264 Single Bitrate 720p“ dauert etwa 6 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 720p“ dauert etwa 12 Minuten.|
| **S3**|Single-Bitrate- und Multi-Bitrate-Codierung.<br/>Videos mit Full HD- und 4K-Auflösung. Zeitkritisch, schnellere Codierung|Die Codierung mit der Voreinstellung „H264 Single Bitrate 1080p“ dauert etwa 3 Minuten.<br/><br/>Die Codierung mit der Voreinstellung „H264 Multiple Bitrate 1080p“ dauert etwa 8 Minuten.|

> [!NOTE]
> Wenn Sie für Ihr Konto keine MRUs bereitstellen, werden Ihre Medientasks mit der Leistung einer S1-MRU verarbeitet und nacheinander aufgerufen. Es wird keine Verarbeitungskapazität reserviert. Die Wartezeit zwischen dem Abschließen eines Tasks und dem Start des nächsten hängt also von der Verfügbarkeit von Ressourcen im System ab.

## <a name="considerations"></a>Überlegungen

* Für Audio- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend empfohlen, das Konto mit zehn S3-Einheiten bereitzustellen. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket, falls Sie mehr als zehn S3-MRUs benötigen.
* Bei der Codierung von Tasks ohne MRUs können sich die Tasks beliebig lange in der Warteschlange befinden, und es wird immer nur maximal ein Task ausgeführt.

## <a name="billing"></a>Abrechnung

Die Abrechnung erfolgt nach der Anzahl der Minuten, in denen die reservierten Einheiten für Medien auf Ihrem Konto bereitgestellt werden. Dies erfolgt unabhängig davon, ob in Ihrem Konto Aufträge ausgeführt werden. Eine ausführliche Erläuterung finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="quotas-and-limitations"></a>Kontingente und Einschränkungen

Informationen zu Kontingenten und Einschränkungen sowie zum Öffnen von Supporttickets finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, das Skalieren der Medienverarbeitung mit einer dieser Technologien durchzuführen:

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)