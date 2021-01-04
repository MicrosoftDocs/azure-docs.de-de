---
title: Einrichten der Diagnoseprotokolle – Azure Event Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Aktivitäts- und Diagnoseprotokolle für Event Hubs in Azure einrichten.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: a7230746dc4225b04b0507c872416368aa14442b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912598"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Einrichten von Diagnoseprotokollen für Azure Event Hubs

Sie können zwei Typen von Protokollen für Azure Event Hubs anzeigen:

* **[Aktivitätsprotokolle](../azure-monitor/platform/platform-logs-overview.md)** : Diese Protokolle enthalten Informationen zu Vorgängen, die für einen Auftrag ausgeführt werden. Diese Protokolle sind immer aktiviert. Aktivitätsprotokolleinträge können angezeigt werden, indem Sie **Aktivitätsprotokoll** im linken Bereich für Ihren Event Hub-Namespace im Azure-Portal auswählen. Beispiel: „Namespace erstellen oder aktualisieren“, „Event Hub erstellen oder aktualisieren“.

    ![Aktivitätsprotokoll für einen Event Hubs-Namespace](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnoseprotokolle](../azure-monitor/platform/platform-logs-overview.md)** : Diagnoseprotokolle bieten ausführlichere Informationen zu Vorgängen und Aktionen, die für Ihren Namespace mithilfe der API oder über Verwaltungsclients im Sprach-SDK ausgeführt werden. 
    
    Im folgenden Abschnitt erfahren Sie, wie Sie Diagnoseprotokolle für einen Event Hubs-Namespace aktivieren.

## <a name="enable-diagnostic-logs"></a>Aktivieren von Diagnoseprotokollen
Diagnoseprotokolle sind standardmäßig deaktiviert. Führen Sie folgende Schritte aus, um die Diagnoseprotokolle zu aktivieren:

