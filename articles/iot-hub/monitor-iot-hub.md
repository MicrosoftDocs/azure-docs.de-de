---
title: Überwachen von Azure IoT Hub
description: Beginnen Sie hier, um zu erfahren, wie Sie Azure IoT Hub überwachen können.
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: db85594ad4fef37ec7411f7d5791fdb5279ec504
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581651"
---
# <a name="monitoring-azure-iot-hub"></a>Überwachen von Azure IoT Hub

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. In diesem Artikel wird das Überwachen von Daten beschrieben, die von Azure IoT Hub generiert wurden. Außerdem wird erläutert, wie Sie die Funktionen von Azure Monitor nutzen können, um diese Daten zu analysieren und Warnungen dafür zu erstellen.

## <a name="monitor-overview"></a>Azure Monitor: Übersicht

Die Seite **Übersicht** im Azure-Portal für jeden IoT-Hub enthält Diagramme, die einige Nutzungsmetriken bereitstellen, z. B. die Anzahl der verwendeten Nachrichten und die Anzahl der mit dem IoT-Hub verbundenen Geräte.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Standardmetrikdiagramme auf der IoT Hub-Seite „Übersicht“.":::

Hinweis: Der Wert für die Nachrichtenanzahl kann um eine Minute verzögert sein und beim Aktualisieren manchmal zwischen höheren und niedrigeren Werten schwanken. Dies hängt mit der Infrastruktur des IoT Hub-Diensts zusammen. Dieser Zähler sollte nur bei Werten aus der letzten Minute inkorrekt sein.

Die Informationen im Übersichtsbereich sind zwar praktisch, stellen aber nur einen kleinen Teil der Überwachungsdaten dar, die für einen IoT-Hub verfügbar sind. Einige Überwachungsdaten werden automatisch erfasst und sind zur Analyse verfügbar, sobald Sie Ihren IoT-Hub erstellen. Mit einigen Konfigurationsschritten können Sie zusätzliche Typen von Datensammlungen aktivieren.

## <a name="what-is-azure-monitor"></a>Was ist Azure Monitor?

Azure IoT Hub erstellt Überwachungsdaten mithilfe von [Azure Monitor](../azure-monitor/overview.md), einem umfassenden Stapelüberwachungsdienst in Azure, der einen vollständigen Satz von Funktionen zum Überwachen Ihrer Azure-Ressourcen sowie von Ressourcen in anderen Clouds und lokalen Umgebungen bereitstellt.

Beginnen Sie mit dem Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), in dem die folgenden Konzepte beschrieben werden:

- Was ist Azure Monitor?
- Kosten für die Überwachung
- In Azure gesammelte Überwachungsdaten
- Konfigurieren der Datensammlung
- Standardtools in Azure zum Analysieren von Überwachungsdaten sowie zum Generieren von Warnungen

Die folgenden Abschnitte basieren auf diesem Artikel. Darin werden die spezifischen von Azure IoT Hub erfassten Daten beschrieben, und es werden Beispiele für die Konfiguration der Datensammlung und die Analyse dieser Daten mit Azure-Tools bereitgestellt.

## <a name="monitoring-data"></a>Überwachungsdaten

Azure IoT Hub sammelt dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die in [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) beschrieben werden.

Ausführliche Informationen zu den Metriken und Protokollen, die von Azure IoT Hub erstellt werden, finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten](monitor-iot-hub-reference.md).

> [!IMPORTANT]
> Die vom IoT Hub-Dienst in den Ressourcenprotokollen von Azure Monitor ausgegebenen Ereignisse sind nicht unbedingt zuverlässig oder geordnet. Einige Ereignisse sind möglicherweise verloren gegangen oder nicht in der richtigen Reihenfolge. Ressourcenprotokolle sind auch nicht als Echtzeitprotokolle gedacht, und es kann mehrere Minuten dauern, bis die Ereignisse an dem von Ihnen gewählten Zielort protokolliert werden.

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.

