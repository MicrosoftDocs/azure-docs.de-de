---
title: Skalieren von Partitionen und Replikaten für die Abfrage und Indizierung – Azure Search
description: Passen Sie Partitions- und Replikatcomputerressourcen in Azure Search an, wobei jede Ressource in abrechenbaren Sucheinheiten abgerechnet wird.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6879dd975f97ba2746165e87a135e5d90e8b229f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60308727"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Skalieren von Partitionen und Replikaten für Abfrage und Indizierung von Arbeitslasten in Azure Search
Nach dem [Auswählen eines Tarifs](search-sku-tier.md) und dem [Bereitstellen eines Suchdiensts](search-create-service-portal.md) kann im nächsten Schritt optional die Anzahl der von dem Dienst verwendeten Replikate oder Partitionen erhöht werden. Jeder Tarif bietet eine feste Anzahl von Abrechnungseinheiten. In diesem Artikel wird erläutert, wie Sie diese Einheiten für eine optimale Konfiguration zuordnen, bei der Ihre Anforderungen für die Abfrageausführung, Indizierung und Speicherung ausgeglichen sind.

Die Ressourcenkonfiguration ist verfügbar, wenn Sie einen Dienst zum [Basic-Tarif](https://aka.ms/azuresearchbasic) oder zu einem der [Tarife vom Typ „Standard“ oder „Storage Optimized“](search-limits-quotas-capacity.md) bereitstellen. Bei Diensten in diesen Tarifen wird die Kapazität in Abstufungen von *Sucheinheiten* (Search Units, SUs) erworben, wobei jede Partition und jedes Replikat jeweils als einzelne SU zählt. 

Bei Verwendung von weniger Sucheinheiten fällt die Rechnung entsprechend niedriger aus. Die Abrechnung ist aktiviert, solange der Dienst bereitgestellt wird. Wenn Sie einen Dienst vorübergehend nicht verwenden und eine Abrechnung vermeiden möchten, müssen Sie den Dienst löschen und später bei Bedarf neu erstellen.

> [!Note]
> Durch Löschen eines Diensts werden alle darin befindlichen Elemente gelöscht. Es gibt keine Möglichkeit in Azure Search, beibehaltene Suchdaten zu sichern und wiederherzustellen. Um einen vorhandenen Index in einem neuen Dienst erneut bereitzustellen, sollten Sie das Programm ausführen, das ursprünglich zum Erstellen und Laden des Index verwendet wurde. 

## <a name="terminology-replicas-and-partitions"></a>Terminologie: Replikate und Partitionen
Ein Suchdienst basiert in erster Linie auf Replikaten und Partitionen.

| Resource | Definition |
|----------|------------|
|*Partitionen* | Partitionen stellen Indexspeicher und E/A für Lese-/Schreibvorgänge (beispielsweise bei der Neuerstellung oder Aktualisierung eines Index) bereit.|
|*Replikate* | Replikate sind Instanzen des Suchdiensts und dienen in erster Linie zum Lastenausgleich bei Abfragevorgängen. Jedes Replikat hostet jeweils eine Kopie eines Index. Wenn Sie über 12 Replikate verfügen, stehen für jeden im Dienst geladenen Index 12 Kopien zur Verfügung.|

> [!NOTE]
> Welche Indizes auf einem Replikat ausgeführt werden, ist nicht direkt beeinflussbar. Eine Kopie der einzelnen Indizes jedes Replikats ist Teil der Dienstarchitektur.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Gewusst wie: Zuordnen von Replikaten und Partitionen
In Azure Search wird einem Dienst zunächst eine Mindestmenge von Ressourcen (bestehend aus einer Partition und einem Replikat) zugeordnet. Bei Tarifen, die dies unterstützen, können Sie die Verarbeitungsressourcen schrittweise anpassen, indem Sie die Anzahl an Partitionen erhöhen, um mehr Speicher- und E/A-Kapazität zu erhalten oder mehr Replikate hinzuzufügen, um ein höheres Abfrageaufkommen zu bewältigen oder die Leistung zu verbessern. Ein einzelner Dienst muss über genügend Ressourcen verfügen, um sämtliche Workloads (Indizierung und Abfragen) bewältigen zu können. Workloads können nicht auf mehrere Dienste aufgeteilt werden.

Wir empfehlen, die Replikat- und Partitionszuordnung über das Azure-Portal zu erhöhen oder zu verändern. Das Portal erzwingt Grenzwerte für zulässige Kombinationen, damit die Obergrenzen nicht überschritten werden. Wenn die Bereitstellung skript- oder codebasiert erfolgen soll, sind die [Azure PowerShell](https://docs.microsoft.com/rest/api/searchmanagement/services) oder die [Verwaltungs-REST-API](search-manage-powershell.md) alternative Lösungen.

Allgemein gilt: Suchanwendungen benötigen mehr Replikate als Partitionen, insbesondere, wenn die Dienstvorgänge auf Abfrageworkloads ausgerichtet sind. Warum das so ist, erfahren Sie im [Abschnitt zu Hochverfügbarkeit](#HA).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie den Suchdienst aus.
2. Öffnen Sie in **Einstellungen** die Seite **Skalierung**, um Replikate und Partitionen zu ändern. 

   Der folgende Screenshot zeigt einen Standarddienst, der mit einem Replikat und einer Partition bereitgestellt wurde. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden (1). Wenn der Preis pro Einheit 100 US-Dollar wäre (kein echter Preis), würden die monatlichen Kosten für die Ausführung dieses Diensts durchschnittlich 100 US-Dollar betragen.

   ![Seite „Skalierung“ mit aktuellen Werten](media/search-capacity-planning/1-initial-values.png "Seite „Skalierung“ mit aktuellen Werten")

3. Mit dem Schieberegler können Sie die Anzahl der Partitionen erhöhen oder verringern. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden.

   In diesem Beispiel wird die Kapazität verdoppelt – mit jeweils zwei Replikaten und Partitionen. Beachten Sie die Anzahl der Sucheinheiten. Sie beträgt jetzt vier, weil die Formel für die Abrechnung lautet: Replikate, multipliziert mit Partitionen (2 x 2). Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten für die Ausführung des Diensts an. Wenn die Kosten für die Sucheinheit 100 US-Dollar wären, würde die neue Monatsrechnung jetzt 400 US-Dollar betragen.

   Wenn Sie die aktuellen Kosten pro Einheit für die einzelnen Tarife erfahren möchten, besuchen Sie die [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/).

   ![Hinzufügen von Replikaten und Partitionen](media/search-capacity-planning/2-add-2-each.png "Hinzufügen von Replikaten und Partitionen")

3. Klicken Sie auf **Speichern**, um die Änderungen zu bestätigen.

   ![Bestätigen von Änderungen an Skalierung und Abrechnung](media/search-capacity-planning/3-save-confirm.png "Bestätigen von Änderungen an Skalierung und Abrechnung")

   Kapazitätsänderungen dauern mehrere Stunden. Sie können den Prozess nach seinem Start nicht abbrechen, und es gibt keine Echtzeitüberwachung für Replikat- und Partitionsanpassungen. Allerdings bleibt die folgende Meldung sichtbar, während Änderungen vorgenommen werden.

   ![Statusmeldung im Portal](media/search-capacity-planning/4-updating.png "Statusmeldung im Portal")


> [!NOTE]
> Sobald ein Dienst bereitgestellt ist, kann kein direktes Upgrade auf eine höhere SKU erfolgen. In diesem Fall müssen Sie einen Suchdienst unter dem neuen Tarif erstellen und Ihre Indizes neu laden. Informationen zur Dienstbereitstellung finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md) .
>
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
> Die Anzahl der Replikate und Partitionen ist ein ganzzahliger Teiler von 12 (d.h. 1, 2, 3, 4, 6, 12). Der Grund: Azure Search unterteilt jeden Index vorab in 12 Shards, damit er gleichmäßig auf alle Partitionen verteilt werden kann. Wenn Ihr Dienst z.B. drei Partitionen aufweist und Sie einen Index erstellen, enthält jede Partition 4 Shards des Indexes. Azure Search erstellt Shards eines Index in Form von Implementierungsdetails, die sich bei zukünftigen Versionen ändern können. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.
>


<a id="HA"></a>

## <a name="high-availability"></a>Hochverfügbarkeit
Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Abfrageworkloads werden in erster Linie auf Replikaten ausgeführt. Wenn Sie einen höheren Durchsatz oder Hochverfügbarkeit benötigen, sind wahrscheinlich zusätzliche Replikate erforderlich.

Allgemeine Empfehlungen für Hochverfügbarkeit sind:

* Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)
* Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/Schreibworkloads (Abfragen und Indizierung, wenn einzelne Dokumente hinzugefügt, aktualisiert oder gelöscht werden)

Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) für Azure Search sind auf Abfragevorgänge und auf Indexupdates (Hinzufügen, Aktualisieren oder Löschen von Dokumenten) ausgerichtet.

