---
title: Skalieren der Funktionen von Machine Learning Studio (klassisch) in Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie Stream Analytics-Aufträge mit Funktionen von Machine Learning Studio (klassisch) skalieren, indem Sie die Partitionierung und Streamingeinheiten konfigurieren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: feeb709f67a0e75f5980ec0520b95feb7edd5960
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018816"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skalieren eines Stream Analytics-Auftrags mit Azure Machine Learning Studio-Funktionen (klassisch)

> [!TIP]
> Es wird ausdrücklich empfohlen, [UDFs für Azure Machine Learning](machine-learning-udf.md) anstelle von UDFs für Azure Machine Learning Studio (Classic) zu verwenden, um Leistung und Zuverlässigkeit zu verbessern.

In diesem Artikel wird beschrieben, wie Sie Azure Stream Analytics-Aufträge effizient skalieren, die Funktionen von Azure Machine Learning Studio (klassisch) nutzen. Allgemeine Informationen zum Skalieren von Stream Analytics-Aufträgen finden Sie im Artikel [Skalieren von Aufträgen](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Was ist eine Studio-Funktion (klassisch) in Stream Analytics?

Eine Funktion von Machine Learning Studio (klassisch) kann in Stream Analytics wie ein normaler Funktionsaufruf in der Stream Analytics-Abfragesprache verwendet werden. Im Hintergrund handelt es sich bei den Funktionsaufrufen aber um Webdienstanforderungen von Studio (klassisch).

Sie können den Durchsatz der Webdienstanforderungen von Studio (klassisch) verbessern, indem Sie mehrere Zeilen im selben Webdienst-API-Aufruf zusammen im Batch verarbeiten. Diese Gruppierung wird als Mini-Batch bezeichnet. Weitere Informationen finden Sie unter [Azure Machine Learning Studio-Webdienste (klassisch)](../machine-learning/classic/consume-web-services.md). Die Unterstützung für Studio (klassisch) in Stream Analytics befindet sich in der Vorschauphase.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Konfigurieren eines Stream Analytics-Auftrags mit Funktionen von Studio (klassisch)

Die vom Stream Analytics-Auftrag verwendete Studio-Funktion (klassisch) wird mit zwei Parametern konfiguriert:

* Batchgröße der Studio-Funktionsaufrufe (klassisch)
* Der Anzahl der Streamingeinheiten (Streaming Units, SUs), die für den Stream Analytics-Auftrag bereitgestellt werden

Um die geeigneten Werte für SUs zu ermitteln, entscheiden Sie, ob Sie die Latenz des Stream Analytics-Auftrags oder den Durchsatz der einzelnen SUs optimieren möchten. SUs können einem Auftrag immer hinzugefügt werden, um den Durchsatz einer gut partitionierten Stream Analytics-Abfrage zu erhöhen. Durch zusätzliche SUs erhöhen sich aber die Kosten für die Ausführung des Auftrags.

Ermitteln Sie die *Latenztoleranz* für Ihren Stream Analytics-Auftrag. Durch eine höhere Batchgröße erhöht sich auch die Wartezeit Ihrer Studio-Anforderungen (klassisch) und die Wartezeit des Stream Analytics-Auftrags.

Eine höhere Batchgröße ermöglicht es dem Stream Analytics-Auftrag, **mehr Ereignisse** mit **derselben Anzahl** von Webdienstanforderungen von Studio (klassisch) zu verarbeiten. Die höhere Wartezeit des Webdiensts von Studio (klassisch) verhält sich in der Regel sublinear zum Anstieg der Batchgröße. 

Es ist wichtig, in der jeweiligen Situation immer die kostengünstigste Batchgröße für einen Webdienst von Studio (klassisch) zu ermitteln. Die Standardbatchgröße für Webdienstanforderungen beträgt 1.000 Ereignisse. Sie können die Standardgröße mit der [Stream Analytics-REST-API](/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics-REST-API") oder dem [PowerShell-Client für Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md) ändern.

Nachdem Sie sich für eine Batchgröße entschieden haben, können Sie die Anzahl von Streamingeinheiten (SUs) basierend auf der Anzahl von Ereignissen festlegen, die von der Funktion pro Sekunde verarbeitet werden müssen. Weitere Informationen zu Streamingeinheiten finden Sie unter [Stream Analytics-Skalierungsaufträge](stream-analytics-scale-jobs.md).

Für jeweils 6 SUs werden 20 gleichzeitige Verbindungen mit dem Webdienst von Studio (klassisch) bereitgestellt. Allerdings werden für einen 1-SU-Auftrag und 3-SU-Aufträge 20 gleichzeitige Verbindungen bereitgestellt.  

Wenn Ihre Anwendung 200.000 Ereignisse pro Sekunde generiert und die Batchgröße 1.000 beträgt, ergibt sich eine Webdienstlatenz von 200 ms. Dieser Wert bedeutet, dass über jede Verbindung pro Sekunde fünf Anforderungen an den Webdienst von Studio (klassisch) übermittelt werden können. Bei 20 Verbindungen kann der Stream Analytics-Auftrag 20.000 Ereignisse in 200 ms verarbeiten, also 100.000 Ereignisse pro Sekunde.

Zum Verarbeiten von 200.000 Ereignissen pro Sekunde benötigt der Stream Analytics-Auftrag 40 gleichzeitige Verbindungen und somit 12 SUs. Im folgenden Diagramm ist der Verlauf der Anforderungen vom Stream Analytics-Auftrag zum Webdienstendpunkt von Studio (klassisch) dargestellt. Für 6 SUs sind jeweils maximal 20 gleichzeitige Verbindungen mit dem Webdienst von Studio (klassisch) vorhanden.

![Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Beispiel mit zwei Aufträgen](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Beispiel mit zwei Aufträgen")

Wenn **_B_* _ die Batchgröße und _*_L_*_ die Webdienstlatenz bei Batchgröße B in Millisekunden ist, beträgt der Durchsatz eines Stream Analytics-Auftrags mit _*_N_*_ SUs:

![Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Formel")

Sie können auch den Parameter „Max. gleichzeitige Aufrufe“ für den Webdienst von Studio (klassisch) konfigurieren. Es wird empfohlen, diesen Parameter auf den maximalen Wert (derzeit 200) festzulegen.

Weitere Informationen zu dieser Einstellung finden Sie im [Artikel zur Skalierung für Machine Learning Studio-Webdienste (klassisch)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Beispiel: Stimmungsanalyse
Das folgende Beispiel enthält einen Stream Analytics-Auftrag mit der Studio-Funktion (klassisch) für die Stimmungsanalyse, die im [Tutorial zur Integration von Machine Learning Studio (klassisch) für Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md) beschrieben ist.

Die Abfrage umfasst eine einfache vollständig partitionierte Abfrage gefolgt von der Funktion _ *sentiment**, wie im folgenden Beispiel dargestellt:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Betrachten wir nun die erforderlichen Konfigurationsschritte zum Erstellen eines Stream Analytics-Auftrags, mit dem eine Standpunktanalyse für 10.000 Tweets pro Sekunde durchgeführt wird.

Könnte der Datenverkehr von diesem Stream Analytics-Auftrag mit 1 SU bewältigt werden? Bei Verwendung der Standardbatchgröße von 1.000 können die Eingabedaten vom Auftrag verarbeitet werden. Bei der Standardwartezeit des Webdiensts von Studio (klassisch) für die Standpunktanalyse (mit einer Standardbatchgröße von 1.000) entsteht eine Wartezeit von nur einer Sekunde.

Die **gesamte** Latenz bzw. End-to-End-Latenz des Stream Analytics-Auftrags würde normalerweise einige Sekunden betragen. Es ist ratsam, sich diesen Stream Analytics-Auftrag genauer anzusehen, *vor allem* die Funktionsaufrufe von Studio (klassisch). Bei einer Batchgröße von 1.000 werden bei einem Durchsatz von 10.000 Ereignissen etwa 10 Anforderungen an den Webdienst benötigt. Auch bei nur einer SU sind genügend gleichzeitige Verbindungen vorhanden, um diesen Datenverkehrseingang abzudecken.

Wenn sich die Eingangsrate der Ereignisse um das Hundertfache erhöht, muss der Stream Analytics-Auftrag 1.000.000 Tweets pro Sekunde verarbeiten. Es gibt zwei Möglichkeiten, um die höhere Skalierung zu erreichen:

1. Erhöhen Sie die Batchgröße.
2. Partitionieren Sie den Eingabestream, um die Ereignisse parallel zu verarbeiten.

Bei der ersten Option erhöht sich die **Latenz** des Auftrags.

Bei der zweiten Option müssen mehr SUs bereitgestellt werden, damit mehr gleichzeitige Studio-Webdienstanforderungen (klassisch) unterstützt werden. Durch die größere Anzahl von SUs erhöhen sich die **Kosten** des Auftrags.

Betrachten wir die Skalierung anhand der folgenden Latenzmessungen für die einzelnen Batchgrößen:

| Latency | Batchgröße |
| --- | --- |
| 200 ms | Batches mit maximal 1.000 Ereignissen |
| 250 ms | Batches mit 5.000 Ereignissen |
| 300 ms | Batches mit 10.000 Ereignissen |
| 500 ms | Batches mit 25.000 Ereignissen |

1. Mit der ersten Option (**keine** Bereitstellung von mehr SUs) könnte die Batchgröße auf **25.000** erhöht werden. Durch die höhere Batchgröße könnten vom Auftrag dann 1.000.000 Ereignisse mit 20 gleichzeitigen Verbindungen mit dem Webdienst von Studio (klassisch) verarbeitet werden (bei einer Wartezeit von 500 ms pro Aufruf). Die zusätzliche Wartezeit des Stream Analytics-Auftrags würde sich also aufgrund der Anforderungen der sentiment-Funktion an die Webdienstanforderungen von Studio (klassisch) von **200 ms** auf **500 ms** erhöhen. Die Batchgröße kann jedoch **nicht** unendlich erhöht werden, da die Studio-Webdienste (klassisch) erfordern, dass die Nutzlast einer Anforderung maximal 4 MB beträgt. Für Webdienstanforderungen tritt nach 100 Sekunden eine Zeitüberschreitung auf.
1. Bei der zweiten Option wird die Batchgröße von 1000 beibehalten. Bei einer Webdienstlatenz von 200 ms können mit 20 gleichzeitigen Verbindungen mit dem Webdienst also jeweils Ereignisse in folgendem Umfang verarbeitet werden: 1000 × 20 × 5 Ereignisse = 100.000 pro Sekunde. Es sind also 60 SUs für den Auftrag erforderlich, damit 1.000.000 Ereignisse pro Sekunde verarbeitet werden können. Verglichen mit der ersten Option fallen für den Stream Analytics-Auftrag mehr Webdienst-Batchanforderungen an, sodass sich die Kosten erhöhen.

Unten ist eine Tabelle mit Informationen zum Durchsatz des Stream Analytics-Auftrags für unterschiedliche SUs und Batchgrößen angegeben (Anzahl von Ereignissen pro Sekunde).

| Batchgröße (ML-Latenz) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **12 SUs** |5\.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7\.500 |15.000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Sie sollten nun bereits über gute Grundlagenkenntnisse verfügen und wissen, wie Studio-Funktionen (klassisch) in Stream Analytics funktionieren. Sie wissen vermutlich auch, dass bei Stream Analytics-Aufträgen Daten aus Datenquellen abgerufen werden („Pull“) und bei jedem Vorgang dieser Art ein Batch mit Ereignissen zur Verarbeitung durch den Stream Analytics-Auftrag zurückgegeben wird. Wie wirkt sich dieses Abrufmodell auf die Webdienstanforderungen von Studio (klassisch) aus?

Normalerweise lässt sich die Batchgröße, die wir für Funktionen von Studio (klassisch) festlegen, nicht genau durch die Anzahl von Ereignissen teilen, die bei jedem Abrufvorgang eines Stream Analytics-Auftrags zurückgegeben werden. In diesem Fall wird der Studio-Webdienst (klassisch) mit „Teilbatches“ aufgerufen. Durch die Verwendung von Teilbatches wird verhindert, dass ein Auftrag zusätzliche Latenzen verursacht und Ereignisse von einem zum nächsten Abruf gebündelt werden.

## <a name="new-function-related-monitoring-metrics"></a>Neue funktionsbezogene Überwachungsmetriken
Im Überwachungsbereich eines Stream Analytics-Auftrags wurden drei zusätzliche funktionsbezogene Metriken hinzugefügt. Dies sind **FUNKTIONSANFORDERUNGEN**, **FUNKTIONSEREIGNISSE** und **FEHLERHAFTE FUNKTIONSANFORDERUNGEN**, wie in der folgenden Grafik dargestellt.

![Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Metriken](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skalieren von Stream Analytics mit Studio-Funktionen (klassisch) – Metriken")

Diese sind wie folgt definiert:

**FUNKTIONSANFORDERUNGEN**: Die Anzahl von Funktionsanforderungen.

**FUNKTIONSEREIGNISSE**: Die Anzahl von Ereignissen in den Funktionsanforderungen.

**FEHLER BEI FUNKTIONSANFORDERUNGEN**: Die Anzahl von Funktionsanforderungen mit Fehlern.

## <a name="key-takeaways"></a>Wesentliche Punkte

Beim Skalieren eines Stream Analytics-Auftrags mit Studio-Funktionen (klassisch) sollten die folgenden Faktoren beachtet werden:

1. Die Eingangsrate der Ereignisse
2. Die tolerierte Wartezeit für den ausgeführten Stream Analytics-Auftrag (und somit die Batchgröße der Webdienstanforderungen von Studio (klassisch))
3. Die bereitgestellten Stream Analytics-Streamingeinheiten (SUs) und die Anzahl der Webdienstanforderungen von Studio (klassisch) (zusätzliche funktionsbezogene Kosten)

Als Beispiel wurde eine vollständig partitionierte Stream Analytics-Abfrage verwendet. Falls Sie eine komplexere Abfrage benötigen, ist die [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) eine hervorragende Ressource, um vom Stream Analytics-Team weitere Hilfe zu erhalten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Stream Analytics finden Sie unter:

* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)