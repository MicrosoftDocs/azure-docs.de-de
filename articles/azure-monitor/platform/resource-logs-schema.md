---
title: 'Azure-Ressourcenprotokolle: Unterstützte Dienste und Schemas'
description: Erläuterung der unterstützten Dienste und Ereignisschemas für Azure-Ressourcenprotokolle.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 1f8754245e394ecdffbb045d7f4cc96ec8a3ff78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184500"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Allgemeines und dienstspezifisches Schema für Azure-Ressourcenprotokolle

> [!NOTE]
> Ressourcenprotokolle wurden zuvor als Diagnoseprotokolle bezeichnet. Der Name wurde im Oktober 2019 geändert, da die Typen der von Azure Monitor gesammelten Protokolle nicht mehr nur die Azure-Ressource umfassen. Außerdem war bisher die Liste der Ressourcenprotokollkategorien, die von Ihnen gesammelt werden können, in diesem Artikel enthalten. Diese wurde nun in den Artikel [Ressourcenprotokollkategorien](resource-logs-categories.md) verschoben. 

[Azure Monitor-Ressourcenprotokolle](./platform-logs-overview.md) sind von Azure-Diensten ausgegebene Protokolle, die die Vorgänge der jeweiligen Dienste oder Ressourcen beschreiben. Für alle Ressourcenprotokolle, die über Azure Monitor verfügbar sind, wird ein Schema der obersten Ebene gemeinsam genutzt. Auf diese Weise kann jeder Dienst für seine eigenen Ereignisse flexibel eindeutige Eigenschaften ausgeben.

Ein Schema wird mit einer Kombination aus dem Ressourcentyp (in der `resourceId`-Eigenschaft verfügbar) und dem `category`-Element eindeutig identifiziert. In diesem Artikel wird das Schema der obersten Ebene für Ressourcenprotokolle beschrieben, und es sind Links zu den Schemas für jeden Dienst vorhanden.


## <a name="top-level-common-schema"></a>Allgemeines Schema der obersten Ebene

