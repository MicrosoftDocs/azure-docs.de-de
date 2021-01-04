---
title: Konsistenzebenen in Azure Cosmos DB
description: Azure Cosmos DB bietet fünf Konsistenzebenen, um für vorhersehbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz sorgen zu können.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 742ff2e6cff4569b5b7eeb131cd4394277b6c3cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100455"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsistenzebenen in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Verteilte Datenbanken, die auf Replikation angewiesen sind, um Hochverfügbarkeit, niedrige Latenzzeiten oder beides sicherzustellen, bilden den grundlegenden Kompromiss zwischen Lesekonsistenz, Verfügbarkeit, Latenz sowie Durchsatz, wie in dem [PACLC-Theorem](https://en.wikipedia.org/wiki/PACELC_theorem) definiert. Die Linearisierbarkeit oder das Modell für starke Konsistenz ist der Goldstandard bei der Datenprogrammierbarkeit. Aber die höheren Schreiblatenzen, die dadurch entstehen, dass Daten über große Entfernungen repliziert und übertragen werden müssen, verursachen zusätzliche Kosten. Eine hohe Konsistenz kann auch Nachteile durch eine geringere Verfügbarkeit (bei Ausfällen) haben, da die Daten nicht in jeder Region repliziert und committet werden können. Die letztliche Konsistenz führt zu höherer Verfügbarkeit und besserer Leistung, aber es ist schwieriger, Anwendungen zu programmieren, da die Daten möglicherweise nicht in allen Regionen vollständig konsistent sind.

Die meisten kommerziell verfügbaren, verteilten NoSQL-Datenbanken, die heute auf dem Markt erhältlich sind, bieten ausschließlich eine hohe und letztliche Konsistenz. Azure Cosmos DB bietet fünf klar definierte Ebenen. Diese sind (von der stärksten bis zur schwächsten Ebene):

- *Starke Konsistenz*
- *Begrenzte Veraltung*
- *Sitzungskonsistenz*
- *Präfixkonsistenz*
- *Letztliche Konsistenz*

Jede Ebene bietet Verfügbarkeits- und Leistungskompromisse. In der folgenden Abbildung sind die verschiedenen Konsistenzebenen als Spektrum dargestellt.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konsistenz als Spektrum" border="false" :::

Die Konsistenzebenen sind regionsunabhängig und für alle Vorgänge garantiert, unabhängig von der Region, in der die Lese- und Schreibvorgänge verarbeitet werden, der Anzahl der mit Ihrem Azure Cosmos-Konto verbundenen Regionen und davon, ob Ihr Konto mit einer oder mehreren Schreibregionen konfiguriert ist.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsistenzebenen und Azure Cosmos DB-APIs

Azure Cosmos DB bietet für gängige Datenbanken native Unterstützung für Wire Protocol-kompatible APIs. Dazu zählen MongoDB, Apache Cassandra, Gremlin und Azure Table Storage. Für die Gremlin-API und die Tabellen-API wird die im Azure Cosmos-Konto konfigurierte Standardkonsistenzebene verwendet. Ausführliche Informationen zum Mapping von Konsistenzebenen zwischen Cassandra-API oder der API für MongoDB und Azure Cosmos DB-Konsistenzebenen finden Sie unter [Cassandra API consistency mapping](cassandra-consistency.md) (Cassandra-API-Konsistenzmapping) und [API for MongoDB consistency mapping](mongodb-consistency.md) (API für MongoDB-Konsistenzmapping).

## <a name="scope-of-the-read-consistency"></a>Geltungsbereich der Lesekonsistenz

Die Lesekonsistenz gilt für einen einzelnen Lesevorgang innerhalb einer logischen Partition. Ein Lesevorgang kann durch einen Remoteclient oder eine gespeicherte Prozedur ausgelöst werden.

## <a name="configure-the-default-consistency-level"></a>Konfigurieren der Standardkonsistenzebene

