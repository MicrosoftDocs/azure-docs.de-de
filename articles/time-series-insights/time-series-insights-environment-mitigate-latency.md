---
title: Überwachen und Reduzieren der Drosselung in Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Leistungsprobleme überwacht, diagnostiziert und verringert werden können, die zu Latenz und Drosselung in Azure Time Series Insights führen.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 6151af941b89198812f2a33a522b30ff0a8796a0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242072"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Überwachen und Mindern der Drosselung zur Verhinderung von Latenz in Azure Time Series Insights

Wenn die Menge der eingehenden Daten die Konfiguration Ihrer Umgebung übersteigt, kann dies zu Latenz oder Drosselung in Azure Time Series Insights führen.

Sie können Latenz und Drosselung vermeiden, indem Sie die Umgebung korrekt für die Menge der zu analysierenden Daten konfigurieren.

Am wahrscheinlichsten treten Latenz und Drosselung in folgenden Fällen auf:

- Sie fügen eine Ereignisquelle hinzu, die alte Daten enthält, die möglicherweise die zugewiesene Eingangsrate überschreiten. (Time Series Insights muss auf den neuesten Stand gebracht werden.)
- Sie fügen einer Umgebung weitere Ereignisquellen hinzu, was zu einer Spitze durch zusätzliche Ereignisse führt (die die Kapazität der Umgebung überschreiten kann).
- Sie übertragen große Mengen an Verlaufsereignissen in eine Ereignisquelle, was zu einer Verzögerung führt. (Time Series Insights muss auf den neuesten Stand gebracht werden.)
- Sie verknüpfen Verweisdaten mit Telemetriedaten, was zu einer umfangreicheren Ereignisgröße führt.  In Bezug auf die Drosselung wird ein eingehendes Datenpaket mit einer Paketgröße von 32 KB als 32 Ereignisse mit jeweils einer Größe von 1 KB behandelt. Die maximal zulässige Ereignisgröße ist 32 KB. Datenpakete mit einer Größe von über 32 KB werden abgeschnitten.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Erfahren Sie mehr über das Time Series Insights-Dateneingangsverhalten und dessen Planung.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Überwachen von Latenz und Drosselung mit Warnungen

Mithilfe von Warnungen können Sie durch die Umgebung verursachte Latenzprobleme diagnostizieren und verringern.

