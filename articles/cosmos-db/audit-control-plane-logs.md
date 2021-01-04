---
title: Überwachen von Azure Cosmos DB-Vorgängen auf Steuerungsebene
description: Hier erfahren Sie, wie Sie Vorgänge wie das Hinzufügen einer Region, Aktualisieren des Durchsatzes, Durchführen von Failovern für Regionen und das Hinzufügen eines VNETs auf Steuerungsebene in Azure Cosmos DB überwachen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: a0feaf4a984f40ddee7a30291fe0a8f671b6512a
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636842"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Überwachen von Azure Cosmos DB-Vorgängen auf Steuerungsebene
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Die Steuerungsebene in Azure Cosmos DB ist ein RESTful-Dienst, mit dem Sie eine Vielzahl von Vorgängen im Azure Cosmos-Konto ausführen können. Sie stellt ein öffentliches Ressourcenmodell (z. B. Datenbank, Konto) und verschiedene Vorgänge für die Endbenutzer bereit, um Aktionen für das Ressourcenmodell auszuführen. Vorgänge der Steuerungsebene schließen Änderungen am Azure Cosmos-Konto oder -Container ein. Zu den Vorgängen der Steuerungsebene gehören beispielsweise das Erstellen eines Azure Cosmos-Kontos, das Hinzufügen einer Region, das Aktualisieren des Durchsatzes, das Durchführen von Failovern für Regionen und das Hinzufügen von VNETs. In diesem Artikel wird erläutert, wie die Vorgänge auf Steuerungsebene in Azure Cosmos DB überwacht werden. Sie können die Vorgänge der Steuerungsebene für Azure Cosmos-Konten mithilfe der Azure-Befehlszeilenschnittstelle, des Azure-Portals oder von PowerShell ausführen, während Sie für Container die Azure-Befehlszeilenschnittstelle oder PowerShell verwenden.

Im Folgenden finden Sie einige Beispielszenarien, in denen das Überwachen von Vorgängen der Steuerungsebene hilfreich ist:

* Sie möchten eine Warnung erhalten, wenn die Firewallregeln für Ihr Azure Cosmos-Konto geändert werden. Die Warnung ist erforderlich, um nicht autorisierte Änderungen an Regeln, mit denen die Netzwerksicherheit Ihres Azure Cosmos-Kontos gesteuert wird, zu ermitteln und schnell Maßnahmen ergreifen zu können.

* Sie möchten eine Warnung erhalten, wenn in Ihrem Azure Cosmos-Konto eine Region hinzugefügt oder entfernt wird. Das Hinzufügen oder Entfernen von Regionen hat Auswirkungen auf die Abrechnung und die Anforderungen an die Datenhoheit. Mit dieser Warnung können Sie ein versehentliches Hinzufügen oder Entfernen von Regionen in Ihrem Konto erkennen.

* Sie möchten aus den Diagnoseprotokollen mehr Einzelheiten zu Änderungen erfahren. Ein Beispiel ist ein geändertes VNET.

## <a name="disable-key-based-metadata-write-access"></a>Deaktivieren des schlüsselbasierten Metadatenschreibzugriffs

