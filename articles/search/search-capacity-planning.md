---
title: Schätzen der Kapazität für Abfrage- und Indexworkloads
titleSuffix: Azure Cognitive Search
description: Passen Sie Partitions- und Replikatcomputerressourcen in der kognitiven Azure-Suche an, wobei jede Ressource in abrechenbaren Sucheinheiten abgerechnet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537220"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Schätzen und Verwalten der Kapazität eines Azure Cognitive Search-Diensts

Nehmen Sie sich einige Minuten Zeit, bevor Sie [einen Suchdienst bereitstellen](search-create-service-portal.md) und einen bestimmten Tarif festlegen, um zu verstehen, wie die Kapazität funktioniert und wie Sie Replikate und Partitionen an Schwankungen der Workload anpassen können.

Kapazität ist eine Funktion der [Dienstebene](search-sku-tier.md). Sie bestimmt die maximale Speichergröße pro Dienst und Partition sowie die Obergrenze für die Anzahl von Objekten, die Sie erstellen können. Der Tarif „Basic“ ist für Apps vorgesehen, die relativ geringe Speicheranforderungen (nur eine Partition) haben, aber die Möglichkeit zur Ausführung in einer Konfiguration mit hoher Verfügbarkeit (3 Replikate) bieten. Andere Tarife sind für bestimmte Arbeitsauslastungen oder Muster, z. B. Mehrinstanzenfähigkeit, konzipiert. Intern profitieren auf diesen Tarifen erstellte Dienste von Hardware, die diese Szenarien unterstützt.

Die Skalierbarkeitsarchitektur in Azure Cognitive Search basiert auf flexiblen Kombinationen von Replikaten und Partitionen, sodass Sie die Kapazität variieren können – je nachdem, ob Sie mehr Abfrage- oder mehr Indizierungsleistung benötigen. Nach der Erstellung eines Diensts können Sie die Anzahl von Replikaten oder Partitionen unabhängig voneinander erhöhen oder verringern. Die Kosten erhöhen sich mit jeder zusätzlichen physischen Ressource. Nach Abschluss umfangreicher Workloads können Sie jedoch herunterskalieren, um Ihre Kosten zu senken. Je nach Tarif und Umfang der Anpassung kann das Hinzufügen oder Reduzieren von Kapazität zwischen 15 Minuten und mehreren Stunden dauern.

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

+ Anomalien beim Erstellen der Rangfolge: Suchbewertungen werden zunächst auf der Shard-Ebene berechnet und dann zu einem einzelnen Resultset aggregiert. Abhängig von den Merkmalen des Shardinhalts können Übereinstimmungen aus einem Shard höher eingestuft werden als Übereinstimmungen in einem anderen Shard. Wenn Sie in den Suchergebnissen intuitive Rangfolgen bemerken, ist dies höchstwahrscheinlich auf die Auswirkungen des Shardings zurückzuführen – insbesondere wenn die Indizes klein sind. Sie können diese Anomalien in der Rangfolge vermeiden, indem Sie die Option zum [globalen Berechnen von Bewertungen über den gesamten Index](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions) auswählen. Dies ist jedoch mit Leistungseinbußen verbunden.

+ Anomalien beim AutoVervollständigen: AutoVervollständigen-Abfragen, bei denen Vergleiche anhand der ersten paar Zeichen eines teilweise eingegebenen Begriffs vorgenommen werden, akzeptieren einen Fuzzyparameter, der kleine Abweichungen in der Rechtschreibung verzeiht. Bei AutoVervollständigen ist die Fuzzyübereinstimmung auf Begriffe innerhalb des aktuellen Shards beschränkt. Wenn z. B. ein Shard „Microsoft“ enthält und ein Teilbegriff von „micor“ eingegeben wird, findet die Suchmaschine in diesem Shard eine Übereinstimmung mit „Microsoft“, aber nicht in anderen Shards, die die restlichen Teile des Indexes enthalten.

## <a name="how-to-evaluate-capacity-requirements"></a>Auswerten von Kapazitätsanforderungen

Zwischen der Kapazität und den Kosten für die Ausführung des Diensts besteht ein direkter Zusammenhang. Tarife sind mit Grenzwerten auf zwei Ebenen verbunden: Speicher und Inhalt (z. B. eine Anzahl von Indizes für einen Dienst). Weil der zuerst erreichte Grenzwert gilt, müssen Sie beide Aspekte berücksichtigen.

