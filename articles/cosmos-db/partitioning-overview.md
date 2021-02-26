---
title: Partitionierung und horizontale Skalierung in Azure Cosmos DB
description: Erfahren Sie mehr über die Partitionierung, logische und physische Partitionen in Azure Cosmos DB, bewährte Methoden bei der Auswahl eines Partitionsschlüssels und das Verwalten logischer Partitionen.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: b91c846b5a79125c1cee9c36ce81b5c3d3229ba9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627768"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionierung und horizontale Skalierung in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB arbeitet mit Partitionierung, um einzelne Container in einer Datenbank entsprechend den Leistungsanforderungen Ihrer Anwendung zu skalieren. Mithilfe der Partitionierung werden die Elemente in einem Container in Teilgruppen unterteilt, die als *logische Partitionen* bezeichnet werden. Logische Partitionen werden basierend auf dem Wert des *Partitionsschlüssels* erstellt, die jedem Element in einem Container zugeordnet ist. Alle Elemente in einer logischen Partition haben denselben Partitionsschlüsselwert.

Angenommen, ein Container enthält Elemente. Jedes Element hat für die `UserID`-Eigenschaft einen eindeutigen Wert. Wenn `UserID` als Partitionsschlüssel für die Elemente in einem Container dient und es 1.000 eindeutige `UserID`-Werte gibt, werden für den Container 1.000 logische Partitionen erstellt.