Der Tarif „Basic“ erreicht den Höchstwert bei einer Partition und drei Replikaten. Wenn Sie die Flexibilität benötigen, auf Schwankungen beim Bedarf an Indizierungen und Abfragedurchsatz sofort zu reagieren, ziehen Sie einen der Standard-Tarife in Betracht.  Wenn Sie feststellen, dass Ihre Speicheranforderungen viel schneller wachsen als Ihr Abfragedurchsatz, sollten Sie einen der Tarife vom Typ „Storage Optimized“ in Betracht ziehen.

### <a name="index-availability-during-a-rebuild"></a>Indexverfügbarkeit während einer Neuerstellung

Die Hochverfügbarkeit von Azure Search gilt für Abfragen und Indexaktualisierungen ohne Indexneuerstellung. Wenn Sie ein Feld löschen, einen Datentyp ändern oder ein Feld umbenennen, müssen Sie den Index neu erstellen. Um den Index neu zu erstellen, müssen Sie ihn löschen, neu erstellen und die Daten neu laden.

> [!NOTE]
> Sie können einem Azure Search-Index neue Felder hinzufügen, ohne den Index neu zu erstellen. Der Wert des neuen Felds lautet NULL für alle Dokumente, die bereits im Index vorhanden sind.

Um die Verfügbarkeit des Index während einer Neuerstellung zu gewährleisten, muss im gleichen Dienst eine Kopie des Index mit einem anderen Namen oder eine Kopie des Index mit dem gleichen Namen in einem anderen Dienst vorhanden sein und Ihr Code mit einer Umleitungs- oder Failoverlogik versehen werden.