Sie können die Standardkonsistenzebene für Ihr Azure Cosmos-Konto jederzeit konfigurieren. Die für Ihr Konto konfigurierte Standardkonsistenzebene gilt für alle Azure Cosmos-Datenbanken und -Container in diesem Konto. Bei allen in einem Container oder einer Datenbank ausgeführten Lesevorgängen und Abfragen wird standardmäßig die angegebene Konsistenzebene verwendet. Weitere Informationen finden Sie unter [Konfigurieren der Standardkonsistenzebene](how-to-manage-consistency.md#configure-the-default-consistency-level). Sie können auch die Standardkonsistenzebene für eine bestimmte Anforderung außer Kraft setzen. Weitere Informationen hierzu finden Sie im Artikel [Außerkraftsetzen der Standardkonsistenzebene](how-to-manage-consistency.md?#override-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantien in Zusammenhang mit Konsistenzebenen

Azure Cosmos DB garantiert, dass 100 Prozent aller Leseanforderungen die Konsistenzgarantie für jede ausgewählte Konsistenzebene erfüllen. Die genauen Definitionen der fünf Konsistenzebenen in Azure Cosmos DB – unter Verwendung der TLA+-Spezifikationssprache – finden Sie im GitHub-Repository [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla).

Im Folgenden wird die Semantik der fünf Konsistenzebenen beschrieben:

- **Starke Konsistenz** : Starke Konsistenz bietet garantierte Linearisierbarkeit. Linearisierbarkeit bedeutet die gleichzeitige Verarbeitung von Anforderungen. Die Lesevorgänge geben garantiert die neueste Version eines Elements zurück, für die ein Commit ausgeführt wurde. Einem Client wird nie ein partieller Schreibvorgang bzw. ein Schreibvorgang, für den kein Commit ausgeführt wurde, angezeigt. Benutzer haben immer die Garantie, dass sie den neuesten Schreibvorgang lesen, für den ein Commit ausgeführt wurde.

  In der folgenden Grafik wird die starke Konsistenz anhand von Noten veranschaulicht. Nachdem die Daten in die Region „USA, Westen 2“ geschrieben wurden, erhalten Sie beim Lesen der Daten aus anderen Regionen den neuesten Wert:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Abbildung: Konsistenzebene „Stark“":::

- **Begrenzte Veraltung (Bounded staleness)** : Die Lesevorgänge berücksichtigen immer die Garantie der Präfixkonsistenz. Lesevorgänge bleiben höchstens um *„K“* Versionen (Updates) eines Elements oder um ein durch *„T“* definiertes Zeitintervall hinter Schreibvorgängen zurück, je nachdem, was zuerst erreicht wird. Wenn Sie also die begrenzte Veraltung auswählen, kann die Veraltung auf zwei Arten konfiguriert werden:

- Anhand der Anzahl von Versionen ( *K* ) des Elements
- Das Zeitintervall ( *T* ) kann hinter den Schreibvorgängen liegen.

Bei einem Konto mit einer einzelnen Region ist der Mindestwert von *K* und *T* 10 Schreibvorgänge oder 5 Sekunden. Bei Konten mit mehreren Regionen ist der Mindestwert von *K* und *T* 100.000 Schreibvorgänge oder 300 Sekunden.

Begrenzte Veraltung bietet eine vollständige globale Reihenfolge außerhalb des „Veraltungsfensters“. Wenn ein Client Lesevorgänge in einer Region ausführt, die Schreibvorgänge akzeptiert, bietet die begrenzte Veraltung die gleichen Garantien wie die starke Konsistenz. Wenn sich das Veraltungsfenster entweder für Zeit oder Updates nähert, je nachdem, welcher Zeitpunkt am nächsten liegt, wird der Dienst neue Schreibvorgänge drosseln, damit die Replikation aufgeholt und die Konsistenzgarantie eingehalten werden kann.

Im Veraltungsfenster bietet die begrenzte Veraltung die folgenden Konsistenzgarantien:

- Konsistenz für Clients in derselben Region für ein Konto mit einer einzelnen Schreibregion = Stark
- Konsistenz für Clients in unterschiedlichen Regionen für ein Konto mit einer einzelnen Schreibregion = Präfixkonsistenz
- Konsistenz für Clients, die in nur einer Region schreiben, für ein Konto mit mehreren Schreibregionen = Präfixkonsistenz
- Konsistenz für Clients, die in unterschiedlichen Region schreiben, für ein Konto mit mehreren Schreibregionen = Letztlich

  Die begrenzte Veraltung wird häufig von global verteilten Anwendungen genutzt, die niedrige Schreibwartezeiten erwarten, aber eine garantierte vollständige globale Reihenfolge benötigen. Begrenzte Veraltung eignet sich hervorragend für Anwendungen mit Gruppenzusammenarbeit und -freigabe, Börsenticker, Veröffentlichen-Abonnieren-Muster/Queueing und Ähnlichem. In der folgenden Grafik wird die begrenzte Veraltung anhand von Noten veranschaulicht. Nachdem die Daten in die Region „USA, Westen 2“ geschrieben wurden, wird der geschriebene Wert in den Regionen „USA, Osten 2“ und „Australien, Osten“ basierend auf der konfigurierten maximalen Verzögerung oder den maximalen Vorgängen gelesen:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Abbildung: Konsistenzebene „Begrenzte Veraltung“":::

- **Sitzung (Session)** :  Innerhalb einer einzelnen Clientsitzung berücksichtigen Lesevorgänge immer die folgenden Garantien: Präfixkonsistenz, monotone Lesevorgänge, monotone Schreibvorgänge, Lesen der eigenen Schreibvorgänge, Schreibvorgänge folgen Lesevorgängen. Dabei wird davon ausgegangen, dass eine einzelne Schreibsitzung oder das Sitzungstoken für mehrere Writer gemeinsam genutzt wird.

Für Clients außerhalb der Sitzung, die Schreibvorgänge ausführen, gelten folgende Garantien:

- Konsistenz für Clients in derselben Region für ein Konto mit einer einzelnen Schreibregion = Präfixkonsistenz
- Konsistenz für Clients in unterschiedlichen Regionen für ein Konto mit einer einzelnen Schreibregion = Präfixkonsistenz
- Konsistenz für Clients, die in nur einer Region schreiben, für ein Konto mit mehreren Schreibregionen = Präfixkonsistenz
- Konsistenz für Clients, die in mehreren Region schreiben, für ein Konto mit mehreren Schreibregionen = Letztlich

  Die Sitzungskonsistenz ist die gängigste Konsistenzebene für Anwendungen in einer einzelnen Region sowie für weltweit verteilte Anwendungen. Sie bietet Schreibwartezeiten, Verfügbarkeit und Lesedurchsatz, die mit der letztlichen Konsistenz vergleichbar sind. Darüber hinaus stellt sie die Konsistenzgarantien für Anwendungen bereit, die für den Betrieb im Benutzerkontext geschrieben wurden. In der folgenden Grafik wird die Sitzungskonsistenz anhand von Noten veranschaulicht. Für „USA, Westen 2“ (Schreiben) und „USA, Westen 2“ (Lesen) wird dieselbe Sitzung (Sitzung A) verwendet, sodass beide dieselben Daten zur gleichen Zeit lesen. Die Region „Australien, Osten“ dagegen verwendet „Sitzung B“ und empfängt Daten somit später, aber weiterhin in der Reihenfolge der Schreibvorgänge.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Abbildung: Konsistenzebene „Sitzung“":::

- **Präfixkonsistenz** : Die zurückgegebenen Updates enthalten ein bestimmtes Präfix aller Updates ohne Lücken. Die konsistente Präfixkonsistenzebene garantiert, dass für Lesevorgänge niemals Schreibvorgänge in falscher Reihenfolge angezeigt werden.

Wenn Schreibvorgänge in der Reihenfolge `A, B, C` erfolgen, wird einem Client entweder `A`, `A,B` oder `A,B,C`, aber niemals falsche Permutationen wie `A,C` oder `B,A,C` angezeigt. Die Präfixkonsistenz bietet Schreibwartezeiten, Verfügbarkeit und Lesedurchsatz, die mit der letztlichen Konsistenz vergleichbar sind. Darüber hinaus stellt sie Reihenfolgengarantien für Szenarien bereit, in denen die Reihenfolge relevant ist.

Im Folgenden finden Sie die Konsistenzgarantien für Präfixkonsistenz:

- Konsistenz für Clients in derselben Region für ein Konto mit einer einzelnen Schreibregion = Präfixkonsistenz
- Konsistenz für Clients in unterschiedlichen Regionen für ein Konto mit einer einzelnen Schreibregion = Präfixkonsistenz
- Konsistenz für Clients, die in nur einer Region schreiben, für ein Konto mit mehreren Schreibregionen = Präfixkonsistenz
- Konsistenz für Clients, die in mehreren Region schreiben, für ein Konto mit mehreren Schreibregionen = Letztlich

In der folgenden Grafik wird die Präfixkonsistenz anhand von Noten veranschaulicht. In allen Regionen werden bei Lesevorgängen niemals Schreibvorgänge in der falschen Reihenfolge angezeigt:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Abbildung: Präfixkonsistenz":::

- **Letztlich (Eventual)** : Es gibt keine Reihenfolgengarantie für Lesevorgänge. Wenn keine weiteren Schreibvorgänge vorhanden sind, konvergieren die Replikate schließlich.  
Die letztliche Konsistenz stellt die schwächste Form der Konsistenz dar, da ein Client unter Umständen Werte liest, die älter als die zuvor gelesenen Werte sind. Letztliche Konsistenz ist ideal, wenn die Anwendung keinerlei Reihenfolgengarantien erfordert. Ein Beispiel wäre etwa das Zählen von Retweets, „Gefällt mir“-Markierungen oder Kommentaren ohne Thread. In der folgenden Grafik wird die letztliche Konsistenz anhand von Noten veranschaulicht:

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="Abbildung: Letztliche Konsistenz":::

## <a name="consistency-guarantees-in-practice"></a>Konsistenzgarantien in der Praxis

In der Praxis erhalten Sie häufig stärkere Konsistenzgarantien. Die Konsistenzgarantien für einen Lesevorgang entsprechen der Aktualität und der Sortierung des Zustands der angeforderten Datenbank. Die Lesekonsistenz ist an die Sortierung und Verbreitung der Schreib-/Aktualisierungsvorgänge gebunden.  

Wenn keine Schreibvorgänge für die Datenbank ausgeführt werden, liefert ein Lesevorgang mit der Konsistenzebene **letztliche Konsistenz** , **Sitzungskonsistenz** oder **Präfixkonsistenz** wahrscheinlich die gleichen Ergebnisse wie ein Lesevorgang mit starker Konsistenzebene.

Wenn Ihr Azure Cosmos-Konto mit einer anderen Konsistenzebene als der starken Konsistenz konfiguriert ist, können Sie anhand der *PBS* -Metrik (Probabilistically Bounded Staleness) die Wahrscheinlichkeit ermitteln, dass Ihre Clients starke und konsistente Lesevorgänge für Ihre Workloads erzielen. Diese Metrik wird im Azure-Portal verfügbar gemacht. Weitere Informationen finden Sie unter [Überwachen der PBS-Metrik (Probabilistically Bounded Staleness)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

PBS (Probabilistic Bounded Staleness) zeigt, wie letztendlich Ihre letztendliche Konsistenz ist. Diese Metrik gibt Aufschluss darüber, wie oft Sie eine stärkere Konsistenz erreichen können als die Konsistenzebene, die Sie aktuell in Ihrem Azure Cosmos-Konto konfiguriert haben. Das heißt, Sie sehen die Wahrscheinlichkeit (gemessen in Millisekunden), dass stark konsistente Lesevorgänge in Regionen mit Schreib- und Lesevorgängen auftreten.

## <a name="consistency-levels-and-latency"></a>Konsistenzebenen und Latenz

Für alle Konsistenzebenen wird eine Leselatenz garantiert, die jederzeit unter 10 Millisekunden im 99. Perzentil liegt. Die durchschnittliche Leselatenz (im 50. Perzentil) beträgt typischerweise 4 Millisekunden oder weniger.

Für alle Konsistenzebenen wird eine Schreiblatenz garantiert, die jederzeit unter 10 Millisekunden im 99. Perzentil liegt. Die durchschnittliche Schreiblatenz (im 50. Perzentil) beträgt üblicherweise 5 Millisekunden oder weniger. Eine Ausnahme bei dieser Garantie bilden Azure Cosmos-Konten, die sich über mehrere Regionen erstrecken und mit starker Konsistenz konfiguriert sind.

### <a name="write-latency-and-strong-consistency"></a>Schreiblatenz und starke Konsistenz

Für Azure Cosmos-Konten, die mit starker Konsistenz mit mehreren Regionen konfiguriert sind, beträgt die Schreiblatenz der doppelten Roundtripzeit (Round-Trip Time, RTT) zwischen den beiden am weitesten entfernten Regionen plus 10 Millisekunden im 99. Perzentil. Eine hohe Netzwerk-RTT zwischen den Regionen führt zu einer höheren Latenz für Cosmos DB-Anforderungen, da ein Vorgang bei starker Konsistenz erst abgeschlossen wird, nachdem sichergestellt wurde, dass er in allen Regionen innerhalb eines Kontos committet wurde.

Die exakte RTT-Latenz richtet sich nach der physischen Entfernung und der Azure-Netzwerktopologie. Azure-Netzwerke bieten keine Wartezeit-SLAs für die RTT zwischen zwei Azure-Regionen, aber Sie veröffentlichen [Roundtrip-Latenzstatistiken von Azure-Netzwerken](../networking/azure-network-latency.md). Die Replikationslatenzzeiten für Ihr Azure Cosmos-Konto werden im Azure-Portal angezeigt. Sie können das Azure-Portal (wählen Sie auf dem Blatt „Metriken“ die Registerkarte „Konsistenz“ aus) nutzen, um die Replikationslatenz zwischen verschiedenen Regionen zu überwachen, die Ihrem Azure Cosmos-Konto zugeordnet sind.

> [!IMPORTANT]
> Starke Konsistenz wird für Konten mit Regionen, die mehr als 5.000 Meilen (8.000 Kilometer) umfassen, standardmäßig aufgrund der hohen Schreiblatenz blockiert. Wenden Sie sich an den Support, um diese Funktion zu aktivieren.

## <a name="consistency-levels-and-throughput"></a>Konsistenzebenen und Durchsatz

- Für starke und begrenzte Veraltung werden Lesevorgänge für zwei Replikate in einer Gruppe mit vier Replikaten (Minderheitsquorum) durchgeführt, um die Konsistenz zu garantieren. Die Lesevorgänge werden in der Reihenfolge „Sitzung“, „Präfixkonsistenz“ und „Letztlich“ durchgeführt. Im Ergebnis ist der Lesedurchsatz für die gleiche Anzahl von Anforderungseinheiten für starke und begrenzte Veraltung halb so hoch wie bei anderen Konsistenzebenen.

- Für bestimmte Schreibvorgänge (z.B. Einfügen, Ersetzen, Upsert, Löschen) ist der Schreibdurchsatz für RUs für alle Konsistenzebenen identisch.

|**Konsistenzebene**|**Quorumlesevorgänge**|**Quorumschreibvorgänge**|
|--|--|--|
|**Starke Konsistenz**|Lokale Minderheit|Globale Mehrheit|
|**Begrenzte Veraltung**|Lokale Minderheit|Lokale Mehrheit|
|**Sitzungskonsistenz**|Einzelnes Replikat (mit Sitzungstoken)|Lokale Mehrheit|
|**Präfixkonsistenz**|Einzelnes Replikat|Lokale Mehrheit|
|**Letztliche Konsistenz**|Einzelnes Replikat|Lokale Mehrheit|

> [!NOTE]
> Die Kosten der RU/s für die Lesevorgänge von lokalen Minderheiten sind doppelt so hoch wie bei niedrigeren Konsistenzebenen, da die Lesevorgänge aus zwei Replikaten erstellt werden, um Konsistenzgarantien für hohe und begrenzte Veraltung zu bieten.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Konsistenzebenen und Datendauerhaftigkeit

Bei einer global verteilten Datenbankumgebung besteht eine direkte Beziehung zwischen der Konsistenzebene und der Datendauerhaftigkeit bei einem Ausfall in der gesamten Region. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie wissen, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist. Die Zeit, die für die vollständige Wiederherstellung einer Anwendung erforderlich ist, wird als **RTO** ( **Recovery Time Objective** ) bezeichnet. Sie müssen auch wissen, über welchen Zeitraum kürzlich durchgeführte Datenupdates maximal verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird. Der Zeitraum der Updates, der verloren gehen darf, wird als **RPO** ( **Recovery Point Objective** ) bezeichnet.

In der folgenden Tabelle wird die Beziehung zwischen dem Konsistenzmodell und der Datendauerhaftigkeit bei einem regionsweiten Ausfall definiert. Beachten Sie unbedingt, dass es in einem verteilten System auch bei starker Konsistenz unmöglich ist, eine verteilte Datenbank mit einem RPO- und RTO-Wert von 0 (null) zu erzielen. Grund ist das [CAP-Theorem](https://en.wikipedia.org/wiki/CAP_theorem).

|**Region(en)**|**Replikationsmodus**|**Konsistenzebene**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Eine oder mehrere Schreibregionen|Jede Konsistenzebene|< 240 Minuten|< 1 Woche|
|> 1|Eine Schreibregion|Sitzung, Präfixkonsistenz, Letztlich|< 15 Minuten|< 15 Minuten|
|> 1|Eine Schreibregion|Begrenzte Veraltung (Bounded staleness)|*K* & *T*|< 15 Minuten|
|> 1|Eine Schreibregion|STARK (Strong)|0|< 15 Minuten|
|> 1|Mehrere Schreibregionen|Sitzung, Präfixkonsistenz, Letztlich|< 15 Minuten|0|
|> 1|Mehrere Schreibregionen|Begrenzte Veraltung (Bounded staleness)|*K* & *T*|0|

*K* = Anzahl von *„K“* -Versionen (d. h. Updates) eines Elements.

*T* = Zeitintervall *„T“* seit dem letzten Update.

Bei einem Konto mit einer einzelnen Region ist der Mindestwert von *K* und *T* 10 Schreibvorgänge oder 5 Sekunden. Bei Konten mit mehreren Regionen ist der Mindestwert von *K* und *T* 100.000 Schreibvorgänge oder 300 Sekunden. Hiermit wird die minimale RPO für Daten bei Verwendung von begrenzter Veraltung definiert.

## <a name="strong-consistency-and-multiple-write-regions"></a>Hohe Konsistenz und mehrere Schreibregionen

Mit mehreren Schreibregionen konfigurierte Cosmos-Konten können nicht für hohe Konsistenz konfiguriert werden, da es für ein verteiltes System nicht möglich ist, einen RPO-Wert von 0 (null) und einen RTO-Wert von 0 zu erreichen. Außerdem gibt es keine Vorteile hinsichtlich der Schreiblatenz bei Verwendung von hoher Konsistenz mit mehreren Schreibregionen, da ein Schreibvorgang für eine beliebige Region repliziert und in allen konfigurierten Regionen innerhalb des Kontos committet werden muss. Dies führt zur selben Schreiblatenz wie bei einem Konto mit nur einer Schreibregion.

## <a name="additional-reading"></a>Zusätzliche Lektüre

In folgenden Ressourcen erfahren Sie mehr über Konsistenzkonzepte:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Allgemeine TLA+-Spezifikationen für die fünf von Azure Cosmos DB angebotenen Konsistenzebenen)
- [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Video) von Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Whitepaper) von Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sitzungsgarantien für schwach konsistente replizierte Daten](https://dl.acm.org/citation.cfm?id=383631)
- [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme: CAP ist nur ein Teil der Wahrheit](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventually Consistent – Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Letztliche Konsistenz – Neuauflage)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über die Konsistenzebenen in Azure Cosmos DB zu erfahren:

- [Konfigurieren der Standardkonsistenzebene](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Außerkraftsetzen der Standardkonsistenzebene](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)