---
title: Überwachen von Azure IoT Hub mithilfe von Metriken | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Azure IoT Hub-Metriken die allgemeine Integrität Ihrer IoT Hub-Instanzen bewerten und überwachen.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 8eac70db0c563f102dfa0e3fcece9d4604582cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595647"
---
# <a name="understand-iot-hub-metrics"></a>Grundlegendes zu IoT Hub-Metriken

IoT Hub-Metriken liefern bessere Daten zum Zustand der Azure IoT-Ressourcen in Ihrem Azure-Abonnement. Auf der Grundlage von IoT Hub-Metriken können Sie die allgemeine Integrität des IoT Hub-Diensts und der mit ihm verbundenen Geräte beurteilen. Benutzerorientierte Statistiken spielen eine wichtige Rolle. Sie bieten die Basis, um die Vorgänge in Ihrem IoT Hub zu erkennen und Probleme zu beheben, ohne den Azure-Support zu kontaktieren.

Metriken sind standardmäßig aktiviert. IoT Hub-Metriken können über das Azure-Portal angezeigt werden.

## <a name="how-to-view-iot-hub-metrics"></a>Anzeigen von IoT Hub-Metriken

1. Erstellen Sie einen IoT Hub. Anweisungen zum Erstellen eines IoT-Hubs finden Sie im Leitfaden [Senden von Telemetriedaten von einem Gerät an IoT Hub](quickstart-send-telemetry-dotnet.md).

2. Öffnen Sie das Blatt Ihres IoT Hubs. Klicken Sie dort auf **Metriken**.
   
    ![Screenshot, in dem dargestellt ist, wo sich die Option „Metriken“ auf der Seite für IoT Hub-Ressourcen befindet](./media/iot-hub-metrics/enable-metrics-1.png)

