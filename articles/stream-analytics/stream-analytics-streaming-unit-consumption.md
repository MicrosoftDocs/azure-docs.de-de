---
title: Überblick über Streamingeinheiten und Informationen zu Anpassungen in Azure Stream Analytics
description: Dieser Artikel beschreibt Einstellungen für Streamingeinheiten sowie weitere Faktoren, die sich auf die Leistung in Azure Stream Analytics auswirken.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/17/2019
ms.openlocfilehash: 60cddf15ee8a93aa01a72fdc124fa27e68184e04
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65916045"
---
# <a name="understand-and-adjust-streaming-units"></a>Überblick über Streamingeinheiten und Informationen zu Anpassungen

Streamingeinheiten stellen die Computeressourcen dar, die zur Ausführung eines Auftrags zugeordnet werden. Je höher die Anzahl von Streamingeinheiten ist, desto mehr CPU- und Arbeitsspeicherressourcen werden Ihrem Auftrag zugeordnet. Mit dieser Kapazität können Sie sich auf die Abfragelogik konzentrieren. Zudem wird der Bedarf zur Verwaltung der Hardware abstrahiert, um Ihren Stream Analytics-Auftrag rechtzeitig auszuführen.

Um eine Streamingverarbeitung mit geringer Latenz zu erreichen, führen Azure Stream Analytics-Aufträge (ASA) die gesamte Verarbeitung im Arbeitsspeicher durch. Wenn nicht genügend Arbeitsspeicher vorhanden ist, tritt beim Streamingauftrag ein Fehler auf. Daher ist es bei einem Produktionsauftrag wichtig, die Ressourcennutzung eines Streamingauftrags zu überwachen und sicherzustellen, dass genügend Ressourcen zugewiesen werden, um die Aufträge rund um die Uhr auszuführen.