Mengen von Indizes und anderen Objekten werden normalerweise von geschäftlichen und technischen Anforderungen vorgegeben. Beispielsweise könnten Sie mehrere Versionen desselben Indexes für aktive Entwicklung, Tests und Produktion haben.

Speicheranforderungen werden durch die Größe der Indizes bestimmt, die Sie für die Erstellung erwarten. Es gibt keine solide Heuristik oder allgemein gültige Regeln, die bei Schätzungen helfen. Die einzige Möglichkeit zur Ermittlung der Größe eines Indexes ist, [einen zu erstellen](search-what-is-an-index.md). Dessen Größe basiert auf den importierten Daten, der Textanalyse und Indexkonfiguration, also beispielsweise, ob Sie Vorschlagsfunktionen, die Filterung und Sortierung aktivieren.

Bei der Volltextsuche entspricht die primäre Datenstruktur der Struktur eines [invertierten Indexes](https://en.wikipedia.org/wiki/Inverted_index), die über andere Eigenschaften als die Quelldaten verfügt. Bei einem invertierten Index werden Größe und Komplexität vom Inhalt bestimmt, nicht notwendigerweise von der Menge der Daten, die Sie eingeben. Eine große Datenquelle mit hoher Redundanz könnte einen kleineren Index ergeben als ein kleineres Dataset mit stark variierendem Inhalt. Daher ist es kaum möglich, die Indexgröße aus der Größe des ursprünglichen Datasets abzuleiten.

> [!NOTE] 
> Auch wenn sich das Schätzen der künftig benötigten Indizes und des erforderlichen Speichers wie bloßes Mutmaßen anfühlt, lohnt sich der Aufwand. Stellt sich die Kapazität eines Tarifs als zu gering heraus, müssen Sie einen neuen Dienst in einem höheren Tarif bereitstellen und anschließend die [Indizes neu laden](search-howto-reindex.md). Für einen Dienst kann kein direktes Upgrade von einem Tarif auf einen anderen durchgeführt werden.
>

### <a name="estimate-with-the-free-tier"></a>Schätzung mit dem Free-Tarif

Eine Möglichkeit zum Abschätzen der Kapazität besteht darin, mit dem Tarif „Free“ zu beginnen. Bedenken Sie, dass der Dienst vom Typ „Free“ bis zu drei Indizes, 50 MB Speicherplatz und 2 Minuten Indizierungszeit bietet. Es kann schwierig sein, mit diesen Einschränkungen eine voraussichtliche Indexgröße zu schätzen. Sie können es dennoch wie folgt versuchen:

+ [Erstellen Sie einen kostenlosen Dienst](search-create-service-portal.md).
+ Bereiten Sie ein kleines repräsentatives Dataset vor.
+ Erstellen Sie einen Index, und laden Sie Ihre Daten. Wenn das Dataset in einer von Indexern unterstützten Azure-Datenquelle gehostet werden kann, können Sie den Index mithilfe des [Datenimport-Assistenten im Portal](search-get-started-portal.md) erstellen und laden. Andernfalls sollten Sie REST und [Postman](search-get-started-rest.md) oder [Visual Studio Code](search-get-started-vs-code.md) verwenden, um den Index zu erstellen und die Daten zu pushen. Beim Pushmodell müssen Daten in Form von JSON-Dokumenten vorliegen, wobei Felder im Dokument Feldern im Index entsprechen.
+ Sammeln Sie Informationen zum Index, z. B. die Größe. Funktionen und Attribute haben Auswirkungen auf den Speicher. Durch das Hinzufügen von Vorschlagsfunktionen (Search-as-you-Type-Abfragen) erhöhen sich beispielsweise die Speicheranforderungen. 

  Sie können mehrere Versionen eines Index unter Verwendung desselben Datasets erstellen und dabei verschiedene Attribute für die einzelnen Felder verwenden, um zu ermitteln, wie sich die Speicheranforderungen ändern. Weitere Informationen finden Sie [im Abschnitt „Hinweise zum Speicher“ unter „Erstellen eines einfachen Index“](search-what-is-an-index.md#index-size).

Mit einer groben Schätzung können Sie die betreffende Größe für zwei Indizes verdoppeln (Entwicklung und Produktion) und dann Ihren Tarif entsprechend auswählen.

### <a name="estimate-with-a-billable-tier"></a>Schätzung mit einem abrechenbaren Tarif

Dedizierte Ressourcen ermöglichen längere Sampling- und Verarbeitungszeiten und eignen sich besser für realistische Schätzungen bezüglich Indexmenge, Größe und Abfragevolumen während der Entwicklung. Einige Kunden beginnen direkt mit einem abrechenbaren Tarif und werten ihn im Lauf des Entwicklungsprojekts erneut aus.

1. [Überprüfen Sie die Dienstgrenzwerte in jedem Tarif](./search-limits-quotas-capacity.md#index-limits), um festzustellen, ob niedrigere Tarife die Anzahl der benötigten Indizes unterstützen können. In den Tarifen „Basic“, „S1“ und „S2“ betragen die Grenzwerte für Indizes 15, 50 bzw. 200. Der Tarif „Speicheroptimiert“ ist auf zehn Indizes beschränkt, da er für eine geringe Anzahl sehr großer Indizes ausgelegt ist.

1. [Erstellen Sie einen Dienst unter einem abzurechnenden Tarif](search-create-service-portal.md):

    + Beginnen Sie mit einem niedrigen Tarif, z. B. „Basic“ oder „S1“, wenn Sie sich über die projizierte Last nicht sicher sind.
    + Starten Sie hoch, mit „S2“ oder sogar „S3“, wenn Tests umfangreiche Indizierungs- und Abfrageworkloads enthalten.
    + Beginnen Sie mit einem Tarif vom Typ „Speicheroptimiert“ („L1“ oder „L2“), wenn Sie sehr viele Daten indizieren möchten und die Abfragelast relativ gering ist (etwa im Fall einer internen Geschäftsanwendung).

1. [Erstellen Sie einen anfänglichen Index](search-what-is-an-index.md), um zu bestimmen, wie Quelldaten in einen Index übersetzt werden. Dies ist die einzige Möglichkeit, die Größe des Indexes zu schätzen.

1. [Überwachen Sie Speicher, Dienstgrenzwerte, Abfragevolumen und Latenz](search-monitor-usage.md) im Portal. Im Portal werden die Abfragen pro Sekunde, gedrosselte Abfragen und die Wartezeit bei Suchvorgängen angezeigt. Diese Werte können Ihnen dabei helfen, den richtigen Tarif auszuwählen.

1. Fügen Sie Replikate hinzu, wenn Sie Hochverfügbarkeit benötigen oder wenn die Abfrageleistung verlangsamt wird.

   Es gibt keine Richtlinien zur Anzahl der Replikate, die für bestimmte Abfragelasten benötigt werden. Die Abfrageleistung hängt von der Komplexität der Abfrage und den konkurrierenden Workloads ab. Obwohl das Hinzufügen von Replikaten die Leistung deutlich erhöht, ist das Endergebnis aber nicht streng linear: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz. Leitlinien zur Schätzung der QPS (Queries per Second, Abfragen pro Sekunde) für Ihre Lösung finden Sie unter [Skalieren zur Verbesserung der Leistung](search-performance-optimization.md) und [Überwachen von Abfragen](search-monitor-queries.md).

> [!NOTE]
> Speicheranforderungen können vergrößert werden, wenn Sie Daten einbeziehen, die nie durchsucht werden. Im Idealfall enthalten Dokumente nur die Daten, die Sie für die Suche benötigen. Binäre Daten sind nicht durchsuchbar und sollten separat gespeichert werden (beispielsweise in einer Azure-Tabelle oder in Azure Blob Storage). In diesem Fall sollte dem Index ein Feld mit einem URL-Verweis auf die externen Daten hinzugefügt werden. Die maximale Größe eines einzelnen Suchdokuments beträgt 16 MB (oder weniger, wenn Sie im Rahmen einer einzelnen Anforderung mehrere Dokumente gleichzeitig hochladen). Weitere Informationen finden Sie unter [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md).
>

**Überlegungen zum Abfragevolumen**

„Abfragen pro Sekunde“ (Queries per Second, QPS) ist eine wichtige Metrik bei der Leistungsoptimierung, spielt aber im Allgemeinen bei der Tarifauswahl nur dann eine Rolle, wenn Sie zu Beginn ein hohes Abfragevolumen erwarten.

Die Tarife vom Typ „Standard“ bieten ein ausgewogenes Verhältnis von Replikaten und Partitionen. Durch Hinzufügen von Replikaten für den Lastenausgleich oder durch Hinzufügen von Partitionen für eine parallele Verarbeitung können Sie kürzere Abfrageverarbeitungszeiten erzielen. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

Ist von Anfang an ein hohes durchgängiges Abfragevolumen zu erwarten, sollten Sie höhere Tarife vom Typ „Standard“ in Verbindung mit einer leistungsfähigeren Hardware in Erwägung ziehen. Bleiben diese Abfragevolumen aus, können Sie Partitionen und Replikate offline schalten oder auch zu einem Dienst mit niedrigerem Tarif wechseln. Weitere Informationen zum Berechnen des Abfragedurchsatzes finden Sie unter [Überlegungen zur Leistung und Optimierung der kognitiven Azure-Suche](search-performance-optimization.md).

Die Tarife vom Typ „Speicheroptimiert“ eigenen sich für Workloads mit großen Datenmengen und unterstützen insgesamt mehr verfügbaren Indexspeicher bei niedrigeren Anforderungen an die Abfragewartezeit. Dabei sollten Sie weiterhin zusätzliche Replikate für den Lastenausgleich und zusätzliche Partitionen für die parallele Verarbeitung verwenden. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

**Vereinbarungen zum Servicelevel**

Die Funktionen des Tarifs „Free“ und der Vorschauversion bieten keine [Vereinbarungen zum Servicelevel (Service Level Agreements, SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Für alle abrechenbaren Tarife gelten SLAs, wenn Sie genügend Redundanz für Ihren Dienst bereitstellen. Für Abfrage-SLAs (Lesezugriff) benötigen Sie zwei oder mehr Replikate. Für Abfrage- und Indizierungs-SLAs (Lese-/Schreibzugriff) sind drei oder mehr Replikate erforderlich. Die Anzahl der Partitionen hat keine Auswirkungen auf die SLAs.

## <a name="tips-for-capacity-planning"></a>Tipps zur Kapazitätsplanung

+ Erstellen Sie Metriken zu Abfragen, und sammeln Sie Daten zu Verwendungsmustern (Abfragen während der Geschäftszeiten, Indizierung außerhalb der Spitzenzeiten). Treffen Sie anhand dieser Daten Entscheidungen zur Dienstbereitstellung. Da ein stündliches oder tägliches Intervall ungeeignet ist, können Sie Partitionen und Ressourcen dynamisch so anpassen, dass geplante Änderungen von Abfragevolumen ermöglicht werden. Auch ungeplante, aber anhaltende Änderungen können berücksichtigt werden, wenn Ebenen beständig genug sind, um die Durchführung von Aktionen zu garantieren.

+ Beachten Sie, dass der einzige Nachteil einer unterdimensionierten Bereitstellung darin besteht, dass Sie ggf. einen Dienst entfernen müssen, wenn die tatsächlichen Anforderungen Ihre Schätzungen überschreiten. Um Dienstunterbrechungen zu vermeiden, sollten Sie einen neuen Dienst in einem höheren Tarif erstellen und parallel ausführen, bis alle Apps und Anforderungen den neuen Endpunkt zum Ziel haben.

## <a name="when-to-add-partitions-and-replicas"></a>Wann Partitionen und Replikate hinzugefügt werden sollten

Zunächst wird einem Dienst eine Mindestmenge von Ressourcen (bestehend aus einer Partition und einem Replikat) zugeordnet.

Ein einzelner Dienst muss über genügend Ressourcen verfügen, um sämtliche Workloads (Indizierung und Abfragen) bewältigen zu können. Beide Workloads laufen nicht im Hintergrund. Sie können die Indizierung für Zeiten planen, in denen Abfrageanforderungen naturgemäß weniger häufig sind, aber der Dienst priorisiert ansonsten keine Aufgabe gegenüber einer anderen. Zusätzlich gleicht ein gewisses Maß an Redundanz die Abfrageleistung aus, wenn Dienste oder Knoten intern aktualisiert werden.

Allgemein gilt: Suchanwendungen benötigen in der Regel mehr Replikate als Partitionen – insbesondere, wenn die Dienstvorgänge auf Abfrageworkloads ausgerichtet sind. Warum das so ist, erfahren Sie im [Abschnitt zu Hochverfügbarkeit](#HA).

Der [ausgewählte Tarif](search-sku-tier.md) bestimmt die Größe und Geschwindigkeit der Partition, und jeder Tarif ist um eine Reihe von Eigenschaften optimiert, die auf bestimmte Szenarien ausgerichtet sind. Wenn Sie sich für einen höherwertigen Tarif entscheiden, benötigen Sie möglicherweise weniger Partitionen als bei S1. Eine der Fragen, die Sie durch selbstgesteuerte Tests beantworten müssen, lautet: Bringt eine größere und teurere Partition eine bessere Leistung als zwei billigere Partitionen bei einem Dienst, der in einem niedrigeren Tarif bereitgestellt wird?

Suchanwendungen, die Datenaktualisierung nahezu in Echtzeit erfordern, benötigen proportional mehr Partitionen als Replikate. Das Hinzufügen von Partitionen verteilt Lese-/ Schreibvorgänge über eine größere Anzahl von Computerressourcen. Außerdem erhalten Sie mehr Speicherplatz auf dem Datenträger zum Speichern zusätzlicher Indizes und Dokumente.

Größere Indizes erfordern eine längere Abfragezeit. Daher werden Sie feststellen, dass jede inkrementelle Zunahme an Partitionen einen kleineren, aber proportionalen Anstieg der Replikate erforderlich macht. Die Komplexität Ihrer Abfragen und das Abfragevolumen haben darauf Einfluss, wie schnell die Abfrage ausgeführt wird.

> [!NOTE]
> Wenn Sie weitere Replikate oder Partitionen hinzufügen, erhöhen sich die Kosten für die Ausführung des Diensts. Außerdem kann die Sortierung der Ergebnisse leicht variieren. Sie sollten daher den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) verwenden, um die Auswirkungen des Hinzufügens weiterer Knoten auf die Abrechnung zu verstehen. Das [Diagramm unten ](#chart) kann Ihnen helfen, die Anzahl der für eine bestimmte Konfiguration erforderlichen Sucheinheiten zu ermitteln. Weitere Informationen dazu, wie sich zusätzliche Replikate auf die Abfrageverarbeitung auswirken, finden Sie unter [Sortieren von Ergebnissen](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Gewusst wie: Zuordnen von Replikaten und Partitionen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie den Suchdienst aus.

1. Öffnen Sie unter **Einstellungen** die Seite **Skalierung**, um Replikate und Partitionen zu ändern. 

   Der folgende Screenshot zeigt einen Basic Standard, der mit einem Replikat und einer Partition bereitgestellt wurde. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden (1). Wenn der Preis pro Einheit 100 US-Dollar wäre (kein echter Preis), würden die monatlichen Kosten für die Ausführung dieses Diensts durchschnittlich 100 US-Dollar betragen.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Seite „Skalierung“ mit aktuellen Werten" border="true":::

1. Mit dem Schieberegler können Sie die Anzahl der Partitionen erhöhen oder verringern. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden. Wählen Sie **Speichern** aus.

   In diesem Beispiel werden ein zweites Replikat und eine zweite Partition hinzugefügt. Beachten Sie die Anzahl der Sucheinheiten. Sie beträgt jetzt vier, weil die Formel für die Abrechnung lautet: Replikate, multipliziert mit Partitionen (2 x 2). Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten für die Ausführung des Diensts an. Wenn die Kosten für die Sucheinheit 100 US-Dollar wären, würde die neue Monatsrechnung jetzt 400 US-Dollar betragen.

   Wenn Sie die aktuellen Kosten pro Einheit für die einzelnen Tarife erfahren möchten, besuchen Sie die [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Hinzufügen von Replikaten und Partitionen" border="true":::

1. Nach dem Speichern können Sie Benachrichtigungen überprüfen, um zu bestätigen, dass die Aktion erfolgreich war.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Änderungen speichern" border="true":::

   Änderungen bei der Kapazität können mehrere Stunden bis zum Abschluss dauern. Sie können den Prozess nach seinem Start nicht abbrechen, und es gibt keine Echtzeitüberwachung für Replikat- und Partitionsanpassungen. Allerdings bleibt die folgende Meldung sichtbar, während Änderungen vorgenommen werden.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Statusmeldung im Portal" border="true":::

> [!NOTE]
> Sobald ein Dienst bereitgestellt ist, kann kein direktes Upgrade auf einen höheren Tarif erfolgen. In diesem Fall müssen Sie einen Suchdienst unter dem neuen Tarif erstellen und Ihre Indizes neu laden. Informationen zur Dienstbereitstellung finden Sie unter [Erstellen eines Diensts für die kognitive Azure-Suche im Portal](search-create-service-portal.md).
>
> Darüber hinaus werden die Partitionen und Replikate ausschließlich und intern vom Dienst verwaltet. Es gibt kein Konzept der Prozessoraffinität oder der Zuweisung einer Workload auf einen bestimmten Knoten.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partitions- und Replikatskombinationen

Ein Basic-Dienst kann genau eine Partition und bis zu drei Replikate besitzen. Die Obergrenze liegt bei drei SUs. Nur die Replikate können angepasst werden. Für Hochverfügbarkeit bei Abfragen sind mindestens zwei Replikate erforderlich.

Alle Suchdienste vom Typ „Standard“ oder „Datenspeicheroptimiert“ können die folgenden Kombinationen von Replikaten und Partitionen annehmen, vorbehaltlich dem für diese Ebenen zulässigen Limit von 36 SU. 

|   | **1 Partition** | **2 Partitionen** | **3 Partitionen** | **4 Partitionen** | **6 Partitionen** | **12 Partitionen** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 Replikat:** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 Replikate** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 Replikate** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 Replikate** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 Replikate** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 Replikate** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 Replikate** |12 SU |24 SU |36 SU |– |– |Nicht zutreffend |

SUs, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Die Anzahl der Replikate und Partitionen ist ein ganzzahliger Teiler von 12 (d.h. 1, 2, 3, 4, 6, 12). Der Grund: Die kognitive Azure-Suche unterteilt jeden Index vorab in 12 Shards, damit er gleichmäßig auf alle Partitionen verteilt werden kann. Wenn Ihr Dienst z.B. drei Partitionen aufweist und Sie einen Index erstellen, enthält jede Partition 4 Shards des Indexes. Die kognitive Azure-Suche erstellt Shards eines Index in Form von Implementierungsdetails, die sich bei zukünftigen Versionen ändern können. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hochverfügbarkeit

Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Abfrageworkloads werden in erster Linie auf Replikaten ausgeführt. Wenn Sie einen höheren Durchsatz oder Hochverfügbarkeit benötigen, sind wahrscheinlich zusätzliche Replikate erforderlich.

Allgemeine Empfehlungen für Hochverfügbarkeit sind:

+ Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)

+ Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/Schreibworkloads (Abfragen und Indizierung, wenn einzelne Dokumente hinzugefügt, aktualisiert oder gelöscht werden)

Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) für die kognitive Azure-Suche sind auf Abfragevorgänge und auf Indexupdates (Hinzufügen, Aktualisieren oder Löschen von Dokumenten) ausgerichtet.

Der Tarif „Basic“ erreicht den Höchstwert bei einer Partition und drei Replikaten. Wenn Sie die Flexibilität benötigen, auf Schwankungen beim Bedarf an Indizierungen und Abfragedurchsatz sofort zu reagieren, ziehen Sie einen der Standard-Tarife in Betracht.  Wenn Sie feststellen, dass Ihre Speicheranforderungen viel schneller wachsen als Ihr Abfragedurchsatz, sollten Sie einen der Tarife vom Typ „Storage Optimized“ in Betracht ziehen.

## <a name="about-queries-per-second-qps"></a>Informationen zu Abfragen pro Sekunde (QPS)

Aufgrund der großen Anzahl von Faktoren, die die Abfrageleistung beeinflussen, veröffentlicht Microsoft keine Zahlen zu den erwarteten Abfragen pro Sekunde (QPS). QPS-Schätzungen müssen von jedem Kunden unabhängig und unter Berücksichtigung der für die jeweilige Anwendung gültigen Serviceebene, Konfiguration, Indizes und Abfragekonstrukte entwickelt werden. Indexgröße und Komplexität, Abfragegröße und Komplexität sowie der Umfang des Datenverkehrs sind Hauptentscheidungskriterium für den QPS-Wert. Es gibt keine Möglichkeit, sinnvolle Schätzungen abzugeben, wenn diese Faktoren unbekannt sind.

Schätzungen sind besser vorhersagbar, wenn sie für Dienste berechnet werden, die auf dedizierten Ressourcen ausgeführt werden (Basic- und Standard-Tarife). Sie können den QPS-Wert genauer schätzen, da Sie die Kontrolle über mehr Parameter haben. Anleitungen zur Herangehensweise für Schätzungen finden Sie unter [Leistung und Optimierung der kognitiven Azure-Suche](search-performance-optimization.md).

Für die Tarife vom Typ „Storage Optimized“ (L1 und L2) sollten Sie einen geringeren Abfragedurchsatz und eine höhere Latenz als für die Tarife vom Typ „Standard“ erwarten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schätzen und Verwalten von Kosten](search-sku-manage-costs.md)