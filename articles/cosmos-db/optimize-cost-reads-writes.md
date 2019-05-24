---
title: Optimieren der Kosten für Lese- und Schreibvorgänge in Azure Cosmos DB
description: In diesem Artikel wird erläutert, wird Sie in Azure Cosmos DB die Kosten beim Ausführen von Lese- und Schreibvorgängen für die Daten senken können.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 13ce5ee8b0e2a5d9cc84ea1a408ebba152b46050
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967404"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimieren der Lese- und Schreibkosten in Azure Cosmos DB

In diesem Artikel wird beschrieben, wie die Kosten für das Lesen und Schreiben von Daten in Azure Cosmos DB berechnet werden. Zu den Lesevorgängen zählen GET-Vorgänge für die Elemente, und zu den Schreibvorgängen gehören Einfüge-, Ersetzungs-, Löschungs- und Upsert-Vorgänge für die Elemente.  

## <a name="cost-of-reads-and-writes"></a>Kosten für Lese- und Schreibvorgänge

Durch die Verwendung eines Modells mit bereitgestelltem Durchsatz garantiert Azure Cosmos DB eine vorhersagbare Leistung in Hinblick auf Durchsatz und Latenz. Der bereitgestellte Durchsatz wird in Form von [Anforderungseinheiten](request-units.md) pro Sekunde bzw. RUs/Sek. dargestellt. Eine Anforderungseinheit (RU) ist eine logische Abstraktion der Compute-Ressourcen wie CPU, Arbeitsspeicher, E/A usw., die zum Ausführen einer Anforderung erforderlich sind. Der bereitgestellte Durchsatz (RUs/Sek.) wird reserviert und fest dem Container oder der Datenbank zugeordnet, um vorhersagbaren Durchsatz und vorhersehbare Latenz bereitzustellen. Mit dem bereitgestellten Durchsatz kann Azure Cosmos DB für eine vorhersagbare und konsistente Leistung sorgen, die niedrige Latenzzeiten und eine hohe Verfügbarkeit auf jeder Skalierungsstufe garantiert. Anforderungseinheiten stellen eine normalisierte Währung dar, welche die Überlegungen bezüglich der Menge der für eine Anwendung benötigten Ressourcen vereinfacht. 

Sie brauchen nicht über die Unterscheidung der Anforderungseinheiten für Lese- und Schreibvorgängen nachzudenken. Das einheitliche Währungsmodell in Form von Anforderungseinheiten sorgt für die nötige Effizienz, um die gleiche Durchsatzkapazität austauschbar für Lese- und Schreibvorgänge zu nutzen. Die folgende Tabelle enthält die Kosten für Lese- und Schreibvorgänge in Form von RUs/Sek. für Elemente der Größe 1 KB und 100 KB.

|**Elementgröße**  |**Kosten eines Lesevorgangs** |**Kosten eines Schreibvorgangs**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |50 RUs |

Das Lesen eines Elements mit einer Größe von 1 KB kostet eine Anforderungseinheit (1 RU). Das Schreiben eines Elements mit einer Größe von 1 KB kostet fünf Anforderungseinheiten (5 RUs). Die Kosten für Lese- und Schreibvorgänge sind anwendbar, wenn Sie die standardmäßige [Konsistenzebene „Sitzung“](consistency-levels.md) verwenden.  Die Überlegungen in Bezug auf Anforderungseinheiten (RUs) enthalten folgende Aspekte: Elementgröße, Anzahl der Eigenschaften, Datenkonsistenz, indizierte Eigenschaften, Indizierung und Abfragemuster.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Normalisierte Kosten für 1 Million Lese- und Schreibvorgänge

Die Bereitstellung von 1.000 RUs/Sek. entspricht 3,6 Mio. RUs/Stunde und kostet 0,08 US-Dollar pro Stunde (in den USA und Europa). Für ein Element mit 1 KB können Sie mit diesem bereitgestellten Durchsatz pro Stunde 3,6 Mio. Lesevorgänge oder 0,72 Mio. Schreibvorgänge ausführen (Rechenvorgang: `3.6 million RU / 5`). Normalisiert auf eine Million Lese- und Schreibvorgänge würden die Kosten 0,022 US-Dollar für 1 Mio. Lesevorgänge (Rechenvorgang: 0,08 US-Dollar/3,6 Millionen) und 0,111 US-Dollar für 1 Mio. Schreibvorgänge (Rechenvorgang: 0,08 US-Dollar/0,72 Millionen) betragen.

## <a name="number-of-regions-and-the-request-units-cost"></a>Anzahl der Regionen und Kosten der Anforderungseinheiten

Die Kosten für Schreibvorgänge sind konstant, und zwar unabhängig von der Anzahl der Regionen, die mit dem Azure Cosmos-Konto verknüpft sind. Das heißt, ein Schreibvorgang für 1 KB kostet fünf RUs, unabhängig von der Anzahl der Regionen, die dem Konto zugeordnet sind. Eine nicht unerhebliche Menge von Ressourcen wird für das Replizieren, Akzeptieren und Verarbeiten des Replikationsdatenverkehrs für jede Region aufgewendet. Ausführliche Informationen zur Kostenoptimierung bei mehreren Regionen finden Sie im Artikel [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md).

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimieren der Kosten für Schreib- und Lesevorgänge

Wenn Sie Schreibvorgänge ausführen, sollten Sie ausreichend Kapazität bereitstellen, um die Anzahl der erforderlichen Schreibvorgänge pro Sekunde zu unterstützen. Sie können mithilfe des SDKs, des Portals oder der CLI den bereitgestellten Durchsatz erhöhen, bevor Sie die Schreibvorgänge ausführen, und nach dem Abschluss der Schreibvorgänge den Durchsatz wieder reduzieren. Der Durchsatz für den Zeitraum der Schreibvorgänge errechnet sich aus dem für die jeweiligen Daten erforderlichen Mindestdurchsatz plus dem Durchsatz, der für das Einfügen der Workload erforderlich ist (sofern keine anderen Workloads ausgeführt werden). 

Wenn Sie gleichzeitig andere Workloads (z. B. Abfrage-, Lese-,Aktualisierungs-, Löschvorgänge) ausführen, müssen Sie auch die für diese Vorgänge erforderlichen Anforderungseinheiten hinzuaddieren. Wenn Schreibvorgänge der Ratenbegrenzung unterliegen, können Sie die Wiederholungs-/Backoffrichtlinie mithilfe der Azure Cosmos DB-SDKs anpassen. Sie können beispielsweise die Last erhöhen, bis nur noch ein kleiner Prozentsatz der Anforderungen der Ratenbegrenzung unterliegt. Bei einer Ratenbegrenzung sollte die Clientanwendung die ratenbegrenzten Anforderungen für das angegebene Wiederholungsintervall zurückstellen. Vor der Wiederholung der Schreibvorgänge und zwischen den Wiederholungsversuchen sollte eine minimale Zeitspanne liegen. Wiederholungsrichtlinien werden in SQL .NET-, Java-, Node.js- und Python-SDKs sowie in allen unterstützten Versionen der .NET Core-SDKs unterstützt. 

Mithilfe von [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) können Sie auch einen Massenimport von Daten in Azure Cosmos DB vornehmen oder Daten aus einem beliebigen unterstützten Quelldatenspeicher nach Azure Cosmos DB kopieren. Azure Data Factory ist nativ in die Azure Cosmos DB-Bulk-API integriert, um beim Schreiben von Daten die beste Leistung zu erzielen.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](optimize-cost-queries.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
