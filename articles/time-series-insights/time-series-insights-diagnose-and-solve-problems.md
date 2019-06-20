---
title: Diagnostizieren und Beheben von Problemen in Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie häufig auftretende Probleme in Azure Time Series Insights-Umgebung diagnostiziert und behoben werden.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: fa2e26666ce863d98b5c47201eeadb1d7f6a5d2c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164514"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostizieren und Beheben von Problemen in der Time Series Insights-Umgebung

In diesem Artikel werden einige Probleme beschrieben, die in Ihrer Azure Time Series Insights-Umgebung auftreten können. Der Artikel stellt mögliche Ursachen und Lösungen dar.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Hier werden allgemeine Herausforderungen und Lösungen behandelt, mit denen Time Series Insights-Kunden zu tun haben.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: Es werden keine Daten angezeigt.

Es gibt verschiedene mögliche Gründe, aus denen Ihre Daten im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com) nicht angezeigt werden:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Ursache A: Ihre Ereignisquelldaten sind nicht im JSON-Format.

Azure Time Series Insights unterstützt nur JSON-Daten. JSON-Beispiele finden Sie unter [Unterstützte JSON-Formen](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Ursache B: Dem Schlüssel der Ereignisquelle fehlt eine erforderliche Berechtigung.

* Für einen IoT-Hub in Azure IoT Hub müssen Sie den Schlüssel angeben, der die Berechtigung **Dienstverbindung** besitzt. Beide Richtlinien **iothubowner** oder **service** funktionieren, da beide mit der Berechtigung **Dienstverbindung** ausgestattet sind.

   [![Berechtigungen zum Verbinden mit dem IoT Hub-Dienst](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Für einen Event Hub in Azure Event Hubs müssen Sie den Schlüssel angeben, der die Berechtigung **Lauschen** besitzt. Beide Richtlinien **read** oder **manage** funktionieren, da beide mit der Berechtigung **Lauschen** ausgestattet sind.

   [![Event Hub-Lauschberechtigungen](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Ursache C: Die bereitgestellte Consumergruppe ist für Time Series Insights nicht exklusiv.

Wenn Sie einen IoT-Hub oder einen Event Hub registrieren, ist es wichtig, die Consumergruppe festzulegen, die Sie zum Lesen der Daten verwenden möchten. Diese Consumergruppe darf *nicht freigegeben* sein. Wenn die Consumergruppe freigegeben wird, trennt der zugrunde liegende IoT-Hub bzw. Event Hub automatisch und zufällig einen der Leser. Stellen Sie eine eindeutige Consumergruppe bereit, aus der Time Series Insights lesen soll.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: Einige Daten werden angezeigt, andere hingegen fehlen.

Wenn Daten nur teilweise angezeigt werden, und die Daten im Rückstand zu sein scheinen, sollten Sie mehrere Möglichkeiten in Betracht ziehen.

### <a name="cause-a-your-environment-is-being-throttled"></a>Ursache A: Ihre Umgebung wird gedrosselt.

Drosselung ist ein häufiges Problem, wenn Umgebungen nach der Erstellung einer Ereignisquelle, die über Daten verfügt, bereitgestellt werden. In Azure IoT Hubs und Azure Events Hubs werden Daten bis zu sieben Tage lang gespeichert. Time Series Insights beginnt immer mit dem ältesten Ereignis in der Ereignisquelle (First In, First Out, auch *FIFO*).

Wenn Sie beispielsweise in einer Ereignisquelle über fünf 5 Millionen Ereignisse verfügen und eine Verbindung mit einer Time Series Insights-Umgebung vom Typ „S1, eine Einheit“ herstellen, liest Time Series Insights also ca. 1 Million Ereignisse pro Tag. Es kann aussehen, als ob bei Time Series Insights eine fünf Tage lange Wartezeit auftritt. Was aber tatsächlich passiert, ist, dass die Umgebung gedrosselt wird.

Falls Ihre Ereignisquelle ältere Ereignisse enthält, können Sie Drosselung mit zwei Ansätzen begegnen:

- Ändern Sie die Aufbewahrungslimits Ihrer Ereignisquelle, damit ältere Ereignisse, die nicht in Time Series Insights angezeigt werden sollen, entfernt werden.
- Stellen Sie eine größere Umgebung bereit (Anzahl von Einheiten), um den Durchsatz für ältere Ereignisse zu erhöhen. Wenn Sie im vorangehenden Beispiel dieselbe S1-Umgebung für einen Tag auf fünf Einheiten erhöhen, sollte der Rückstand für die Umgebung innerhalb eines Tages abgebaut werden. Falls bei Ihnen im stabilen Zustand pro Tag maximal 1 Million Ereignisse produziert werden, können Sie die Kapazität für Ereignisse nach Abbau des Rückstands auf eine Einheit verringern.

Der Drosselungsgrenzwert wird basierend auf dem Typ und der Kapazität der Umgebungs-SKU erzwungen. Alle Ereignisquellen in der Umgebung nutzen diese Kapazität gemeinsam. Wenn Ihre Event Hub-/IoT Hub-Ereignisquelle Daten per Push über die erzwungenen Grenzwerte hinaus überträgt, treten die Drosselung und eine Verzögerung ein.

Die folgende Abbildung zeigt eine Time Series Insights-Umgebung mit SKU S1 und Kapazität 3. Es können 3 Millionen Ereignisse pro Tag eingehen.

![Aktuelle Kapazität der Umgebungs-SKU](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Angenommen, diese Umgebung erfasst Nachrichten von einem Event Hub. Die folgende Abbildung zeigt die Erfassungsrate:

[![Beispielerfassungsrate für einen Event Hub](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)](media/diagnose-and-solve-problems/eventhub-ingress-rate.png#lightbox)

Die tägliche Erfassungsrate beträgt ca. 67.000 Nachrichten. Diese Rate ergibt somit ungefähr 46 Nachrichten pro Minute. Wenn jede Event Hub-Nachricht auf ein einzelnes Time Series Insights-Ereignis reduziert wird, kommt es zu keiner Drosselung. Wenn jede Event Hub-Nachricht auf 100 Time Series Insights-Ereignisse reduziert wird, sollten 4.600 Ereignisse pro Minute erfasst werden. Eine SKU S1-Umgebung mit einer Kapazität von 3 kann nur 2.100 Ereignisse pro Minute erfassen (1 Mio. Ereignisse pro Tag = 700 Ereignisse pro Minute, bei drei Einheiten = 2.100 Ereignisse pro Minute). In diesem Szenario tritt aufgrund der Drosselung eine Verzögerung auf.

Eine Übersicht über die Funktionsweise der Vereinfachungslogik finden Sie unter [Unterstützte JSON-Formen](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Empfohlene Lösungen für übermäßige Drosselung

Um die Verzögerung zu beheben, erhöhen Sie die SKU-Kapazität Ihrer Umgebung. Weitere Informationen finden Sie unter [Skalieren Ihrer Time Series Insights-Umgebung](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Ursache B: Die anfängliche Erfassung von historischen Daten bewirkt eine langsame Erfassung.

Wenn Sie eine Verbindung mit einer vorhandenen Ereignisquelle herstellen, ist es wahrscheinlich, dass Ihr Event Hub oder IoT Hub bereits Daten enthält. Die Umgebung beginnt mit dem Abrufen von Daten am Anfang der Aufbewahrungsdauer für Nachrichten aus der Ereignisquelle. Diese Standardverarbeitung kann nicht außer Kraft gesetzt werden. Sie können Drosselung verwenden. Bis eine Drosselung den Rückstand bei der Erfassung von historischen Daten abgebaut hat, kann möglicherweise eine Weile vergehen.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Empfohlene Lösungen für eine umfassende anfängliche Erfassung

So beheben Sie den Rückstand

1. Erhöhen Sie die SKU-Kapazität auf den maximal zulässigen Wert (in diesem Fall 10). Nachdem die Kapazität erhöht wurde, beginnt der Erfassungsprozess, wesentlich schneller aufzuholen. Die erhöhte Kapazität wird Ihnen in Rechnung gestellt. Um die Aufholgeschwindigkeit bei den historischen Daten zu visualisieren, können Sie das Verfügbarkeitsdiagramm im [Time Series Insights-Explorer](https://insights.timeseries.azure.com) anzeigen.

2. Sobald der Rückstand aufgeholt ist, verringern Sie die SKU-Kapazität auf die normale Erfassungsrate.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: Meine Einstellung für den Namen der Zeitstempeleigenschaft der Ereignisquelle funktioniert nicht.

Stellen Sie sicher, dass Name und Wert der Timestamp-Eigenschaft den folgenden Regeln entsprechen:

* Der Name der Timestamp-Eigenschaft beachtet Groß-/Kleinschreibung.
* Der Wert für die Time-Eigenschaft, der als JSON-Zeichenfolge von Ihrer Ereignisquelle eingeht, sollte das Format _JJJJ-MM-TTTHH:mm:ss.FFFFFFFK_ aufweisen. Ein Beispiel ist **2008-04-12T12:53Z**.

Sie können am einfachsten sicherstellen, dass Ihr Name der Timestamp-Eigenschaft erfasst wird und richtig funktioniert, indem Sie den Time Series Insights-Explorer verwenden. Wählen Sie im Time Series Insights-Explorer mithilfe des Diagramms einen Zeitraum aus, nachdem Sie den Namen der Timestamp-Eigenschaft eingegeben haben. Klicken Sie mit der rechten Maustaste auf die Auswahl, und wählen Sie die Option **Ereignisse untersuchen** aus.

Die erste Spaltenüberschrift sollte der Name Ihrer Timestamp-Eigenschaft sein. Neben dem Wort **Timestamp**, sollte **($ts)** angezeigt werden.

Folgende Werte sollten nicht angezeigt werden:

- *(abc)* : Zeigt an, dass Time Series Insights die Datenwerte als Zeichenfolgen liest.
- *Kalendersymbol*: Zeigt an, dass Time Series Insights den Datenwert als *datetime* (Datum/Uhrzeit) liest.
- *#* : Zeigt an, dass Time Series Insights die Datenwerte als „integer“ (ganze Zahl) liest.

## <a name="next-steps"></a>Nächste Schritte

- Unterstützung erhalten Sie im [MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) oder bei [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights).

- Um zusätzliche Unterstützung zu erhalten, wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/).