Bevor Sie die Vorgänge auf Steuerungsebene in Azure Cosmos DB überwachen, deaktivieren Sie den schlüsselbasierten Metadatenschreibzugriff für Ihr Konto. Wenn der schlüsselbasierte Metadatenschreibzugriff deaktiviert ist, können Clients, die über Kontoschlüssel eine Verbindung mit dem Azure Cosmos-Konto herstellen, nicht auf das Konto zugreifen. Sie können den Schreibzugriff deaktivieren, indem Sie Eigenschaft `disableKeyBasedMetadataWriteAccess` auf „true“ festlegen. Nachdem Sie diese Eigenschaft festgelegt haben, können Änderungen an allen Ressourcen von Benutzern vorgenommen werden, die über die richtige Azure-Rolle und Anmeldeinformationen verfügen. Weitere Informationen zum Festlegen dieser Eigenschaft finden Sie im Artikel [Verhindern von Änderungen im Cosmos SDK](role-based-access-control.md#prevent-sdk-changes). 

Wenn `disableKeyBasedMetadataWriteAccess` aktiviert wurde und die SDK-basierten Clients Erstell- oder Aktualisierungsvorgänge ausführen, wird der Fehler *Vorgang „POST“ für die Ressource „ContainerNameorDatabaseName“ ist über den Azure Cosmos DB-Endpunkt nicht zulässig* zurückgegeben. Sie müssen den Zugriff auf solche Vorgänge für Ihr Konto aktivieren oder die Erstellungs-/Aktualisierungsvorgänge über Azure Resource Manager, Azure CLI oder Azure PowerShell ausführen. Um die Umstellung rückgängig zu machen, legen Sie disableKeyBasedMetadataWriteAccess mit Azure CLI auf **false** fest, wie im Artikel [Verhindern von Änderungen im Cosmos SDK](role-based-access-control.md#prevent-sdk-changes) beschrieben. Stellen Sie sicher, dass Sie den Wert von `disableKeyBasedMetadataWriteAccess` in „false“ anstelle von „true“ ändern.

Beachten Sie die folgenden Punkte, wenn Sie den Metadatenschreibzugriff deaktivieren:

* Überprüfen Sie die Einstellungen, und stellen Sie sicher, dass Ihre Anwendungen mithilfe des SDK oder mit Kontoschlüsseln keine Metadatenaufrufe durchführen, die die oben genannten Ressourcen ändern (z. B. Sammlung erstellen, Durchsatz aktualisieren usw.).

* Derzeit verwendet das Azure-Portal Kontoschlüssel für Metadatenvorgänge, weshalb diese Vorgänge blockiert werden. Verwenden Sie alternativ die Azure CLI, SDKs oder Resource Manager-Vorlagenbereitstellungen, um solche Vorgänge auszuführen.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Aktivieren von Diagnoseprotokollen für Vorgänge auf Steuerungsebene

Mithilfe des Azure-Portals können Sie Diagnoseprotokolle für Vorgänge auf Steuerungsebene aktivieren. Nach dem Aktivieren wird der Vorgang als Paar von Start- und Abschlussereignissen mit relevanten Details in den Diagnoseprotokollen aufgezeichnet. So bilden beispielsweise *RegionFailoverStart* und *RegionFailoverComplete* die Grenzen eines Failoverereignisses für eine Region.

Führen Sie die folgenden Schritte aus, um die Protokollierung für Vorgänge auf Steuerungsebene zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Azure Cosmos-Konto.

1. Öffnen Sie den Bereich **Diagnoseeinstellungen**, und geben Sie einen **Namen** für die zu erstellenden Protokolle an.

1. Wählen Sie **ControlPlaneRequests** als Protokolltyp aus, und klicken Sie dann auf die Option **An Log Analytics senden**.

Sie können die Protokolle auch in einem Speicherkonto oder Stream in einem Event Hub speichern. In diesem Artikel wird gezeigt, wie Sie Protokolle an Log Analytics senden und diese Abfragen. Nach der Aktivierung dauert es einige Minuten, bis die Diagnoseprotokolle erstellt werden. Alle ab diesem Zeitpunkt durchgeführten Vorgänge auf Steuerungsebene können nachverfolgt werden. Der folgende Screenshot veranschaulicht die Aktivierung von Protokollen auf Steuerungsebene:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Aktivieren der Anforderungsprotokollierung auf Steuerungsebene":::

## <a name="view-the-control-plane-operations"></a>Anzeigen von Vorgängen auf Steuerungsebene

Nachdem Sie die Protokollierung aktiviert haben, führen Sie die folgenden Schritte aus, um Vorgänge für ein bestimmtes Konto nachzuverfolgen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

1. Öffnen Sie im linken Navigationsbereich die Registerkarte **Überwachen**, und klicken Sie dann auf den Bereich **Protokolle**. Es wird eine Benutzeroberfläche geöffnet, mit der Sie problemlos Abfragen mit diesem spezifischen Konto im Bereich ausführen können. Führen Sie die folgende Abfrage aus, um die Protokolle auf Steuerungsebene anzuzeigen:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Die folgenden Screenshots zeigen Protokolle beim Ändern der Konsistenzebene für ein Azure Cosmos-Konto:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Protokolle auf Steuerungsebene beim Hinzufügen eines VNET":::

In den folgenden Screenshots werden Protokolle erfasst, wenn der Keyspace oder eine Tabelle eines Cassandra-Kontos erstellt wird und der Durchsatz aktualisiert wird. Die Protokolle auf Steuerungsebene für Erstellungs- und Aktualisierungsvorgänge in der Datenbank und dem Container werden separat protokolliert, wie es im folgenden Screenshot zu sehen ist:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Protokolle auf Steuerungsebene beim Aktualisieren des Durchsatzes":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifizieren der einem bestimmten Vorgang zugeordneten Identität

Wenn Sie weitere debuggen möchten, können Sie einen bestimmten Vorgang im **Aktivitätsprotokoll** ermitteln, indem Sie die Aktivitäts-ID oder den Zeitstempel des Vorgangs verwenden. Der Zeitstempel wird für einige Resource Manager-Clients verwendet, bei denen die Aktivitäts-ID nicht explizit übermittelt wurde. Das Aktivitätsprotokoll enthält Details zur Identität, mit der der Vorgang initiiert wurde. Der folgende Screenshot veranschaulicht, wie Sie die Aktivitäts-ID verwenden und die damit verbundenen Vorgänge im Aktivitätsprotokoll ermitteln:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Verwenden der Aktivitäts-ID und Suchen der Vorgänge":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Vorgänge der Steuerungsebene für Azure Cosmos-Konten

Die folgenden Vorgänge der Steuerungsebene sind auf Kontoebene verfügbar. Die meisten Vorgänge werden auf Kontoebene nachverfolgt. Diese Vorgänge sind als Metriken in Azure Monitor verfügbar:

* Region hinzugefügt
* Region entfernt
* Konto gelöscht
* Failover der Region
* Erstelltes Konto
* Virtuelles Netzwerk gelöscht
* Netzwerkeinstellungen für Konto aktualisiert
* Replikationseinstellungen für Konto aktualisiert
* Kontoschlüssel aktualisiert
* Sicherungseinstellungen des Kontos aktualisiert
* Diagnoseeinstellungen des Kontos aktualisiert

## <a name="control-plane-operations-for-database-or-containers"></a>Vorgänge der Steuerungsebene für Datenbanken oder Container

Die folgenden Vorgänge der Steuerungsebene sind auf Datenbank- und Containerebene verfügbar. Diese Vorgänge sind als Metriken in Azure Monitor verfügbar:

* SQL-Datenbank erstellt
* SQL-Datenbank aktualisiert
* Durchsatz der SQL-Datenbank aktualisiert
* SQL-Datenbank gelöscht
* SQL-Container erstellt
* SQL-Container aktualisiert
* Durchsatz des SQL-Containers aktualisiert
* SQL-Container gelöscht
* Cassandra-Keyspace erstellt
* Cassandra-Keyspace aktualisiert
* Durchsatz eines Cassandra-Keyspace aktualisiert
* Cassandra-Keyspace gelöscht
* Cassandra-Tabelle erstellt
* Cassandra-Tabelle aktualisiert
* Durchsatz der Cassandra-Tabelle aktualisiert
* Cassandra-Tabelle gelöscht
* Gremlin-Datenbank erstellt
* Gremlin-Datenbank aktualisiert
* Durchsatz der Gremlin-Datenbank aktualisiert
* Gremlin-Datenbank gelöscht
* Gremlin-Diagramm erstellt
* Gremlin-Diagramm aktualisiert
* Durchsatz des Gremlin-Diagramms aktualisiert
* Gremlin-Diagramm gelöscht
* Mongo-Datenbank erstellt
* Mongo-Datenbank aktualisiert
* Durchsatz der Mongo-Datenbank aktualisiert
* Mongo-Datenbank gelöscht
* Mongo-Sammlung erstellt
* Mongo-Sammlung aktualisiert
* Durchsatz der Mongo-Sammlung aktualisiert
* Mongo-Sammlung gelöscht
* AzureTable-Tabelle erstellt
* AzureTable-Tabelle aktualisiert
* Durchsatz der AzureTable-Tabelle aktualisiert
* AzureTable-Tabelle gelöscht

## <a name="diagnostic-log-operations"></a>Vorgänge in Diagnoseprotokollen

Die folgenden Vorgangsbezeichnungen werden in Diagnoseprotokollen für die verschiedenen Vorgänge verwendet:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Bei API-spezifischen Vorgängen hat die Vorgangsbezeichnung folgendes Format:

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + „Throughput“ + operationType

**Beispiel** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

Die *ResourceDetails*-Eigenschaft enthält den gesamten Ressourcentext als Anforderungsnutzlast und alle Eigenschaften, für die eine Aktualisierung angefordert wurde.

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Diagnoseprotokollabfragen für Vorgänge auf Steuerungsebene

Im Folgenden finden Sie einige Beispiele für den Abruf von Diagnoseprotokollen für Vorgänge auf Steuerungsebene:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

Abfrage zum Abrufen der Aktivitäts-ID und des aufrufenden Benutzers, der den Vorgang zum Löschen des Containers initiiert hat:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Abfrage zum Abrufen von Index- oder TTL-Aktualisierungen. Anschließend können Sie die Ausgabe dieser Abfrage mit einer früheren Aktualisierung vergleichen, um die Änderung bei Index oder TTL zu sehen.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**Ausgabe:**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu Azure Monitor für Azure Cosmos DB (Vorschau)](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md)