Ressourcenprotokolle werden erst dann gesammelt und gespeichert, wenn Sie eine Diagnoseeinstellung erstellt und die Protokolle an mindestens einen Speicherort weitergeleitet haben.

Metriken und Protokolle können an verschiedene Speicherorte weitergeleitet werden. Hierzu zählen unter anderem folgende:
- Der Azure Monitor-Protokollspeicher über einen zugeordneten Log Analytics-Arbeitsbereich. Dort können sie mithilfe von Log Analytics analysiert werden.
- Azure Storage zur Archivierung und Offlineanalyse 
- Ein Event Hubs-Endpunkt, an dem sie von externen Anwendungen (beispielsweise SIEM-Tools von Drittanbietern) gelesen werden können.

Im Azure-Portal können Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Diagnoseeinstellungen** und dann **Diagnoseeinstellung hinzufügen** auswählen, um Diagnoseeinstellungen zu erstellen, die auf die von Ihrem IoT-Hub ausgegebenen Protokolle und Plattformmetriken ausgelegt sind.

Der folgende Screenshot zeigt eine Diagnoseeinstellung für die Weiterleitung des Ressourcenprotokolltyps *Verbindungsvorgänge* und aller Plattformmetriken an einen Log Analytics-Arbeitsbereich:

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Bereich „Diagnoseeinstellungen“ für einen IoT-Hub.":::

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/platform/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für Azure IoT Hub werden in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Ressourcenprotokolle“](monitor-iot-hub-reference.md#resource-logs) aufgeführt.

Beachten Sie beim Routing von IoT Hub-Plattformmetriken an andere Speicherorte:

- Die folgenden Plattformmetriken können über Diagnoseeinstellungen nicht exportiert werden: *Verbundene Geräte (Vorschau)* und *Geräte gesamt (Vorschau)* .

- Mehrdimensionale Metriken, z B. einige [Routingmetriken](monitor-iot-hub-reference.md#routing-metrics), werden zurzeit als vereinfachte eindimensionale Metriken exportiert, die über Dimensionswerte aggregiert werden. Weitere Informationen finden Sie unter [Exportieren von Plattformmetriken an andere Speicherorte](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können Metriken für Azure IoT Hub mit Metriken aus anderen Azure-Diensten mithilfe des Metrik-Explorers analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/platform/metrics-getting-started.md).

Im Azure-Portal können Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Metriken** zum Öffnen des Metrik-Explorers auswählen, der standardmäßig auf die von Ihrem IoT-Hub ausgegebenen Plattformmetriken ausgelegt ist:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Seite „Metrik-Explorer“ für einen IoT-Hub.":::

Eine Liste der Plattformmetriken, die für Azure IoT Hub gesammelt werden, finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Metriken“](monitor-iot-hub-reference.md#metrics). Eine Liste der Plattformmetriken, die für alle Azure-Dienste gesammelt werden, finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md).

Bei IoT Hub-Plattformmetriken, die in Anzahlen von Einheiten gesammelt werden, sind einige Aggregationen möglicherweise nicht verfügbar oder können nicht verwendet werden. Weitere Informationen finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Unterstützte Aggregationen“](monitor-iot-hub-reference.md#supported-aggregations).

Einige IoT Hub-Metriken, z. B. [Routingmetriken](monitor-iot-hub-reference.md#routing-metrics), sind mehrdimensional. Bei diesen Metriken können Sie [Filter](../azure-monitor/platform/metrics-charts.md#filters) anwenden und auf der Grundlage einer Dimension auf Ihre Diagramme [aufteilen](../azure-monitor/platform/metrics-charts.md#apply-splitting).

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat. Die Daten in diesen Tabellen sind einem Log Analytics-Arbeitsbereich zugeordnet und können in Log Analytics abgefragt werden. Weitere Informationen zu Azure Monitor-Protokollen finden Sie in der Azure Monitor-Dokumentation unter [Übersicht über Azure Monitor-Protokolle](../azure-monitor/platform/data-platform-logs.md). 

Zum Weiterleiten von Daten an Azure Monitor-Protokolle müssen Sie eine Diagnoseeinstellung erstellen, um Ressourcenprotokolle oder Plattformmetriken an einen Log Analytics-Arbeitsbereich zu senden. Weitere Informationen finden Sie unter [Sammlung und Routing](#collection-and-routing).

Im Azure-Portal können Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Protokolle** zum Durchführen von Log Analytics Abfragen auswählen, die standardmäßig auf die Protokolle und Metriken ausgelegt sind, die in Azure Monitor-Protokollen für Ihren IoT-Hub erfasst werden.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Seite „Protokolle“ für einen IoT-Hub.":::

Eine Liste der Tabellen, die von Azure Monitor-Protokollen verwendet und durch Log Analytics abgefragt werden können, finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Tabellen in Azure Monitor-Protokollen“](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird in [Azure Monitor-Ressourcenprotokollschema](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema) beschrieben. Sie finden das Schema und die Kategorien von Ressourcenprotokollen, die für Azure IoT Hub gesammelt werden, in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Ressourcenprotokolle“](monitor-iot-hub-reference.md#resource-logs).

Das [Aktivitätsprotokoll](../azure-monitor/platform/activity-log.md) ist ein Plattformprotokoll in Azure, das einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

Beachten Sie beim Routing von IoT Hub-Plattformmetriken an Azure Monitor-Protokolle:

- Die folgenden Plattformmetriken können über Diagnoseeinstellungen nicht exportiert werden: *Verbundene Geräte (Vorschau)* und *Geräte gesamt (Vorschau)* .

- Mehrdimensionale Metriken, z B. einige [Routingmetriken](monitor-iot-hub-reference.md#routing-metrics), werden zurzeit als vereinfachte eindimensionale Metriken exportiert, die über Dimensionswerte aggregiert werden. Weitere Informationen finden Sie unter [Exportieren von Plattformmetriken an andere Speicherorte](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Einige allgemeine Abfragen mit IoT Hub finden Sie unter [Kusto-Beispielabfragen](#sample-kusto-queries). Ausführliche Informationen zur Verwendung von Log Analytics-Abfragen finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>SDK-Version in IoT Hub-Protokollen

Einige Vorgänge in IoT Hub-Ressourcenprotokollen geben in ihrem `properties`-Objekt eine `sdkVersion`-Eigenschaft zurück. Wenn für diese Vorgänge ein Gerät oder eine Back-End-App eines der Azure IoT-SDKs verwendet wird, enthält diese Eigenschaft Informationen zum verwendeten SDK, die SDK-Version und die Plattform, auf der das SDK ausgeführt wird. Das folgende Beispiel zeigt die Eigenschaft `sdkVersion`, die bei Verwendung des Node.js-Geräte-SDKs für den Vorgang [`deviceConnect`](monitor-iot-hub-reference.md#connections) ausgegeben wird: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"`. Dieser Wert wird z. B. für das .NET SDK (C#) ausgegeben: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`.

Die folgende Tabelle zeigt den SDK-Namen für verschiedene Azure IoT SDKs:

| SDK-Name in der sdkVersion-Eigenschaft | Sprache |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.devices | .NET-Dienst-SDK (C#) |
| microsoft.azure.devices.client | .NET-Geräte-SDK (C#) |
| iothubclient | Geräte-SDK mit C oder Python v1 (veraltet) |
| iothubserviceclient | Dienst-SDK mit C oder Python v1 (veraltet) |
| azure-iot-device-iothub-py | Python-Geräte-SDK |
| azure-iot-device | Node.js-Geräte-SDK |
| azure-iothub | Node.js-Dienst-SDK |
| com.microsoft.azure.iothub-java-client | Java-Geräte-SDK |
| com.microsoft.azure.iothub.service.sdk | Java-Dienst-SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Java-Geräte-SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Java-Dienst-SDK |
| C | Embedded C |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

Sie können die Eigenschaft für die SDK-Version extrahieren, wenn Sie Abfragen für IoT Hub-Ressourcenprotokolle durchführen. Die folgende Abfrage beispielsweise extrahiert die Eigenschaft für die SDK-Version (und die Geräte-ID) aus den Eigenschaften, die von Verbindungsvorgängen zurückgegeben wurden. Diese beiden Eigenschaften werden zusammen mit dem Zeitpunkt des Vorgangs und der Ressourcen-ID des IoT-Hubs, mit dem das Gerät eine Verbindung herstellt, in die Ergebnisse geschrieben.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie im Menü von IoT Hub **Protokolle** auswählen, wird Log Analytics geöffnet, wobei der Abfragebereich auf den aktuellen IoT-Hub festgelegt ist. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage durchführen möchten, die Daten aus anderen IoT-Hubs oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md).

Die folgenden Abfragen sind Abfragen, mit denen Sie Ihren IoT-Hub überwachen können.

- Verbindungsfehler: Identifizieren Sie Geräteverbindungsfehler.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Drosselungsfehler: Identifizieren Sie Geräte, die die meisten Anforderungen ausgegeben haben, was zu Drosselungsfehlern führte.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Unzustellbare Endpunkte: Identifizieren Sie unzustellbare oder fehlerhafte Endpunkte anhand der Häufigkeit, mit der das Problem gemeldet wurde, sowie den Grund dafür.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Fehlerzusammenfassung: Anzahl von Fehlern über alle Vorgänge nach Typ.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Zuletzt verbundene Geräte: Liste der Geräte, die im angegebenen Zeitraum mit IoT Hub verbunden wurden.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- SDK-Version von Geräten: Liste der Geräte und ihrer SDK-Versionen für Geräteverbindungen oder Gerät-zu-Cloud-Zwillingsvorgängen.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

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

## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/platform/alerts-metric-overview.md), [Protokolle](../azure-monitor/platform/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/platform/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben Vor- und Nachteile.

Beachten Sie Folgendes beim Erstellen einer Warnungsregel, die auf Plattformmetriken basiert: Bei IoT Hub-Plattformmetriken, die in Anzahlen von Einheiten gesammelt werden, sind einige Aggregationen möglicherweise nicht verfügbar oder können nicht verwendet werden. Weitere Informationen finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten im Abschnitt „Unterstützte Aggregationen“](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Überwachen der Verbindungstrennungen pro Gerät mithilfe von Event Grid

Azure Monitor bietet die Metrik *Verbundene Geräte*, mit der Sie die Anzahl der mit Ihrem IoT-Hub verbundenen Geräte überwachen und eine Warnung auslösen können, wenn die Anzahl der verbundenen Geräte unter einen Schwellenwert fällt. Während dies in einigen Szenarien ausreichend sein mag, bietet [Azure Event Grid](../event-grid/index.yml) eine Überwachungslösung mit geringer Latenz pro Gerät, die Sie zum Nachverfolgen von Geräteverbindungen bei kritischen Geräten und einer kritischen Infrastruktur verwenden können.

Mit Event Grid können Sie die [Ereignisse **DeviceConnected** und **DeviceDisconnected**](iot-hub-event-grid.md#event-types) in IoT Hub abonnieren, um Warnungen auszulösen und den Geräteverbindungsstatus zu überwachen. Event Grid bietet eine viel geringere Ereignislatenz als Azure Monitor, und Sie können die Überwachung pro Gerät statt für die Gesamtanzahl verbundener Geräte durchführen. Diese Faktoren machen Event Grid zur bevorzugten Methode für die Überwachung von Verbindungen von kritischen Geräten und einer kritischen Infrastruktur. Es wird dringend empfohlen, Event Grid zur Überwachung von Geräteverbindungen in Produktionsumgebungen zu verwenden.

Weitere ausführliche Informationen zum Überwachen von Geräteverbindungen mithilfe von Event Grid und Azure Monitor finden Sie unter [Überwachen, Diagnostizieren und Behandeln von Problemen bei der Trennung von Geräteverbindungen mit Azure IoT Hub](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von [Dienstname] erstellt werden, finden Sie in der [Referenz zu Azure IoT Hub-Überwachungsdaten](monitor-iot-hub-reference.md).

- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
