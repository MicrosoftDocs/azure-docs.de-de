---
title: Machine Learning-Funktion in Azure Data Explorer
description: Verwenden des Machine Learning-Clusterings für die Ursachenanalyse in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233492"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine Learning-Funktion in Azure Data Explorer

Azure Data Explorer, eine Big Data-Analyseplattform, dient zur Überwachung von Dienstintegrität, QoS oder fehlerhaften Geräten auf anormales Verhalten mithilfe der integrierten Funktionen zur [Erkennung und Prognose von Anomalien](/azure/data-explorer/anomaly-detection). Sobald ein anomales Muster erkannt wird, wird eine Ursachenanalyse (Root Cause Analysis, RCA) durchgeführt, um die Anomalie abzuschwächen oder zu beheben.

Der komplexe und langwierige Diagnoseprozess wird von Domänenexperten durchgeführt. Der Prozess umfasst das Abrufen und Verknüpfen zusätzlicher Daten aus verschiedenen Quellen für denselben Zeitrahmen, die Suche nach Änderungen in der Verteilung der Werte auf mehrere Dimensionen, die Darstellung zusätzlicher Variablen und andere Techniken auf der Grundlage von Informationen über die Domäne und Intuition. Da diese Diagnoseszenarien in Azure Data Explorer gängig sind, stehen Machine Learning-Plug-Ins zur Verfügung, um die Diagnosephase zu vereinfachen und die Dauer der RCA zu verkürzen.

Azure Data Explorer verfügt über drei Machine Learning-Plug-Ins: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin) und [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Alle Plug-Ins implementieren Clusteringalgorithmen. Die Plug-Ins `autocluster` und `basket` gruppieren eine einzelne Datensatzgruppe und das Plug-in `diffpatterns` gruppiert die Unterschiede zwischen zwei Datensatzgruppen.

## <a name="clustering-a-single-record-set"></a>Gruppieren einer einzelnen Datensatzgruppe

Ein gängiges Szenario enthält ein nach bestimmten Kriterien wie z.B. einem Zeitfenster ausgewähltes Dataset, das anormales Verhalten, hohe Temperaturmesswerte bei Geräten, lange Befehlsausführungsdauer und intensive Nutzung durch Benutzer aufweist. Wir suchen eine einfache und schnelle Möglichkeit, um allgemeine Muster (Segmente) in den Daten zu finden. Muster sind eine Teilmenge des Datasets, deren Datensätze gleiche Werte in mehreren Dimensionen (Kategoriespalten) aufweisen. Die folgende Abfrage erstellt und zeigt eine Zeitreihe von Dienstausnahmen im Laufe einer Woche in Zehn-Minuten-Intervallen:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Zeitdiagramm von Dienstausnahmen](media/machine-learning-clustering/service-exceptions-timechart.png)

Die Anzahl der Dienstausnahmen korreliert mit dem gesamten Dienstdatenverkehr. Sie erkennen deutlich das tägliche Muster für Arbeitstage von Montag bis Freitag mit einem Anstieg der Anzahl der Dienstausnahmen mittags und einem Abfall während der Nacht. Am Wochenende ist die Anzahl gering. Ausnahmespitzen können mithilfe der [Anomalieerkennung in Zeitreihen](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in Azure Data Explorer erkannt werden.

Die zweite Spitze in den Daten tritt Dienstagnachmittag auf. Mit der folgenden Abfrage wird diese Spitze näher diagnostiziert. Verwenden Sie die Abfrage, um das Diagramm um die Spitze herum in höherer Auflösung (acht Stunden in Eine-Minute-Intervallen) neu zu zeichnen, um zu überprüfen, ob es sich um eine scharfe Spitze handelt, und ihre Ränder anzuzeigen.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Zeitdiagramm mit Konzentration auf die Spitze](media/machine-learning-clustering/focus-spike-timechart.png)

Wir sehen eine schmale Zwei-Minuten-Spitze zwischen 15:00 Uhr und 15:02 Uhr. In der folgenden Abfrage werden die Ausnahmen in diesem Fenster von zwei Minuten gezählt:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

In der folgenden Abfrage werden 20 Ausnahmen aus 972 als Stichprobe entnommen:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region | ScaleUnit | DeploymentId                     | Ablaufverfolgungspunkt | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Verwenden von „autocluster()“ zum Gruppieren einer einzelnen Datensatzgruppe

Auch wenn weniger als eintausend Ausnahmen vorhanden sind, ist es weiterhin schwierig, allgemeine Segmente zu finden, da in jeder Spalte mehrere Werte vorhanden sind. Sie können mit dem [`autocluster()`](/azure/kusto/query/autoclusterplugin)-Plug-In sofort eine kleine Liste allgemeiner Segmente extrahieren und die interessanten Cluster innerhalb der zwei Minuten der Spitze suchen, wie in der folgenden Abfrage gezeigt:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Percent | Region | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Sie können in den obigen Ergebnissen erkennen, dass das dominanteste Segment 65,74% der gesamten Ausnahmedatensätze enthält und vier Dimensionen verwendet. Das nächste Segment tritt deutlich seltener auf, enthält nur 9,67% der Datensätze und verwendet drei Dimensionen. Die anderen Segmente treten sogar noch seltener auf. 

