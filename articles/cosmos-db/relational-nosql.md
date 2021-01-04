---
title: Verstehen der Unterschiede zwischen Azure Cosmos DB NoSQL- und relationalen Datenbanken
description: In diesem Artikel werden die Unterschiede zwischen NoSQL- und relationalen Datenbanken aufgeführt.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088470"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Verstehen der Unterschiede zwischen NoSQL- und relationalen Datenbanken
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel werden einige der wichtigsten Vorteile von NoSQL-Datenbanken gegenüber relationalen Datenbanken aufgeführt. Außerdem werden einige der Herausforderungen beim Arbeiten mit NoSQL erörtert. Einen tieferen Einblick in die verschiedenen vorhandenen Datenspeicher finden Sie in unserem Artikel [Auswählen des richtigen Datenspeichers](/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Hoher Durchsatz

Eine der offensichtlichsten Herausforderungen beim Verwalten eines relationalen Datenbanksystems ist die Tatsache, dass die meisten relationalen Engines Sperren und Latches anwenden, um eine strenge [ACID-Semantik](https://en.wikipedia.org/wiki/ACID) zu erzwingen. Dieser Ansatz bietet Vorteile in Bezug auf die Sicherstellung eines konsistenten Datenzustands innerhalb der Datenbank. In Bezug auf die Parallelität, Latenz und Verfügbarkeit müssen jedoch große Kompromisse in Kauf genommen werden. Aufgrund dieser grundlegenden architektonischen Einschränkungen kann ein hohes Transaktionsvolumen dazu führen, dass Daten manuell horizontal partitioniert werden müssen. Das Implementieren von manuellem Sharding kann eine zeitaufwändige und unangenehme Aufgabe darstellen.

In diesen Szenarien können [verteilte Datenbanken](https://en.wikipedia.org/wiki/Distributed_database) eine skalierbarere Lösung bieten. Die Wartung kann jedoch trotzdem kostspielig und zeitaufwändig sein. Administratoren müssen möglicherweise zusätzliche Arbeit leisten, um sicherzustellen, dass die Verteilung des Systems transparent ist. Möglicherweise müssen sie auch den „getrennten“ Charakter der Datenbank berücksichtigen.

Durch [Azure Cosmos DB](./introduction.md) werden diese Aufgaben und Herausforderungen vereinfacht, weil die Bereitstellung weltweit in allen Azure-Regionen erfolgt. Partitionsbereiche können dynamisch unterteilt werden, um die Datenbank nahtlos mit der Anwendung zu vergrößern, während gleichzeitig Hochverfügbarkeit aufrechterhalten wird. Durch eine differenzierte Mehrinstanzenfähigkeit und streng kontrollierte, cloudbasierte Ressourcenkontrolle werden [erstaunliche Latenzgarantien](./consistency-levels.md#consistency-levels-and-latency) und eine vorhersagbare Leistung ermöglicht. Die Partitionierung ist vollständig verwaltet, sodass Administratoren keinen Code schreiben oder Partitionen verwalten müssen.

Wenn Ihr Transaktionsvolumen ein extremes Niveau (z. B. viele Tausende von Transaktionen pro Sekunde) erreicht, sollten Sie eine verteilte NoSQL-Datenbank in Erwägung ziehen. Ziehen Sie die Verwendung von Azure Cosmos DB in Betracht, um von maximaler Effizienz, einer einfachen Wartung und geringeren Gesamtbetriebskosten profitieren zu können.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Back-End" border="false":::

## <a name="hierarchical-data"></a>Hierarchische Daten

Es gibt eine beachtliche Anzahl von Anwendungsfällen, in denen Transaktionen in der Datenbank viele Beziehungen zwischen übergeordneten und untergeordneten Elementen enthalten können. Diese Beziehungen können im Laufe der Zeit erheblich zunehmen und sich als schwer zu verwalten erweisen. Formen von [hierarchischen Datenbanken](https://en.wikipedia.org/wiki/Hierarchical_database_model) sind in den 1980er-Jahren entstanden, waren aber aufgrund von Ineffizienz im Speicher nicht beliebt. Sie verloren auch an Bedeutung, weil das [relationale Modell von Ted Codd](https://en.wikipedia.org/wiki/Relational_model) zum De-Facto-Standard für praktisch alle gängigen Datenbankverwaltungssysteme wurde.

Heutzutage hat die Beliebtheit von Datenbanken im Dokumentstil jedoch deutlich zugenommen. Diese Datenbanken können als eine Neuerfindung des hierarchischen Datenbankparadigmas angesehen werden, das nun nicht mehr durch Bedenken im Hinblick auf die Kosten für das Speichern von Daten auf dem Datenträger gehemmt wird. Folglich könnte die Verwaltung vieler komplexer Beziehungen zwischen übergeordneten und untergeordneten Entitäten in einer relationalen Datenbank inzwischen als Antimuster im Vergleich zu modernen dokumentenorientierten Ansätzen angesehen werden.

Das Aufkommen eines [objektorientierten Entwurfs](https://en.wikipedia.org/wiki/Object-oriented_design) und der [Impedance Mismatch](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch), der bei der Kombination mit relationalen Modellen entsteht, stellen auch ein Antimuster in relationalen Datenbanken für bestimmte Anwendungsfälle dar. Dadurch können versteckte, aber oft erhebliche Wartungskosten entstehen. Obwohl [ORM-Ansätze](https://en.wikipedia.org/wiki/Object-relational_mapping) entwickelt wurden, um dies teilweise zu mildern, können dokumentenorientierte Datenbanken dennoch viel besser mit objektorientierten Ansätzen kombiniert werden. Bei diesem Ansatz sind Entwickler nicht gezwungen, sich auf ORM-Treiber oder maßgeschneiderte sprachspezifische [OO-Datenbank-Engines](https://en.wikipedia.org/wiki/Object_database) festzulegen. Wenn Ihre Daten viele Beziehungen zwischen über- und untergeordneten Elementen und tiefgreifende Hierarchieebenen enthalten, sollten Sie die Verwendung einer NoSQL-Dokumentdatenbank wie die [Azure Cosmos DB SQL-API](./introduction.md) in Erwägung ziehen.

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Komplexe Netzwerke und Beziehungen

Ironischerweise stellen relationale Datenbanken in Anbetracht ihres Namens eine keineswegs optimale Lösung für die Modellierung tiefer und komplexer Beziehungen dar. Der Grund dafür ist, dass Beziehungen zwischen Entitäten in einer relationalen Datenbank nicht tatsächlich vorhanden sind. Sie müssen zur Laufzeit berechnet werden, wobei komplexe Beziehungen kartesische Joins erfordern, um die Zuordnung mithilfe von Abfragen zu ermöglichen. Folglich werden Vorgänge in Bezug auf die Berechnung exponentiell teurer, wenn die Beziehungen zunehmen. In einigen Fällen wird eine relationale Datenbank, die versucht, solche Entitäten zu verwalten, unbrauchbar.

Verschiedene Formen von „Netzwerkdatenbanken“ sind in der Zeit aufgekommen, in der relationale Datenbanken entstanden sind, aber wie hierarchische Datenbanken konnten auch diese Systeme nur schwer an Popularität gewinnen. Die langsame Akzeptanz war auf fehlende Anwendungsfälle zu dieser Zeit und auf Speicherineffizienzen zurückzuführen. Heute könnten Graphdatenbank-Engines als „Wiederauferstehung“ des Netzwerkdatenbank-Paradigmas angesehen werden. Der Hauptvorteil dieser Systeme besteht darin, dass Beziehungen als „First-Class Citizens“ in der Datenbank gespeichert werden. So können Beziehungen in konstanter Zeit durchlaufen werden, anstatt mit jedem neuen Join oder Kreuzprodukt an Zeitkomplexität zuzunehmen.

Wenn Sie ein komplexes Beziehungsnetzwerk in Ihrer Datenbank verwalten, sollten Sie eine Graphdatenbank wie die [Azure Cosmos DB Gremlin-API](./graph-introduction.md) zum Verwalten dieser Daten in Betracht ziehen.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Datenbankdiagramm mit mehreren Mitarbeitern und Abteilungen, die miteinander verbunden sind":::

Azure Cosmos DB ist ein Datenbankdienst mit mehreren Modellen, der eine API-Projektion für alle wichtigen NoSQL-Modelltypen (Spaltenfamilie, Dokument, Graph und Schlüssel-Wert) bietet. Die Schichten der APIs [Gremlin (Graph)](./gremlin-support.md) und SQL (Core) sind vollständig interoperabel. Dies bietet Vorteile beim Wechseln zwischen verschiedenen Modellen auf Programmierbarkeitsebene. Graphspeicher können in Bezug auf komplexe Netzwerktransaktionen und in Bezug auf im selben Speicher als Dokumentdatensätze modellierte Transaktionen abgefragt werden.

## <a name="fluid-schema"></a>Flexibles Schema

Ein weiteres besonderes Merkmal von relationalen Datenbanken ist, dass Schemas zur Entwurfszeit definiert werden müssen. Dies hat Vorteile in Bezug auf die referentielle Integrität und die Konformität der Daten. Dies kann jedoch auch restriktiv sein, wenn die Größe der Anwendung zunimmt. Das Reagieren auf Änderungen im Schema über logisch getrennte Modelle hinweg, die dieselbe Tabelle oder Datenbankdefinition verwenden, kann mit der Zeit kompliziert werden. Solche Anwendungsfälle profitieren häufig von dem Schema, das an die Anwendung zur Verwaltung pro Datensatz übertragen wird. Dazu muss die Datenbank „schemaagnostisch“ sein und zulassen, dass die Datensätze in Bezug auf die darin enthaltenen Daten „selbstbeschreibend“ sind.

Wenn Sie Daten verwalten, deren Strukturen ständig sehr schnell geändert werden, insbesondere wenn Transaktionen aus externen Quellen stammen können, bei denen es schwierig ist, die Konformität in der Datenbank zu erzwingen, sollten Sie einen eher schemaagnostischen Ansatz mit einem verwalteten NoSQL-Datenbankdienst wie Azure Cosmos DB in Erwägung ziehen.

## <a name="microservices"></a>Microservices

Das [Microservice](https://en.wikipedia.org/wiki/Microservices)-Muster hat sich in den letzten Jahren deutlich entwickelt. Dieses Muster hat seine Wurzeln in der [dienstorientierten Architektur](https://en.wikipedia.org/wiki/Service-oriented_architecture). Der De-Facto-Standard für die Datenübertragung in diesen modernen Microservice-Architekturen ist [JSON](https://en.wikipedia.org/wiki/JSON), bei dem es sich auch um das Speichermedium für die große Mehrheit der dokumentenorientierten NoSQL-Datenbanken handelt. Dadurch werden NoSQL-Dokumentspeicher zu einer viel nahtloseren Lösung für die Persistenz und Synchronisierung (unter Verwendung von [Ereignissourcingmustern](https://en.wikipedia.org/wiki/Event-driven_architecture)) in komplexen Microservice-Implementierungen. Die Wartung von herkömmlichen relationalen Datenbanken kann in diesen Architekturen sehr viel komplexer sein. Dies liegt an dem größeren Transformationsaufwand, der sowohl für den Status als auch für die Synchronisierung über APIs erforderlich ist. Insbesondere verfügt Azure Cosmos DB über zahlreiche Features, durch die der Dienst zu einer noch nahtloseren Lösung für JSON-basierte Microservice-Architekturen wird als viele NoSQL-Datenbanken:

* Eine Auswahl reiner JSON-Datentypen
* Eine JavaScript-Engine und [Abfrage-API](./javascript-query-api.md), die in die Datenbank integriert sind.
* Ein hochmoderner [Änderungsfeed](./change-feed.md), den Clients abonnieren können, um über Änderungen an einem Container benachrichtigt zu werden.

## <a name="some-challenges-with-nosql-databases"></a>Einige Herausforderungen bei NoSQL-Datenbanken

Obwohl die Implementierung von NoSQL-Datenbanken einige deutliche Vorteile bietet, sind damit auch einige Herausforderungen verbunden, die Sie berücksichtigen sollten. Diese sind nicht im selben Maß vorhanden wie beim Arbeiten mit dem relationalen Modell:

* Transaktionen mit vielen Beziehungen, die auf dieselbe Entität verweisen
* Transaktionen, die eine hohe Konsistenz im gesamten Dataset erfordern

Im Hinblick auf die erste Herausforderung ist die Faustregel in NoSQL-Datenbanken im Allgemeinen die Denormalisierung, die (wie bereits erwähnt) effizientere Lesevorgänge in einem verteilten System ermöglicht. Bei diesem Ansatz kommen jedoch einige Entwurfsherausforderungen zum Tragen. Nehmen wir ein Beispiel für ein Produkt, das mit einer Kategorie und mehreren Tags verknüpft ist:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Joins":::

Eine bewährte Methode in einer NoSQL-Dokumentdatenbank wäre die direkte Denormalisierung des Kategorienamens und der Tagnamen in einem „Produktdokument“. Um jedoch Kategorien, Tags und Produkte synchron zu halten, sind die Entwurfsoptionen, die dies erleichtern, komplexer in der Verwaltung, weil die Daten über mehrere Datensätze im Produkt dupliziert werden, anstatt eine einfache Aktualisierung in einer „Eins-zu-viele“-Beziehung und ein Join zum Abrufen der Daten zu sein. 

Der Kompromiss besteht darin, dass Lesevorgänge im denormalisierten Datensatz effizienter sind und immer effizienter werden, wenn die Anzahl der konzeptionell verknüpften Entitäten zunimmt. Doch so wie die Leseeffizienz mit zunehmender Anzahl verknüpfter Entitäten in einem denormalisierten Datensatz zunimmt, erhöht sich auch die Verwaltungskomplexität der Synchronisierung von Entitäten. Eine Möglichkeit, diesen Kompromiss abzuschwächen, besteht darin, ein [Hybriddatenmodell](./modeling-data.md#hybrid-data-models) zu erstellen.

Obwohl NoSQL-Datenbanken mehr Flexibilität für den Umgang mit diesen Kompromissen bieten, kann eine erhöhte Flexibilität auch zu weiteren Entwurfsentscheidungen führen. Lesen Sie unseren Artikel [Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels](./how-to-model-partition-example.md). Darin finden Sie einen Ansatz zur [Synchronisierung denormalisierter Benutzerdaten](./how-to-model-partition-example.md#denormalizing-usernames), bei dem sich die Benutzer nicht nur in verschiedenen Partitionen, sondern auch in verschiedenen Containern befinden.

Im Hinblick auf eine hohe Konsistenz ist es selten, dass diese im gesamten Dataset erforderlich ist. In Fällen, in denen dies erforderlich ist, kann dies jedoch in verteilten Datenbanken eine Herausforderung darstellen. Um eine hohe Konsistenz sicherzustellen, müssen die Daten in allen Replikaten und Regionen synchronisiert werden, bevor sie von Clients gelesen werden können. Dadurch kann sich die Latenz von Lesevorgängen erhöhen.

Auch hier bietet Azure Cosmos DB mehr Flexibilität als relationale Datenbanken für die verschiedenen, hier relevanten Kompromisse, aber bei kleinen Implementierungen sind bei diesem Ansatz möglicherweise weitere Entwurfsüberlegungen zu berücksichtigen. Weitere Details zu diesem Thema finden Sie in unserem Artikel [Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung](./consistency-levels.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten Ihres Azure Cosmos-Kontos und zu anderen Konzepten:

* [Verwalten von Datenbankkonten in Azure Cosmos DB](how-to-manage-database-account.md)
* [Globale Verteilung](distribute-data-globally.md)
* [Konsistenzebenen](consistency-levels.md)
* [Arbeiten mit Azure Cosmos-Datenbanken, -Containern und -Elementen](account-databases-containers-items.md)
* [Sicherer Zugriff auf ein Azure Cosmos DB-Konto durch Verwenden eines Azure Virtual Network-Dienstendpunkts](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos DB-Firewallunterstützung](how-to-configure-firewall.md)
* [Hinzufügen/Entfernen von Regionen für Ihr Datenbankkonto](how-to-manage-database-account.md)
* [SLA für Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)