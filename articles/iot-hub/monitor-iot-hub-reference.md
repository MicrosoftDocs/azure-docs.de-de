---
title: Referenz zu Azure IoT Hub-Überwachungsdaten
description: Wichtige Referenzmaterialien für die Überwachung von Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: f198b59f106b7d2a29e35d77b54274328be6fa93
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581600"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Referenz zu Azure IoT Hub-Überwachungsdaten

Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für Azure IoT Hub finden Sie unter [Überwachen von Azure IoT Hub](monitor-iot-hub.md).

## <a name="metrics"></a>Metriken

In diesem Abschnitt werden alle automatisch gesammelten Plattformmetriken für Azure IoT Hub aufgeführt. Der Ressourcenanbieter-Namespace für IoT Hub-Metriken ist **Microsoft.Devices**, und der Namespacetyp lautet **IoTHubs**.

In den folgenden Unterabschnitten werden die IoT Hub-Plattformmetriken nach allgemeiner Kategorie beschrieben. Außerdem werden sie nach dem Anzeigenamen aufgelistet, mit dem sie im Azure-Portal angezeigt werden. Darüber hinaus erhalten Sie relevante Informationen zu den Metriken, die in den einzelnen Unterabschnitten enthalten sind.

Außerdem wird eine einzelne Tabelle bereitgestellt, in der alle IoT Hub-Plattformmetriken nach dem Metriknamen unter [Microsoft.Devices/IotHubs](../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs) in der Azure Monitor-Dokumentation aufgeführt sind. Beachten Sie, dass diese Tabelle nicht alle in diesem Artikel beschriebenen Informationen enthält, z. B. [unterstützte Aggregationen](#supported-aggregations) für bestimmte Metriken.

Informationen zu den von anderen Azure-Diensten unterstützten Metriken finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md).

**Themen in diesem Abschnitt**

