---
title: Anpassen der Kapazität für Abfrage- und Indexworkloads
titleSuffix: Azure Cognitive Search
description: Passen Sie Partitions- und Replikatcomputerressourcen in der kognitiven Azure-Suche an, wobei jede Ressource in abrechenbaren Sucheinheiten abgerechnet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 92dcbfd360938724bb65b734d7c69ea61d7826b0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533042"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Anpassen der Kapazität eines Azure Cognitive Search-Diensts

Nehmen Sie sich einige Minuten Zeit, bevor Sie [einen Suchdienst bereitstellen](search-create-service-portal.md) und einen bestimmten Tarif festlegen, um zu verstehen, wie die Kapazität funktioniert und wie Sie Replikate und Partitionen an Schwankungen der Workload anpassen können.

Die Kapazität ist vom [ausgewählten Tarif](search-sku-tier.md) (Tarife bestimmen die Hardwareeigenschaften) und der für geplante Workloads erforderlichen Kombination aus Replikat und Partition abhängig. Nach der Erstellung eines Diensts können Sie die Anzahl von Replikaten oder Partitionen unabhängig voneinander erhöhen oder verringern. Die Kosten erhöhen sich mit jeder zusätzlichen physischen Ressource. Nach Abschluss umfangreicher Workloads können Sie jedoch herunterskalieren, um Ihre Kosten zu senken. Je nach Tarif und Umfang der Anpassung kann das Hinzufügen oder Reduzieren von Kapazität zwischen 15 Minuten und mehreren Stunden dauern.

Wir empfehlen, die Replikat- und Partitionszuordnung über das Azure-Portal zu ändern. Das Portal erzwingt Grenzwerte für zulässige Kombinationen, damit die Obergrenzen eines Tarifs nicht überschritten werden. Wenn die Bereitstellung jedoch skript- oder codebasiert erfolgen soll, sind die [Azure PowerShell](/rest/api/searchmanagement/services) oder die [Verwaltungs-REST-API](search-manage-powershell.md) alternative Lösungen.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Konzepte: Sucheinheiten, Replikate, Partitionen, Shards

Die Kapazität wird in *Sucheinheiten* ausgedrückt, die in Kombinationen von *Partitionen* und *Replikaten* unter Verwendung eines zugrunde liegenden *Sharding*-Mechanismus zur Unterstützung flexibler Konfigurationen zugewiesen werden können:

| Konzept  | Definition|
|----------|-----------|
|*Sucheinheit* | Eine einzelnes Inkrement der verfügbaren Gesamtkapazität (36 Einheiten). Es ist auch die Abrechnungseinheit für einen Azure Cognitive Search-Dienst. Zum Ausführen des Diensts ist mindestens eine Einheit erforderlich.|
|*Replikat* | Replikate sind Instanzen des Suchdiensts und dienen in erster Linie zum Lastenausgleich bei Abfragevorgängen. Jedes Replikat hostet eine Kopie eines Indexes. Wenn Sie drei Replikate zuordnen, stehen Ihnen drei Kopien eines Indexes für die Bearbeitung von Abfrageanforderungen zur Verfügung.|
|*Partition* | Physischer Speicher und E/A für Lese-/Schreibvorgänge (z. B. bei der Neuerstellung oder Aktualisierung eines Index). Jede Partition hat einen Anteil am Gesamtindex. Wenn Sie drei Partitionen zuordnen, wird Ihr Index in Drittel aufgeteilt. |
|*Shard* | Ein Block eines Indexes. Azure Cognitive Search unterteilt jeden Index in Shards, um das Hinzufügen von Partitionen zu beschleunigen (durch Verschieben der Shards in neue Sucheinheiten).|

Das folgende Diagramm zeigt die Beziehung zwischen Replikaten, Partitionen, Shards und Sucheinheiten. Es zeigt ein Beispiel dafür, wie ein einzelner Index in einem Dienst mit zwei Replikaten und zwei Partitionen über vier Sucheinheiten verteilt ist. Jede der vier Sucheinheiten speichert nur die Hälfte der Shards des Indexes. Die Sucheinheiten in der linken Spalte speichern die erste Hälfte der Shards, bestehend aus der ersten Partition, während die in der rechten Spalte die zweite Hälfte der Shards, bestehend aus der zweiten Partition, speichern. Da es zwei Replikate gibt, sind zwei Kopien der einzelnen Indexshards vorhanden. Die Sucheinheiten in der oberen Zeile speichern eine aus dem ersten Replikat bestehende Kopie, während die Sucheinheiten in der unteren Zeile eine aus dem zweiten Replikat bestehende weitere Kopie speichern.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Für Suchindizes erfolgt das Sharding partitionsübergreifend.":::