1. Klicken Sie im Azure-Portal auf **Metriken**.

   [![Metriken](media/environment-mitigate-latency/add-metrics.png)](media/environment-mitigate-latency/add-metrics.png#lightbox)

1. Klicken Sie auf **Metrikwarnung hinzufügen**.  

   [![Hinzufügen einer Metrikwarnung](media/environment-mitigate-latency/add-metric-alert.png)](media/environment-mitigate-latency/add-metric-alert.png#lightbox)

Hier können Sie Warnungen mithilfe der folgenden Metriken konfigurieren:

|Metrik  |BESCHREIBUNG  |
|---------|---------|
|**Ingress Received Bytes** (Eingang empfangene Bytes)     | Anzahl der aus Ereignisquellen gelesenen Rohbytes. Die Anzahl der Rohbytes umfasst normalerweise den Eigenschaftennamen und den Eigenschaftswert.  |  
|**Ingress Received Invalid Messages** (Eingang empfangene ungültige Nachrichten)     | Anzahl der aus allen Azure Event Hubs- oder Azure IoT Hub-Ereignisquellen gelesenen ungültigen Nachrichten.      |
|**Ingress Received Messages** (Eingang empfangene Nachrichten)   | Anzahl der aus allen Event Hubs- oder IoT Hub-Ereignisquellen gelesenen Nachrichten.        |
|**Ingress Stored Bytes** (Eingang gespeicherte Bytes)     | Gesamtgröße der gespeicherten und für Abfragen verfügbaren Ereignisse. Die Größe wird lediglich für den Eigenschaftswert berechnet.        |
|**Ingress Stored Events** (Eingang gespeicherte Ereignisse)     |   Anzahl der gespeicherten und für Abfragen verfügbaren vereinfachten Ereignisse.      |
|**Ingress Received Message Time Lag** (Eingang Zeitverzögerung für empfangene Nachrichten)    |  Der Unterschied in Sekunden zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung im Eingang.      |
|**Ingress Received Message Count Lag** (Eingang Verzögerung aufgrund der Anzahl empfangener Nachrichten)    |  Dies ist der Unterschied zwischen der Sequenznummer der Nachricht, die auf der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der im Eingang verarbeiteten Nachricht.      |

![Latency](media/environment-mitigate-latency/latency.png)

* Bei einer Drosselung wird ein Wert für *Ingress Received Message Time Lag* (Eingang Zeitverzögerung für empfangene Nachrichten) angezeigt, um sie zu informieren, wie viele Sekunden TSI hinter dem tatsächlichen Zeitpunkt zurückliegt, zu dem die Nachricht auf der Ereignisquelle eintrifft (ohne Indizierungszeitraum von ca. 30 bis 60 Sekunden).  *Eingang Verzögerung aufgrund der Anzahl empfangener Nachrichten* sollte ebenfalls über einen Wert verfügen, sodass Sie bestimmen können, um wie viele Nachrichten Sie zurückliegen.  Die einfachste Möglichkeit zum Aufholen des Rückstands besteht darin, die Kapazität Ihrer Umgebung auf eine Einstellung zu erhöhen, die Ihnen das Wettmachen des Unterschieds ermöglicht.  

  Wenn Sie beispielsweise über eine S1-Umgebung mit nur einer Einheit verfügen und sehen, dass ein Rückstand von 5.000.000 Nachrichten besteht, können Sie die Größe Ihrer Umgebung ungefähr einen Tag lang auf sechs Einheiten erhöhen, um den Rückstand aufzuholen.  Sie können die Größe auch stärker erhöhen, um den Rückstand schneller wettzumachen. Der Abgleichszeitraum tritt bei der anfänglichen Bereitstellung einer Umgebung häufig auf, z.B. wenn Sie diesen mit einer Ereignisquelle verbinden, die bereits Ereignisse enthält, oder wenn Sie einen Massenupload für viele Verlaufsdaten durchführen.

* Eine andere Möglichkeit besteht darin, eine Warnung vom Typ **Ingress Stored Events** (Eingang gespeicherte Ereignisse) für einen Zeitraum von zwei Stunden so festzulegen, dass der Schwellenwert leicht unterhalb der Gesamtkapazität Ihrer Umgebung liegt.  Anhand dieser Warnung können Sie feststellen, ob die Kapazität konstant ausgelastet ist. Dies gibt eine hohe Wahrscheinlichkeit von Latenz an. 

  Wenn Sie beispielsweise drei S1-Einheiten (oder eine Eingangskapazität von 2.100 Ereignissen pro Minute) bereitgestellt haben, können Sie eine Warnung vom Typ **Ingress Stored Events** (Eingang gespeicherte Ereignisse) >= 1.900 Ereignisse für 2 Stunden festlegen. Wenn dieser Schwellenwert konstant überschritten und dadurch die Warnung ausgelöst wird, ist die Bereitstellung wahrscheinlich nicht ausreichend.  

* Wenn Sie vermuten, dass eine Drosselung vorliegt, können Sie die Warnung vom Typ **Ingress Received Messages** (Eingang empfangene Nachrichten) mit den ausgehenden Nachrichten der Ereignisquelle vergleichen.  Wenn der Eingang in den Event Hub größer ist als der Wert für **Ingress Received Messages** (Eingang empfangene Nachrichten), wird der Time Series Insights-Dienst wahrscheinlich gedrosselt.

## <a name="improving-performance"></a>Verbessern der Leistung

Um die Drosselung oder Latenz zu reduzieren, empfiehlt es sich, dies durch Erhöhen der Kapazität der Umgebung zu beheben.

Sie können Latenz und Drosselung vermeiden, indem Sie die Umgebung korrekt für die Menge der zu analysierenden Daten konfigurieren. Weitere Informationen zum Hinzufügen von Kapazität zu Ihrer Umgebung finden Sie unter [Skalieren Ihrer Umgebung](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Schritte zur Problembehandlung finden Sie unter [Diagnostizieren und Beheben von Problemen in der Time Series Insights-Umgebung](time-series-insights-diagnose-and-solve-problems.md).

- Zusätzliche Unterstützung erhalten Sie im [MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) oder bei [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Zudem können Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/) wenden, um Supportunterstützung zu erhalten.