- [Unterstützte Aggregationen](#supported-aggregations)
- [Metriken zu Cloud-zu-Gerät-Befehlen](#cloud-to-device-command-metrics)
- [Metriken zu direkten Cloud-zu-Gerät-Methoden](#cloud-to-device-direct-methods-metrics)
- [Metriken zu Cloud-zu-Gerät-Zwillingsvorgängen](#cloud-to-device-twin-operations-metrics)
- [Konfigurationsmetriken](#configurations-metrics)
- [Metriken zum täglichen Kontingent](#daily-quota-metrics)
- [Gerätemetriken](#device-metrics)
- [Metriken zu Gerätetelemetriedaten](#device-telemetry-metrics)
- [Metriken zu Gerät-zu-Cloud-Zwillingsvorgängen](#device-to-cloud-twin-operations-metrics)
- [Event Grid-Metriken](#event-grid-metrics)
- [Auftragsmetriken](#jobs-metrics)
- [Routingmetriken](#routing-metrics)
- [Metriken zu Zwillingsabfragen](#twin-query-metrics)

### <a name="supported-aggregations"></a>Unterstützte Aggregationen

Die Spalte **Aggregationstyp** in jeder Tabelle entspricht der Standardaggregation, die verwendet wird, wenn die Metrik für ein Diagramm oder eine Warnung ausgewählt wird.

   ![Screenshot der Aggregation von Metriken](./media/monitor-iot-hub-reference/aggregation-type.png)

Bei den meisten Metriken sind alle Aggregationstypen zulässig. Bei Metriken vom Typ „Anzahl“ mit dem Wert **Anzahl** in der Spalte **Einheit** sind jedoch nur einige Aggregationen gültig. Metriken vom Typ „Anzahl“ können einen von zwei Typen aufweisen:

* Bei Metriken vom Typ „Anzahl“ für **Einzelpunkte** registriert IoT Hub jedes Mal einen einzelnen Datenpunkt (eine „1“), wenn der gemessene Vorgang stattfindet. Azure Monitor addiert diese Datenpunkte dann gemäß der angegebenen Granularität. Beispiele für **Einzelpunkt**-Metriken sind *Gesendete Telemetrienachrichten* und *Abgeschlossene C2D-Nachrichtenübermittlungen*. Für diese Metriken ist der einzig relevante Aggregationstyp „Gesamt“ (Summe). Sie können im Portal zwar den Mindest-, Höchst- und Durchschnittswert auswählen, diese Werte sind jedoch immer 1.

* Bei Metriken vom Typ „Anzahl“ für **Momentaufnahmen** registriert IoT Hub eine Gesamtanzahl, wenn der gemessene Vorgang stattfindet. Derzeit gibt es drei **Momentaufnahme**-Metriken, die von IoT Hub ausgegeben werden: *Gesamtzahl verwendeter Nachrichten*, *Geräte gesamt (Vorschau)* und *Verbundene Geräte (Vorschau)* . Da diese Metriken bei jeder Ausgabe eine Gesamtmenge darstellen, ergibt ihre Addition für die angegebene Granularität keinen Sinn. Azure Monitor schränkt die Auswahl von Durchschnitts-, Mindest- und Höchstwert als Aggregationstyp für diese Metriken ein.

### <a name="cloud-to-device-command-metrics"></a>Metriken zu Cloud-zu-Gerät-Befehlen

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Abgelaufene C2D-Nachrichten (Vorschau)|C2DMessagesExpired|Anzahl|Gesamt|Anzahl von abgelaufenen Cloud-zu-Gerät-Nachrichten|Keine|
|Abgeschlossene C2D-Nachrichtenübermittlungen|c2d.commands.egress.complete.success|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichtenübermittlungen, die vom Gerät erfolgreich abgeschlossen wurden|Keine|
|Abgebrochene C2D-Nachrichten|c2d.commands.egress.abandon.success|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgebrochen wurden|Keine|
|Abgelehnte C2D-Nachrichten|c2d.commands.egress.reject.success|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgelehnt wurden|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Metriken zu direkten Cloud-zu-Gerät-Methoden

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|c2d.methods.failure|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine|
|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|c2d.methods.requestSize|Byte|Average|Die Anzahl aller erfolgreichen direkten Methodenaufrufe.|Keine|
|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|c2d.methods.responseSize|Byte|Average|Die Anzahl aller erfolgreichen direkten Methodenantworten.|Keine|
|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|c2d.methods.success|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Metriken zu Cloud-zu-Gerät-Zwillingsvorgängen

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|c2d.twin.read.failure|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine|
|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|c2d.twin.update.failure|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine|
|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|c2d.twin.read.size|Byte|Average|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine|
|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|c2d.twin.update.size|Byte|Average|Die Anzahl aller erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine|
|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|c2d.twin.read.success|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine|
|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|c2d.twin.update.success|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="configurations-metrics"></a>Konfigurationsmetriken

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Konfigurationsmetriken|Konfigurationen|Anzahl|Gesamt|Die Gesamtanzahl aller CRUD-Vorgänge, die für die Gerätekonfiguration und die IoT Edge-Bereitstellung auf einer Gruppe von Zielgeräten ausgeführt wurden. Diese Metrik enthält auch die Anzahl von Vorgängen, die den Geräte- bzw. Modulzwilling aufgrund dieser Konfigurationen ändern.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Metriken zum täglichen Kontingent

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Gerätedatennutzung gesamt|deviceDataUsage|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine|
|Gerätedatennutzung gesamt (Vorschau)|deviceDataUsageV2|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine|
|Gesamtzahl verwendeter Nachrichten|dailyMessageQuotaUsed|Anzahl|Average|Gesamtzahl der heute verwendeten Nachrichten. Dies ist ein kumulativer Wert, der jeden Tag um 00:00 Uhr UTC auf 0 zurückgesetzt wird.|Keine|

Bei *Gesamtzahl verwendeter Nachrichten* werden nur die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ unterstützt. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="device-metrics"></a>Gerätemetriken

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Geräte gesamt (veraltet)|devices.totalDevices|Anzahl|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine|
|Verbundene Geräte (veraltet) |devices.connectedDevices.allProtocol|Anzahl|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine|
|Geräte gesamt (Vorschau)|totalDeviceCount|Anzahl|Average|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine|
|Verbundene Geräte (Vorschau)|connectedDeviceCount|Anzahl|Average|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine|

Bei *Geräte gesamt (veraltet)* und *Verbundene Geräte (veraltet)* ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

Für *Geräte gesamt (Vorschau)* und *Verbundene Geräte (Vorschau)* sind nur die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ gültig. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

*Verbundene Geräte (Vorschau)* und *Geräte gesamt (Vorschau)* können nicht über Diagnoseeinstellungen exportiert werden.

### <a name="device-telemetry-metrics"></a>Metriken zu Gerätetelemetriedaten

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Anzahl von Drosselungsfehlern|d2c.telemetry.ingress.sendThrottle|Anzahl|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine|
|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|d2c.telemetry.Ingress.allProtocol|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine|
|Telemetry messages sent (Gesendete Telemetrienachrichten)|d2c.telemetry.ingress.success|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Metriken zu Gerät-zu-Cloud-Zwillingsvorgängen

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|d2c.twin.read.failure|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine|
|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|d2c.twin.update.failure|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine|
|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|d2c.twin.read.size|Byte|Average|Die Anzahl aller erfolgreichen Zwillingslesevorgängen, die vom Gerät initiiert wurden.|Keine|
|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|d2c.twin.update.size|Byte|Average|Die Gesamtgröße aller erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine|
|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|d2c.twin.read.success|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine|
|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|d2c.twin.update.success|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="event-grid-metrics"></a>Event Grid-Metriken

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Event Grid-Übermittlungen (Vorschau)|EventGridDeliveries|Anzahl|Gesamt|Die Anzahl von IoT Hub-Ereignissen, die in Event Grid veröffentlicht werden. Verwenden Sie die Dimension „Result“ für die Anzahl der erfolgreichen und fehlerhaften Anforderungen. Die „EventType“-Dimension zeigt den Typ des Ereignisses an(https://aka.ms/ioteventgrid).|Result,<br/>EventType<br>*Weitere Informationen finden Sie unter [Metrikdimensionen](#metric-dimensions).*|
|Event Grid-Wartezeit (Vorschau)|EventGridLatency|Millisekunden|Average|Die durchschnittliche Wartezeit (in Millisekunden) für den Zeitraum zwischen der Generierung des Iot Hub-Ereignisses und der Veröffentlichung des Ereignisses in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType<br>*Weitere Informationen finden Sie unter [Metrikdimensionen](#metric-dimensions).*|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="jobs-metrics"></a>Auftragsmetriken

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Abgeschlossene Aufträge|jobs.completed|Anzahl|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine|
|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|jobs.listJobs.failure|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine|
|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|jobs.createDirectMethodJob.failure|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine|
|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|jobs.createTwinUpdateJob.failure|Anzahl|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine|
|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|jobs.cancelJob.failure|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine|
|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|jobs.queryJobs.failure|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine|
|Fehlerhafte Aufträge|jobs.failed|Anzahl|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine|
|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|jobs.listJobs.success|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine|
|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|jobs.createDirectMethodJob.success|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine|
|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|jobs.createTwinUpdateJob.<br>success|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine|
|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|jobs.cancelJob.success|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine|
|Successful job queries (Erfolgreiche Auftragsabfragen)|jobs.queryJobs.success|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="routing-metrics"></a>Routingmetriken

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
| Routingzustellversuche (Vorschau) |RoutingDeliveries | Anzahl | Gesamt |Dies ist die Metrik zur Routingzustellung. Identifizieren Sie anhand der Dimensionen den Zustellungsstatus für einen bestimmten Endpunkt oder eine bestimmte Routingquelle.| Result,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Weitere Informationen finden Sie unter [Metrikdimensionen](#metric-dimensions).* |
| Größe der Routingzustellungsdaten in Bytes (Vorschau)|RoutingDataSizeInBytesDelivered| Byte | Gesamt |Die Gesamtanzahl von Bytes, die von IoT Hub an einen benutzerdefinierten und einen integrierten Endpunkt weitergeleitet wurden. Identifizieren Sie anhand der Dimensionen die Größe der Daten, die an einen bestimmten Endpunkt oder eine bestimmte Routingquelle weitergeleitet wurden.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Weitere Informationen finden Sie unter [Metrikdimensionen](#metric-dimensions).*|
| Routinglatenz (Vorschau) |RoutingDeliveryLatency| Millisekunden | Average |Dies ist die Metrik zur Routingzustellungslatenz. Identifizieren Sie anhand der Dimensionen die Latenz für einen bestimmten Endpunkt oder eine bestimmte Routingquelle.| RoutingSource,<br>EndpointType,<br>EndpointName<br>*Weitere Informationen finden Sie unter [Metrikdimensionen](#metric-dimensions).*|
|Routing: An den Speicher übermittelte Blobs|d2c.endpoints.egress.storage.blobs|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine|
|Routing: An den Speicher übermittelte Daten|d2c.endpoints.egress.storage.bytes|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine|
|Routing: Nachrichtenwartezeit für Event Hub|d2c.endpoints.latency.eventHubs|Millisekunden|Average|Die durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang einer Nachricht bei IoT Hub und dem Eingang der Nachricht bei benutzerdefinierten Endpunkten vom Typ „Event Hub“. Diese Metrik enthält keine an integrierte Endpunkte übermittelten Nachrichten (Ereignisse).|Keine|
|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|d2c.endpoints.latency.serviceBusQueues|Millisekunden|Average|Die durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang einer Nachricht bei IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine|
|Routing: Nachrichtenwartezeit für Service Bus-Thema|d2c.endpoints.latency.serviceBusTopics|Millisekunden|Average|Die durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang einer Nachricht bei IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themenendpunkt.|Keine|
|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|d2c.endpoints.latency.builtIn.events|Millisekunden|Average|Die durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang einer Nachricht bei IoT Hub und dem Eingang der Nachricht beim integrierten Endpunkt (Nachrichten/Ereignisse) und der Fallbackroute.|Keine|
|Routing: Nachrichtenwartezeit für Speicher|d2c.endpoints.latency.storage|Millisekunden|Average|Die durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang einer Nachricht bei IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine|
|Routing: An Event Hub übermittelte Nachrichten|d2c.endpoints.egress.eventHubs|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an benutzerdefinierte Endpunkt vom Typ „Event Hub“ übermittelt hat. Diese Metrik enthält keine an integrierte Endpunkte übermittelten Nachrichten (Ereignisse).|Keine|
|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|d2c.endpoints.egress.serviceBusQueues|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine|
|Routing: An Service Bus-Thema übermittelte Nachrichten|d2c.endpoints.egress.serviceBusTopics|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine|
|Routing: An den Fallback übermittelte Nachrichten|d2c.telemetry.egress.fallback|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine|
|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|d2c.endpoints.egress.builtIn.events|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) und die Fallbackroute übermittelt hat.|Keine|
|Routing: An den Speicher übermittelte Nachrichten|d2c.endpoints.egress.storage|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine|
|Routing: Übermittelte Telemetrienachrichten|d2c.telemetry.egress.success|Anzahl|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine|
|Routing: Verworfene Telemetrienachrichten |d2c.telemetry.egress.dropped|Anzahl|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine|
|Routing: Nicht kompatible Telemetrienachrichten|d2c.telemetry.egress.invalid|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Eine Nachricht ist mit einem Endpunkt inkompatibel, wenn IoT Hub versucht, die Nachricht an den Endpunkt zu übermitteln und ein nicht vorübergehender Fehler auftritt. Es wird nicht versucht, ungültige Nachrichten erneut zuzustellen. Dieser Wert umfasst keine Wiederholungen.|Keine|
|Routing: Verwaiste Telemetrienachrichten |d2c.telemetry.egress.orphaned|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing zu verwaisten Nachrichten führte, weil sie keiner Routingabfrage entsprachen (wenn die Fallbackroute deaktiviert wurde).|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

### <a name="twin-query-metrics"></a>Metriken zu Zwillingsabfragen

|Metrikanzeigename|Metrik|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|twinQueries.failure|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine|
|Successful twin queries (Erfolgreiche Zwillingsabfragen)|twinQueries.success|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine|
|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|twinQueries.resultSize|Byte|Average|Die Gesamtgröße der Ergebnisse aller erfolgreichen Zwillingsabfragen.|Keine|

Bei Metriken mit dem Wert **Anzahl** für **Einheit** ist nur die Aggregation „Gesamt“ (Summe) gültig. Die Aggregationen „Minimum“, „Maximum“ und „Durchschnitt“ geben immer „1“ zurück. Weitere Informationen finden Sie unter [Unterstützte Aggregationen](#supported-aggregations).

## <a name="metric-dimensions"></a>Metrikdimensionen

In Azure IoT Hub weisen einige der Routing- und Event Grid-Metriken die folgenden Dimensionen auf.

|Dimensionsname | BESCHREIBUNG|
|---|---|
||
|**EndpointName**| Der Endpunktname.|
|**EndpointType**|Einer der folgenden: **eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **builtin** oder **blobStorage**.|
|**EventType**| Einer der folgenden Event Grid-Ereignistypen: **Microsoft.Devices.DeviceCreated**. **Microsoft.Devices.DeviceDeleted**, **Microsoft.Devices.DeviceConnected**, **Microsoft.Devices.DeviceDisconnected** oder **Microsoft.Devices.DeviceTelemetry**. Weitere Informationen finden Sie unter [Ereignistypen](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Eine der folgenden: **invalid**, **dropped**, **orphaned** oder **null**.|
|**Ergebnis**| Entweder **success** (Erfolg) oder **failure** (Fehler).|
|**RoutingSource**| Gerätemeldungen<br>Änderungsereignisse für Zwillinge<br>Ereignisse zum Gerätelebenszyklus|

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Ressourcenprotokolle

In diesem Abschnitt werden alle Typen und Schemas für die Kategorie der Ressourcenprotokolle aufgelistet, die für Azure IoT Hub erfasst werden. Der Ressourcenanbieter und der Typ lauten für alle IoT Hub-Protokolle [Microsoft.Devices/IotHubs](../azure-monitor/platform/resource-logs-categories.md#microsoftdevicesiothubs).

**Themen in diesem Abschnitt**

- [Verbindungen](#connections)
- [Gerätetelemetrie](#device-telemetry)
- [C2D-Befehle](#cloud-to-device-commands)
- [Geräte-Identitätsvorgänge](#device-identity-operations)
- [Dateiuploadvorgänge](#file-upload-operations)
- [Routen](#routes)
- [Gerät-zu-Cloud-Zwillingsvorgänge](#device-to-cloud-twin-operations)
- [Cloud-zu-Gerät-Zwillingsvorgänge](#cloud-to-device-twin-operations)
- [Zwillingsabfragen](#twin-queries)
- [Auftragsvorgänge](#jobs-operations)
- [Direkte Methoden](#direct-methods)
- [Verteilte Ablaufverfolgung (Vorschauversion)](#distributed-tracing-preview)
  - [IoT Hub-D2C-Protokolle (Gerät-zu-Cloud)](#iot-hub-d2c-device-to-cloud-logs)
  - [Eingehende IoT Hub-Protokolle](#iot-hub-ingress-logs)
  - [Ausgehende IoT Hub-Protokolle](#iot-hub-egress-logs)
- [Konfigurationen](#configurations)
- [Gerätestreams (Vorschau)](#device-streams-preview)

### <a name="connections"></a>Verbindungen

Die Kategorie für Verbindungen verfolgt Ereignisse zum Herstellen und Trennen von Verbindungen mit einem IoT Hub sowie Fehler nach. Diese Kategorie ist bei der Identifizierung nicht autorisierter Verbindungsversuche und/oder bei Warnungen hilfreich, wenn die Verbindung mit Geräten getrennt wird.

> [!NOTE]
> Überprüfen Sie den [Gerätetakt](iot-hub-devguide-identity-registry.md#device-heartbeat), um zuverlässige Informationen zum Verbindungsstatus von Geräten zu erhalten.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Gerätetelemetrie

Die Kategorie für Gerätetelemetrie dient der Nachverfolgung von Fehlern, die auf dem IoT Hub auftreten und in Beziehung zur Telemetriepipeline stehen. Zu dieser Kategorie gehören Fehler, die beim Senden von Telemetrie-Ereignissen (z.B. Drosselung) und Empfangen von Telemetrie-Ereignissen (z.B. nicht autorisierte Leser) auftreten. Diese Kategorie kann keine Fehler abfangen, die vom Code verursacht werden, der auf dem Gerät selbst ausgeführt wird.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>C2D-Befehle

Die Kategorie der C2D-Befehle dient der Nachverfolgung von Fehlern, die auf dem IoT Hub auftreten und in Beziehung zur C2D-Nachrichtenpipeline stehen. Zu dieser Kategorie gehören Fehler, die bei folgenden Aktionen auftreten:

* Senden von Cloud-zu-Gerät-Nachrichten (z. B. Fehler aufgrund nicht autorisierter Absender),
* Empfangen von Cloud-zu-Gerät-Nachrichten (z. B. Fehler des Typs „Übermittlungsanzahl wurde überschritten“) und
* Empfangen von Cloud-zu-Gerät-Nachrichtenfeedback (z. B. Fehler aufgrund von abgelaufenem Feedback).

Diese Kategorie fängt keine Fehler ab, wenn die Cloud-zu-Gerät-Nachricht erfolgreich übermittelt wurde, aber dann vom Gerät nicht ordnungsgemäß verarbeitet wird.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Geräte-Identitätsvorgänge

Die Kategorie für Geräte-Identitätsvorgänge dient der Nachverfolgung von Fehlern, die auftreten, wenn Sie versuchen, einen Eintrag in der Identitätsregistrierung Ihres IoT Hubs zu erstellen, zu aktualisieren oder zu löschen. Das Nachverfolgen dieser Kategorie ist für Bereitstellungsszenarien nützlich.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Dateiuploadvorgänge

Die Dateiuploadkategorie dient der Nachverfolgung von Fehlern, die auf dem IoT Hub auftreten und in Beziehung zur Dateiuploadfunktionalität stehen. In diese Kategorie fällt Folgendes:

* Fehler, die beim SAS-URI auftreten, beispielsweise wenn er abläuft, bevor ein Gerät den Hub über einen abgeschlossenen Upload informiert

* Vom Gerät gemeldete Uploadfehler

* Fehler, die auftreten, wenn während der IoT Hub-Benachrichtigungserstellung eine Datei nicht im Speicher gefunden wird

Diese Kategorie kann keine Fehler abfangen, die auftreten, während das Gerät eine Datei in den Speicher hochlädt.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Routen

Die Kategorie [Nachrichtenweiterleitung](./iot-hub-devguide-messages-d2c.md) verfolgt Fehler, die während der Auswertung der Nachrichtenweiterleitung auftreten, und die durch IoT Hub wahrgenommene Endpunktintegrität. Diese Kategorie umfasst z. B. folgende Ereignisse:

* Eine Regel wird zu „nicht definiert“ ausgewertet,
* IoT Hub markiert einen Endpunkt für unzustellbare Nachrichten, oder
* Alle Fehler, die von einem Endpunkt empfangen werden.

Diese Kategorie umfasst keine bestimmten Fehler zu den Nachrichten selbst (z. B. Gerätedrosselungsfehler), die in der Kategorie „Gerätetelemetrie“ gemeldet werden.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Im Folgenden finden Sie weitere Informationen zum Routing von Ressourcenprotokollen:

* [Liste der Fehlercodes in Routingressourcenprotokollen](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Liste der Vorgangsnamen in Routingressourcenprotokollen](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Gerät-zu-Cloud-Zwillingsvorgänge

Die Kategorie der Gerät-zu-Cloud-Zwillingsvorgänge verfolgt von einem Gerät initiierte Ereignisse auf Gerätezwillingen nach. Diese Vorgänge können das Abrufen des Zwillings, das Aktualisieren gemeldeter Eigenschaften und das Abonnieren gewünschter Eigenschaften einschließen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Cloud-zu-Gerät-Zwillingsvorgänge

Die Kategorie der Cloud-zu-Gerät-Zwillingsvorgänge verfolgt von einem Dienst initiierte Ereignisse auf Gerätezwillingen nach. Diese Vorgänge können das Abrufen des Zwillings, Aktualisieren oder Ersetzen von Tags und Aktualisieren oder Ersetzen gewünschter Eigenschaften einschließen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Zwillingsabfragen

Die Kategorie der Zwillingsabfragen berichtet über Abfrageanforderungen für Gerätezwillinge, die in der Cloud initiiert werden.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Auftragsvorgänge

Die Kategorie der Auftragsvorgänge berichtet über Auftragsanforderungen zum Aktualisieren von Gerätezwillingen oder Aufrufen direkter Methoden auf mehreren Geräten. Diese Anforderungen werden in der Cloud initiiert.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Direkte Methoden

Die Kategorie der direkten Methoden verfolgt Anforderung-Antwort-Interaktionen, die an die einzelnen Geräte gesendet werden. Diese Anforderungen werden in der Cloud initiiert.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Verteilte Ablaufverfolgung (Vorschauversion)

Die Kategorie für verteilte Ablaufverfolgung verfolgt die Korrelations-IDs für Nachrichten nach, die den Kontextheader für Ablaufverfolgung enthalten. Zur vollständigen Aktivierung dieser Protokolle muss der clientseitige Code gemäß der Anleitung in [Analyze and diagnose IoT applications end-to-end with IoT Hub distributed tracing (preview)](iot-hub-distributed-tracing.md) (End-to-End-Analyse und -Diagnose von IoT-Anwendungen mit der verteilten Ablaufverfolgung von IoT Hub (Vorschauversion)) aktualisiert werden.

Hinweis: `correlationId` entspricht dem [W3C-Vorschlag für Ablaufverfolgungskontext](https://github.com/w3c/trace-context) insofern, dass sowohl ein `trace-id`- als auch ein `span-id`-Element enthalten ist.

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub-D2C-Protokolle (Gerät-zu-Cloud)

IoT Hub zeichnet dieses Protokoll auf, wenn eine Nachricht mit gültigen Ablaufverfolgungseigenschaften bei IoT Hub eingeht.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Hier wird `durationMs` nicht berechnet, da die Uhr von IoT Hub möglicherweise nicht mit der Geräteuhr synchronisiert ist und daher eine Berechnung der Dauer irreführend sein kann. Es wird empfohlen, Logik unter Verwendung der Zeitstempel im Abschnitt `properties` zu schreiben, um Spitzen in der Gerät-zu-Cloud-Latenz zu erfassen.

| Eigenschaft | type | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Die Größe einer Gerät-zu-Cloud-Nachricht in Bytes |
| **deviceId** | Zeichenfolge aus alphanumerischen 7-Bit-ASCII-Zeichen | Die Identität des Geräts |
| **callerLocalTimeUtc** | UTC-Zeitstempel | Der Erstellungszeitpunkt der Nachricht wie von der Uhr des lokalen Geräts gemeldet |
| **calleeLocalTimeUtc** | UTC-Zeitstempel | Der Eingangszeitpunkt der Nachricht beim IoT Hub Gateway wie von der Uhr des IoT Hub-Diensts gemeldet |

#### <a name="iot-hub-ingress-logs"></a>Eingehende IoT Hub-Protokolle

IoT Hub zeichnet dieses Protokoll auf, wenn die Nachricht mit gültigen Ablaufverfolgungseigenschaften in interne oder integrierte Event Hub-Instanzen schreibt.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Im `properties` Abschnitt enthält dieses Protokoll zusätzliche Informationen zum Nachrichteneingang.

| Eigenschaft | type | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Entweder „true“ oder „false“; gibt an, ob Nachrichtenweiterleitung in IoT Hub aktiviert ist |
| **parentSpanId** | String | Das [span-id](https://w3c.github.io/trace-context/#parent-id)-Element der übergeordneten Nachricht, in diesem Fall die D2C-Nachrichtenablaufverfolgung |

#### <a name="iot-hub-egress-logs"></a>Ausgehende IoT Hub-Protokolle

IoT Hub zeichnet dieses Protokoll auf, wenn die [Weiterleitung](iot-hub-devguide-messages-d2c.md) aktiviert ist und die Nachricht an einen [Endpunkt](iot-hub-devguide-endpoints.md) geschrieben wird. Wenn die Weiterleitung nicht aktiviert ist, zeichnet IoT Hub dieses Protokoll nicht auf.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Im `properties` Abschnitt enthält dieses Protokoll zusätzliche Informationen zum Nachrichteneingang.

| Eigenschaft | type | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Der Name des Routingendpunkts |
| **endpointType** | String | Der Typ des Routingendpunkts |
| **parentSpanId** | String | Das [span-id](https://w3c.github.io/trace-context/#parent-id)-Element der übergeordneten Nachricht, in diesem Fall die Ablaufverfolgung des IoT Hub-Nachrichteneingangs |

### <a name="configurations"></a>Configurations

Die Kategorie „IoT Hub-Konfigurationsprotokolle“ verfolgt Ereignisse und Fehler für die Featuregruppe „Automatische Geräteverwaltung“.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Gerätestreams (Vorschau)

Die Kategorie „Gerätestreams“ verfolgt Anforderung-Antwort-Interaktionen, die an die einzelnen Geräte gesendet werden.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen
<!-- REQUIRED. Please keep heading in this order -->

Dieser Abschnitt bezieht sich auf alle Kusto-Tabellen in Azure Monitor-Protokollen, die für Azure IoT Hub relevant und für die Abfrage durch Log Analytics verfügbar sind. Eine Liste dieser Tabellen sowie Links zu weiteren Informationen für den Ressourcentyp „IoT Hub“ finden Sie unter [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) in der Referenz zu Tabellen in Azure Monitor-Protokollen.

Eine Referenz zu allen Azure Monitor-Protokollen und Log Analytics-Tabellen finden Sie in der [Referenz zu Tabellen in Azure Monitor-Protokollen](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Weitere Informationen

* Unter [Überwachen von Azure IoT Hub](monitor-iot-hub.md) finden Sie eine Beschreibung der Überwachung von Azure IoT Hub.
* Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
