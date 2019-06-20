---
title: Grundlegendes zum Azure IoT Hub-Nachrichtenrouting | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Verwenden des Nachrichtenroutings zum Senden von D2C-Nachrichten. Enthält Informationen zum Senden von Telemetriedaten und anderen Daten.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 094641baaa1472b481140072cd5d3d35d27d5ed7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390526"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Das Nachrichtenrouting ermöglicht es Ihnen, Nachrichten automatisiert, skalierbar und zuverlässig von Ihren Geräten an Clouddienste zu senden. Das Nachrichtenrouting kann für Folgendes verwendet werden: 

* **Senden von Gerätetelemetrienachrichten sowie von Ereignissen** – insbesondere von Ereignissen im Gerätelebenszyklus und Änderungsereignissen für Gerätezwillinge im integrierten Endpunkt und in benutzerdefinierten Endpunkten. Erfahren Sie mehr über [Routingendpunkte](#routing-endpoints).

* **Filtern von Daten vor dem Weiterleiten an verschiedene Endpunkte** durch Anwenden umfassender Abfragen. Mithilfe des Nachrichtenroutings können Sie Abfragen für Nachrichteneigenschaften und Nachrichtentext sowie für Gerätezwillingstags und Gerätezwillingseigenschaften durchführen. Erfahren Sie mehr über die Verwendung von [Abfragen im Nachrichtenrouting](iot-hub-devguide-routing-query-syntax.md).

IoT Hub benötigt Schreibzugriff auf diese Dienstendpunkte, damit das Nachrichtenrouting funktioniert. Wenn Sie Ihre Endpunkte über das Azure-Portal konfigurieren, werden die erforderlichen Berechtigungen für Sie hinzugefügt. Stellen Sie sicher, dass Sie Ihre Dienste zur Unterstützung des erwarteten Durchsatzes konfigurieren. Nach der Erstkonfiguration Ihrer IoT-Lösung müssen Sie möglicherweise Ihre zusätzlichen Endpunkte überwachen und ggf. Anpassungen an die tatsächliche Last vornehmen.

Der IoT Hub definiert ein [gemeinsames Format](iot-hub-devguide-messages-construct.md) für alle Gerät-zu-Cloud-Nachrichten, um Interoperabilität zwischen Protokollen zu ermöglichen. Wenn eine Nachricht mehreren Routen entspricht, die auf den gleichen Endpunkt verweisen, übermittelt IoT Hub die Nachricht nur einmal an diesen Endpunkt. Aus diesem Grund müssen Sie keine Deduplizierung für Ihre Service Bus-Warteschlange oder Ihr Service Bus-Thema konfigurieren. In partitionierten Warteschlangen garantiert die Partitionsaffinität die Nachrichtensortierung. In diesem Tutorial lernen Sie, wie Sie das [Nachrichtenrouting konfigurieren](tutorial-routing.md).

## <a name="routing-endpoints"></a>Routingendpunkte

Ein IoT Hub verfügt über einen standardmäßigen integrierten Endpunkt (**messages/events**), der mit Event Hubs kompatibel ist. Sie können [benutzerdefinierte Endpunkte](iot-hub-devguide-endpoints.md#custom-endpoints) für die Weiterleitung von Nachrichten erstellen, indem Sie andere Dienste Ihres Abonnements mit dem IoT Hub verknüpfen. IoT Hub unterstützt derzeit folgende Dienste als benutzerdefinierte Endpunkte:

### <a name="built-in-endpoint"></a>Integrierter Endpunkt

Sie können standardmäßige [Event Hubs-Integration und -SDKs](iot-hub-devguide-messages-read-builtin.md) zum Empfangen von D2C-Nachrichten vom integrierten Endpunkt (**messages/events**) verwenden. Sobald eine Route erstellt wird, werden keine Daten mehr an den integrierten Endpunkt gesendet, es sei denn, eine Route zu diesem Endpunkt wird erstellt.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub unterstützt das Schreiben von Daten in Azure Blob Storage im [Apache Avro](https://avro.apache.org/)- und im JSON-Format. Die Möglichkeit zum Codieren des JSON-Formats steht jetzt allgemein in allen Regionen zur Verfügung, in der IoT Hub verfügbar ist. Standardwert: AVRO. Das Codierungsformat kann nur festgelegt werden, wenn der Endpunkt des Blobspeichers konfiguriert ist. Das Format kann nicht für einen vorhandenen Endpunkt bearbeitet werden. Wenn Sie die JSON-Codierung verwenden, müssen Sie in der Nachricht [Systemeigenschaften](iot-hub-devguide-routing-query-syntax.md#system-properties) „contentType“ auf JSON und „contentEncoding“ auf UTF-8 festlegen. Wenn dies nicht festgelegt ist, schreibt IoT Hub die Nachrichten in Base64-codiertem Format. Sie können das Codierungsformat über die IoT Hub-REST-API „Create“ oder „Update“ auswählen – insbesondere [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), das Azure-Portal, die [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) oder die [Azure Powershell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Das folgende Diagramm zeigt, wie Sie das Codierungsformat im Azure-Portal auswählen.

![Endpunktcodierung für Blobspeicher](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub verarbeitet Nachrichten batchweise und schreibt Daten in ein Blob, wenn das Batch eine bestimmte Größe erreicht hat oder ein bestimmter Zeitraum verstrichen ist. IoT Hub folgt standardmäßig der nachstehenden Dateibenennungskonvention:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Sie können eine beliebige Dateibenennungskonvention verwenden, müssen jedoch alle aufgelisteten Tokens verwenden. IoT Hub schreibt in ein leeres Blob, wenn keine Daten zum Schreiben vorhanden sind.

Beim Weiterleiten an Blobspeicher sollten die Blobs eingetragen und dann durchlaufen werden, um sicherzustellen, dass alle Container gelesen werden, ohne Annahmen zu Partitionen anzustellen. Der Partitionsbereich könnte sich möglicherweise bei einem [von Microsoft initiierten Failover](iot-hub-ha-dr.md#microsoft-initiated-failover) oder einem [manuellen Failover](iot-hub-ha-dr.md#manual-failover-preview) in Zusammenhang mit IoT Hub ändern. Sie können die [API zum Auflisten von Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) verwenden, um die Liste der Blobs aufzulisten. Das folgende Beispiel dient als Anleitung.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus-Warteschlangen und Service Bus-Themen

Für Service Bus-Warteschlangen und -Themen, die als IoT Hub-Endpunkte verwendet werden, dürfen **Sitzungen** oder **Duplikaterkennung** nicht aktiviert werden. Wenn eine dieser Optionen aktiviert ist, wird der Endpunkt im Azure-Portal als **Nicht erreichbar** angezeigt.

### <a name="event-hubs"></a>Event Hubs

Sie können Daten nicht nur an den mit Event Hubs kompatiblen integrierten Endpunkt, sondern auch an benutzerdefinierte Endpunkte vom Typ „Event Hubs“ weiterleiten. 

## <a name="reading-data-that-has-been-routed"></a>Lesen weitergeleiteter Daten

Sie können mit diesem [Tutorial](tutorial-routing.md) eine Route konfigurieren.

Verwenden Sie die folgenden Tutorials, um zu erfahren, wie Sie Nachrichten aus einem Endpunkt auslesen.

* Lesen vom [integrierten Endpunkt](quickstart-send-telemetry-node.md)

* Lesen aus [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Lesen aus [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lesen aus [Service Bus-Warteschlangen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lesen aus [Service Bus-Themen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Fallbackroute

Die Fallbackroute sendet alle Nachrichten, die die Abfragebedingungen in einer der vorhandenen Routen nicht erfüllen, an den integrierten Endpunkt (**messages/events**), der mit [Event Hubs](/azure/event-hubs/) kompatibel ist. Wenn das Nachrichtenrouting aktiviert ist, können Sie die Funktion der Fallbackroute verwenden. Sobald eine Route erstellt wird, werden keine Daten mehr an den integrierten Endpunkt gesendet, es sei denn, eine Route zu diesem Endpunkt wird erstellt. Wenn keine Routen zum integrierten Endpunkt vorhanden sind und eine Fallbackroute aktiviert ist, werden nur Nachrichten an den integrierten Endpunkt gesendet, die keinen Abfragebedingungen in Routen entsprechen. Wenn alle vorhandenen Routen gelöscht wurden, muss eine Fallbackroute aktiviert werden, um alle Daten im integrierten Endpunkt zu empfangen.

Sie können die Fallbackroute im Azure-Portal auf dem Blatt „Nachrichtenrouting“ aktivieren und deaktivieren. Sie können auch Azure Resource Manager verwenden, um [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) für die Nutzung eines benutzerdefinierten Endpunkts für die Fallbackroute festzulegen.

## <a name="non-telemetry-events"></a>Nicht telemetriebezogene Ereignisse

Das Nachrichtenrouting ermöglicht zusätzlich zum Weiterleiten von Gerätetelemetriedaten auch das Senden von Änderungsereignissen für Gerätezwillinge und Ereignissen im Gerätelebenszyklus. Wenn beispielsweise eine Route erstellt wird, deren Datenquelle auf **Änderungsereignisse für Gerätezwillinge** festgelegt ist, sendet IoT Hub Nachrichten an den Endpunkt, der die Änderung im Gerätezwilling enthält. Ebenso gilt: Wenn eine Route erstellt wird, deren Datenquelle auf **Ereignisse im Gerätelebenszyklus** festgelegt ist, sendet IoT Hub eine Nachricht, die darauf hinweist, ob das Gerät erstellt oder gelöscht wurde. 

[IoT Hub lässt sich auch in Azure Event Grid integrieren](iot-hub-event-grid.md), um Geräteereignisse zu veröffentlichen und so Echtzeitintegrationen und die Automatisierung von Workflows basierend auf diesen Ereignissen zu unterstützen. Informationen dazu, welche Methode sich am besten für Ihr Szenario eignet, finden Sie unter [Vergleichen von Nachrichtenweiterleitung und Event Grid für IoT Hub](iot-hub-event-grid-routing-comparison.md).

## <a name="testing-routes"></a>Testen von Routen

Wenn Sie eine neue Route erstellen oder eine vorhandene Route bearbeiten, sollten Sie die Routenabfrage mit einer Beispielnachricht testen. Sie können einzelne Routen oder alle Routen gleichzeitig testen. Während des Tests werden keine Nachrichten an die Endpunkte weitergeleitet. Zum Testen können Sie das Azure-Portal, Azure Resource Manager, Azure PowerShell oder die Azure CLI verwenden. Anhand der Ausgaben können Sie herausfinden, ob die Beispielnachricht der Abfrage entspricht oder nicht oder ob der Test nicht ausgeführt werden konnte, weil Beispielnachricht oder Abfragesyntax falsch sind. Weitere Informationen finden Sie unter [Testen einer Route](/rest/api/iothub/iothubresource/testroute) und [Testen aller Routen](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latency

Wenn Sie Geräte-zu-Cloud-Telemetrienachrichten über integrierte Endpunkte weiterleiten, kommt es nach der Erstellung der ersten Route zu einer leichten Erhöhung der Gesamtlatenz.

In den meisten Fällen beträgt der durchschnittliche Latenzanstieg weniger als 500 ms. Sie können die Latenz mithilfe der IoT Hub-Metriken **Routing: Nachrichtenlatenz für „messages/events“** oder **d2c.endpoints.latency.builtIn.events** überwachen. Das Erstellen oder Löschen einer Route nach der ersten hat keinen Einfluss auf die Gesamtlatenz.

## <a name="monitoring-and-troubleshooting"></a>Überwachung und Problembehandlung

IoT Hub bietet mehrere Metriken in Bezug auf Routing und Endpunkte, um Ihnen einen Überblick über die Integrität Ihres Hubs und der gesendeten Nachrichten zu verschaffen. Sie können Informationen aus mehreren Metriken kombinieren, um die Grundursache eines Problems zu ermitteln. Verwenden Sie z. B. die Metriken **Routing: verworfene Telemetrienachrichten** oder **d2c.telemetry.egress.dropped**, um herauszufinden, wie viele Nachrichten verworfen wurden, weil sie keiner Abfrage in einer der Routen entsprachen und die Fallbackroute deaktiviert war. Unter [IoT Hub-Metriken](iot-hub-metrics.md) werden alle Metriken aufgeführt, die standardmäßig für Ihren IoT Hub aktiviert sind.

Sie können die REST-API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) (Endpunktintegrität abrufen) verwenden, um den [Integritätsstatus](iot-hub-devguide-endpoints.md#custom-endpoints) der Endpunkte abzurufen. Es empfiehlt sich, die [IoT Hub-Metriken](iot-hub-metrics.md), die sich auf die Routinglatenz von Nachrichten beziehen, zu verwenden, um Fehler bei beschädigter Endpunktintegrität zu erkennen und zu beheben. Sie können z. B. für Endpunkttyp-Event Hubs **d2c.endpoints.latency.eventHubs** überwachen. Der Status eines fehlerhaften Endpunkts wird zu fehlerfrei aktualisiert, wenn IoT Hub einen möglicherweise konsistenten Integritätsstatus erreicht hat.

Mithilfe der Diagnoseprotokolle für **Routen** in den Azure Monitor-[Diagnoseeinstellungen](../iot-hub/iot-hub-monitor-resource-health.md) können Sie Fehler bei der Auswertung einer Routingabfrage und der Endpunktintegrität nachverfolgen, die von IoT Hub registriert werden, wenn beispielsweise ein Endpunkt nicht erreichbar ist. Diese Diagnoseprotokolle können zur benutzerdefinierten Verarbeitung an Azure Monitor-Protokolle, Event Hubs oder Azure Storage gesendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Wie Sie Nachrichtenrouten erstellen, erfahren Sie unter [Verarbeiten von IoT Hub-D2C-Nachrichten mit Routen](tutorial-routing.md).

* [Senden von D2C-Nachrichten](quickstart-send-telemetry-node.md)

* Informationen zu den SDKs, die Sie zum Senden von D2C-Nachrichten verwenden können, finden Sie unter [Azure IoT-SDKs](iot-hub-devguide-sdks.md).