Die Nutzungsmetrik der Streamingeinheit in Prozent, die von 0 % bis 100 % reicht, zeigt die Arbeitsspeichernutzung Ihrer Workload auf. Bei einem Streamingauftrag mit minimalem Ressourcenbedarf liegt die Metrik in der Regel zwischen 10 % und 20 %. Wenn die prozentuale Nutzung der Streamingeinheit niedrig ist und Eingabeereignisse in den Rückstand geraten, benötigt Ihre Workload wahrscheinlich mehr Computeressourcen, sodass Sie die Anzahl der Streamingeinheiten erhöhen müssen. Die Metrik für die Streamingeinheit sollte im Idealfall bei unter 80 % liegen, damit gelegentliche Spitzen verarbeitet werden können. Microsoft empfiehlt, eine Warnung für die Metrik „Nutzung der Speichereinheit in %“ bei 80 % festzulegen, um eine Ressourcenauslastung zu verhindern. Weitere Informationen finden Sie unter [Tutorial: Einrichten von Warnungen für Azure Stream Analytics-Aufträge](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurieren von Streamingeinheiten (SUs) für Stream Analytics-Aufträge
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie in der Liste der Ressourcen nach dem zu skalierenden Stream Analytics-Auftrag, und öffnen Sie ihn anschließend. 

3. Wählen Sie auf der Auftragsseite unter der Überschrift **Konfigurieren** die Option **Skalieren** aus. 

    ![Konfiguration von Stream Analytics-Aufträgen im Azure-Portal][img.stream.analytics.preview.portal.settings.scale]
    
4. Verwenden Sie den Schieberegler, um die SUs für den Auftrag festzulegen. Beachten Sie, dass Sie auf die jeweiligen SU-Einstellungen beschränkt sind. 

## <a name="monitor-job-performance"></a>Überwachen der Auftragsleistung
Über das Azure-Portal können Sie den Durchsatz eines Auftrags nachverfolgen:

![Überwachen von Aufträgen in Azure Stream Analytics][img.stream.analytics.monitor.job]

Berechnen Sie den erwarteten Durchsatz der Workload. Für den Fall, dass der Durchsatz kleiner als erwartet ist, optimieren Sie die Eingabepartition und die Abfrage, und fügen Sie dem Auftrag zusätzliche SUs hinzu.

## <a name="how-many-sus-are-required-for-a-job"></a>Wie viele Premium-Streamingeinheiten sind für einen Auftrag erforderlich?

Die benötigte Anzahl an Premium-Streamingeinheiten für einen bestimmten Auftrag hängt von der Partitionskonfiguration für die Eingaben und der Abfrage ab, die für den Auftrag definiert ist. Auf der Seite **Skalieren** können Sie die richtige Anzahl von SUs festlegen. Es wird empfohlen, mehr Premium-Streamingeinheiten als erforderlich zuzuweisen. Die Stream Analytics-Verarbeitungs-Engine führt zu einer niedrigeren Latenz und einem höheren Durchsatz. Dabei wird zusätzlicher Speicherplatz beansprucht.

Im Allgemeinen wird empfohlen, für Abfragen, die nicht **PARTITIONIEREN NACH** verwenden, mit 6 Premium-Streamingeinheiten zu beginnen. Ermitteln Sie dann die optimale Anzahl mittels Trial-and-Error-Methode. Dabei ändern Sie die Anzahl der SUs, nachdem Sie eine repräsentative Datenmenge übertragen und die Metrik „Speichereinheitnutzung in %“ überprüft haben. Die Höchstzahl der von einem Stream Analytics-Auftrag verwendbaren Streamingeinheiten hängt von der Anzahl an Schritten in der für den Auftrag definierten Abfrage und der Anzahl an Partitionen für die einzelnen Schritte ab. Weitere Informationen zu diesen Grenzwerten finden Sie [hier](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Weitere Informationen zum Auswählen der richtigen Anzahl an SUs finden Sie auf der folgenden Seite: [Skalieren von Azure Stream Analytics-Aufträgen zur Erhöhung des Durchsatzes](stream-analytics-scale-jobs.md)

> [!Note]
> Die benötigte SU-Anzahl für einen bestimmten Auftrag hängt von der Partitionskonfiguration für die Eingaben und der für den Auftrag definierten Abfrage ab. Sie können die für Ihr Kontingent maximal festgelegte Anzahl von SUs für einen Auftrag auswählen. Jedes Azure-Abonnement verfügt standardmäßig über ein Kontingent von höchstens 500 SUs für alle Analyseaufträge in einer bestimmten Region. Wenn Sie die SUs für Ihre Abonnements über dieses Kontingent hinaus erhöhen möchten, wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com). Die gültigen Werte für SUs pro Auftrag sind 1, 3, 6 und danach Werte in Schritten von 6.

## <a name="factors-that-increase-su-utilization"></a>Faktoren für die Erhöhung der Nutzung der Speichereinheit in % 

Temporale (zeitlich orientierte) Abfrageelemente sind die Kerngruppe der zustandsbehafteten Operatoren, die von Stream Analytics bereitgestellt werden. Stream Analytics verwaltet den Zustand dieser Vorgänge intern im Auftrag des Benutzers durch Verwaltung des Speicherverbrauchs, Prüfpunktausführung für höhere Resilienz und Zustandswiederherstellung bei Dienstupgrades. Obwohl Stream Analytics die Zustände vollständig verwaltet, sollte der Benutzer einige Empfehlungen zu bewährten Methoden berücksichtigen.

Beachten Sie, dass ein Auftrag mit komplexer Abfragelogik eine hohe prozentuelle Auslastung der SUs aufweisen kann, wenn er nicht kontinuierlich Eingabeereignisse empfängt. Dies kann nach einer plötzlichen Spitze bei den Eingabe- und Ausgabeereignissen auftreten. Möglicherweise wird der Zustand des Auftrags im Arbeitsspeicher weiterhin beibehalten, wenn die Abfrage komplex ist.

Die Speichereinheitnutzung in % kann für einen kurzen Zeitraum plötzlich auf 0 fallen, bevor wieder die erwartete Ebene erreicht wird. Dies geschieht aufgrund von vorübergehenden Fehlern oder durch vom System initiierte Upgrades.

## <a name="stateful-query-logicin-temporal-elements"></a>Zustandsbehaftete Abfragelogik in temporalen Elementen
Eine einzigartige Funktion eines Azure Stream Analytics-Auftrags besteht darin, eine zustandsbehaftete Verarbeitung wie etwa Aggregate, temporale Verknüpfungen und temporale Analysefunktionen im Fenstermodus auszuführen. Die einzelnen Operatoren enthalten Zustandsinformationen. Die maximale Fenstergröße für diese Abfrageelemente beträgt sieben Tage. 

Der Begriff der temporalen Fenster kommt in mehreren Stream Analytics-Abfrageelementen vor:
1. Aggregate im Fenstermodus: GROUP BY von rollierenden, springenden und gleitenden Fenstern

2. Temporale Joins: JOIN mit DATEDIFF-Funktion

3. Temporale Analysefunktionen: ISFIRST, LAST und LAG mit LIMIT DURATION

Die folgenden Faktoren beeinflussen den belegten Arbeitsspeicher (Teil der Metrik „Streamingeinheit“) von Stream Analytics-Aufträgen:

## <a name="windowed-aggregates"></a>Aggregate im Fenstermodus
Der belegte Arbeitsspeicher (Zustandsgröße) für ein Aggregat im Fenstermodus ist nicht immer direkt proportional zur Fenstergröße. Der belegte Arbeitsspeicher verhält sich stattdessen proportional zur Kardinalität der Daten oder der Anzahl der Gruppen in jedem Zeitfenster.


Beispiel: In der folgenden Abfrage ist die mit `clusterid` verknüpfte Zahl die Kardinalität der Abfrage. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Um Probleme zu beheben, die durch eine hohe Kardinalität in der vorherigen Abfrage verursacht wurden, können Sie Ereignisse an den durch `clusterid` partitionierten Event Hub senden und die Abfrage horizontal hochskalieren, indem Sie dem System die separate Verarbeitung aller Eingangspartitionen durch **PARTITION BY** ermöglichen. Dies wird im folgenden Beispiel gezeigt:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Nach dem Partitionieren der Abfrage wird sie auf mehrere Knoten verteilt. Infolgedessen verringert sich die Anzahl eingehender `clusterid`-Werte auf den einzelnen Knoten, wodurch wiederum die Kardinalität des GROUP BY-Operators reduziert wird. 

Event Hub-Partitionen sollten durch den Gruppierungsschlüssel partitioniert werden, um die Notwendigkeit eines Reduzierungsschritts zu vermeiden. Weitere Informationen finden Sie unter [Übersicht über Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Temporale Joins
Der belegte Arbeitsspeicher (Zustandsgröße) eines temporalen Joins verhält sich proportional zur Anzahl der Ereignisse im zeitlichen Spielraum des Joins, der die Ereigniseingangsrate multipliziert mit der Größe des Spielraums darstellt. Anders ausgedrückt: Der durch Joins belegte Arbeitsspeicher ist proportional zum DateDiff-Zeitbereich multipliziert mit der durchschnittlichen Ereignisrate.

Die Anzahl nicht abgeglichener Ereignisse im Verknüpfungsvorgang wirkt sich auf die Arbeitsspeichernutzung für die Abfrage aus. Mit der folgenden Abfrage werden die Anzeigenaufrufe ermittelt, die Klicks generieren:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

In diesem Beispiel kann es sein, dass viele Anzeigen angezeigt werden, aber nur wenige Benutzer auf diese klicken, und alle Ereignisse müssen im Zeitfenster bleiben. Der belegte Arbeitsspeicher ist proportional zu Fenstergröße und Ereignisrate. 

Um dies zu beheben, senden Sie Ereignisse an den durch die Verknüpfungsschlüssel (in diesem Fall IDs) partitionierten Event Hub, und skalieren Sie die Abfrage horizontal hoch, indem Sie dem System die separate Verarbeitung jeder Eingangspartition mit **PARTITION BY** ermöglichen. Dies wird im folgenden Beispiel gezeigt:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Nach dem Partitionieren der Abfrage wird sie auf mehrere Knoten verteilt. Infolgedessen verringert sich die Anzahl eingehender Ereignisse auf den einzelnen Knoten, wodurch wiederum die Größe des Zustands im Verknüpfungsfenster reduziert wird. 

## <a name="temporal-analytic-functions"></a>Temporale Analysefunktionen
Der belegte Arbeitsspeicher (Zustandsgröße) einer temporalen Analysefunktion verhält sich proportional zu der mit der Dauer multiplizierten Ereignisrate. Der durch Analysefunktionen belegte Arbeitsspeicher verhält sich nicht proportional zur Fenstergröße, sondern zur Partitionsanzahl in jedem Zeitfenster.

Die Wiederherstellung weist Ähnlichkeiten mit der temporalen Verknüpfung auf. Sie können die Abfrage durch **PARTITION BY** horizontal hochskalieren. 

## <a name="out-of-order-buffer"></a>Puffer für Ereignisse in falscher Reihenfolge 
Benutzer können die Größe eines Puffers für Ereignisse in falscher Reihenfolge im Konfigurationsbereich „Ereignisreihenfolge“ konfigurieren. Der Puffer wird verwendet, um Eingaben für die Dauer des Fensters zu speichern und neu anzuordnen. Die Größe des Puffers verhält sich proportional zu der mit der Größe des Fensters für Ereignisse in falscher Reihenfolge multiplizierten Ereigniseingangsrate. Die Standardfenstergröße beträgt 0. 

Um einen Überlauf des Puffers in einer anderen Reihenfolge zu beheben, skalieren Sie die Abfrage mit **PARTITION BY** horizontal hoch. Nach dem Partitionieren der Abfrage wird sie auf mehrere Knoten verteilt. Infolgedessen verringert sich die Anzahl eingehender Ereignisse auf den einzelnen Knoten, wodurch wiederum die Größe der Ereignisse in jedem Neuanordnungspuffer reduziert wird. 

## <a name="input-partition-count"></a>Anzahl von Eingabepartitionen 
Jede Eingabepartition einer Auftragseingabe weist einen Puffer auf. Je größer die Anzahl der Eingabepartitionen, desto mehr Ressourcen verbraucht der Auftrag. Für jede Streamingeinheit kann Azure Stream Analytics ungefähr 1 MB/s der Eingabe verarbeiten. Daher können Sie eine Optimierung vornehmen, indem Sie die Anzahl der Stream Analytics-Streamingeinheiten an die Anzahl von Partitionen in Ihrem Event Hub anpassen. 

In der Regel ist ein Auftrag, der mit einer Streamingeinheit konfiguriert ist, für einen Event Hub mit zwei Partitionen (d.h. der Mindestanzahl für einen Event Hub) ausreichend. Wenn der Event Hub eine größere Anzahl von Partitionen aufweist, verbraucht Ihr Stream Analytics-Auftrag mehr Ressourcen, nutzt jedoch nicht zwingend den zusätzlichen vom Event Hub bereitgestellten Durchsatz. 

Für einen Auftrag mit 6 Streamingeinheiten benötigen Sie eventuell 4 oder 8 Partitionen vom Event Hub. Verwalten Sie jedoch nicht zu viele nicht benötigte Partitionen, da dies zu einem übermäßigen Ressourceneinsatz führt. Beispiel: Ein Event Hub mit mindestens 16 Partitionen in einem Stream Analytics-Auftrag mit 1 Streamingeinheit. 

## <a name="reference-data"></a>Verweisdaten 
Die Verweisdaten in ASA werden zur schnellen Suche in den Speicher geladen. Bei der aktuellen Implementierung wird bei jedem Verknüpfungsvorgang mit Verweisdaten eine Kopie der Verweisdaten im Speicher beibehalten, auch wenn Sie dieselben Verweisdaten mehrmals verknüpfen müssen. Bei Abfragen mit **PARTITION BY** behält jede Partition eine Kopie der Verweisdaten bei, sodass die Partitionen vollständig entkoppelt sind. Durch den Multiplikationseffekt kann die Speicherverwendung schnell in die Höhe schießen, wenn Sie Verweisdaten mehrmals mit mehreren Partitionen verknüpfen.  

### <a name="use-of-udf-functions"></a>Verwenden von UDF-Funktionen
Wenn Sie eine UDF-Funktion hinzufügen, lädt Azure Stream Analytics die JavaScript-Laufzeit in den Arbeitsspeicher. Dies wirkt sich auf die Speichereinheitnutzung in % aus.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen von parallelisierbaren Abfragen in Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalieren von Azure Stream Analytics-Aufträgen zur Erhöhung des Durchsatzes](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
