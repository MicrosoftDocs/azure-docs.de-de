---
title: Vertikales Hoch- und Herunterskalieren in Azure Stream Analytics-Aufträgen
description: In diesem Artikel wird beschrieben, wie Sie einen Stream Analytics-Auftrag skalieren, indem Sie Eingabedaten partitionieren, die Abfrage optimieren und Streamingeinheiten für den Auftrag festlegen.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: c12c4b9f4a3757a3974e4aff7699d0265bfd7840
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124372"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skalieren eines Azure Stream Analytics-Auftrags zur Erhöhung des Durchsatzes
In diesem Artikel erfahren Sie, wie Sie eine Stream Analytics-Abfrage zur Steigerung des Durchsatzes für Stream Analytics-Aufträge optimieren. Im folgenden Leitfaden wird erläutert, wie Sie Ihren Auftrag zur Verarbeitung höherer Lasten skalieren und von einer größeren Menge an Systemressourcen (z.B. Bandbreite, CPU-Ressourcen, Arbeitsspeicher) profitieren können.
Hierfür sollten Sie folgende Artikel gelesen haben:
-   [Verstehen und Anpassen von Streamingeinheiten](stream-analytics-streaming-unit-consumption.md)
-   [Erstellen von parallelisierbaren Aufträgen](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Fall 1: Ihre Abfrage ist prinzipiell über alle Eingabepartitionen hinweg vollständig parallelisierbar.
Wenn Ihre Abfrage prinzipiell über alle Eingabepartitionen hinweg vollständig parallelisierbar ist, können Sie folgende Schritte durchführen:
1.  Erstellen Sie mithilfe des Schlüsselworts **PARTITION BY** eine Abfrage mit hohem Parallelitätsgrad. Einzelheiten finden Sie [auf dieser Seite](stream-analytics-parallelization.md) im Abschnitt „Aufträge mit hohem Parallelitätsgrad“.
2.  Je nach den in der Abfrage verwendeten Ausgabetypen sind einige Ausgaben möglicherweise nicht parallelisierbar oder erfordern weitere Konfigurationen im Hinblick auf einen hohen Parallelitätsgrad. Beispielsweise ist die Power BI-Ausgabe nicht parallelisierbar. Ausgaben werden immer zusammengeführt, bevor sie an die Ausgabesenke gesendet werden. Blobs, Tabellen, ADLS, Service Bus und Azure-Funktionen werden automatisch parallelisiert. SQL- und Azure Synapse Analytics-Ausgaben verfügen über eine Option zur Parallelisierung. Beim Event Hub muss der PartitionKey-Konfigurationssatz dem Feld **PARTITION BY** entsprechen (in der Regel „PartitionId“). Achten Sie bei Event Hubs insbesondere darauf, dass die Anzahl der Partitionen für alle Eingaben der Anzahl der Partitionen für alle Ausgaben entspricht, um eine Überkreuzung zwischen den Partitionen zu verhindern. 
3.  Führen Sie Ihre Abfrage mit **6 SUs** (d.h. der Gesamtkapazität eines einzelnen Computeknotens) aus, um den maximal erreichbaren Durchsatz zu messen, und messen Sie bei Verwendung von **GROUP BY** die Anzahl der Gruppen (Kardinalität), die der Auftrag verarbeiten kann. Zu den allgemeinen Symptomen, die bei Erreichen der Systemressourcenlimits durch den Auftrag auftreten, zählen Folgende:
    - Die Metrik „Speichereinheitnutzung in %“ liegt bei über 80 %. Dies weist auf eine hohe Speicherauslastung hin. Die Faktoren, die zur Erhöhung dieser Metrik beitragen, werden [hier](stream-analytics-streaming-unit-consumption.md) beschrieben. 
    -   Der Ausgabezeitstempel liegt gegenüber der Gesamtbetrachtungszeit im Rückstand. Je nach Abfragelogik kann der Ausgabezeitstempel eine Logikabweichung von der Gesamtbetrachtungszeit aufweisen. Diese sollten jedoch in ungefähr derselben Geschwindigkeit verlaufen. Wenn der Ausgabezeitstempel immer weiter zurückfällt, weist dies auf eine Überlastung des Systems hin. Dies kann die Folge einer Drosselung nachgeschalteter Ausgabesenken oder einer hohen CPU-Auslastung sein. Zurzeit stellen wir nicht die Metrik „CPU-Auslastung“ bereit, sodass es schwierig sein kann, zwischen beiden Ursachen zu differenzieren.
        - Wenn das Problem auf eine Drosselung der Senke zurückzuführen ist, müssen Sie möglicherweise die Anzahl der Ausgabepartitionen (und auch die der Eingabepartitionen, damit der Auftrag vollständig parallelisierbar bleibt) oder aber die Ressourcenmenge der Senke erhöhen (z.B. die Anzahl der Anforderungseinheiten für CosmosDB).
    - Das Auftragsdiagramm enthält für jede Eingabe eine Backlogereignismetrik pro Partition. Wenn die Backlogereignismetrik weiter steigt, ist dies auch ein Indikator dafür, dass die Systemressource eingeschränkt ist (entweder aufgrund einer Drosselung der Ausgabesenke oder einer hohen CPU-Auslastung).
4.  Sobald Sie die Grenzen dessen, was ein Auftrag mit 6 SUs erreichen kann, bestimmt haben, können Sie die Verarbeitungskapazität des Auftrags linear extrapolieren, je mehr SUs Sie hinzufügen. Dies gilt jedoch nur unter der Voraussetzung, dass keine Datenschiefe vorliegt, die dazu führt, dass bestimmte Partitionen einen überaus hohen Datendurchsatz aufweisen.

> [!NOTE]
> Wählen Sie die richtige Anzahl von Streamingeinheiten aus: Da Stream Analytics für jeweils 6 hinzugefügte SUs einen Verarbeitungsknoten erstellt, wird empfohlen, die Anzahl der Eingabepartitionen als durch die Anzahl der Knoten teilbar festzulegen, damit die Partitionen gleichmäßig auf die Knoten aufgeteilt werden können.
> Beispiel: Sie haben gemessen, dass Ihr 6-SU-Auftrag eine Verarbeitungsrate von 4 MB/s erreichen kann. Die Anzahl Ihrer Eingabepartitionen beträgt 4. Sie können festlegen, dass Aufträge mit 12 SUs ungefähr eine Verarbeitungsrate von 8 MB/s erreichen und Aufträge mit 24 SUs 16 MB/s erreichen sollen. Anschließend können Sie entscheiden, auf welchen Wert die Anzahl der SUs für den Auftrag in Abhängigkeit von der Eingangsrate erhöht werden soll.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Fall 2: Ihre Abfrage weist keinen hohen Parallelitätsgrad auf.
Wenn Ihre Abfrage keinen hohen Parallelitätsgrad aufweist, können Sie folgende Schritte durchführen.
1.  Beginnen Sie zuerst mit einer Abfrage ohne **PARTITION BY** , um die Komplexität der Partitionierung gering zu halten, und führen Sie Ihre Abfrage mit 6 SUs aus, um wie in [Fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) die maximale Last zu messen.
2.  Wenn Sie Ihre erwartete Last im Hinblick auf den Durchsatz erreichen können, sind Sie fertig. Alternativ können Sie denselben Auftrag mit 3 SUs und 1 SU messen lassen, um die minimale Anzahl von SUs zu ermitteln, die für Ihr Szenario funktioniert.
3.  Wenn Sie den gewünschten Durchsatz nicht erreichen können, versuchen Sie, Ihre Abfrage möglichst in mehrere Schritte zu unterteilen, sofern dies noch nicht geschehen ist. Weisen Sie dann bis zu 6 SUs pro Schritt in der Abfrage zu. Beispiel: Wenn die Abfrage 3 Schritte aufweist, weisen Sie in der Option „Skalieren“ 18 SUs zu.
4.  Bei der Ausführung eines solchen Auftrags verlagert Stream Analytics jeden Schritt in einen eigenen Knoten mit dedizierten Ressourcen von 6 SUs. 
5.  Wenn Sie Ihr Lastziel dennoch nicht erreicht haben, können Sie versuchen, **PARTITION BY** beginnend mit den Schritten näher an der Eingabe zu verwenden. Für den Operator **GROUP BY** , der normalerweise nicht partitionierbar ist, können Sie das lokale bzw. globale Aggregatmuster verwenden, um eine partitionierte **GROUP BY** -Abfrage gefolgt von einer nicht partitionierten **GROUP BY** -Abfrage durchzuführen. Beispiel: Sie möchten alle 3 Minuten die Anzahl der Fahrzeuge zählen, die die einzelnen Mautstellen passieren, und die Menge der Daten ermitteln, die mit 6 SUs verarbeitet werden können. In diesem Fall lautet die Abfrage wie folgt:

Abfrage:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
In der obigen Abfrage zählen Sie die Anzahl der Fahrzeuge pro Mautstation pro Partition und addieren dann die Anzahl aller Partitionen.

Weisen Sie nach der Partitionierung für jede Partition des Schritts bis zu 6 SUs zu, wobei jede Partition maximal 6 SUs aufweisen kann. Jede Partition kann somit auf einen eigenen Verarbeitungsknoten platziert werden.

> [!Note]
> Wenn Ihre Abfrage nicht partitioniert werden kann, kann der Durchsatz nicht immer durch Hinzufügen zusätzlicher SUs in einer Abfrage mit mehreren Schritten verbessert werden. Eine Möglichkeit zur Leistungssteigerung besteht darin, die Anzahl der anfänglichen Schritte mithilfe des lokalen bzw. globalen Aggregatmusters zu verringern, wie in Schritt 5 oben beschrieben wurde.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Fall 3: Sie führen eine Vielzahl von unabhängigen Abfragen in einem Auftrag aus.
In bestimmten Anwendungsfällen mit ISVs ist es kosteneffizienter, Daten von mehreren Mandanten in einem einzigen Auftrag zu verarbeiten. So können durch die Verwendung separater Eingaben und Ausgaben für jeden Mandanten mehrere (z.B. 20) unabhängige Abfragen in einem einzigen Auftrag ausgeführt werden. Es wird davon ausgegangen, dass die Last der einzelnen Unterabfragen relativ gering ist. In diesem Fall können Sie folgende Schritte durchführen:
1.  Verwenden Sie in diesem Fall nicht **PARTITION BY** in der Abfrage.
2.  Wenn Sie den Event Hub verwenden, reduzieren Sie die Anzahl der Eingabepartitionen auf den kleinstmöglichen Wert 2.
3.  Führen Sie die Abfrage mit 6 SUs aus. Fügen Sie bei erwarteten Lasten für jede Unterabfrage möglichst viele Unterabfragen hinzu, bis der Auftrag die Systemressourcengrenzen erreicht. Informationen zu den Symptomen, die in diesem Fall auftreten, finden Sie unter [Fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
4.  Wenn Sie die oben gemessene Unterabfragegrenze erreicht haben, gehen Sie nun dazu über, die Unterabfrage einem neuen Auftrag hinzuzufügen. Die Anzahl der Aufträge, die in Abhängigkeit von der Anzahl der unabhängigen Abfragen ausgeführt werden muss, sollte relativ linear sein. Dies gilt nur unter der Voraussetzung, dass keine Datenschiefe vorliegt. Sie können dann prognostizieren, wie viele 6-SU-Aufträge in Abhängigkeit von der Anzahl der Mandanten, für die die Bereitstellung erfolgen soll, ausgeführt werden müssen.
5.  Wenn Sie bei solchen Abfragen die JOIN-Anweisung für Verweisdaten verwenden, fassen Sie die Eingaben zusammen, bevor Sie sie mit den gleichen Verweisdaten zusammenfassen. Gliedern Sie anschließend die Ereignisse bei Bedarf aus. Andernfalls wird bei jeder JOIN-Anweisung für Verweisdaten eine Kopie der Verweisdaten im Arbeitsspeicher gespeichert, wodurch aller Wahrscheinlichkeit nach die Speicherauslastung unnötig in die Höhe schießt.

> [!Note] 
> Wie viele Mandanten sollten in einem Auftrag aufgenommen werden?
> Dieses Abfragemuster weist häufig eine große Anzahl von Unterabfragen auf und führt zu einer sehr großen und komplexen Topologie. Der Controller des Auftrags ist dann eventuell nicht in der Lage, eine derart große Topologie zu verarbeiten. Als Faustregel gilt daher, maximal 40 Mandanten bei einem 1-SU-Auftrag und 60 Mandanten bei Aufträgen mit 3 SUs und 6 SUs aufzunehmen. Wenn Sie die Kapazität des Controllers überschreiten, wird der Auftrag nicht erfolgreich gestartet.



## <a name="get-help"></a>Hier erhalten Sie Hilfe
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/