Autocluster verwendet einen proprietären Algorithmus zum Mining mehrerer Dimensionen und Extrahieren interessanter Segmente. „Interessant“ bedeutet, dass jedes Segment sowohl die Datensatzgruppe als auch die Featuregruppe signifikant abdeckt. Die Segmente sind auch abweichend, was bedeutet, dass sie sich erheblich voneinander unterscheiden. Ein oder mehrere dieser Segmente können für den RCA-Prozess relevant sein. Um Segmentprüfung und -bewertung zu minimieren, extrahiert Autocluster nur eine kleine Segmentliste.

### <a name="use-basket-for-single-record-set-clustering"></a>Verwenden von „basket()“ zum Gruppieren einer einzelnen Datensatzgruppe

Sie können auch das [`basket()`](/azure/kusto/query/basketplugin)-Plug-In wie in der folgenden Abfrage gezeigt verwenden:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Percent | Region | ScaleUnit | DeploymentId | Ablaufverfolgungspunkt | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket implementiert den Apriori-Algorithmus zum Mining der Elementgruppe und extrahiert alle Segmente, deren Abdeckung der Datensatzgruppe einen Schwellenwert (Standardwert 5%) überschreitet. Sie können sehen, dass weitere Segmente mit ähnlichen Prozentwerten (z.B. Segmente 0,1 oder 2,3) extrahiert wurden.

Beide Plug-Ins sind leistungsstark und benutzerfreundlich, aber ihre erhebliche Einschränkung besteht darin, dass sie eine einzelne Datensatzgruppe in einer nicht überwachten Weise (ohne Bezeichnungen) gruppieren. Darum ist unklar, ob die extrahierten Muster die ausgewählte Datensatzgruppe (die anomalen Einträge) oder die globale Datensatzgruppe charakterisieren.

## <a name="clustering-the-difference-between-two-records-sets"></a>Clustering des Unterschieds zwischen zwei Datensatzgruppen

Das [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin)-Plug-In überwindet die Einschränkung von `autocluster` und `basket`. `Diffpatterns` nimmt zwei Datensatzgruppen und extrahiert die wichtigsten Segmente, die zwischen ihnen unterschiedlich sind. Eine Gruppe enthält in der Regel die anomale Datensatzgruppe, die untersucht wird (eine wird von `autocluster` und die andere von `basket` analysiert). Die andere Gruppe enthält die Referenz-Datensatzgruppe (Baseline). 

In der folgenden Abfrage verwenden wir `diffpatterns`, um interessante Cluster innerhalb der zwei Minuten der Spitze zu finden, die sich von Clustern innerhalb der Baseline unterscheiden. Wir definieren das Baselinefenster als die acht Minuten vor 15:00 Uhr (als die Spitze begann). Wir müssen auch um eine binäre Spalte (AB) erweitern, die angibt, ob ein bestimmter Datensatz zur Baseline oder der anomalen Gruppe gehört. `Diffpatterns` implementiert einen überwachten Lernalgorithmus, in dem die zwei Klassenbezeichnungen durch das anomale im Vergleich zum Baselineflag (AB) generiert wurden.

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Region | ScaleUnit | DeploymentId | Ablaufverfolgungspunkt |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Das dominanteste Segment ist das gleiche Segment, das von `autocluster` extrahiert wurde, seine Abdeckung im anomalen Fenster beträgt auch 65,74%. Aber seine Abdeckung im Acht-Minuten-Baseline-Fenster beträgt nur 1,7%. Der Unterschied beträgt 64,04%. Dieser Unterschied scheint mit der anomalen Spitze in Zusammenhang zu stehen. Sie können diese Annahme überprüfen, indem Sie das ursprüngliche Diagramm in die Datensätze, die zu diesem problematischen Segment gehören, und die anderen Segmente aufteilen, wie in der folgenden Abfrage dargestellt:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Überprüfen mit „diffpattern“ – Segmentzeitdiagramm](media/machine-learning-clustering/validating-diffpattern-timechart.png)

In diesem Diagramm können wir sehen, dass die Spitze am Dienstagnachmittag aufgrund von Ausnahmen in diesem bestimmten Segment auftraten, das mithilfe des `diffpatterns`-Plug-Ins ermittelt wurde.

## <a name="summary"></a>Zusammenfassung

Die Machine Learning-Plug-Ins von Azure Data Explorer sind für viele Szenarien hilfreich. `autocluster` und `basket` implementieren einen nicht überwachten Lernalgorithmus und sind benutzerfreundlich. `Diffpatterns` implementiert einen überwachten Lernalgorithmus und ist zwar komplexer, aber leistungsfähiger beim Extrahieren von Differenzierungssegmenten für die RCA.

Diese Plug-Ins werden interaktiv in Ad-hoc-Szenarien und in automatischen, nahezu in Echtzeit ablaufenden Überwachungsdiensten verwendet. In Azure Data Explorer folgt auf die Anomalieerkennung in Zeitreihen ein Diagnoseprozess, der stark optimiert ist, um die erforderlichen Leistungsstandards zu erfüllen.