Das obige Diagramm ist nur ein Beispiel. Es sind viele Kombinationen von Partitionen und Replikaten möglich, bis zu einer Gesamtzahl von maximal 36 Sucheinheiten.

In Cognitive Search ist die Shardverwaltung ein Implementierungsdetail und nicht konfigurierbar. Aber das Wissen, dass das Sharding für einen Index ausgeführt wird, hilft dabei, die gelegentlichen Anomalien bei der Erstellung der Rangfolge und beim Verhalten von AutoVervollständigen zu verstehen:

+ Anomalien beim Erstellen der Rangfolge: Suchbewertungen werden zunächst auf der Shard-Ebene berechnet und dann zu einem einzelnen Resultset aggregiert. Abhängig von den Merkmalen des Shardinhalts können Übereinstimmungen aus einem Shard höher eingestuft werden als Übereinstimmungen in einem anderen Shard. Wenn Sie nicht intuitive Rangfolgen in den Suchergebnissen bemerken, ist dies höchstwahrscheinlich auf die Auswirkungen des Shardings zurückzuführen, insbesondere wenn die Indizes klein sind. Sie können diese Anomalien in der Rangfolge vermeiden, indem Sie die Option zum [globalen Berechnen von Bewertungen über den gesamten Index](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions) auswählen. Dies ist jedoch mit Leistungseinbußen verbunden.

+ Anomalien beim AutoVervollständigen: AutoVervollständigen-Abfragen, bei denen Vergleiche anhand der ersten paar Zeichen eines teilweise eingegebenen Begriffs vorgenommen werden, akzeptieren einen Fuzzyparameter, der kleine Abweichungen in der Rechtschreibung verzeiht. Bei AutoVervollständigen ist die Fuzzyübereinstimmung auf Begriffe innerhalb des aktuellen Shards beschränkt. Wenn z. B. ein Shard „Microsoft“ enthält und ein Teilbegriff von „micor“ eingegeben wird, findet die Suchmaschine in diesem Shard eine Übereinstimmung mit „Microsoft“, aber nicht in anderen Shards, die die restlichen Teile des Indexes enthalten.

## <a name="when-to-add-nodes"></a>Zeitpunkt zum Hinzufügen von Knoten

Zunächst wird einem Dienst eine Mindestmenge von Ressourcen (bestehend aus einer Partition und einem Replikat) zugeordnet.

Ein einzelner Dienst muss über genügend Ressourcen verfügen, um sämtliche Workloads (Indizierung und Abfragen) bewältigen zu können. Beide Workloads laufen nicht im Hintergrund. Sie können die Indizierung für Zeiten planen, in denen Abfrageanforderungen naturgemäß weniger häufig sind, aber der Dienst priorisiert ansonsten keine Aufgabe gegenüber einer anderen. Zusätzlich gleicht ein gewisses Maß an Redundanz die Abfrageleistung aus, wenn Dienste oder Knoten intern aktualisiert werden.

