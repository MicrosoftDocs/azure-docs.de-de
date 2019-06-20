---
title: Anomalieerkennung in Azure Stream Analytics
description: Dieser Artikel beschreibt, wie Sie Azure Stream Analytics und Azure Machine Learning zusammen verwenden, um Anomalien zu erkennen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: fdf98a0c0c40010bb55955b54dc7b04db8e199f5
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493263"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomalieerkennung in Azure Stream Analytics

Azure Stream Analytics ist sowohl in der Cloud als auch in Azure IoT Edge verfügbar und bietet integrierte Anomalieerkennungsfunktionen auf Machine Learning-Basis, die zum Überwachen der beiden am häufigsten auftretenden Anomalien verwendet werden können: temporäre und permanente. Mit den Funktionen **AnomalyDetection_SpikeAndDip** und **AnomalyDetection_ChangePoint** können Sie die Erkennung von Anomalien direkt in Ihrem Stream Analytics-Auftrag ausführen.

Die Machine Learning-Modelle setzen einheitlich als Stichprobe entnommene Zeitreihen voraus. Wenn die Zeitreihen nicht einheitlich sind, können Sie einen Aggregationsschritt mit einem rollierenden Fenster vor dem Aufrufen der Erkennung von Anomalien einfügen.

Die Machine Learning-Vorgänge unterstützen keine saisonalen Trends oder Korrelationen mit mehreren Varianten.

## <a name="model-accuracy-and-performance"></a>Genauigkeit der Modelle und Leistung

In der Regel wird die Genauigkeit des Modells besser, je mehr Daten das gleitende Fenster enthält. Die Daten im angegebenen gleitenden Fenster werden als Teil des normalen Wertebereichs für diesen Zeitrahmen behandelt. Das Modell berücksichtigt nur den Ereignisverlauf im gleitenden Fenster, um zu überprüfen, ob das aktuelle Ereignis anomal ist. Wenn das gleitende Fenster verschoben wird, werden die alten Werte des Trainings des Modells entfernt.

Die Funktionen legen basierend auf dem bisher Beobachteten einen bestimmten Normalwert ein. Ausreißer werden durch Vergleich mit dem festgelegten Normalwert innerhalb des Zuverlässigkeitsgrads identifiziert. Die Größe des Fensters sollte auf der Anzahl von Ereignissen basieren, die mindestens erforderlich sind, um das Modell für das normale Verhalten zu trainieren, sodass es eine Anomalie erkennen kann, sobald sie auftritt.

Bedenken Sie, dass die Antwortzeit des Modells mit der Größe des Verlaufs wächst, da der Vergleich mit einer höheren Anzahl vergangener Ereignisse durchgeführt werden muss. Zur Verbesserung der Leistung sollte nur die erforderliche Anzahl von Ereignissen einbezogen werden.

Lücken in der Zeitreihe können darauf zurückzuführen sein, dass das Modell zu bestimmten Zeitpunkten keine Ereignisse empfangen hat. Diese Situation wird von Stream Analytics mit Annahmen behandelt. Für das gleiche gleitende Fenster wird die Größe des Verlaufs ebenso wie die Dauer zum Berechnen der durchschnittlichen Rate verwendet, mit der Ereignisse auftreten.

## <a name="spike-and-dip"></a>Spitzen und Senken

Temporäre Anomalien im Ereignisdatenstrom einer Zeitreihe werden als Spitzen und Senken bezeichnet. Spitzen und Senken können mithilfe des auf Machine Learning basierenden [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)-Operators überwacht werden.

![Beispiel für Anomalien bei Spitzen und Senken](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Wenn im gleichen gleitenden Fenster eine zweite Spitze kleiner ist als die erste, ist das berechnete Ergebnis für die kleinere Spitze wahrscheinlich nicht signifikant genug im Vergleich zum Ergebnis für die erste Spitze innerhalb des angegebenen Zuverlässigkeitsgrads. Sie können versuchen, die Zuverlässigkeitsgradeinstellung des Modells zu verringern, um solche Anomalien abzufangen. Sollten Sie jedoch zu viele Warnungen erhalten, können Sie ein höheres Konfidenzintervall verwenden.

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

Permanente Anomalien im Ereignisdatenstrom einer Zeitreihe sind Änderungen bei der Verteilung der Werte im Ereignisdatenstrom, wie Änderungen des Zuverlässigkeitsgrads und Trends. In Stream Analytics werden diese Anomalien mithilfe des auf Machine Learning basierenden [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics)-Operators erkannt.

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

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomalieerkennung unter Verwendung von Machine Learning in Azure Stream Analytics

Das folgende Video veranschaulicht, wie eine Anomalie in Echtzeit mithilfe von Machine Learning-Funktionen in Azure Stream Analytics erkannt wird. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

