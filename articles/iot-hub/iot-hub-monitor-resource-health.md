---
title: Überwachen der Integrität von Azure IoT Hub | Microsoft-Dokumentation
description: Verwenden Sie Azure Monitor und Azure Resource Health zur schnellen Überwachung Ihres IoT Hubs und Diagnose von Problemen.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 6dea1add1e329cfc894068732898a856a69c9b4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166210"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Schnelle Überwachung der Integrität von Azure IoT Hub und Diagnose von Problemen

Unternehmen, die Azure IoT Hub implementieren, erwarten zuverlässige Leistung von ihren Ressourcen. Damit Sie Ihre Vorgänge immer im Blick haben, ist IoT Hub vollständig in [Azure Monitor](../azure-monitor/index.yml) und [Azure Resource Health](../service-health/resource-health-overview.md) integriert. Diese beiden Dienste liefern Ihnen die Daten, die Sie benötigen, um Ihre IoT-Lösungen in Betrieb und einem ordnungsgemäßen Zustand zu halten.

Azure Monitor ist eine einzelne Überwachungs- und Protokollierungsquelle für alle Azure-Dienste. Sie können die Diagnoseprotokolle, die Azure Monitor generiert, zur benutzerdefinierten Verarbeitung an Azure Monitor-Protokolle, Event Hubs oder Azure Storage senden. Die Metriken und Diagnoseeinstellungen von Azure Monitor liefern Ihnen Einblicke in die Leistung Ihrer Ressourcen. Um mehr über das [Verwenden von Azure Monitor](#use-azure-monitor) mit Ihrem IoT Hub zu erfahren, lesen Sie nun diesen Artikel. 

> [!IMPORTANT]
> Die vom IoT Hub-Dienst in den Diagnoseprotokollen von Azure Monitor ausgegebenen Ereignisse sind nicht unbedingt zuverlässig oder geordnet. Einige Ereignisse sind möglicherweise verloren gegangen oder nicht in der richtigen Reihenfolge. Diagnoseprotokolle sind auch nicht als Echtzeitprotokolle gedacht, und es kann einige Minuten dauern, bis die Ereignisse an dem von Ihnen gewählten Zielort protokolliert werden.

Azure Resource Health unterstützt Sie bei der Diagnose und bei Supportanfragen, wenn ein Problem mit Azure Auswirkungen auf Ihre Ressourcen hat. Ein Dashboard zeigt aktuelle und vergangene Integritätsstatus für jeden Ihrer IoT Hubs. Um mehr über das [Verwenden von Azure Resource Health](#use-azure-resource-health) mit Ihrem IoT Hub zu erfahren, fahren Sie mit dem Abschnitt am Ende dieses Artikels fort. 

IoT Hub bietet auch eigene Metriken, die Sie verwenden können, um den Status Ihrer IoT-Ressourcen zu verstehen. Weitere Informationen finden Sie unter [Grundlegendes zu IoT Hub-Metriken](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Verwenden von Azure Monitor

Azure Monitor stellt Diagnoseinformationen für Azure-Ressourcen bereit, was bedeutet, dass Sie Vorgänge überwachen können, die in Ihrem IoT-Hub ausgeführt werden.

Die Diagnoseeinstellungen von Azure Monitor ersetzen die IoT Hub-Vorgangsüberwachung. Wenn Sie derzeit die Vorgangsüberwachung verwenden, sollten Sie Ihre Workflows migrieren. Weitere Informationen finden Sie unter [Migrieren Ihres IoT Hub von der Vorgangsüberwachung zu Diagnoseeinstellungen](iot-hub-migrate-to-diagnostics-settings.md).

Weitere Informationen zu den bestimmten Metriken und Ereignissen, die Azure Monitor überwacht, finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md) und [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Grundlegendes zu den Protokollen

Azure Monitor verfolgt verschiedene Vorgänge nach, die in IoT Hub auftreten. Jede Kategorie verfügt über ein Schema, das definiert, wie Ereignisse in dieser Kategorie gemeldet werden.

#### <a name="connections"></a>Verbindungen

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
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>C2D-Befehle

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

#### <a name="device-identity-operations"></a>Geräte-Identitätsvorgänge

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

#### <a name="routes"></a>Routen

Die Kategorie „Nachrichtenweiterleitung“ verfolgt Fehler, die während der Auswertung der Nachrichtenweiterleitung auftreten, und die durch IoT Hub wahrgenommene Endpunktintegrität. Diese Kategorie umfasst z. B. folgende Ereignisse:

* Eine Regel wird zu „nicht definiert“ ausgewertet,
* IoT Hub markiert einen Endpunkt für unzustellbare Nachrichten, oder
* Alle Fehler, die von einem Endpunkt empfangen werden. 

Diese Kategorie umfasst keine bestimmten Fehler zu den Nachrichten selbst (z. B. Gerätedrosselungsfehler), die in der Kategorie „Gerätetelemetrie“ gemeldet werden.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Gerätetelemetrie

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

#### <a name="file-upload-operations"></a>Dateiuploadvorgänge

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

#### <a name="cloud-to-device-twin-operations"></a>Cloud-zu-Gerät-Zwillingsvorgänge

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

#### <a name="device-to-cloud-twin-operations"></a>Gerät-zu-Cloud-Zwillingsvorgänge

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

#### <a name="twin-queries"></a>Zwillingsabfragen

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

#### <a name="jobs-operations"></a>Auftragsvorgänge

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

#### <a name="direct-methods"></a>Direkte Methoden

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

#### <a name="distributed-tracing-preview"></a>Verteilte Ablaufverfolgung (Vorschauversion)

Die Kategorie für verteilte Ablaufverfolgung verfolgt die Korrelations-IDs für Nachrichten nach, die den Kontextheader für Ablaufverfolgung enthalten. Zur vollständigen Aktivierung dieser Protokolle muss der clientseitige Code gemäß der Anleitung in [Analyze and diagnose IoT applications end-to-end with IoT Hub distributed tracing (preview)](iot-hub-distributed-tracing.md) (End-to-End-Analyse und -Diagnose von IoT-Anwendungen mit der verteilten Ablaufverfolgung von IoT Hub (Vorschauversion)) aktualisiert werden.

Hinweis: `correlationId` entspricht dem [W3C-Vorschlag für Ablaufverfolgungskontext](https://github.com/w3c/trace-context) insofern, dass sowohl ein `trace-id`- als auch ein `span-id`-Element enthalten ist.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub-D2C-Protokolle (Gerät-zu-Cloud)

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

| Eigenschaft | Typ | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Die Größe einer Gerät-zu-Cloud-Nachricht in Bytes |
| **deviceId** | Zeichenfolge aus alphanumerischen 7-Bit-ASCII-Zeichen | Die Identität des Geräts |
| **callerLocalTimeUtc** | UTC-Zeitstempel | Der Erstellungszeitpunkt der Nachricht wie von der Uhr des lokalen Geräts gemeldet |
| **calleeLocalTimeUtc** | UTC-Zeitstempel | Der Eingangszeitpunkt der Nachricht beim IoT Hub Gateway wie von der Uhr des IoT Hub-Diensts gemeldet |

##### <a name="iot-hub-ingress-logs"></a>Eingehende IoT Hub-Protokolle

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

| Eigenschaft | Typ | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | string | Entweder „true“ oder „false“; gibt an, ob Nachrichtenweiterleitung in IoT Hub aktiviert ist |
| **parentSpanId** | string | Das [span-id](https://w3c.github.io/trace-context/#parent-id)-Element der übergeordneten Nachricht, in diesem Fall die D2C-Nachrichtenablaufverfolgung |

##### <a name="iot-hub-egress-logs"></a>Ausgehende IoT Hub-Protokolle

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

| Eigenschaft | Typ | BESCHREIBUNG |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | string | Der Name des Routingendpunkts |
| **endpointType** | string | Der Typ des Routingendpunkts |
| **parentSpanId** | string | Das [span-id](https://w3c.github.io/trace-context/#parent-id)-Element der übergeordneten Nachricht, in diesem Fall die Ablaufverfolgung des IoT Hub-Nachrichteneingangs |

### <a name="read-logs-from-azure-event-hubs"></a>Lesen von Protokollen aus Azure Event Hubs

Nachdem Sie die Ereignisprotokollierung über Diagnoseeinstellungen eingerichtet haben, können Sie Anwendungen erstellen, die die Protokolle lesen, sodass Sie anhand der darin enthaltenen Informationen Maßnahmen ergreifen können. Dieser Beispielcode ruft Protokolle von einem Event Hub ab:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Verwenden von Azure Resource Health

Verwenden Sie Azure Resource Health, um zu überwachen, ob Ihr IoT Hub ausgeführt wird. Sie können auch erfahren, ob ein regionaler Ausfall Auswirkungen auf die Integrität Ihres IoT Hub hat. Um bestimmte Details des Integritätsstatus Ihres Azure IoT Hub zu verstehen, empfehlen wir Ihnen das [Verwenden von Azure Monitor](#use-azure-monitor).

Azure IoT Hub zeigt die Integrität auf regionaler Ebene an. Wenn ein regionaler Ausfall Auswirkungen auf Ihren IoT-Hub hat, wird der Integritätsstatus **Unbekannt** angezeigt. Weitere Informationen finden Sie unter [Ressourcentypen und Integritätsprüfungen in Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Um die Integrität Ihrer IoT Hubs zu überprüfen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu **Dienstintegrität** > **Ressourcenintegrität**.

3. Wählen Sie in den Dropdownlisten Ihr Abonnement und dann **IoT Hub** als Ressourcentyp aus.

Weitere Informationen zum Interpretieren von Integritätsdaten finden Sie unter [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu IoT Hub-Metriken](iot-hub-metrics.md)
* [IoT-Remoteüberwachung und -Benachrichtigungen mit Azure Logic Apps zum Herstellen einer Verbindung zwischen Ihrem IoT Hub und Postfach](iot-hub-monitoring-notifications-with-azure-logic-apps.md)