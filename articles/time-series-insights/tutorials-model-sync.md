---
title: Modellsynchronisierung zwischen Azure Digital Twins und Time Series Insights Gen2 | Microsoft-Dokumentation
description: Bewährte Methoden und Tools, die zum Übersetzen des Objektmodells in ADT in das Objektmodell in Azure TSI verwendet werden.
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: 0d02a6e3eb2aef4a02c90360b2016e64af579081
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014729"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Modellsynchronisierung zwischen Azure Digital Twins und Time Series Insights Gen2

In diesem Artikel werden bewährte Methoden und Tools erläutert, die verwendet werden, um das Objektmodell in Azure Digital Twins (ADT) in das Objektmodell in Azure Time Series Insights (TSI) zu übersetzen.  Dieser Artikel ist der zweite Teil einer zweiteiligen Tutorialreihe, die die Integration von Azure Digital Twins in Azure Time Series Insights erläutert. Die Integration von Azure Digital Twins in Time Series Insights ermöglicht die Archivierung und Nachverfolgung des Verlaufs von Telemetriedaten und berechneten Eigenschaften von Digital Twins. Diese Tutorialreihe richtet sich an Entwickler, die an der Integration von Time Series Insights in Azure Digital Twins arbeiten. In Teil 1 wird das [Erstellen einer Datenpipeline, die die eigentlichen Zeitreihendaten aus Azure Digital Twins in Time Series Insights einliest](../digital-twins/how-to-integrate-time-series-insights.md), erläutert, und in diesem zweiten Teil der Tutorialreihe wird die Objektmodellsynchronisierung zwischen Azure Digital Twins und Time Series Insights erläutert. In diesem Tutorial werden die bewährten Methoden zum Auswählen und Festlegen der Benennungskonvention für die Zeitreihen-ID (Time Series ID, TS ID) und das manuelle Einrichten von Hierarchien im Zeitreihenmodell (Time Series Model, TSM) erläutert.

## <a name="choosing-a-time-series-id"></a>Auswählen einer Zeitreihen-ID

Die Zeitreihen-ID ist ein eindeutiger Bezeichner zur Identifizierung von Objekten in Time Series Insights. Zeitreihendaten (Telemetriedaten aus dem Feld, bei denen es sich um Zeit-Wert-Paare handelt) werden mithilfe der unter TSID aufgeführten Variablen dargestellt. In Azure Digital Twins werden Zwillingseigenschaften und -telemetriedaten verwendet, um den Zustand eines Zwillings sowie die vom jeweiligen Zwilling erzeugten Messungen darzustellen. Gemäß dem aktuellen Design des TSM müssen TS IDs eindeutig sein. Durch die Verwendung von Zwillings-IDs der Zwillinge in Azure Digital Twins oder eine Kombination mit einem Eigenschafts- oder Telemetrienamen wird eine TS ID im TSM immer eindeutig sein. In einem typischen Fall ist die **_`<Twin ID>`_** mit der TS ID identisch, und die Eigenschafts- und Telemetrienamen sind die Variablen in TSM. Es gibt jedoch Anwendungsfälle, in denen es bevorzugt wird, dass Ressourcenhierarchien in Time Series Insights mit einem zusammengesetzten Schlüsselformat vereinfacht werden, z. B. **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_**. Erläutern wir den letzten Fall anhand eines Beispiels. Stellen Sie sich einen Raum in einem Gebäude vor, der als Zwilling modelliert ist und die Zwillings-ID „Room22“ besitzt. Seine Temperatureinstellungseigenschaft muss als **_TSID Room22_TempSetting_** übersetzt werden, und die Temperaturmessung muss in **_Room22_TempMea_** in TSM übersetzt werden.

