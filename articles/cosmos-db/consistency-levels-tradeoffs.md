---
title: Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen in Azure Cosmos DB
description: Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 09777a9980e4576a5d00123516e33696e845dcac
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990219"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung 

Bei verteilten Datenbanken, die mithilfe der Replikation Hochverfügbarkeit, niedrige Latenzzeiten oder beides sicherstellen, müssen Kompromisse eingegangen werden. Diese Kompromisse betreffen die Lesekonsistenz im Vergleich zu Verfügbarkeit, Latenz und Durchsatz.

Azure Cosmos DB bietet in Bezug auf die Datenkonsistenz vielfältige Auswahlmöglichkeiten. Dieser Ansatz beinhaltet mehr Optionen als die beiden Extreme der starken und der letztlichen Konsistenz. Sie können im Hinblick auf die Konsistenz aus fünf gut definierten Modellen auswählen. Diese sind (vom stärksten bis zum schwächsten Modell):

- *Starke Konsistenz*
- *Begrenzte Veraltung*
- *Sitzungskonsistenz*
- *Präfixkonsistenz*
- *Letztliche Konsistenz*

Jedes Modell ermöglicht Verfügbarkeits- und Leistungskompromisse und wird durch umfassende SLAs abgesichert.

## <a name="consistency-levels-and-latency"></a>Konsistenzebenen und Latenz

Für alle Konsistenzebenen wird eine Leselatenz garantiert, die jederzeit unter 10 Millisekunden im 99. Perzentil liegt. Diese Leselatenz wird durch die SLA abgesichert. Die durchschnittliche Leselatenz (im 50. Perzentil) beträgt typischerweise 2 Millisekunden oder weniger. Eine Ausnahme bei dieser Garantie bilden Azure Cosmos-Konten, die sich über mehrere Regionen erstrecken und mit starker Konsistenz konfiguriert sind.

Für alle Konsistenzebenen wird eine Schreiblatenz garantiert, die jederzeit unter 10 Millisekunden im 99. Perzentil liegt. Außerdem wird sie durch die SLA unterstützt. Die durchschnittliche Schreiblatenz (im 50. Perzentil) beträgt üblicherweise 5 Millisekunden oder weniger.

Für Azure Cosmos-Konten, die mit starker Konsistenz mit mehreren Regionen konfiguriert sind, beträgt die Schreiblatenz garantiert weniger als die doppelte Roundtripzeit (Round-Trip Time, RTT) zwischen den beiden am weitesten entfernten Regionen plus 10 Millisekunden im 99. Perzentil.

Die exakte RTT-Latenz richtet sich nach der physischen Entfernung und der Azure-Netzwerktopologie. Azure-Netzwerke bieten keine Latenz-SLAs für die RTT zwischen zwei Azure-Regionen. Die Replikationslatenzzeiten für Ihr Azure Cosmos-Konto werden im Azure-Portal angezeigt. Sie können das Azure-Portal (wechseln Sie zum Blatt „Metriken“) nutzen, um die Replikationslatenz zwischen verschiedenen Regionen zu überwachen, die Ihrem Azure Cosmos-Konto zugeordnet sind.

## <a name="consistency-levels-and-throughput"></a>Konsistenzebenen und Durchsatz

- Für die gleiche Anzahl von Anforderungseinheiten (RUs) bieten Sitzungskonsistenz, Präfixkonsistenz und letztliche Konsistenz im Vergleich zur starken Konsistenz und der begrenzten Veraltung etwa das 2-fache an Lesedurchsatz.

- Für bestimmte Schreibvorgänge (z.B. Einfügen, Ersetzen, Upsert, Löschen) ist der Schreibdurchsatz für RUs für alle Konsistenzebenen identisch.

## <a id="rto"></a>Konsistenzebenen und Datendauerhaftigkeit

Bei einer global verteilten Datenbankumgebung besteht eine direkte Beziehung zwischen der Konsistenzebene und der Datendauerhaftigkeit bei einem Ausfall in der gesamten Region. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als **RTO** (**Recovery Time Objective**) bezeichnet. Sie müssen auch wissen, über welchen Zeitraum kürzlich durchgeführte Datenupdates maximal verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird. Der Zeitraum der Updates, der verloren gehen darf, wird als **RPO** (**Recovery Point Objective**) bezeichnet.

In der folgenden Tabelle wird die Beziehung zwischen dem Konsistenzmodell und der Datendauerhaftigkeit bei einem regionsweiten Ausfall definiert. Beachten Sie unbedingt, dass es in einem verteilten System auch bei starker Konsistenz unmöglich ist, eine verteilte Datenbank mit einem RPO- und RTO-Wert von 0 (null) zu erzielen. Grund ist das CAP-Theorem. Weitere Informationen finden Sie unter [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md).

|**Region(en)**|**Replikationsmodus**|**Konsistenzebene**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Einzel- oder Multimaster|Jede Konsistenzebene|< 240 Minuten|< 1 Woche|
|> 1|Einzelmaster|Sitzung, Präfixkonsistenz, Letztlich|< 15 Minuten|< 15 Minuten|
|> 1|Einzelmaster|Begrenzte Veraltung (Bounded staleness)|*K* & *T*|< 15 Minuten|
|> 1|Einzelmaster|STARK (Strong)|0|< 15 Minuten|
|> 1|Multimaster|Sitzung, Präfixkonsistenz, Letztlich|< 15 Minuten|0|
|> 1|Multimaster|Begrenzte Veraltung (Bounded staleness)|*K* & *T*|0|

*K* = Anzahl von *„K“*-Versionen (d. h. Updates) eines Elements.

*T* = Zeitintervall *„T“* seit dem letzten Update.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel bieten weitere Informationen zur globalen Verteilung und zu den allgemeinen Konsistenzkompromissen in verteilten Systemen. Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hochverfügbarkeit](high-availability.md)
- [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
