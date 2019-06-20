---
title: Planen der Skalierung Ihrer Azure Time Series Insights-Umgebung | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie beim Planen einer Azure Time Series Insights-Umgebung den bewährten Methoden folgen. Die behandelten Themen umfassen die Speicherkapazität, Datenaufbewahrung, Eingangskapazität, Überwachung und Business Continuity und Disaster Recovery (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 2c11e3f623817894cea801173239cc386c6c3313
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165833"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planen Ihrer Azure Time Series Insights-GA-Umgebung

In diesem Artikel wird beschrieben, wie Sie Ihre Azure Time Series Insights-GA-Umgebung (General Availability, allgemeine Verfügbarkeit) auf der Grundlage der erwarteten Eingangsrate und den Anforderungen an die Datenaufbewahrung planen.

## <a name="video"></a>Video

**Sehen Sie sich dieses Video an, um mehr über die Datenaufbewahrung in Azure Time Series Insights und die entsprechende Planung zu erfahren**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bewährte Methoden

Für den Einstieg in Time Series Insights sollten Sie am besten wissen, wie viele Daten erwartungsgemäß pro Minute übertragen werden und wie lange die Daten gespeichert werden müssen.  

Weitere Informationen zur Kapazität und Aufbewahrung für beide Time Series Insights-SKUs finden Sie unter [Time Series Insights – Preise](https://azure.microsoft.com/pricing/details/time-series-insights/).

Berücksichtigen Sie zur optimalen Planung der Time Series Insights-Umgebung für den langfristigen Erfolg die folgenden Faktoren:

- <a href="#storage-capacity">Speicherkapazität</a>
- <a href="#data-retention">Grundlegendes zur Datenaufbewahrung</a>
- <a href="#ingress-capacity">Grundlegendes zur Eingangskapazität</a>
- <a href="#shape-your-events">Gestalten Ihrer Ereignisse</a>
- <a href="#ensure-that-you-have-reference-data">Sicherstellen des Vorhandenseins von Referenzdaten</a>

## <a name="storage-capacity"></a>Speicherkapazität

Standardmäßig werden Daten in Time Series Insights basierend auf der bereitgestellten Speichermenge (Einheiten &#215; Speichermenge pro Einheit) und den eingehenden Daten aufbewahrt.

## <a name="data-retention"></a>Beibehaltung von Daten

Sie können die Einstellung **Datenaufbewahrungszeit** in Ihrer Time Series Insights-Umgebung ändern. Es ist eine Aufbewahrungsdauer von bis zu 400 Tagen möglich. 

Time Series Insights weist zwei Modi auf. In einem Modus wird durch Optimierung sichergestellt, dass Ihre Umgebung über die neuesten Daten verfügt. Dieser Modus ist standardmäßig aktiviert. 

Im anderen Modus wird durch Optimierung sichergestellt, dass Grenzwerte für die Aufbewahrung eingehalten werden. Im zweiten Modus wird der Dateneingang angehalten, wenn die Gesamtspeicherkapazität der Umgebung erreicht ist. 

Auf der Konfigurationsseite der Umgebung im Azure-Portal können Sie die Aufbewahrungsdauer anpassen und zwischen den beiden Modi wechseln.

Sie können in Ihrer Time Series Insights-Umgebung eine Datenaufbewahrung von maximal 400 Tagen konfigurieren.

### <a name="configure-data-retention"></a>Konfigurieren der Datenaufbewahrung

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihre Time Series Insights-Umgebung aus.

1. Wählen Sie im Bereich **Time Series Insights-Umgebung** unter **Einstellungen** die Option **Konfigurieren** aus.

1. Geben Sie im Feld **Datenaufbewahrungszeit (in Tagen)** einen Wert zwischen 1 und 400 ein.

   [![Konfigurieren der Datenaufbewahrung](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Weitere Informationen zum Implementieren einer entsprechenden Datenaufbewahrungsrichtlinie finden Sie unter [Konfigurieren der Vermerkdauer in Time Series Insights](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Eingangskapazität

Der zweite wichtige Bereich bei der Planung der Time Series Insights-Umgebung ist die Eingangskapazität. Die Eingangskapazität wird von der Zuordnung pro Minute abgeleitet.

In Bezug auf die Drosselung wird ein eingehendes Datenpaket mit einer Paketgröße von 32 KB als 32 Ereignisse mit jeweils einer Größe von 1 KB behandelt. Die maximal zulässige Ereignisgröße ist 32 KB. Datenpakete mit einer Größe von über 32 KB werden abgeschnitten.

In der folgenden Tabelle ist die Eingangskapazität pro Einheit für jede Time Series Insights-SKU zusammengefasst:

|SKU  |Ereignisanzahl pro Monat  |Ereignisgröße pro Monat  |Ereignisanzahl pro Minute  |Ereignisgröße pro Minute  |
|---------|---------|---------|---------|---------|
|S1     |   30 Millionen     |  30 GB     |  720    |  720 KB   |
|S2     |   300 Millionen    |   300 GB   | 7\.200   | 7\.200 KB  |

Sie können die Kapazität einer SKU des Typs S1 oder S2 in einer einzelnen Umgebung auf 10 Einheiten erhöhen. Sie können nicht von einer S1-Umgebung zu einer S2-Umgebung migrieren. Sie können nicht von einer S2-Umgebung zu einer S1-Umgebung migrieren.

Für die Eingangskapazität bestimmen Sie zunächst den Gesamteingang, den Sie pro Monat benötigen. Als Nächstes ermitteln Sie die Anforderungen pro Minute. 

Bei der Kapazität pro Minute spielen Drosselung und Latenz eine Rolle. Wenn Sie eine Spitze im Dateneingang über einen Zeitraum unter 24 Stunden verzeichnen, kann Time Series Insights dies mit einer Eingangsrate „aufholen“, die doppelt so hoch ist wie die in der vorherigen Tabelle aufgeführten Raten.

Wenn Sie z.B. über eine einzelne SKU des Typs S1 verfügen, Daten mit einer Rate von 720 Ereignissen pro Minute eingehen und die Datenrate in einem Zeitraum unter einer Stunde mit einer Rate von maximal 1.440 Ereignissen ihren Spitzenwert erreicht, weist Ihre Umgebung keine merkliche Latenz auf. Wenn jedoch 1.440 Ereignisse pro Minute in einem Zeitraum von über einer Stunde überschritten werden, treten für Daten, die in Ihrer Umgebung visualisiert werden und zur Abfrage verfügbar sind, wahrscheinlich Latenzprobleme auf.

Sie wissen möglicherweise nicht im Voraus, wie viele Daten erwartungsgemäß übertragen werden. In diesem Fall finden Sie die Datentelemetrie für [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) und [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) in Ihrem Azure-Portalabonnement. Anhand der Telemetrie können Sie die Bereitstellung Ihrer Umgebung bestimmen. Verwenden Sie den Bereich **Metriken** im Azure-Portal für die jeweilige Ereignisquelle, um die zugehörige Telemetrie anzuzeigen. Wenn Sie sich mit den Metriken der Ereignisquelle vertraut gemacht haben, können Sie Ihre Time Series Insights-Umgebung effektiver planen und bereitstellen.

### <a name="calculate-ingress-requirements"></a>Berechnen der Eingangsanforderungen

So berechnen Sie die Eingangsanforderungen:

- Überprüfen Sie, ob die Eingangskapazität über der durchschnittlichen Rate pro Minute liegt und ob die Umgebung groß genug ist, um den voraussichtlichen Eingang zu verarbeiten, der doppelt so hoch ist wie die Kapazität über einen Zeitraum von unter einer Stunde.

- Wenn Eingangsspitzen auftreten, die länger als eine Stunde andauern, verwenden Sie die Spitzenrate als Durchschnittswert. Stellen Sie eine Umgebung mit der Kapazität zum Verarbeiten der Spitzenrate bereit.

### <a name="mitigate-throttling-and-latency"></a>Beschränken der Drosselung und Latenz

Informationen zum Verhindern der Drosselung und Latenz finden Sie unter [Verringern der Latenz und Drosselung](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Gestalten Ihrer Ereignisse

Es muss unbedingt sichergestellt sein, dass die Art, in der Sie Ereignisse an Time Series Insights senden, die Größe der von Ihnen bereitgestellten Umgebung unterstützt. (Umgekehrt können Sie die Größe der Umgebung danach zuordnen, wie viele Ereignisse Time Series Insights liest und wie groß die einzelnen Ereignisse sind.) Es ist auch wichtig, sich Gedanken über die Attribute zu machen, die Sie möglicherweise beim Abfragen Ihrer Daten für das Slicing und die Filterung verwenden möchten.

> [!TIP]
> Lesen Sie die Dokumentation zur JSON-Strukturierung in [Senden von Ereignissen an die Azure Time Series Insights-Umgebung mithilfe eines Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Sicherstellen des Vorhandenseins von Referenzdaten

Ein *Referenzdataset* ist eine Sammlung von Elementen, die die Ereignisse aus Ihrer Ereignisquelle ergänzen. Die Time Series Insights-Erfassungs-Engine verknüpft jedes Ereignis aus Ihrer Ereignisquelle mit der entsprechenden Datenzeile in Ihrem Referenzdataset. Das ergänzte Ereignis ist dann für Abfragen verfügbar. Die Verknüpfung basiert auf den in Ihrem Referenzdataset definierten **Primärschlüsselspalten**.

> [!NOTE]
> Referenzdaten werden nicht rückwirkend verknüpft. Nur aktuelle und künftige eingehende Daten werden nach dem Konfigurieren und Hochladen abgeglichen und dem Referenzdataset hinzugefügt. Wenn Sie planen, eine große Menge von Verlaufsdaten an Time Series Insights zu senden, und nicht zuerst Referenzdaten in Time Series Insights hochladen oder erstellen, müssen Sie Ihre Arbeitsschritte unter Umständen erneut durchführen (was keinen Spaß macht).  

Weitere Informationen dazu, wie Sie Ihre Referenzdaten in Time Series Insights erstellen, hochladen und verwalten, finden Sie in unserer [Dokumentation zu Verweis-DataSets](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie zunächst [eine neue Time Series Insights-Umgebung im Azure-Portal](time-series-insights-get-started.md).

- Erfahren Sie, wie Sie Time Series Insights [eine Event Hubs-Ereignisquelle hinzufügen](time-series-insights-how-to-add-an-event-source-eventhub.md).

- Erfahren Sie, wie Sie [eine IoT Hub-Ereignisquelle konfigurieren](time-series-insights-how-to-add-an-event-source-iothub.md).
