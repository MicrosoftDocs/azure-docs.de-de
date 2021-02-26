---
title: 'Reservierte Einheiten für Medien: Azure'
description: Mit reservierten Einheiten für Medien können Sie den Medienprozess skalieren und die Geschwindigkeit Ihrer Medienverarbeitungstasks ermitteln.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: e0cfc47b546c31e6b93d224e64de20b79baacfd9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98891295"
---
# <a name="media-reserved-units"></a>Reservierte Einheiten für Medien

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Azure Media Services können Sie die Medienverarbeitung skalieren, indem Sie reservierte Einheiten für Medien (Media Reserved Units, MRUs) verwalten. Eine MRU bietet zusätzliche Rechenkapazität, die für die Codierung von Medien erforderlich ist. Die Anzahl der MRUs bestimmt, mit welcher Geschwindigkeit die Medientasks verarbeitet werden und wie viele Medientasks gleichzeitig in einem Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf MRUs verfügt und Tasks verarbeitet werden müssen, können fünf Medientasks gleichzeitig ausgeführt werden. Die restlichen Aufgaben werden in eine Warteschlange eingereiht und können nacheinander für die Verarbeitung aufgerufen werden, wenn ein aktiver Task abgeschlossen wird. Für jede von Ihnen bereitgestellte MRU wird zwar Kapazität reserviert, es wird jedoch keine dedizierte Ressource bereitgestellt. In Zeiten mit extrem hoher Nachfrage wird die Verarbeitung aller MRUs möglicherweise nicht umgehend gestartet.

Ein Task ist ein einzelner Arbeitsvorgang in einem Medienobjekt, z. B. die Codierung von adaptivem Streaming. Wenn Sie einen Auftrag übermitteln, muss Media Services diesen in einzelne Vorgänge (z. B. Tasks) aufteilen, die anschließend mit separaten MRUs verknüpft werden.

## <a name="choosing-between-different-reserved-unit-types"></a>Auswählen zwischen verschiedenen Typen reservierter Einheiten

Die folgende Tabelle hilft Ihnen bei der Entscheidung, wenn Sie zwischen verschiedenen Codierungsgeschwindigkeiten wählen müssen.  Es zeigt die Dauer der Codierung für ein 7-minütiges Video mit 1080p in Abhängigkeit von der verwendeten MRU an.

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

Die Abrechnung erfolgt nach der Anzahl der Minuten, in denen die MRUs in Ihrem Konto bereitgestellt werden und unabhängig davon, ob Aufträge ausgeführt werden oder nicht. Eine ausführliche Erläuterung finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-step"></a>Nächster Schritt
[Skalieren von reservierten Einheiten für Medien mit der CLI](media-reserved-units-cli-how-to.md)
[Analysieren von Videos](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Weitere Informationen

* [Kontingente und Grenzwerte](limits-quotas-constraints.md)
