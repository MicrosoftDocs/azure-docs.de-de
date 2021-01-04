---
title: Anomalieerkennung in Azure Stream Analytics
description: Dieser Artikel beschreibt, wie Sie Azure Stream Analytics und Azure Machine Learning zusammen verwenden, um Anomalien zu erkennen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: c57a3920dac3e18e248109fafdf61fdfa871c54d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023396"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomalieerkennung in Azure Stream Analytics

Azure Stream Analytics ist sowohl in der Cloud als auch in Azure IoT Edge verfügbar und bietet integrierte Anomalieerkennungsfunktionen auf Machine Learning-Basis, die zum Überwachen der beiden am häufigsten auftretenden Anomalien verwendet werden können: temporäre und permanente. Mit den Funktionen **AnomalyDetection_SpikeAndDip** und **AnomalyDetection_ChangePoint** können Sie die Erkennung von Anomalien direkt in Ihrem Stream Analytics-Auftrag ausführen.

Die Machine Learning-Modelle setzen einheitlich als Stichprobe entnommene Zeitreihen voraus. Wenn die Zeitreihen nicht einheitlich sind, können Sie einen Aggregationsschritt mit einem rollierenden Fenster vor dem Aufrufen der Erkennung von Anomalien einfügen.

Die Machine Learning-Vorgänge unterstützen derzeit keine saisonalen Trends oder Korrelationen mit mehreren Varianten.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomalieerkennung unter Verwendung von Machine Learning in Azure Stream Analytics

Das folgende Video veranschaulicht, wie eine Anomalie in Echtzeit mithilfe von Machine Learning-Funktionen in Azure Stream Analytics erkannt wird. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modellverhalten

In der Regel wird die Genauigkeit des Modells besser, je mehr Daten das gleitende Fenster enthält. Die Daten im angegebenen gleitenden Fenster werden als Teil des normalen Wertebereichs für diesen Zeitrahmen behandelt. Das Modell berücksichtigt nur den Ereignisverlauf im gleitenden Fenster, um zu überprüfen, ob das aktuelle Ereignis anomal ist. Wenn das gleitende Fenster verschoben wird, werden die alten Werte des Trainings des Modells entfernt.

Die Funktionen legen basierend auf dem bisher Beobachteten einen bestimmten Normalwert ein. Ausreißer werden durch Vergleich mit dem festgelegten Normalwert innerhalb des Zuverlässigkeitsgrads identifiziert. Die Größe des Fensters sollte auf der Anzahl von Ereignissen basieren, die mindestens erforderlich sind, um das Modell für das normale Verhalten zu trainieren, sodass es eine Anomalie erkennen kann, sobald sie auftritt.

Die Antwortzeit des Modells nimmt mit der Größe des Verlaufs zu, da eine höhere Anzahl vergangener Ereignisse verglichen werden muss. Zur Verbesserung der Leistung sollte nur die erforderliche Anzahl von Ereignissen einbezogen werden.

Lücken in der Zeitreihe können darauf zurückzuführen sein, dass das Modell zu bestimmten Zeitpunkten keine Ereignisse empfangen hat. Diese Situation wird von Stream Analytics mit Annahmelogik behandelt. Für das gleiche gleitende Fenster wird die Größe des Verlaufs ebenso wie die Dauer zum Berechnen der durchschnittlichen Rate verwendet, mit der Ereignisse auftreten.