## <a name="disaster-recovery"></a>Notfallwiederherstellung
Derzeit steht kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Das Hinzufügen von Partitionen oder Replikaten wäre die falsche Strategie, um die Zielsetzungen für eine Notfallwiederherstellung zu erfüllen. Der gängigste Ansatz ist, Redundanz auf Dienstebene durch Bereitstellung eines zweiten Suchdiensts in einer anderen Region hinzuzufügen. Die Umleitungs- oder Failoverlogik muss genau wie bei der Verfügbarkeit während der Indexneuerstellung in Ihrem Code bereitgestellt werden.

## <a name="increase-query-performance-with-replicas"></a>Erhöhen der Abfrageleistung mit Replikaten
Eine Abfragelatenz deutet darauf hin, dass zusätzliche Replikate erforderlich sind. Im Allgemeinen besteht der erste Schritt zum Verbessern der Abfrageleistung im Hinzufügen weiterer Instanzen dieser Ressource. Beim Hinzufügen von Replikaten werden zusätzliche Kopien des Index online geschaltet, um größere Abfrageworkloads zu unterstützen und die Anforderungslast gleichmäßig auf mehrere Replikate zu verteilen.

Es können keine festen Schätzungen für die Abfragen pro Sekunde (Queries Per Second, QPS) abgegeben werden: Die Abfrageleistung kann je nach Komplexität der jeweiligen Abfrage und konkurrierenden Workloads stark variieren. Obwohl das Hinzufügen von Replikaten die Leistung deutlich erhöht, ist das Endergebnis aber nicht streng linear: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz.

Anleitungen zum Schätzen der Abfragen pro Sekunde (QPS) für Ihre Workloads finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Erhöhen der Indizierungsleistung mit Partitionen
Suchanwendungen, die Datenaktualisierung nahezu in Echtzeit erfordern, benötigen proportional mehr Partitionen als Replikate. Das Hinzufügen von Partitionen verteilt Lese-/ Schreibvorgänge über eine größere Anzahl von Computerressourcen. Außerdem erhalten Sie mehr Speicherplatz auf dem Datenträger zum Speichern zusätzlicher Indizes und Dokumente.

Größere Indizes erfordern eine längere Abfragezeit. Daher werden Sie feststellen, dass jede inkrementelle Zunahme an Partitionen einen kleineren, aber proportionalen Anstieg der Replikate erforderlich macht. Die Komplexität Ihrer Abfragen und das Abfragevolumen haben darauf Einfluss, wie schnell die Abfrage ausgeführt wird.


## <a name="next-steps"></a>Nächste Schritte

[Auswählen eines Tarifs für Azure Search](search-sku-tier.md)