| Name | Erforderlich/Optional | BESCHREIBUNG |
|---|---|---|
| time | Erforderlich | Der Zeitstempel (UTC) des Ereignisses. |
| resourceId | Erforderlich | Die Ressourcen-ID der Ressource, die das Ereignis ausgegeben hat. Für Mandantendienste sieht das Format folgendermaßen aus: /tenants/tenant-id/providers/provider-name. |
| tenantId | Erforderlich für Mandantenprotokolle | Die Mandanten-ID des Active Directory-Mandanten, mit dem dieses Ereignis verknüpft ist. Diese Eigenschaft wird nur für Protokolle auf Mandantenebene verwendet. Sie erscheint nicht in Protokollen auf Ressourcenebene. |
| operationName | Erforderlich | Der Name des Vorgangs, für den dieses Ereignis steht. Wenn das Ereignis einen Azure RBAC-Vorgang repräsentiert, ist dies der Azure RBAC-Vorgangsname (z. B. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Ist normalerweise in Form eines Resource Manager-Vorgangs modelliert, auch wenn es sich nicht tatsächlich um dokumentierte Resource Manager-Vorgänge handelt (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | Optional | Die API-Version, die dem Vorgang zugeordnet ist, wenn für „operationName“ eine API verwendet wurde (z. B. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| category | Erforderlich | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. Häufige Protokollkategorien sind „Audit“, „Operational“, „Execution“ und „Request“ (Überwachung, Betrieb, Ausführung, Anforderung). |
| resultType | Optional | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| resultSignature | Optional | Der Unterstatus des Ereignisses. Wenn dieser Vorgang einem REST-API-Aufruf entspricht, ist dieses Feld der HTTP-Statuscode des entsprechenden REST-Aufrufs. |
| resultDescription | Optional | Die statische Textbeschreibung dieses Vorgangs, z. B. „Get storage file“ (Speicherdatei abrufen). |
| durationMs | Optional | Die Dauer des Vorgangs in Millisekunden. |
| callerIpAddress | Optional | Die IP-Adresse des Aufrufers, wenn der Vorgang einem API-Aufruf entspricht, der von einer Entität mit einer öffentlich verfügbaren IP-Adresse stammt. |
| correlationId | Optional | Eine GUID, die zum Gruppieren eines Satzes mit verwandten Ereignissen verwendet wird. Wenn zwei Ereignisse über denselben „operationName“, aber zwei verschiedene Status (z. B. „Started“ und „Succeeded“) verfügen, verwenden sie in der Regel dieselbe Korrelations-ID. Hiermit können auch andere Beziehungen zwischen Ereignissen dargestellt werden. |
| identity | Optional | Ein JSON-Blob zum Beschreiben der Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat. Normalerweise sind in diesem Feld auch die Autorisierung und die Ansprüche bzw. das JWT-Token aus Active Directory enthalten. |
| Ebene | Optional | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| location | Optional | Die Region der Ressource, die das Ereignis ausgibt, z. B „USA, Osten“ oder „Frankreich, Süden“. |
| properties | Optional | Alle erweiterten Eigenschaften, die sich auf die jeweilige Kategorie der Ereignisse beziehen. Alle benutzerdefinierten bzw. eindeutigen Eigenschaften müssen in diesem „Teil B“ des Schemas angeordnet werden. |

## <a name="service-specific-schemas"></a>Dienstspezifische Schemas

Das Schema für Ressourcenprotokolle variiert abhängig von der Ressource und der Protokollkategorie. In dieser Liste sind Dienste aufgeführt, über die Ressourcenprotokolle und Links zum Dienst und zum kategoriespezifischen Schema (falls zutreffend) verfügbar gemacht werden. Diese Liste ändert sich ständig, da neue Dienste hinzugefügt werden. Wenn Sie also den gewünschten Dienst unten nicht finden, verwenden Sie eine Suchmaschine, um nach weiteren Dokumentationen zu suchen. Sie können auch ein GitHub-Problem für diesen Artikel öffnen, damit wir ihn aktualisieren können.

| Dienst | Schema und Dokumente |
| --- | --- |
| Azure Active Directory | [Übersicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Überwachungsprotokollschema](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) und [Anmeldeschema](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services: Einrichten der Diagnoseprotokollierung](../../analysis-services/analysis-services-logging.md) |
| API Management | [API Management-Ressourcenprotokolle](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Anwendungsgateways |[Protokollierung für Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure-Automatisierung |[Protokollanalysen für Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch-Protokollierung](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Protokollierung für Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Containerregistrierung | [Protokollierung für Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure-Protokolle für CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB-Protokollierung](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Überwachen von Data Factorys mit Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Zugreifen auf Protokolle für Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Zugreifen auf Protokolle für Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure-Daten-Explorer | [Azure Data Explorer-Protokolle](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Azure Database for MySQL-Diagnoseprotokolle](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL-Protokolle](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Diagnoseprotokollierung in Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Einrichten der Azure Digital Twins-Diagnose](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs-Protokolle](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schema nicht verfügbar. |
| Azure Firewall | Schema nicht verfügbar. |
| Front Door | [Protokollierung für Front Door](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub-Vorgänge](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Azure-Schlüsseltresor-Protokollierung](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes-Protokollierung](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Protokollanalysen für den Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Benutzerdefiniertes Logic Apps-B2B-Nachverfolgungsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netzwerksicherheitsgruppen |[Protokollanalysen für Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS-Schutz | [Verwalten von Azure DDoS Protection Standard](../../ddos-protection/manage-ddos-protection.md) |
| Power BI Dedicated | [Protokollierung für Power BI Embedded in Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datenmodell für Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Suchen, |[Aktivieren und Verwenden von „Datenverkehrsanalyse durchsuchen“](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus-Protokolle](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL-Datenbank | [Azure SQL-Datenbank-Protokollierung](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Auftragsprotokolle](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-Protokollschema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuelle Netzwerke | Schema nicht verfügbar. |
| Gateways für virtuelle Netzwerke | Schema nicht verfügbar. |



## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen der Ressourcenprotokollkategorien, die Sie sammeln können](resource-logs-categories.md)
* [Weitere Informationen zu Ressourcenprotokollen](./platform-logs-overview.md)
* [Streamen von Ressourcenprotokollen an **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändern der Diagnoseeinstellungen für Ressourcenprotokolle mithilfe der Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings)
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)