[![Auswählen einer Zeitreihen-ID](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Kontextualisierung von Zeitreihen

Die Kontextualisierung von Daten (größtenteils räumlicher Natur) in Time Series Insights wird durch Objekthierarchien erreicht, die auch für die einfache Navigation in Daten durch eine Strukturansicht im Time Series Insights-Explorer verwendet wird. Zeitreihentypen und -hierarchien werden mithilfe des Zeitreihenmodells (TSM) in Time Series Insights definiert. Typen im TSM helfen beim Definieren von Variablen, während Hierarchieebenen und Instanzfeldwerte verwendet werden, um die Strukturansicht im Time Series Insights-Explorer zu erstellen. Weitere Informationen zum TSM finden Sie in der [Onlinedokumentation zu Time Series Insights](./concepts-model-overview.md).

In Azure Digital Twins wird die Verbindung zwischen Objekten mithilfe von Zwillingsbeziehungen ausgedrückt. Zwillingsbeziehungen sind schlicht ein Diagramm verbundener Objekte. In Time Series Insight sind Beziehungen zwischen Objekten jedoch hierarchisch organisiert. Das heißt, dass die Beziehungen zwischen Objekten vom Typ „über-/untergeordnet“ sind, die mithilfe einer Baumstruktur dargestellt werden. Um Beziehungsinformationen von Azure Digital Twins in Time Series Insights-Hierarchien zu übersetzen, müssen wir relevante hierarchische Beziehungen aus Azure Digital Twins auswählen. Azure Digital Twins verwendet eine offene Standard, eine Modellierungssprache namens „Digital Twin Definition Language (DTDL)“. In DTDL werden Modelle mithilfe einer Variante von JSON namens „JSON-LD“ beschrieben. Die vollständigen Informationen zu der Spezifikation finden Sie in der [DTDL-Dokumentation](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

[![Verbindung zwischen Objekten](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Übersetzen der Diagrammdarstellung in Azure Digital Twins in eine Baumstruktur in Time Series Insights

In den folgenden Abschnitten des Tutorials werden einige grundlegende Szenarios zum manuellen übersetzen der Diagrammstruktur in Azure Digital Twins in eine Baumstruktur in Time Series Insights behandelt.

Beispielsystem: In diesem Tutorial werden anhand des folgenden Beispiels die unten besprochenen Konzepte erläutert. Dabei handelt es sich um ein einfaches, fiktives Gebäudeverwaltungssystem mit einem Stockwerk und zwei Räumen. Es verfügt über drei Thermostate, einen in jedem der Räume und einen gemeinsamen für das gesamte Stockwerk. Außerdem verfügt es über eine Wasseruhr, die den Wasserdurchfluss von Room21 zu Room22 durch eine Rohrverbindung zwischen den Räumen misst. Wenn man die räumliche Beziehung zwischen Zwillingen betrachtet, sind beide Beziehungstypen vorhanden.

1. Die gängigste ist die hierarchische Beziehung. Zum Beispiel: Building40 -> Floor01 -> FloorTS* -> Temperature
1. Nicht so gängig, die kreisförmige Beziehung. Zum Beispiel kann, beginnend mit Building40 das FlowMtr über zwei verschiedene Pfade verfolgt werden.

   1. Building40 -> Floor01 -> Room21 -> FlowMtr* -> Flow
   1. Building40 -> Floor01 -> Room22 -> FlowMtr* -> Flow  
      Dabei ist „Flow“ die tatsächliche Telemetriemessung, nämlich der Wasserdurchfluss zwischen Room21 und Room22.

> [!Note]
>
> `*` FloorTS steht für „FloorThermoStat“ und FlowMtr steht für „Flow Meter“ und wird typischerweise als TS ID ausgewählt. „Temperature“ und „Flow“ sind die rohen Telemetriedaten, auf die in Time Series Insights als Variablen verwiesen wird.

Angesichts der aktuellen Einschränkung in Time Series Insights, dass ein Objekt nicht in mehreren Verzweigungen dargestellt werden kann, wird in den folgenden Abschnitten die Modellierung hierarchischer und kreisförmiger Beziehungen in Time Series Insights erläutert.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Fall 1: Hierarchische (über-/untergeordnete) Beziehung

Es handelt sich um den gängigsten Beziehungstyp zwischen den Zwillingen. Die Modellierung reiner über-/untergeordneter Beziehungen wird in der folgenden Abbildung erläutert. Instanzfelder und TSID werden von Zwillings-IDs abgeleitet, wie unten gezeigt. Zwar können Instanzfelder manuell mit dem Time Series Insights-Explorer aktualisiert werden, doch im folgenden Abschnitt („Aktualisieren von Instanzfeldern mithilfe von APIs“) wird das Lauschen auf Modelländerungen in Azure Digital Twins und das Aktualisieren von Instanzfeldern in Time Series Insights mithilfe von Azure Functions erläutert.

[![Zuordnen von Zwillings-IDs](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Zuordnen von Zwillings-IDs 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Fall 2: Kreisförmige Beziehung

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Kreisförmige Beziehung in Azure Digital Twins zu einer Beziehung mit einer einzelnen Hierarchie in Time Series Insights

Unter der Voraussetzung, dass die TS ID eindeutig sein muss und nur in einer Hierarchie dargestellt werden kann, stellt dieser Fall das _‚FlowMtr‘_ mit einer Telemetrie namens _‚Flow‘_ direkt unter _‚Room21‘_ dar. In Zukunft, wenn Time Series Insights die mehrfache Darstellung von Zeitreihen im TSM unterstützen kann, wird die Telemetrie _‚Flow‘_ unter _‚Room21‘_ und _‚Room22‘_ dargestellt.

Der folgende Screenshot zeigt das manuelle Zuordnen von Zwillings-IDs in Azure Digital Twins zum Instanzfeld im TM und die resultierende Hierarchie in Time Series Insights.

[![Zuordnen von Zwillings-IDs in Azure Digital Twins](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Kreisförmige Beziehung in Azure Digital Twins zu mehreren Hierarchien in Time Series Insights mittels Duplikaten

In Teil 1 des Tutorials wird erläutert, wie ein Clientprogramm (eine Azure-Funktion) bei der Übertragung von Telemetriedaten aus IoT Hub oder anderen Ereignisquellen an Azure Digital Twins hilft. Dieser Ansatz schlägt vor, dasselbe Clientprogramm zu verwenden, um relevante Eigenschaften der übergeordneten Zwillinge zu aktualisieren. Im vorliegenden Beispiel kann das Programm während des Lesens der FlowMtr-Telemetrie aus IoT Hub und beim Aktualisieren der Eigenschaft „Flow“ im FlowMtr-Zwilling ebenfalls entsprechende Eigenschaften in allen möglichen übergeordneten Zwillingen der Quelle aktualisieren. In unserem Beispiel wären das die „outflowmea“-Eigenschaft (identifiziert mit der ‚outflow‘-Beziehung) von Room21 und die „inflowmea“-Eigenschaft von Room22.  Der folgende Screenshot zeigt die endgültige Benutzererfahrung im Time Series Insights-Explorer. Dabei muss beachtet werden, dass wir bei dieser Vorgehensweise Datenduplikate haben.

[![Time Series Insights-Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Der folgende Codeausschnitt zeigt, wie die Clientanwendung mithilfe von Azure Digital Twins-APIs in der Zwillingsbeziehung navigieren konnte.

> [!Note]
>
> In diesem Codeausschnittbeispiel wird vorausgesetzt, dass die Leser mit [Teil 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) des Tutorials vertraut sind und dass diese Codeänderung in der Funktion „ProcessHubToDTEvents“ vorgenommen wurde.

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Aktualisieren von Instanzfeldern mithilfe von APIs

In diesem Abschnitt des Tutorials wird das Lauschen auf Modelländerungen in Azure Digital Twins erläutert, z. B. das Erstellen bzw. Löschen von Zwillingen oder das Ändern von Beziehungen zwischen Zwillingen und das programmgesteuerte Aktualisieren von Instanzfeldern und -hierarchien mithilfe von Time Series Insights-Modell-APIs. Diese Methode zum Aktualisieren des Time Series Insights-Modells wird in der Regel über Azure Functions erzielt. In Azure Digital Twins können Ereignisbenachrichtigungen wie das Hinzufügen oder Löschen von Zwillingen an Downstreamdienste weitergeleitet werden, z. B. Event Hubs, die wiederum an Azure Functions gesendet werden können. Weitere Informationen zum Routing und Filtern von Ereignissen werden [hier](../digital-twins/how-to-manage-routes-portal.md) erläutert.  In Rest dieses Abschnitts wird erläutert, wie Sie Time Series Insights-Modell-APIs in Azure Functions verwenden, um das Time Series Insights-Modell als Reaktion auf die Hinzufügung eines Zwillings (eine Art von Modelländerung) in Azure Digital Twins zu aktualisieren.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Empfangen und Identifizieren der Ereignisbenachrichtigung beim Hinzufügen eines Zwillings

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Erstellen des Time Series Insights-Clients und Hinzufügen von Instanzdetails

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Anwenden von Hierarchieinformationen auf die Instanz

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Nächste Schritte

Als Drittes in der Reihe von Tutorials steht an, die Vorgehensweise zum Abfragen von Verlaufsdaten aus Azure Digital Twins mithilfe von Time Series Insights-APIs zu zeigen. Es handelt sich um einen in Arbeit befindlichen Abschnitt, der bei Fertigstellung aktualisiert wird. In der Zwischenzeit wird empfohlen, die [Dokumentation zur Time Series Insights-Datenabfrage-API](./concepts-query-overview.md) zu lesen.