1.  Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Event Hubs-Namespace. 
2. Wählen Sie im linken Bereich **Diagnoseeinstellungen** unter **Überwachung** aus, und wählen Sie dann **+ Diagnoseeinstellung hinzufügen** aus. 

    ![Seite „Diagnoseeinstellungen“: „Diagnoseeinstellung hinzufügen“](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Wählen Sie im Abschnitt **Kategoriedetails** die **Typen von Diagnoseprotokollen** aus, die Sie aktivieren möchten. Weitere Informationen zu diesen Kategorien finden Sie weiter unten in diesem Artikel. 
5. Legen Sie im Abschnitt **Zieldetails** das gewünschte Archivierungsziel (Ziel) fest, beispielsweise ein Speicherkonto, einen Event Hub oder einen Log Analytics-Arbeitsbereich.

    ![Seite „Diagnoseeinstellungen hinzufügen“](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Wählen Sie auf der Symbolleiste **Speichern** aus, um die Diagnoseeinstellungen zu speichern.

    Neue Einstellungen werden in etwa zehn Minuten wirksam. Danach werden die Protokolle im gewünschten Archivierungsziel im Bereich **Diagnoseprotokolle** angezeigt.

    Weitere Informationen zum Konfigurieren der Diagnose finden Sie in der [Übersicht über Azure-Diagnoseprotokolle](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Diagnoseprotokollkategorien

Event Hubs erfasst Diagnoseprotokolle für die folgenden Kategorien:

| Category | BESCHREIBUNG | 
| -------- | ----------- | 
| Archivprotokolle | Erfasst Informationen zu [Event Hubs Capture](event-hubs-capture-overview.md)-Vorgängen, insbesondere Protokolle zu Erfassungsfehlern. |
| Betriebsprotokolle | Erfassen alle Verwaltungsvorgänge, die für den Azure Event Hubs-Namespace ausgeführt werden. Datenvorgänge werden aufgrund der hohen Menge an Datenvorgängen, die für Azure Event Hubs durchgeführt werden, nicht erfasst. |
| Protokolle zur automatischen Skalierung | Erfassen automatische Vergrößerungsvorgänge, die für einen Event Hubs-Namespace ausgeführt wurden. |
| Kafka-Koordinatorprotokolle | Erfassen Kafka-Koordinatorvorgänge, die sich auf Event Hubs beziehen. |
| Kafka-Benutzerfehlerprotokolle | Erfassen Informationen zu Kafka-APIs, die für Event Hubs aufgerufen werden. |
| Event Hubs-VNET (Virtual Network)-Verbindungsereignis | Erfasst Informationen zu IP-Adressen und virtuellen Netzwerken, die Datenverkehr an Event Hubs senden. |
| Benutzerprotokolle für kundenseitig verwaltete Schlüssel | Erfassen Vorgänge in Verbindung mit Schlüsseln, die kundenseitig verwaltet werden. |


Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im nachfolgend beschriebenen Format.

## <a name="archive-logs-schema"></a>Archivprotokollschema

JSON-Zeichenfolgen im Archivprotokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

Name | BESCHREIBUNG
------- | -------
`TaskName` | Beschreibung der fehlgeschlagenen Aufgabe
`ActivityId` | Interne ID zur Nachverfolgung
`trackingId` | Interne ID zur Nachverfolgung
`resourceId` | Azure Resource Manager-Ressourcen-ID
`eventHub` | Vollständiger Event Hub-Name (mit Namespace-Name)
`partitionId` | Event Hub-Partition, auf die geschrieben wird
`archiveStep` | Mögliche Werte: ArchiveFlushWriter, DestinationInit
`startTime` | Fehlerstartzeit
`failures` | Häufigkeit des Fehlers
`durationInSeconds` | Dauer des Fehlers
`message` | Fehlermeldung
`category` | ArchiveLogs

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Archivierungsprotokoll:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Schema „Betriebsprotokolle“

JSON-Zeichenfolgen im Betriebsprotokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

Name | BESCHREIBUNG
------- | -------
`ActivityId` | Interne ID zur Nachverfolgung |
`EventName` | Vorgangsname |
`resourceId` | Azure Resource Manager-Ressourcen-ID |
`SubscriptionId` | Abonnement-ID |
`EventTimeString` | Vorgangsdauer |
`EventProperties` | Vorgangseigenschaften |
`Status` | Vorgangsstatus |
`Caller` | Aufrufer des Vorgangs (Azure-Portal oder Verwaltungsclient) |
`Category` | OperationalLogs |

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Betriebsprotokoll:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Schema der Protokolle für automatische Skalierung
Die JSON-Zeichenfolge im Protokoll für automatische Skalierung enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `TrackingId` | Interne ID, die für Ablaufverfolgungszwecke verwendet wird. |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Message` | Informationsmeldung, die Details zur Aktion der automatischen Vergrößerung enthält. Die Meldung enthält den vorherigen und den aktuellen Wert der Durchsatzeinheit für einen bestimmten Namespace und den Grund, warum die Vergrößerung der Durchsatzeinheit ausgelöst wurde. |

Beispiel für ein Autoskalierungsereignis: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Schema der Kafka-Koordinatorprotokolle
Die JSON-Zeichenfolge im Kafka-Koordinatorprotokoll enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `RequestId` | Anforderungs-ID, die zur Ablaufverfolgung verwendet wird |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Operation` | Der Name des Vorgangs, der während der Gruppenkoordination ausgeführt wird. |
| `ClientId` | Client-ID |
| `NamespaceName` | Namespacename | 
| `SubscriptionId` | Azure-Abonnement-ID |
| `Message` | Informative Meldung oder Warnmeldung, die Details zu den Aktionen bereitstellt, die während der Gruppenkoordination ausgeführt wurden. |

### <a name="example"></a>Beispiel

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schema der Kafka-Benutzerfehlerprotokolle
Die JSON-Zeichenfolge im Kafka-Benutzerfehlerprotokoll enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- |
| `TrackingId` | Nachverfolgungs-ID, die zur Ablaufverfolgung verwendet wird. |
| `NamespaceName` | Namespacename |
| `Eventhub` | Event Hub-Name |
| `PartitionId` | Partitions-ID |
| `GroupId` | Gruppen-ID |
| `ClientId` | Client-ID |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Message` | Informationsmeldung, die Details zu einem Fehler enthält. |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Schema für Event Hubs-Verbindungsereignis mit virtuellem Netzwerk
Die JSON-Zeichenfolge für das Event Hubs-Verbindungsereignis mit virtuellem Netzwerk enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---  | ----------- | 
| `SubscriptionId` | Azure-Abonnement-ID |
| `NamespaceName` | Namespacename |
| `IPAddress` | IP-Adresse eines Clients, der eine Verbindung mit dem Event Hubs-Dienst herstellt. |
| `Action` | Aktion, die vom Event Hubs-Dienst beim Auswerten von Verbindungsanforderungen ausgeführt wird. Unterstützt werden die Aktionen zum **Akzeptieren von Verbindungen** und **Ablehnen von Verbindungen**. |
| `Reason` | Gibt einen Grund an, warum die Aktion durchgeführt wurde. |
| `Count` | Anzahl von Vorkommen für die angegebene Aktion. |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |

Protokolle virtueller Netzwerke werden nur dann generiert, wenn der Namespace Zugriff aus **ausgewählten Netzwerken** oder über **spezifische IP-Adressen** (IP-Filterregeln) erlaubt. Wenn Sie den Zugriff auf Ihren Namespace mit diesen Features nicht einschränken möchten und dennoch Protokolle virtueller Netzwerke erhalten möchten, um IP-Adressen von Clients zu verfolgen, die sich mit dem Namespace der Event Hubs verbinden, können Sie die folgende Umgehungslösung verwenden. Aktivieren Sie die IP-Filterung, und fügen Sie den gesamten adressierbaren IPv4-Bereich (1.0.0.0/1 bis 255.0.0.0/1) hinzu. Event Hubs unterstützt keine IPv6-Adressbereiche. 

### <a name="example"></a>Beispiel

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Benutzerprotokolle für kundenseitig verwaltete Schlüssel
Die JSON-Zeichenfolge im Benutzerprotokoll für kundenseitig verwaltete Schlüssel enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `Category` | Typ der Kategorie für eine Meldung. Es handelt sich um einen der folgenden Werte: **error** oder **info** |
| `ResourceId` | Interne Ressourcen-ID, die die Azure-Abonnement-ID und den Namespacenamen umfasst. |
| `KeyVault` | Name der Key Vault-Ressource. |
| `Key` | Name des Key Vault-Schlüssels. |
| `Version` | Version des Key Vault-Schlüssels. |
| `Operation` | Der Name eines Vorgangs, der zum Verarbeiten von Anforderungen ausgeführt wird. |
| `Code` | Statuscode |
| `Message` | Meldung, die Details zu einem Fehler oder einer Informationsmeldung enthält. |



## <a name="next-steps"></a>Nächste Schritte
- [Einführung in Event Hubs](./event-hubs-about.md)
- [Event Hubs-Beispiele](sdks.md)
- Erste Schritte mit Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