3. Auf dem Blatt „Metriken“ können Sie die Metriken für Ihren IoT-Hub anzeigen und benutzerdefinierte Metrikansichten erstellen. 
   
    ![Screenshot der Seite „Metriken“ für IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Sie können Ihre Metrikdaten an einen Event Hubs-Endpunkt oder an ein Azure Storage-Konto senden, indem Sie auf **Diagnoseeinstellungen** und dann auf **Diagnoseeinstellung hinzufügen** klicken.

   ![Screenshot, in dem dargestellt ist, wo sich die Schaltfläche „Diagnoseeinstellungen“ befindet](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub-Metriken und ihre Verwendung

IoT Hub bietet mehrere Metriken, um Ihnen einen Überblick über die Integrität Ihres Hubs und die Gesamtzahl der verbundenen Geräte zu verschaffen. Sie können Informationen aus mehreren Metriken kombinieren, um sich ein umfassenderes Bild des Zustands des IoT Hubs zu machen. In der folgenden Tabelle werden die Metriken beschrieben, die für IoT Hubs nachverfolgt werden. Außerdem erfahren Sie, in welchem Bezug die einzelnen Metriken zum allgemeinen Status des IoT Hubs stehen.

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|Count|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine Dimensionen|
|d2c<br>.telemetry<br>.ingress<br>.success|Telemetry messages sent (Gesendete Telemetrienachrichten)|Count|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine Dimensionen|
|c2d<br>.commands<br>.egress<br>.complete<br>.success|Commands completed (Abgeschlossene Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät erfolgreich abgeschlossen wurden|Keine Dimensionen|
|c2d<br>.commands<br>.egress<br>.abandon<br>.success|Commands abandoned (Abgebrochene Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät abgebrochen wurden|Keine Dimensionen|
|c2d<br>.commands<br>.egress<br>.reject<br>.success|Commands rejected (Abgelehnte Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät abgelehnt wurden|Keine Dimensionen|
|devices<br>.totalDevices|Geräte gesamt (veraltet)|Count|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|devices<br>.connectedDevices<br>.allProtocol|Verbundene Geräte (veraltet) |Count|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|d2c<br>.telemetry<br>.egress<br>.success|Routing: Übermittelte Telemetrienachrichten|Count|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine Dimensionen|
|d2c<br>.telemetry<br>.egress<br>.dropped|Routing: Verworfene Telemetrienachrichten |Count|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine Dimensionen|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Routing: Verwaiste Telemetrienachrichten |Count|Gesamt|Die Häufigkeit, mit der Nachrichten durch das IoT Hub-Routing verwaist wurden, da sie mit keinen Routingregeln (einschließlich der Fallbackregel) übereinstimmten. |Keine Dimensionen|
|d2c<br>.telemetry<br>.egress<br>.invalid|Routing: Nicht kompatible Telemetrienachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Dieser Wert umfasst keine Wiederholungen.|Keine Dimensionen|
|d2c<br>.telemetry<br>.egress<br>.fallback|Routing: An den Fallback übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Routing: An Event Hub übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Event Hub-Endpunkte übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Routing: Nachrichtenwartezeit für Event Hub|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Event Hub-Endpunkt.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Routing: An Service Bus-Thema übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Routing: Nachrichtenwartezeit für Service Bus-Thema|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themaendpunkt.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.latency<br>.builtIn.events|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Telemetrienachricht beim integrierten Endpunkt (Nachrichten/Ereignisse).|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.storage|Routing: An den Speicher übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c<br>.endpoints<br>.latency<br>.storage|Routing: Nachrichtenwartezeit für Speicher|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.bytes|Routing: An den Speicher übermittelte Daten|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine Dimensionen|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.blobs|Routing: An den Speicher übermittelte Blobs|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|EventGridDeliveries|Event Grid-Übermittlungen (Vorschau)|Count|Gesamt|Die Anzahl von Anforderungen für IoT Hub zum Ausgeben von Ereignissen an Event Grid. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen. Verwenden Sie die Dimension „Result“ für die Anzahl der verschiedenen Antworttypen. Wenn Sie sehen möchten, woher die Anforderungen stammen, verwenden Sie die Dimension „EventType“.|Result, EventType|
|EventGridLatency|Event Grid-Wartezeit (Vorschau)|Millisekunden|Durchschnitt|Die durchschnittliche Wartezeit (in Millisekunden) zwischen dem Ereigniseingang bei IoT Hub und dem Ereigniseingang in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType|
|d2c<br>.twin<br>.read<br>.success|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c<br>.twin<br>.read<br>.failure|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c<br>.twin<br>.read<br>.size|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c<br>.twin<br>.update<br>.success|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c<br>.twin<br>.update<br>.failure|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c<br>.twin<br>.update<br>.size|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine Dimensionen|
|c2d<br>.methods<br>.success|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|Count|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine Dimensionen|
|c2d<br>.methods<br>.failure|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|Count|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine Dimensionen|
|c2d<br>.methods<br>.requestSize|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|Byte|Durchschnitt|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen direkten Methodenaufrufe an.|Keine Dimensionen|
|c2d<br>.methods<br>.responseSize|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|Byte|Durchschnitt|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Antworten für die direkte Methode an.|Keine Dimensionen|
|c2d<br>.twin<br>.read<br>.success|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d<br>.twin<br>.read<br>.failure|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d<br>.twin<br>.read<br>.size|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d<br>.twin<br>.update<br>.success|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d<br>.twin<br>.update<br>.failure|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d<br>.twin<br>.update<br>.size|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine Dimensionen|
|twinQueries<br>.success|Successful twin queries (Erfolgreiche Zwillingsabfragen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries<br>.failure|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries<br>.resultSize|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert der Ergebnisgröße aller erfolgreichen Zwillingsabfragen.|Keine Dimensionen|
|jobs<br>.createTwinUpdateJob<br>.success|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs<br>.createTwinUpdateJob<br>.failure|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Count|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs<br>.createDirectMethodJob<br>.success|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs<br>.createDirectMethodJob<br>.failure|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs<br>.listJobs<br>.success|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.listJobs<br>.failure|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.cancelJob<br>.success|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.cancelJob<br>.failure|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.queryJobs<br>.success|Successful job queries (Erfolgreiche Auftragsabfragen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.queryJobs<br>.failure|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs<br>.completed|Abgeschlossene Aufträge|Count|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine Dimensionen|
|jobs<br>.failed|Fehlerhafte Aufträge|Count|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine Dimensionen|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Anzahl von Drosselungsfehlern|Count|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine Dimensionen|
|dailyMessage<br>QuotaUsed|Gesamtzahl verwendeter Nachrichten|Count|Durchschnitt|Gesamtzahl der heute verwendeten Nachrichten. Dies ist ein kumulativer Wert, der jeden Tag um 00:00 Uhr UTC auf 0 zurückgesetzt wird.|Keine Dimensionen|
|deviceDataUsage|Gerätedatennutzung gesamt|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|totalDeviceCount|Geräte gesamt (Vorschau)|Count|Durchschnitt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|connected<br>DeviceCount|Verbundene Geräte (Vorschau)|Count|Durchschnitt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|Konfigurationen|Konfigurationsmetriken|Count|Gesamt|Metriken für Konfigurationsvorgänge|Keine Dimensionen|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über IoT Hub-Metriken verschafft haben, folgen Sie dem folgenden Link, um mehr über die Verwaltung von IoT Hub zu erfahren:

* [Vorgangsüberwachung](iot-hub-operations-monitoring.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