Mit einem [hier](https://aka.ms/asaanomalygenerator) verfügbaren Anomalie-Generator können Sie für einen IoT-Hub Daten mit verschiedenen Anomaliemustern bereitstellen. Mit diesen Funktionen für die Anomalieerkennung kann ein ASA-Auftrag so eingerichtet werden, dass Daten aus diesem IoT-Hub gelesen und Anomalien erkannt werden.

## <a name="spike-and-dip"></a>Spitzen und Senken

Temporäre Anomalien im Ereignisdatenstrom einer Zeitreihe werden als Spitzen und Senken bezeichnet. Spitzen und Senken können mithilfe des auf Machine Learning basierenden [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)-Operators überwacht werden.

![Beispiel für Anomalien bei Spitzen und Senken](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Wenn im gleichen gleitenden Fenster eine zweite Spitze kleiner ist als die erste, ist das berechnete Ergebnis für die kleinere Spitze wahrscheinlich nicht signifikant genug im Vergleich zum Ergebnis für die erste Spitze innerhalb des angegebenen Zuverlässigkeitsgrads. Sie können den Zuverlässigkeitsgrad des Modells verringern, um solche Anomalien zu erkennen. Sollten Sie jedoch zu viele Warnungen erhalten, können Sie ein höheres Konfidenzintervall verwenden.

Die folgende Beispielabfrage setzt eine einheitliche Eingangsrate von einem Ereignis pro Sekunde in einem zweiminütigen gleitenden Fenster mit einer Verlaufsgröße von 120 Ereignissen voraus. Mit einem Zuverlässigkeitsgrad von 95% extrahiert die letzte SELECT-Anweisung Ergebnis und Anomalienstatus und gibt sie aus.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Änderungspunkt

Permanente Anomalien im Ereignisdatenstrom einer Zeitreihe sind Änderungen bei der Verteilung der Werte im Ereignisdatenstrom, wie Änderungen des Zuverlässigkeitsgrads und Trends. In Stream Analytics werden diese Anomalien mithilfe des auf Machine Learning basierenden [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics)-Operators erkannt.

Permanente Änderungen dauern viel länger als Spitzen und Senken und könnten auf katastrophale Ereignisse hinweisen. Permanente Änderungen sind in der Regel mit bloßem Auge nicht sichtbar, können aber mit dem **AnomalyDetection_ChangePoint**-Operator erkannt werden.

Die folgende Abbildung ist ein Beispiel für die Änderung des Zuverlässigkeitsgrads:

![Beispiel für eine Anomalie der Änderung des Zuverlässigkeitsgrads](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Die folgende Abbildung ist ein Beispiel für eine Trendänderung:

![Beispiel für eine Anomalie der Trendänderung](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Die folgende Beispielabfrage setzt eine einheitliche Eingangsrate von einem Ereignis pro Sekunde in einem 20-minütigen gleitenden Fenster mit einer Verlaufsgröße von 1.200 Ereignissen voraus. Mit einem Zuverlässigkeitsgrad von 80% extrahiert die letzte SELECT-Anweisung Ergebnis und Anomalienstatus und gibt sie aus.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Leistungsmerkmale

Die Leistungsfähigkeit dieser Modelle hängt von der Verlaufsgröße, der Fensterdauer, der Ereignislast und davon ab, ob die Partitionierung auf Funktionsebene verwendet wird. In diesem Abschnitt werden die Konfigurationen erläutert und Beispiele für die Unterstützung von Datenerfassungsraten von 1 K, 5 K und 10 K Ereignissen pro Sekunde gezeigt.

* **Verlaufsgröße** – Die Modellleistung verhält sich linear zur **Verlaufsgröße**. Je länger die Verlaufsgröße, umso mehr Zeit benötigen die Modelle, um ein neues Ereignis zu bewerten. Dies liegt daran, dass das neue Ereignis von den Modellen mit jedem vergangenen Ereignis im Verlaufspuffer verglichen wird.
* **Fensterdauer** – Die **Fensterdauer** sollte widerspiegeln, wie lange die Erfassung der Anzahl von Ereignissen dauert, die durch die Verlaufsgröße angegeben ist. Wenn diese Anzahl von Ereignisse im Fenster nicht erreicht wird, werden die fehlenden Werte von Azure Stream Analytics vervollständigt. Daher besteht eine direkte Beziehung zwischen der CPU-Auslastung und der Verlaufsgröße.
* **Ereignislast** – Je größer die **Ereignislast**, desto mehr Arbeit müssen die Modelle leisten, was sich wiederum auf die CPU-Auslastung auswirkt. Der Auftrag kann durch eine extreme Parallelverarbeitung horizontal skaliert werden. Dabei muss die Verwendung zusätzlicher Eingabepartitionen für die Geschäftslogik jedoch sinnvoll sein.
* Bei der **Partitionierung auf Funktionsebene** - **Partitionierung auf Funktionsebene** wird ```PARTITION BY``` innerhalb des Funktionsaufrufs der Anomalieerkennung verwendet. Durch diese Partitionierung entsteht ein Mehraufwand, da der Zustand für mehrere Modelle gleichzeitig aufrechterhalten werden muss. Die Partitionierung auf Funktionsebene wird in Szenarien wie der Partitionierung auf Geräteebene verwendet.

### <a name="relationship"></a>Beziehung
Zwischen der Verlaufsgröße, der Fensterdauer und der Gesamtereignislast besteht folgende Beziehung:

windowDuration (in ms) = 1000 * historySize/(Eingabeereignisse gesamt [s]/Anzahl der Eingabepartitionen)

Wenn Sie die Funktion nach „deviceId“ partitionieren, fügen Sie dem Funktionsaufruf der Anomalieerkennung „PARTITION BY deviceId“ hinzu.

### <a name="observations"></a>Beobachtungen
In der folgenden Tabelle sind die beobachteten Durchsätze bei einem einzelnen Knoten (6 SU) ohne Partitionierung aufgeführt:

| Verlaufsgröße (Ereignisse) | Fensterdauer (ms) | Eingabeereignisse gesamt/s |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2\.200 |
| 600 | 728 | 1\.650 |
| 6\.000 | 10.910 | 1\.100 |

In der folgenden Tabelle sind die beobachteten Durchsätze bei einem einzelnen Knoten (6 SU) mit Partitionierung aufgeführt:

| Verlaufsgröße (Ereignisse) | Fensterdauer (ms) | Eingabeereignisse gesamt/s | Geräteanzahl |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1\.091 | 1\.100 | 10 |
| 600 | 10.910 | 1\.100 | 10 |
| 6\.000 | 218.182 | < 550 | 10 |
| 60 | 21.819 | 550 | 100 |
| 600 | 218.182 | 550 | 100 |
| 6\.000 | 2\.181.819 | < 550 | 100 |

Codebeispiele zum Ausführen der oben genannten Konfigurationen ohne Partitionierung finden Sie in den Azure-Beispielen im [Streaming At Scale-Repository](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh). Im Code wird ein Stream Analytics-Auftrag ohne Partitionierung auf Funktionsebene erstellt. Die Ein- und Ausgabe erfolgt über Event Hub. Die Eingabelast wird mithilfe von Testclients generiert. Jedes Eingabeereignis ist ein JSON-Dokument von 1 KB. Mit den Ereignissen wird ein IoT-Gerät simuliert, das JSON-Daten (für bis zu 1.000 Geräte) sendet. Die Verlaufsgröße, die Fensterdauer und die Gesamtereignislast verteilen sich auf zwei Eingabepartitionen:

> [!Note]
> Um eine genauere Schätzung zu erhalten, passen Sie die Beispiele an Ihr Szenario an.

### <a name="identifying-bottlenecks"></a>Identifizieren von Engpässen
Verwenden Sie den Bereich „Metriken“ in ihrem Azure Stream Analytics-Auftrag, um Engpässe in Ihrer Pipeline zu identifizieren. Überprüfen Sie **Eingabe-/Ausgabeereignisse** hinsichtlich des Durchsatzes sowie ["Wasserzeichenverzögerung"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) oder **Ereignisse im Rückstand**, um festzustellen, ob der Auftrag mit der Eingangsrate Schritt halten kann. Suchen Sie für Event Hub-Metriken nach **Gedrosselte Anforderungen**, und passen Sie die Schwellenwerteinheiten (TU) entsprechend an. Überprüfen Sie für Cosmos DB-Metriken **Max. genutzte RU/Sek. pro Partitionsschlüsselbereich** unter „Durchsatz“, um sicherzustellen, dass Ihre Partitionsschlüsselbereiche gleichmäßig genutzt werden. Überwachen Sie für Azure SQL-DB **Protokoll-E/A** und **CPU**.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)