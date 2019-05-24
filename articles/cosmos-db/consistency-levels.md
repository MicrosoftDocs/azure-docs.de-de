---
title: Konsistenzebenen in Azure Cosmos DB
description: Azure Cosmos DB bietet fünf Konsistenzebenen, um für vorhersehbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz sorgen zu können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: dcd51756a9c5a5a24a082862bb911cc2d2605d61
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954360"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsistenzebenen in Azure Cosmos DB

Verteilte Datenbanken, die auf Replikation angewiesen sind, um Hochverfügbarkeit, niedrige Latenzzeiten oder beides sicherzustellen, bilden den grundlegenden Kompromiss zwischen Lesekonsistenz und Verfügbarkeit, Latenz sowie Durchsatz. Die meisten kommerziell verfügbaren verteilten Datenbanken verlangen von Entwicklern, dass sie zwischen den beiden extremen Konsistenzmodellen auswählen: *starke* Konsistenz und *letztliche* Konsistenz. Die [Linearisierbarkeit](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) oder das Modell für starke Konsistenz ist das Nonplusultra in Sachen Datenprogrammierbarkeit. Es wird jedoch teuer durch hohe Wartezeit (im stabilen Zustand) bzw. durch geringere Verfügbarkeit (im Fall eines Ausfalls) erkauft. Auf der anderen Seite bietet die letztliche Konsistenz eine höhere Verfügbarkeit und bessere Leistung, erschwert jedoch die Programmierung von Anwendungen erheblich. 

Azure Cosmos DB bietet für die Datenkonsistenz nicht nur diese beiden Extreme, sondern ein ganzes Spektrum von Auswahlmöglichkeiten. Die starke und die letztliche Konsistenz bilden die beiden Enden des Spektrums, aber dazwischen gibt es viele weitere Konsistenzoptionen. Entwickler können mit diesen Optionen eine genaue Auswahl treffen und differenzierte Kompromisse in Bezug auf Hochverfügbarkeit und Leistung eingehen. 

Mit Azure Cosmos DB können Entwickler aus fünf klar definierten Konsistenzmodellen für das Konsistenzspektrum auswählen. Dabei handelt es sich um die folgenden Modelle (von der stärksten bis zur schwächsten Option): *starke Konsistenz*, *begrenzte Veraltung*, *Sitzungskonsistenz*, *Präfixkonsistenz* und *letztliche Konsistenz*. Die Modelle sind klar definiert und intuitiv und können für spezifische reale Szenarien verwendet werden. Jedes Modell ermöglicht [Verfügbarkeits- und Leistungskompromisse](consistency-levels-tradeoffs.md) und wird durch SLAs abgesichert. In der folgenden Abbildung sind die verschiedenen Konsistenzebenen als Spektrum dargestellt.

![Konsistenz als Spektrum](./media/consistency-levels/five-consistency-levels.png)

Die Konsistenzebenen sind regionsunabhängig und für alle Vorgänge garantiert, unabhängig von der Region, in der die Lese- und Schreibvorgänge verarbeitet werden, der Anzahl der mit Ihrem Azure Cosmos-Konto verbundenen Regionen und davon, ob Ihr Konto mit einer oder mehreren Schreibregionen konfiguriert ist.

## <a name="scope-of-the-read-consistency"></a>Geltungsbereich der Lesekonsistenz

Die Lesekonsistenz gilt für einen einzelnen Lesevorgang innerhalb eines Partitionsschlüsselbereichs bzw. einer logischen Partition. Ein Lesevorgang kann durch einen Remoteclient oder eine gespeicherte Prozedur ausgelöst werden.

## <a name="configure-the-default-consistency-level"></a>Konfigurieren der Standardkonsistenzebene