Allgemein gilt: Suchanwendungen benötigen in der Regel mehr Replikate als Partitionen – insbesondere, wenn die Dienstvorgänge auf Abfrageworkloads ausgerichtet sind. Warum das so ist, erfahren Sie im [Abschnitt zu Hochverfügbarkeit](#HA).

> [!NOTE]
> Wenn Sie weitere Replikate oder Partitionen hinzufügen, erhöhen sich die Kosten für die Ausführung des Diensts. Außerdem kann die Sortierung der Ergebnisse leicht variieren. Sie sollten daher den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) verwenden, um die Auswirkungen des Hinzufügens weiterer Knoten auf die Abrechnung zu verstehen. Das [Diagramm unten ](#chart) kann Ihnen helfen, die Anzahl der für eine bestimmte Konfiguration erforderlichen Sucheinheiten zu ermitteln. Weitere Informationen dazu, wie sich zusätzliche Replikate auf die Abfrageverarbeitung auswirken, finden Sie unter [Sortieren von Ergebnissen](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Gewusst wie: Zuordnen von Replikaten und Partitionen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie den Suchdienst aus.

1. Öffnen Sie in **Einstellungen** die Seite **Skalierung**, um Replikate und Partitionen zu ändern. 

   Der folgende Screenshot zeigt einen Standarddienst, der mit einem Replikat und einer Partition bereitgestellt wurde. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden (1). Wenn der Preis pro Einheit 100 US-Dollar wäre (kein echter Preis), würden die monatlichen Kosten für die Ausführung dieses Diensts durchschnittlich 100 US-Dollar betragen.

   ![Seite „Skalierung“ mit aktuellen Werten](media/search-capacity-planning/1-initial-values.png "Seite „Skalierung“ mit aktuellen Werten")

1. Mit dem Schieberegler können Sie die Anzahl der Partitionen erhöhen oder verringern. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden.

   In diesem Beispiel wird die Kapazität verdoppelt – mit jeweils zwei Replikaten und Partitionen. Beachten Sie die Anzahl der Sucheinheiten. Sie beträgt jetzt vier, weil die Formel für die Abrechnung lautet: Replikate, multipliziert mit Partitionen (2 x 2). Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten für die Ausführung des Diensts an. Wenn die Kosten für die Sucheinheit 100 US-Dollar wären, würde die neue Monatsrechnung jetzt 400 US-Dollar betragen.

   Wenn Sie die aktuellen Kosten pro Einheit für die einzelnen Tarife erfahren möchten, besuchen Sie die [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/).

   ![Hinzufügen von Replikaten und Partitionen](media/search-capacity-planning/2-add-2-each.png "Hinzufügen von Replikaten und Partitionen")

1. Wählen Sie **Speichern** aus, um die Änderungen zu bestätigen.

   ![Bestätigen von Änderungen an Skalierung und Abrechnung](media/search-capacity-planning/3-save-confirm.png "Bestätigen von Änderungen an Skalierung und Abrechnung")

   Kapazitätsänderungen dauern mehrere Stunden. Sie können den Prozess nach seinem Start nicht abbrechen, und es gibt keine Echtzeitüberwachung für Replikat- und Partitionsanpassungen. Allerdings bleibt die folgende Meldung sichtbar, während Änderungen vorgenommen werden.

   ![Statusmeldung im Portal](media/search-capacity-planning/4-updating.png "Statusmeldung im Portal")

> [!NOTE]
> Sobald ein Dienst bereitgestellt ist, kann kein direktes Upgrade auf einen höheren Tarif erfolgen. In diesem Fall müssen Sie einen Suchdienst unter dem neuen Tarif erstellen und Ihre Indizes neu laden. Informationen zur Dienstbereitstellung finden Sie unter [Erstellen eines Diensts für die kognitive Azure-Suche im Portal](search-create-service-portal.md).
>
> Darüber hinaus werden die Partitionen und Replikate ausschließlich und intern vom Dienst verwaltet. Es gibt kein Konzept der Prozessoraffinität oder der Zuweisung einer Workload auf einen bestimmten Knoten.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partitions- und Replikatskombinationen

Ein Basic-Dienst kann genau eine Partition und bis zu drei Replikate besitzen. Die Obergrenze liegt bei drei SUs. Nur die Replikate können angepasst werden. Für Hochverfügbarkeit bei Abfragen sind mindestens zwei Replikate erforderlich.

Alle Suchdienste vom Typ „Standard“ oder „Storage Optimized“ können die folgenden Kombinationen von Replikaten und Partitionen annehmen, vorbehaltlich dem Limit 36 SU. 

|   | **1 Partition** | **2 Partitionen** | **3 Partitionen** | **4 Partitionen** | **6 Partitionen** | **12 Partitionen** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 Replikat:** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 Replikate** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 Replikate** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 Replikate** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 Replikate** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 Replikate** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 Replikate** |12 SU |24 SU |36 SU |– |– |– |

SUs, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Die Anzahl der Replikate und Partitionen ist ein ganzzahliger Teiler von 12 (d.h. 1, 2, 3, 4, 6, 12). Der Grund: Die kognitive Azure-Suche unterteilt jeden Index vorab in 12 Shards, damit er gleichmäßig auf alle Partitionen verteilt werden kann. Wenn Ihr Dienst z.B. drei Partitionen aufweist und Sie einen Index erstellen, enthält jede Partition 4 Shards des Indexes. Die kognitive Azure-Suche erstellt Shards eines Index in Form von Implementierungsdetails, die sich bei zukünftigen Versionen ändern können. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hochverfügbarkeit

Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Abfrageworkloads werden in erster Linie auf Replikaten ausgeführt. Wenn Sie einen höheren Durchsatz oder Hochverfügbarkeit benötigen, sind wahrscheinlich zusätzliche Replikate erforderlich.

Allgemeine Empfehlungen für Hochverfügbarkeit sind:

* Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)

* Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/Schreibworkloads (Abfragen und Indizierung, wenn einzelne Dokumente hinzugefügt, aktualisiert oder gelöscht werden)

Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) für die kognitive Azure-Suche sind auf Abfragevorgänge und auf Indexupdates (Hinzufügen, Aktualisieren oder Löschen von Dokumenten) ausgerichtet.

Der Tarif „Basic“ erreicht den Höchstwert bei einer Partition und drei Replikaten. Wenn Sie die Flexibilität benötigen, auf Schwankungen beim Bedarf an Indizierungen und Abfragedurchsatz sofort zu reagieren, ziehen Sie einen der Standard-Tarife in Betracht.  Wenn Sie feststellen, dass Ihre Speicheranforderungen viel schneller wachsen als Ihr Abfragedurchsatz, sollten Sie einen der Tarife vom Typ „Storage Optimized“ in Betracht ziehen.

## <a name="disaster-recovery"></a>Notfallwiederherstellung

Derzeit steht kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Das Hinzufügen von Partitionen oder Replikaten wäre die falsche Strategie, um die Zielsetzungen für eine Notfallwiederherstellung zu erfüllen. Der gängigste Ansatz ist, Redundanz auf Dienstebene durch Bereitstellung eines zweiten Suchdiensts in einer anderen Region hinzuzufügen. Die Umleitungs- oder Failoverlogik muss genau wie bei der Verfügbarkeit während der Indexneuerstellung in Ihrem Code bereitgestellt werden.

## <a name="estimate-replicas"></a>Schätzen der Replikate

Für Produktionsdienste sollten Sie aus SLA-Gründen drei Replikate zuordnen. Wenn Sie eine langsame Abfrageleistung feststellen, können Sie Replikate hinzufügen, damit zusätzliche Kopien des Index online geschaltet werden, um größere Abfrageworkloads zu unterstützen und die Anforderungslast gleichmäßig auf mehrere Replikate zu verteilen.

Wir stellen keine Richtlinien zur Verfügung, wie viele Replikate für bestimmte Abfragelasten benötigt werden. Die Abfrageleistung hängt von der Komplexität der Abfrage und den konkurrierenden Workloads ab. Obwohl das Hinzufügen von Replikaten die Leistung deutlich erhöht, ist das Endergebnis aber nicht streng linear: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz.

Leitlinien zur Schätzung der QPS für Ihre Lösung finden Sie unter [Skalieren zur Verbesserung der Leistung](search-performance-optimization.md) und [Überwachen von Abfragen](search-monitor-queries.md).

## <a name="estimate-partitions"></a>Schätzen der Partitionen

Der [ausgewählte Tarif](search-sku-tier.md) bestimmt die Größe und Geschwindigkeit der Partition, und jeder Tarif ist um eine Reihe von Eigenschaften optimiert, die auf bestimmte Szenarien ausgerichtet sind. Wenn Sie sich für einen höherwertigen Tarif entscheiden, benötigen Sie möglicherweise weniger Partitionen als bei S1. Eine der Fragen, die Sie durch selbstgesteuerte Tests beantworten müssen, lautet: Bringt eine größere und teurere Partition eine bessere Leistung als zwei billigere Partitionen bei einem Dienst, der in einem niedrigeren Tarif bereitgestellt wird?

Suchanwendungen, die Datenaktualisierung nahezu in Echtzeit erfordern, benötigen proportional mehr Partitionen als Replikate. Das Hinzufügen von Partitionen verteilt Lese-/ Schreibvorgänge über eine größere Anzahl von Computerressourcen. Außerdem erhalten Sie mehr Speicherplatz auf dem Datenträger zum Speichern zusätzlicher Indizes und Dokumente.

Größere Indizes erfordern eine längere Abfragezeit. Daher werden Sie feststellen, dass jede inkrementelle Zunahme an Partitionen einen kleineren, aber proportionalen Anstieg der Replikate erforderlich macht. Die Komplexität Ihrer Abfragen und das Abfragevolumen haben darauf Einfluss, wie schnell die Abfrage ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Auswählen eines Tarifs für die kognitive Azure-Suche](search-sku-tier.md)