Zusätzlich zu einem Partitionsschlüssel, der die logische Partition des Elements bestimmt, weist jedes Element in einem Container eine (innerhalb der logischen Partition eindeutige) *Element-ID* auf. Durch die Kombination des Partitionsschlüssels und der *Element-ID* wird der *Index* des Elements erstellt, der das Element eindeutig identifiziert. Die [Auswahl eines Partitionsschlüssels](#choose-partitionkey) ist eine wichtige Entscheidung, die Auswirkungen auf die Leistung Ihrer Anwendung hat.

In diesem Artikel wird die Beziehung zwischen logischen und physischen Partitionen erörtert. Außerdem werden bewährte Methoden für die Partitionierung erläutert. Und Sie erhalten eine detaillierte Übersicht über die Funktionsweise der horizontalen Skalierung in Azure Cosmos DB. Sie müssen diese internen Details nicht unbedingt verstehen, um Ihren Partitionsschlüssel auswählen zu können, aber wir haben sie angeführt, damit Sie Klarheit über die Funktionsweise von Azure Cosmos DB haben.

## <a name="logical-partitions"></a>Logische Partitionen

Eine logische Partition besteht aus einer Gruppe von Elementen mit demselben Partitionsschlüssel. Beispielsweise enthalten alle Elemente in einem Container mit Daten über Nahrungsmittel eine `foodGroup`-Eigenschaft. Sie können `foodGroup` als Partitionsschlüssel für den Container verwenden. Elementgruppen mit bestimmten Werten für `foodGroup`, z.B. `Beef Products`, `Baked Products` und `Sausages and Luncheon Meats`, bilden eindeutige logische Partitionen. Sie müssen sich keine Gedanken über das Löschen einer logischen Partition machen, wenn die zugrunde liegenden Daten gelöscht werden.

Eine logische Partition definiert auch den Bereich für Datenbanktransaktionen. Sie können Elemente in einer logischen Partition mithilfe einer [Transaktion mit Momentaufnahmeisolation](database-transactions-optimistic-concurrency.md) aktualisieren. Wenn einem Container neue Elemente hinzugefügt werden, werden neue logische Partitionen transparent vom System erstellt.

Es besteht keine Beschränkung für die Anzahl von logischen Partitionen in Ihrem Container. Jede logische Partition kann bis zu 20 GB Daten speichern. Durch eine sinnvolle Auswahl des Partitionsschlüssels ergeben sich viele mögliche Werte. Beispielsweise können in einem Container, in dem alle Elemente eine `foodGroup`-Eigenschaft aufweisen, die Daten innerhalb der logischen Partition `Beef Products` auf bis zu 20 GB anwachsen. Durch [Auswählen eines Partitionsschlüssels](#choose-partitionkey) mit einer Vielzahl möglicher Werte können Sie sicherstellen, dass der Container skaliert werden kann.

## <a name="physical-partitions"></a>Physische Partitionen

Ein Container wird skaliert, indem Daten und Durchsatz auf physische Partitionen verteilt werden. Intern wird mindestens eine logische Partition einer einzelnen physischen Partition zugeordnet. Normalerweise verfügen kleinere Container über viele logische Partitionen, benötigen aber nur eine einzige physische Partition. Im Gegensatz zu logischen Partitionen sind physische Partitionen eine interne Implementierung des Systems, und sie werden vollständig von Azure Cosmos DB verwaltet.

Die Anzahl der physischen Partitionen in Ihrem Container hängt von folgenden Faktoren ab:

* Der Menge des bereitgestellten Durchsatzes (jede einzelne physische Partition kann einen Durchsatz von bis zu 10.000 Anforderungseinheiten pro Sekunde bereitstellen)
* Dem gesamten Datenspeicher (jede einzelne physische Partition kann bis zu 50 GB Daten speichern)

> [!NOTE]
> Physische Partitionen sind eine interne Implementierung des Systems, die vollständig von Azure Cosmos DB verwaltet werden. Sie müssen sich beim Entwickeln Ihrer Lösungen nicht auf physische Partitionen konzentrieren, da Sie diese nicht kontrollieren können. Konzentrieren Sie sich stattdessen auf die Partitionsschlüssel. Wenn Sie einen Partitionsschlüssel auswählen, der den verbrauchten Durchsatz gleichmäßig auf logische Partitionen verteilt, stellen Sie sicher, dass der Durchsatzverbrauch auf den physischen Partitionen ausgeglichen ist.

Es besteht keine Beschränkung für die Gesamtanzahl von physischen Partitionen in Ihrem Container. Wenn der bereitgestellte Durchsatz oder die Datengröße zunimmt, erstellt Azure Cosmos DB automatisch neue physische Partitionen, indem die vorhandenen aufgeteilt werden. Die Aufteilung physischer Partitionen hat keine Auswirkungen auf die Verfügbarkeit Ihrer Anwendung. Nach der Aufteilung einer physischen Partition bleiben alle Daten innerhalb einer einzelnen logischen Partition weiterhin auf derselben physischen Partition gespeichert. Bei der Aufteilung physischer Partitionen wird einfach eine neue Zuordnung der logischen Partitionen zu physischen Partitionen erstellt.

Der für einen Container bereitgestellte Durchsatz wird gleichmäßig auf physische Partitionen aufgeteilt. Ein Partitionsschlüsselentwurf, der Anforderungen nicht gleichmäßig verteilt, kann dazu führen, dass zu viele Anforderungen an eine kleine Teilmenge von Partitionen geleitet werden, die dadurch „heiß“ werden. „Heiße“ Partitionen bewirken eine ineffiziente Nutzung des bereitgestellten Durchsatzes, was zu einer Ratenbegrenzung und höheren Kosten führen kann.

Sie können die physischen Partitionen Ihres Containers im Azure-Portal auf dem Blatt **Metriken** im Abschnitt **Speicher** anzeigen:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Anzeigen der Anzahl von physischen Partitionen" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Im oben gezeigten Screenshot weist ein Container `/foodGroup` als Partitionsschlüssel auf. Jeder der drei Balken im Diagramm stellt eine physische Partition dar. In der Abbildung entspricht der **Partitionsschlüsselbereich** einer physischen Partition. Die ausgewählte physische Partition enthält die drei wichtigsten logischen Partitionen: `Beef Products`, `Vegetable and Vegetable Products` und `Soups, Sauces, and Gravies`.

Wenn Sie einen Durchsatz von 18.000 Anforderungseinheiten pro Sekunde (RU/s) bereitstellen, kann jede der drei physischen Partitionen 1/3 des insgesamt bereitgestellten Durchsatzes nutzen. Innerhalb der ausgewählten physischen Partition können die logischen Partitionsschlüssel `Beef Products`, `Vegetable and Vegetable Products` und `Soups, Sauces, and Gravies` die 6.000 bereitgestellten RU/s der physischen Partition gemeinsam nutzen. Weil der bereitgestellte Durchsatz gleichmäßig auf die physischen Partitionen Ihres Containers aufgeteilt ist, müssen Sie unbedingt einen Partitionsschlüssel auswählen, der den verbrauchten Durchsatz gleichmäßig aufteilt. Informationen hierzu finden Sie unter [Auswählen eines Partitionsschlüssels](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Verwalten logischer Partitionen

Azure Cosmos DB verwaltet die Platzierung logischer Partitionen in physischen Partitionen transparent und automatisch, um die Anforderungen an Skalierbarkeit und Leistung des Containers effizient zu erfüllen. Wenn sich die Anforderungen einer Anwendung an Durchsatz und Speicher erhöhen, verschiebt Azure Cosmos DB logische Partitionen, um die Last automatisch auf eine größere Anzahl physischer Partitionen zu verteilen. Weitere Informationen zu physischen Partitionen finden Sie [hier](partitioning-overview.md#physical-partitions).

Azure Cosmos DB verwendet eine hashbasierte Partitionierung, um logische Partitionen auf physische Partitionen zu verteilen. Azure Cosmos DB erstellt für den Partitionsschlüsselwert eines Elements einen Hashwert. Das Hashergebnis bestimmt die physische Partition. Anschließendverteilt Azure Cosmos DB den Schlüsselbereich der Partitionsschlüsselhashes gleichmäßig auf die physischen Partitionen.

Transaktionen (in gespeicherten Prozeduren oder Triggern) sind nur für Elemente in einer einzelnen logischen Partition zulässig.

Weitere Informationen darüber, wie Azure Cosmos DB Partitionen verwaltet, finden Sie [hier](partitioning-overview.md). (Es ist nicht notwendig, die internen Details zu verstehen, um Ihre Anwendungen zu erstellen oder auszuführen, sie werden aber hier für neugierige Leser beschrieben.)

## <a name="replica-sets"></a>Replikatgruppen

Jede physische Partition besteht aus [*Replikatgruppen*](global-dist-under-the-hood.md). Jede Replikatgruppe hostet eine Instanz der Datenbank-Engine. Eine Replikatgruppe macht die in der physischen Partition gespeicherten Daten dauerhaft, hochverfügbar und konsistent. Jedes Replikat, aus dem die physische Partition besteht, erbt das Speicherkontingent der Partition. Alle Replikate einer physischen Partition unterstützen gemeinsam den Durchsatz, der der physischen Partition zugeordnet wurde. Replikatgruppen werden von Azure Cosmos DB automatisch verwaltet.

Normalerweise benötigen kleinere Container nur eine einzige physische Partition, sie verfügen jedoch trotzdem über mindestens 4 Replikate.

Die folgende Abbildung zeigt, wie logische Partitionen physischen Partitionen zugeordnet werden, die global verteilt sind:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Abbildung zur Veranschaulichung der Azure Cosmos DB-Partitionierung" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Auswählen eines Partitionsschlüssels

Ein Partitionsschlüssel besteht aus zwei Komponenten: dem **Partitionsschlüsselpfad** und dem **Partitionsschlüsselwert**. Betrachten Sie z. B. dieses Element: { "userId" : "Andrew", "worksFor": "Microsoft" }. Wenn Sie "userId" als Partitionsschlüssel auswählen, sind die folgenden beiden Elemente die beiden Partitionsschlüsselkomponenten:

* Der Partitionsschlüsselpfad (Beispiel: "/userId"). Der Partitionsschlüsselpfad akzeptiert alphanumerische Zeichen und Unterstriche (_). Mithilfe der Standardpfadnotation (/) können Sie auch geschachtelte Objekte verwenden.

* Der Partitionsschlüsselwert (Beispiel: "Andrew"). Der Partitionsschlüsselwert kann vom Typ „string“ oder „numeric“ sein.

Informationen zu den Beschränkungen im Hinblick auf Durchsatz, Speicher und Länge des Partitionsschlüssels finden Sie im Artikel [Kontingente im Azure Cosmos DB-Dienst](concepts-limits.md).

Die Auswahl Ihres Partitionsschlüssels ist eine einfache, aber wichtige Entwurfsentscheidung in Azure Cosmos DB. Nachdem Sie Ihren Partitionsschlüssel ausgewählt haben, ist es nicht möglich, ihn direkt zu ändern. Wenn Sie Ihren Partitionsschlüssel ändern müssen, sollten Sie Ihre Daten mit dem neuen gewünschten Partitionsschlüssel in einen neuen Container verschieben.

Für **alle** Container sollte Ihr Partitionsschlüssel die folgenden Punkte erfüllen:

* Er sollte eine Eigenschaft mit einem Wert sein, der sich nicht ändert. Wenn eine Eigenschaft Ihr Partitionsschlüssel ist, können Sie den Wert dieser Eigenschaft nicht aktualisieren.

* Er sollte eine hohe Kardinalität aufweisen. Anders ausgedrückt: Die Eigenschaft sollte über eine Vielzahl möglicher Werte verfügen.

* Er sollte die zu verbrauchenden Anforderungseinheiten (Request Units, RUs) und die Datenspeicherung gleichmäßig auf alle logischen Partitionen verteilen. Dadurch wird ein gleichmäßiger RU-Verbrauch und eine gleichmäßige Speicherverteilung auf Ihren physischen Partitionen sichergestellt.

Wenn Sie in Azure Cosmos DB [ACID-Transaktionen mit mehreren Elementen](database-transactions-optimistic-concurrency.md#multi-item-transactions) benötigen, müssen Sie [gespeicherte Prozeduren oder Trigger](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) verwenden. Alle JavaScript-basierten gespeicherten Prozeduren und Trigger sind auf den Bereich einer einzelnen logischen Partition beschränkt.

## <a name="partition-keys-for-read-heavy-containers"></a>Partitionsschlüssel für Container mit vielen Lesevorgängen

Bei den meisten Containern müssen Sie beim Auswählen eines Partitionsschlüssels nur die oben aufgeführten Kriterien beachten. Für große Container mit vielen Lesevorgängen sollten Sie jedoch einen Partitionsschlüssel auswählen, der häufig als Filter in Ihren Abfragen verwendet wird. Abfragen können [effizient nur an die relevanten physischen Partitionen weitergeleitet werden](how-to-query-container.md#in-partition-query), indem der Partitionsschlüssel in das Filterprädikat eingeschlossen wird.

Wenn es sich bei den meisten Anforderungen Ihrer Workload um Abfragen handelt und die meisten Abfragen einen Gleichheitsfilter für dieselbe Eigenschaft aufweisen, kann diese Eigenschaft eine gute Wahl für den Partitionsschlüssel darstellen. Wenn Sie z. B. häufig eine Abfrage ausführen, die nach `UserID` filtert, würde durch Auswählen von `UserID` als Partitionsschlüssel die Anzahl der [partitionsübergreifenden Abfragen](how-to-query-container.md#avoiding-cross-partition-queries) reduziert.

Wenn Sie jedoch über einen kleinen Container verfügen, sind wahrscheinlich nicht genügend physische Partitionen vorhanden, um sich Gedanken über die Leistungsauswirkungen von partitionsübergreifenden Abfragen machen zu müssen. Die meisten kleinen Container in Azure Cosmos DB benötigen nur eine oder zwei physische Partitionen.

Wenn Ihr Container auf mehr als ein paar physische Partitionen anwachsen könnte, sollten Sie unbedingt einen Partitionsschlüssel auswählen, der die Anzahl von partitionsübergreifenden Abfragen minimiert. Ihr Container benötigt mehr als ein paar physische Partitionen, wenn einer der folgenden Punkte zutrifft:

* Für Ihren Container werden über 30.000 Anforderungseinheiten bereitgestellt.

* In Ihrem Container werden mehr als 100 GB Daten gespeichert.

## <a name="using-item-id-as-the-partition-key"></a>Verwenden der Element-ID als Partitionsschlüssel

Wenn Ihr Container über eine Eigenschaft mit einer Vielzahl möglicher Werte verfügt, stellt diese Eigenschaft wahrscheinlich eine gute Wahl für den Partitionsschlüssel dar. Ein mögliches Beispiel für eine solche Eigenschaft ist die *Element-ID*. Für kleine Container mit vielen Lesevorgängen oder für beliebig große Container mit vielen Schreibvorgängen stellt die *Element-ID* natürlich eine gute Wahl für den Partitionsschlüssel dar.

Die Systemeigenschaft *Element-ID* ist in jedem in Ihrem Container enthaltenen Element vorhanden. Möglicherweise verfügen Sie über weitere Eigenschaften, die eine logische ID Ihres Elements darstellen. In vielen Fällen handelt es sich auch dabei aus den gleichen Gründen wie bei der *Element-ID* um eine gute Wahl für den Partitionsschlüssel.

Die *Element-ID* stellt aus den folgenden Gründen eine gute Wahl für den Partitionsschlüssel dar:

* Es ist eine Vielzahl möglicher Werte vorhanden (eine eindeutige *Element-ID* pro Element).
* Weil pro Element eine eindeutige *Element-ID* vorhanden ist, leistet die *Element-ID* hervorragende Arbeit, um den RU-Verbrauch und die Datenspeicherung gleichmäßig zu verteilen.
* Sie können problemlos effiziente Punktlesevorgänge ausführen, weil Ihnen der Partitionsschlüssel eines Elements immer bekannt ist, wenn Sie die entsprechende *Element-ID* kennen.

Wenn Sie die *Element-ID* als Partitionsschlüssel auswählen, müssen Sie die folgenden Punkte berücksichtigen:

* Bei Verwendung der *Element-ID* als Partitionsschlüssel wird diese im gesamten Container zu einem eindeutigen Bezeichner. Sie können keine Elemente verwenden, die eine doppelte *Element-ID* aufweisen.
* Wenn Sie über einen Container mit vielen Lesevorgängen verfügen, der viele [physische Partitionen](partitioning-overview.md#physical-partitions) umfasst, sind Abfragen effizienter, wenn ein Gleichheitsfilter mit der *Element-ID* verwendet wird.
* Sie können keine gespeicherten Prozeduren oder Trigger für mehrere logische Partitionen ausführen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über den [bereitgestellten Durchsatz in Azure Cosmos DB](request-units.md).
* Erfahren Sie mehr über die [globale Verteilung in Azure Cosmos DB](distribute-data-globally.md).
* Erfahren Sie mehr über das [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).
* Erfahren Sie mehr über das [Bereitstellen des Durchsatzes für eine Datenbank in Azure Cosmos](how-to-provision-database-throughput.md).
