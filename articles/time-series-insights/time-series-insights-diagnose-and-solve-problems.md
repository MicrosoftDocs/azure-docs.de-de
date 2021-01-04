---
title: Diagnostizieren und Beheben von Problemen – Azure Time Series Insights
description: In diesem Artikel wird beschrieben, wie Sie häufige Probleme in ihrer Azure Time Series Insights-Umgebung diagnostizieren und beheben.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023274"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnostizieren und Beheben von Problemen in der Azure Time Series Insights Gen1-Umgebung

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel werden Probleme beschrieben, die in Ihrer Azure Time Series Insights-Umgebung auftreten können. Der Artikel stellt mögliche Ursachen und Lösungen dar.

## <a name="video"></a>Video

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Informationen zu allgemeinen Herausforderungen und Lösungen im Zusammenhang mit Azure Time Series Insights</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: Es werden keine Daten angezeigt.

Wenn keine Daten im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com) angezeigt werden, erwägen Sie diese häufigen Ursachen.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Ursache A: Ereignisquelldaten sind nicht im JSON-Format.

Azure Time Series Insights unterstützt nur JSON-Daten. JSON-Beispiele finden Sie unter [Unterstützte JSON-Formen](./concepts-json-flattening-escaping-rules.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Ursache B: Dem Schlüssel der Ereignisquelle fehlt eine erforderliche Berechtigung.

* Für einen IoT-Hub in Azure IoT Hub müssen Sie den Schlüssel bereitstellen, der die Berechtigung „Dienstverbindung“ besitzt. Wählen Sie entweder die Richtlinie **iothubowner** oder **service** aus. Beide besitzen Berechtigungen zum Verbinden mit dem Dienst.

   [![Berechtigungen zum Verbinden mit dem IoT Hub-Dienst](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Für einen Event Hub in Azure Event Hubs müssen Sie den Schlüssel bereitstellen, der die Berechtigung „Lauschen“ besitzt. Die Richtlinien **read** und **manage** funktionieren beide, da beide über die Berechtigung „Lauschen“ verfügen.

   [![Event Hub-Lauschberechtigungen](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Ursache C: Die bereitgestellte Consumergruppe wird nicht exklusiv für Azure Time Series Insights verwendet.

Wenn Sie einen IoT-Hub oder einen Event Hub registrieren, ist es wichtig, die Consumergruppe festzulegen, die Sie zum Lesen der Daten verwenden möchten. Diese Consumergruppe darf *nicht freigegeben* sein. Wenn die Consumergruppe freigegeben wird, trennt der zugrunde liegende IoT-Hub bzw. Event Hub automatisch und zufällig einen der Leser. Stellen Sie eine eindeutige Consumergruppe bereit, aus der Azure Time Series Insights Daten lesen soll.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Ursache D: Die Umgebung wurde gerade erst bereitgestellt.

Die Daten werden innerhalb weniger Minuten nach der erstmaligen Erstellung der Umgebung und der zugehörigen Daten in Azure Time Series Insights-Explorer angezeigt.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: Einige Daten werden angezeigt, andere hingegen fehlen.

Wenn Daten nur teilweise angezeigt werden, und die Daten im Rückstand zu sein scheinen, erwägen Sie diese möglichen Probleme.

### <a name="cause-a-your-environment-is-being-throttled"></a>Ursache A: Ihre Umgebung wird gedrosselt.

[Drosselung](time-series-insights-environment-mitigate-latency.md) ist ein häufiges Problem, wenn Umgebungen bereitgestellt werden, nachdem eine Ereignisquelle mit Daten erstellt wurde. In Azure IoT Hubs und Azure Events Hubs werden Daten bis zu sieben Tage lang gespeichert. Azure Time Series Insights beginnt immer mit dem ältesten Ereignis in der Ereignisquelle (First In, First Out, auch *FIFO*).

Wenn Sie beispielsweise in einer Ereignisquelle über 5 Millionen Ereignisse verfügen und eine Verbindung mit einer Azure Time Series Insights-Umgebung vom Typ „S1, eine Einheit“ herstellen, liest Azure Time Series Insights ca. 1 Million Ereignisse pro Tag. Es kann aussehen, als ob bei Azure Time Series Insights eine fünf Tage lange Wartezeit auftritt. Was aber tatsächlich passiert, ist, dass die Umgebung gedrosselt wird.

Falls Ihre Ereignisquelle ältere Ereignisse enthält, können Sie Drosselung mit zwei Ansätzen begegnen:

* Ändern Sie die Aufbewahrungslimits Ihrer Ereignisquelle, damit ältere Ereignisse, die nicht in Azure Time Series Insights angezeigt werden sollen, entfernt werden.
* Stellen Sie eine größere Umgebung bereit (Anzahl von Einheiten), um den Durchsatz für ältere Ereignisse zu erhöhen. Wenn Sie im vorangehenden Beispiel dieselbe S1-Umgebung für einen Tag auf fünf Einheiten erhöhen, sollte der Rückstand für die Umgebung innerhalb eines Tages abgebaut werden. Falls bei Ihnen im stabilen Zustand pro Tag maximal 1 Million Ereignisse generiert werden, können Sie die Ereigniskapazität nach Abbau des Rückstands durch Azure Time Series Insights auf eine Einheit verringern.

Der erzwungene Drosselungsgrenzwert basiert auf dem Typ und der Kapazität der Umgebungs-SKU. Alle Ereignisquellen in der Umgebung nutzen diese Kapazität gemeinsam. Wenn Ihre Event Hub-/IoT Hub-Ereignisquelle Daten per Push über die erzwungenen Grenzwerte hinaus überträgt, treten die Drosselung und eine Verzögerung ein.

Die folgende Abbildung zeigt eine Azure Time Series Insights-Umgebung mit SKU S1 und Kapazität 3. Es können 3 Millionen Ereignisse pro Tag eingehen.

[![Kapazität der Umgebung](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Angenommen, eine Umgebung erfasst Nachrichten von einem Event Hub. Die tägliche Erfassungsrate beträgt ca. 67.000 Nachrichten. Diese Rate ergibt somit ungefähr 46 Nachrichten pro Minute.

* Wenn jede Event Hub-Nachricht auf ein einzelnes Azure Time Series Insights-Ereignis reduziert wird, kommt es zu keiner Drosselung.
* Wenn jede Event Hub-Nachricht auf 100 Azure Time Series Insights-Ereignisse reduziert wird, sollten 4.600 Ereignisse pro Minute erfasst werden.

Eine SKU S1-Umgebung mit einer Kapazität von 3 kann nur 2.100 Ereignisse pro Minute erfassen (1 Mio. Ereignisse pro Tag = 700 Ereignisse pro Minute, bei drei Einheiten = 2.100 Ereignisse pro Minute).

Eine Übersicht über die Funktionsweise der Vereinfachungslogik finden Sie unter [Unterstützte JSON-Formen](./concepts-json-flattening-escaping-rules.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Empfohlene Lösungen für übermäßige Drosselung

Um die Verzögerung zu beheben, erhöhen Sie die SKU-Kapazität Ihrer Umgebung. Weitere Informationen finden Sie unter [Skalieren Ihrer Azure Time Series Insights-Umgebung](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Ursache B: Die anfängliche Erfassung von historischen Daten bewirkt eine langsame Erfassung.

Wenn Sie eine Verbindung mit einer vorhandenen Ereignisquelle herstellen, ist es wahrscheinlich, dass Ihr Event Hub oder IoT Hub bereits Daten enthält. Die Umgebung beginnt mit dem Abrufen von Daten am Anfang der Aufbewahrungsdauer für Nachrichten aus der Ereignisquelle. Diese Standardverarbeitung kann nicht außer Kraft gesetzt werden. Sie können Drosselung verwenden. Bis eine Drosselung den Rückstand bei der Erfassung von historischen Daten abgebaut hat, kann möglicherweise eine Weile vergehen.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Empfohlene Lösungen für eine umfassende anfängliche Erfassung

So beheben Sie den Rückstand

1. Erhöhen Sie die SKU-Kapazität auf den maximal zulässigen Wert (in diesem Fall 10). Nachdem die Kapazität erhöht wurde, beginnt der Erfassungsprozess, wesentlich schneller aufzuholen. Die erhöhte Kapazität wird Ihnen in Rechnung gestellt. Um die Aufholgeschwindigkeit bei den historischen Daten zu visualisieren, können Sie das Verfügbarkeitsdiagramm im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com) anzeigen.

2. Sobald der Rückstand aufgeholt ist, verringern Sie die SKU-Kapazität auf die normale Erfassungsrate.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problem: Die Daten wurden zuvor angezeigt, jetzt aber nicht mehr

Wenn Azure Time Series Insights keine Daten mehr erfasst, Ereignisse aber immer noch in den IoT-Hub oder den Event Hub gestreamt werden, erwägen Sie diese potenzielle Ursache.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Ursache A: Ihr Zugriffsschlüssel für den Hub wurde neu generiert und Ihre Umgebung muss aktualisiert werden.

Dieses Problem tritt auf, wenn der Schlüssel, der beim Erstellen der Ereignisquelle angegeben wurde, nicht mehr gültig ist. In Azure Time Series Insights werden in Ihrem Hub Telemetriedaten, aber keine empfangenen Nachrichten angezeigt. Wenn Sie nicht sicher sind, ob der Schlüssel neu generiert wurde, können Sie das Aktivitätsprotokoll Ihres Event Hubs nach „Namespace-Autorisierungsregeln erstellen oder aktualisieren“ durchsuchen. Für einen IoT-Hub suchen Sie nach „IoT Hub-Ressource erstellen oder aktualisieren“.

Um Ihre Azure Time Series Insights-Umgebung mit dem neuen Schlüssel zu aktualisieren, öffnen Sie die Hubressource im Azure-Portal und kopieren den neuen Schlüssel. Wechseln Sie zu Ihrer Azure Time Series Insights-Ressource, und wählen Sie **Ereignisquellen** aus:

   [![Auswählen von Ereignisquellen](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Wählen Sie die Ereignisquelle(n) aus, deren Erfassung beendet wurde, fügen Sie den neuen Schlüssel ein, und wählen Sie dann **Speichern** aus:

   [![Einfügen des neuen Schlüssels](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: Die Einstellung für den Namen der Timestamp-Eigenschaft der Ereignisquelle funktioniert nicht.

Stellen Sie sicher, dass der Wert der Timestamp-Eigenschaft, der als JSON-Zeichenfolge von Ihrer Ereignisquelle eingeht, das Format _JJJJ-MM-TTTHH:mm:ss.FFFFFFFK_ aufweist. Hier sehen Sie ein Beispiel: Ein Beispiel ist **2008-04-12T12:53Z**.

Denken Sie daran, dass der Name der Timestamp-Eigenschaft Groß-/Kleinschreibung beachtet.

Am einfachsten können Sie mit dem Azure Time Series Insights-Explorer ermitteln, ob der Name der Timestamp-Eigenschaft erfasst wird und richtig funktioniert. Wählen Sie im Azure Time Series Insights-Explorer mithilfe des Diagramms einen Zeitraum nach Eingabe des Namens der Timestamp-Eigenschaft aus. Klicken Sie mit der rechten Maustaste auf die Auswahl, und wählen Sie dann **Ereignisse untersuchen** aus.

Die erste Spaltenüberschrift sollte der Name Ihrer Timestamp-Eigenschaft sein. Neben dem Wort **Timestamp** wird **($TS)** angezeigt.

Folgende Werte werden nicht angezeigt:

* *(abc)* : Zeigt an, dass Azure Time Series Insights die Datenwerte als Zeichenfolgen liest.
* *Kalendersymbol*: Zeigt an, dass Azure Time Series Insights die Datenwerte als datetime-Werte (Datum/Uhrzeit) liest.
* *#* : Zeigt an, dass Azure Time Series Insights die Datenwerte als integer-Werter liest.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum [Mildern von Wartezeiten in Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

* Machen Sie sich mit der [Vorgehensweise zur Skalierung Ihrer Azure Time Series Insights-Umgebung](time-series-insights-how-to-scale-your-environment.md) vertraut.