Sie können die Standardkonsistenzebene für Ihr Azure Cosmos-Konto jederzeit konfigurieren. Die für Ihr Konto konfigurierte Standardkonsistenzebene gilt für alle Azure Cosmos-Datenbanken und -Container in diesem Konto. Bei allen in einem Container oder einer Datenbank ausgeführten Lesevorgängen und Abfragen wird standardmäßig die angegebene Konsistenzebene verwendet. Weitere Informationen finden Sie unter [Konfigurieren der Standardkonsistenzebene](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantien in Zusammenhang mit Konsistenzebenen

Die von Azure Cosmos DB bereitgestellten umfassenden SLAs garantieren, dass 100 Prozent aller Leseanforderungen die Konsistenzgarantie für jede von Ihnen ausgewählte Konsistenzebene erfüllen. Eine Leseanforderung erfüllt die Konsistenz-SLA, wenn alle Konsistenzgarantien der Konsistenzebene erfüllt werden. Die genauen Definitionen der fünf Konsistenzebenen in Azure Cosmos DB – unter Verwendung der [TLA+-Spezifikationssprache](https://lamport.azurewebsites.net/tla/tla.html) – finden Sie im GitHub-Repository [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla). 

Im Folgenden wird die Semantik der fünf Konsistenzebenen beschrieben:

- **Starke Konsistenz**: Die starke Konsistenz bietet garantierte [Linearisierbarkeit](https://aphyr.com/posts/313-strong-consistency-models). Die Lesevorgänge geben garantiert die neueste Version eines Elements zurück, für die ein Commit ausgeführt wurde. Einem Client wird nie ein partieller Schreibvorgang bzw. ein Schreibvorgang, für den kein Commit ausgeführt wurde, angezeigt. Benutzer haben immer die Garantie, dass sie den neuesten Schreibvorgang lesen, für den ein Commit ausgeführt wurde.

- **Begrenzte Veraltung (Bounded staleness)**: Die Lesevorgänge berücksichtigen immer die Garantie der Präfixkonsistenz. Lesevorgänge bleiben höchstens *„K“* Versionen (d. h. Updates) eines Elements oder um ein durch *„T“* definiertes Zeitintervall hinter Schreibvorgängen zurück. Wenn Sie also die begrenzte Veraltung auswählen, kann die Veraltung auf zwei Arten konfiguriert werden: 

  * Anhand der Anzahl von Versionen (*K*) des Elements
  * Anhand des Zeitintervalls (*T*) zwischen Lese- und Schreibvorgängen 

  Begrenzte Veraltung bietet eine vollständige globale Reihenfolge außer innerhalb des „Veraltungsfensters“. Die monotonen Lesegarantien bestehen innerhalb einer Region sowohl innerhalb als auch außerhalb des Veraltungszeitfensters. Die starke Konsistenz weist die gleiche Semantik auf wie die begrenzte Veraltung. Das Veraltungszeitfenster ist gleich Null (0). Die begrenzte Veraltung wird auch als „Linearisierbarkeit mit Zeitverzögerung“ bezeichnet. Wenn ein Client Lesevorgänge in einer Region ausführt, die Schreibvorgänge akzeptiert, bietet die begrenzte Veraltung die gleichen Garantien wie die starke Konsistenz.

- **Sitzung (Session)**: Die Lesevorgänge berücksichtigen immer die folgenden Garantien: Präfixkonsistenz (sofern es sich um eine einzelne Schreibsitzung handelt), monotone Lesevorgänge, monotone Schreibvorgänge, Lesen der eigenen Schreibvorgänge, Schreibvorgänge folgen Lesevorgängen. Die Sitzungskonsistenz gilt immer für eine Clientsitzung.

- **Präfixkonsistenz**: Die zurückgegebenen Updates enthalten ein bestimmtes Präfix aller Updates ohne Lücken. Die konsistente Präfixkonsistenzebene garantiert, dass für Lesevorgänge niemals Schreibvorgänge in falscher Reihenfolge angezeigt werden.

- **Letztlich (Eventual)**: Es gibt keine Reihenfolgengarantie für Lesevorgänge. Wenn keine weiteren Schreibvorgänge vorhanden sind, konvergieren die Replikate schließlich.

## <a name="consistency-levels-explained-through-baseball"></a>Konsistenzebenen – erläutert am Beispiel Baseball

Wir verwenden hier das Szenario eines Baseballspiels als Beispiel. Stellen Sie sich eine Sequenz von Schreibvorgängen vor, die den Punktestand eines Baseballspiels darstellen. Das Ergebnis eines Baseballspiels mit den Punkteständen für jedes Inning wird im Dokument [Erläuterung der Konsistenz replizierter Daten anhand von Baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) beschrieben. Dieses hypothetische Baseballspiel befindet sich aktuell in der Mitte des siebten Innings. Der Seventh-inning stretch wird durchgeführt. Die Gastmannschaft liegt mit 2:5 im Rückstand (wie unten dargestellt).

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Runs** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Gäste** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Heim** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Ein Azure Cosmos-Container enthält die Gesamtanzahl von Runs des Gast- und des Heimteams. Während das Spiel läuft, können verschiedene Lesegarantien dazu führen, dass Clients verschiedene Punktestände lesen. In der folgenden Tabelle sind sämtliche Punktestände aufgelistet, die durch Lesen der Punktestände des Gast- und des Heimteams mit jeder der fünf Konsistenzgarantien zurückgegeben werden können. Die Punkte der Gastmannschaft werden zuerst aufgeführt. Die verschiedenen möglichen Rückgabewerte werden durch Kommas getrennt.

| **Konsistenzebene** | **Punktestände (Gäste, Heim)** |
| - | - |
| **Starke Konsistenz** | 2-5 |
| **Begrenzte Veraltung** | Punktestände, die mindestens ein Inning alt sind: 2-3, 2-4, 2-5 |
| **Sitzungskonsistenz** | <ul><li>Für den Writer: 2-5</li><li> Für alle anderen (außer dem Writer): 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>Nach dem Lesen von 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Präfixkonsistenz** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Letztliche Konsistenz** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Zusätzliche Lektüre

In folgenden Ressourcen erfahren Sie mehr über Konsistenzkonzepte:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Allgemeine TLA+-Spezifikationen für die fünf von Azure Cosmos DB angebotenen Konsistenzebenen)
- [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Video) von Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Whitepaper) von Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sitzungsgarantien für schwach konsistente replizierte Daten](https://dl.acm.org/citation.cfm?id=383631)
- [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme: CAP ist nur ein Teil der Wahrheit](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventually Consistent – Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Letztliche Konsistenz – Neuauflage)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über die Konsistenzebenen in Azure Cosmos DB zu erfahren:

* [Auswählen der richtigen Konsistenzebene für Ihre Anwendung](consistency-levels-choosing.md)
* [Konsistenzebenen und Azure Cosmos DB-APIs](consistency-levels-across-apis.md)
* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](consistency-levels-tradeoffs.md)
* [Konfigurieren der Standardkonsistenzebene](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Außerkraftsetzen der Standardkonsistenzebene](how-to-manage-consistency.md#override-the-default-consistency-level)

