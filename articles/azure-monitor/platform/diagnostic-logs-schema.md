---
title: 'Azure-Diagnoseprotokolle: Unterstützte Dienste und Schemas'
description: Erläuterung der unterstützten Dienste und Ereignisschemas für Azure-Diagnose-Protokolle.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: 21eec5ee2fef185a927f6a416732303765e02b1c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789317"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle

[Azure Monitor-Diagnoseprotokolle](../../azure-monitor/platform/diagnostic-logs-overview.md) sind von Azure-Diensten ausgegebene Protokolle, die die Vorgänge der jeweiligen Dienste oder Ressourcen beschreiben. Für alle Diagnoseprotokolle, die über Azure Monitor verfügbar sind, wird ein Schema der obersten Ebene gemeinsam genutzt. Auf diese Weise kann jeder Dienst für seine eigenen Ereignisse flexibel eindeutige Eigenschaften ausgeben.

Ein Schema wird mit einer Kombination aus dem Ressourcentyp (in der `resourceId`-Eigenschaft verfügbar) und dem `category`-Element eindeutig identifiziert. In diesem Artikel wird das Schema der obersten Ebene für Diagnoseprotokolle beschrieben, und es sind Links zu den Schemas für jeden Dienst vorhanden.

## <a name="top-level-diagnostic-logs-schema"></a>Schema der obersten Ebene für Diagnoseprotokolle

| NAME | Erforderlich/Optional | BESCHREIBUNG |
|---|---|---|
| time | Erforderlich | Der Zeitstempel (UTC) des Ereignisses. |
| resourceId | Erforderlich | Die Ressourcen-ID der Ressource, die das Ereignis ausgegeben hat. Für Mandantendienste sieht das Format folgendermaßen aus: /tenants/tenant-id/providers/provider-name. |
| tenantId | Erforderlich für Mandantenprotokolle | Die Mandanten-ID des Active Directory-Mandanten, mit dem dieses Ereignis verknüpft ist. Diese Eigenschaft wird nur für Protokolle auf Mandantenebene verwendet. Sie erscheint nicht in Protokollen auf Ressourcenebene. |
| operationName | Erforderlich | Der Name des Vorgangs, für den dieses Ereignis steht. Wenn das Ereignis für einen RBAC-Vorgang steht, ist dies der RBAC-Vorgangsname (z.B. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Ist normalerweise in Form eines Resource Manager-Vorgangs modelliert, auch wenn es sich nicht tatsächlich um dokumentierte Resource Manager-Vorgänge handelt (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | Optional | Die API-Version, die dem Vorgang zugeordnet ist, wenn für „operationName“ eine API verwendet wurde (z.B. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| category | Erforderlich | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. Häufige Protokollkategorien sind „Audit“, „Operational“, „Execution“ und „Request“ (Überwachung, Betrieb, Ausführung, Anforderung). |
| resultType | Optional | Der Status des Ereignisses. Häufige Werte sind „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“ und „Resolved“ (Gestartet, In Bearbeitung, Erfolgreich, Fehler, Aktiv, Gelöst). |
| resultSignature | Optional | Der Unterstatus des Ereignisses. Wenn dieser Vorgang einem REST-API-Aufruf entspricht, ist dies der HTTP-Statuscode des entsprechenden REST-Aufrufs. |
| resultDescription | Optional | Die statische Textbeschreibung dieses Vorgangs, z.B. „Get storage file“ (Speicherdatei abrufen). |
| durationMs | Optional | Die Dauer des Vorgangs in Millisekunden. |
| callerIpAddress | Optional | Die IP-Adresse des Aufrufers, wenn der Vorgang einem API-Aufruf entspricht, der von einer Entität mit einer öffentlich verfügbaren IP-Adresse stammt. |
| correlationId | Optional | Eine GUID, die zum Gruppieren eines Satzes mit verwandten Ereignissen verwendet wird. Wenn zwei Ereignisse über den gleichen „operationName“ verfügen, aber über einen unterschiedlichen Status (z.B. „Started“ (Gestartet) und „Succeeded“ (Erfolgreich)), weisen sie die gleiche Korrelations-ID auf. Hiermit können auch andere Beziehungen zwischen Ereignissen dargestellt werden. |
| identity | Optional | Ein JSON-Blob zum Beschreiben der Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat. Normalerweise sind hierin auch die Autorisierung und die Ansprüche bzw. das JWT-Token aus Active Directory enthalten. |
| Ebene | Optional | Der Schweregrad des Ereignisses. Er kann „Informational“ (Information), „Warning“ (Warnung), „Error“ (Fehler) oder „Critical“ (Kritisch) lauten. |
| location | Optional | Die Region der Ressource, die das Ereignis ausgibt, z.B. „USA, Osten“ oder „Frankreich, Süden“. |
| properties | Optional | Alle erweiterten Eigenschaften, die sich auf die jeweilige Kategorie der Ereignisse beziehen. Alle benutzerdefinierten bzw. eindeutigen Eigenschaften müssen in diesem „Teil B“ des Schemas angeordnet werden. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Dienstspezifische Schemas für Ressourcendiagnoseprotokolle
Das Schema für Diagnoseprotokolle für Ressourcen variiert abhängig von der Ressource und der Protokollkategorie. In dieser Liste sind alle Dienste aufgeführt, über die Diagnoseprotokolle und Links zum Dienst und zum kategoriespezifischen Schema (falls zutreffend) verfügbar gemacht werden.

| Dienst | Schema und Dokumente |
| --- | --- |
| Azure Active Directory | [Übersicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Überwachungsprotokollschema](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) und [Anmeldeschema](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management-Diagnoseprotokolle](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Anwendungsgateways |[Diagnoseprotokollierung für Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure-Automatisierung |[Protokollanalysen für Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnoseprotokolle für Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL-Diagnoseprotokolle](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL-Diagnoseprotokolle](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | Schema nicht verfügbar. |
| Content Delivery Network | [Azure-Diagnoseprotokolle für CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB-Protokollierung](../../cosmos-db/logging.md) |
| Data Factory | [Überwachen von Data Factorys mit Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Zugreifen auf Diagnoseprotokolle für Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Zugreifen auf Diagnoseprotokolle für Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs-Diagnoseprotokolle](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schema nicht verfügbar. |
| Azure Firewall | Schema nicht verfügbar. |
| IoT Hub | [IoT Hub-Vorgänge](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure-Schlüsseltresor-Protokollierung](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Protokollanalysen für den Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Benutzerdefiniertes Logic Apps-B2B-Nachverfolgungsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netzwerksicherheitsgruppen |[Protokollanalysen für Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS-Schutz | [Verwalten von Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| PowerBI dediziert | [Diagnoseprotokollierung für Power BI Embedded in Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datenmodell für Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Suchen, |[Aktivieren und Verwenden von „Datenverkehrsanalyse durchsuchen“](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus-Diagnoseprotokolle](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL-Datenbank | [Azure SQL-Datenbank-Diagnoseprotokollierung](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Auftragsdiagnoseprotokolle](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-Protokollschema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuelle Netzwerke | Schema nicht verfügbar. |
| Gateways für virtuelle Netzwerke | Schema nicht verfügbar. |

## <a name="supported-log-categories-per-resource-type"></a>Unterstützte Protokollkategorien pro Ressourcentyp
|Ressourcentyp|Category (Kategorie)|Anzeigename der Kategorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Dienst|Dienst|
|Microsoft.ApiManagement/service|GatewayLogs|Protokolle im Zusammenhang mit dem ApiManagement-Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Auftragsprotokolle|
|Microsoft.Automation/automationAccounts|JobStreams|Auftragsdatenströme|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-Knotenstatus|
|Microsoft.Batch/batchAccounts|ServiceLog|Dienstprotokolle|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Ruft die Metriken des Endpunkts ab, z.B. Bandbreite, ausgehenden Datenverkehr usw.|
|Microsoft.ClassicNetwork/networksecuritygroups|Regelflussereignis der Netzwerksicherheitsgruppe|Regelflussereignis der Netzwerksicherheitsgruppe|
|Microsoft.CognitiveServices/accounts|Audit|Überwachungsprotokolle|
|Microsoft.CognitiveServices/accounts|RequestResponse|Anforderungs- und Antwortprotokolle|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes-API-Server|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes-Controller-Manager|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Automatische Kubernetes-Clusterskalierung|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes-Scheduler|
|Microsoft.ContainerService/managedClusters|guard|Authentifizierungs-Webhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Pipeline-Aktivitätsausführungsprotokoll|
|Microsoft.DataFactory/factories|PipelineRuns|Pipelineausführungsprotokoll|
|Microsoft.DataFactory/factories|TriggerRuns|Triggerausführungsprotokoll|
|Microsoft.DataLakeAnalytics/accounts|Audit|Überwachungsprotokolle|
|Microsoft.DataLakeAnalytics/accounts|Requests|Anforderungsprotokolle|
|Microsoft.DataLakeStore/accounts|Audit|Überwachungsprotokolle|
|Microsoft.DataLakeStore/accounts|Requests|Anforderungsprotokolle|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL Server-Protokolle|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-Serverprotokolle|
|Microsoft.Devices/IotHubs|Verbindungen|Verbindungen|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Gerätetelemetrie|
|Microsoft.Devices/IotHubs|C2DCommands|C2D-Befehle|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Geräteidentitätsvorgänge|
|Microsoft.Devices/IotHubs|FileUploadOperations|Dateiuploadvorgänge|
|Microsoft.Devices/IotHubs|Routen|Routen|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D-Zwillingsvorgänge|
|Microsoft.Devices/IotHubs|TwinQueries|Zwillingsabfragen|
|Microsoft.Devices/IotHubs|JobsOperations|Auftragsvorgänge|
|Microsoft.Devices/IotHubs|DirectMethods|Direkte Methoden|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E-Diagnose (Vorschauversion)|
|Microsoft.Devices/IotHubs|Configurations|Configurations|
|Microsoft.Devices/provisioningServices|DeviceOperations|Gerätevorgänge|
|Microsoft.Devices/provisioningServices|ServiceOperations|Dienstoperationen|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archivprotokolle|
|Microsoft.EventHub/namespaces|OperationalLogs|Betriebsprotokolle|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Protokolle zur automatischen Skalierung|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Bewertungen der Autoskalierung|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Skalierungsaktionen der Autoskalierung|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Bei Betrieb|Bei Betrieb|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Eingehende Daten|Eingehende Daten|
|Microsoft.IoTSpaces/Graph|Ausgehende Daten|Ausgehende Daten|
|Microsoft.KeyVault/vaults|AuditEvent|Überwachungsprotokolle|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnoseereignisse zur Workflowlaufzeit|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrationskonto –Nachverfolgen von Ereignissen|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Ereignis der Netzwerksicherheitsgruppe|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regelzähler der Netzwerksicherheitsgruppe|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer-Warnereignisse|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Integritätsstatus der Load Balancer-Stichprobe|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-Schutz-Benachrichtigungen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Datenflussprotokolle von Entscheidungen zur DDoS-Risikominderung|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Berichte zur DDoS-Risikominderung|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-Schutz-Warnungen|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway-Zugriffsprotokoll|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway-Leistungsprotokoll|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway-Firewallprotokoll|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Azure Firewall-Anwendungsregel|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Azure Firewall-Netzwerkregel|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall-Anwendungsregel|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall-Netzwerkregel|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Gatewaydiagnoseprotokolle|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunneldiagnoseprotokolle|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Routendiagnoseprotokolle|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE-Diagnoseprotokolle|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S-Diagnoseprotokolle|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager-Testintegritätsergebnisse (Ereignis)|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Routentabellenprotokolle zum Peering|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Frontdoor-Zugriffsprotokoll|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor-Web Application Firewall-Protokoll|
|Microsoft.PowerBIDedicated/capacities|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup-Berichtsdaten|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-Aufträge|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-Ereignisse|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Replizierte Azure Site Recovery-Elemente|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery-Replikationsstatistiken|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-Wiederherstellungspunkte|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Uploadrate für Azure Site Recovery-Replikationsdaten|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Datenänderungen auf mit Azure Site Recovery geschützten Datenträgern|
|Microsoft.Search/searchServices|OperationLogs|Vorgangsprotokolle|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Betriebsprotokolle|
|Microsoft.Sql/servers/databases|SQLInsights|SQL-Informationen|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatische Optimierung|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Wartestatistik der Datenbank|
|Microsoft.Sql/servers/databases|Zeitlimits|Zeitlimits|
|Microsoft.Sql/servers/databases|Blöcke|Blöcke|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Audit|Überwachungsprotokolle|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS-Worker|
|Microsoft.Sql/servers/databases|ExecRequests|Ausführungsanforderungen|
|Microsoft.Sql/servers/databases|RequestSteps|Anforderungsschritte|
|Microsoft.Sql/servers/databases|SqlRequests|SQL-Anforderungen|
|Microsoft.Sql/servers/databases|Wartevorgänge|Wartevorgänge|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Nutzungsstatistiken zu Ressourcen|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL-Informationen|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.StreamAnalytics/streamingjobs|Ausführung|Ausführung|
|Microsoft.StreamAnalytics/streamingjobs|Erstellen|Erstellen|
|microsoft.web/sites|FunctionExecutionLogs|Protokolle zur Funktionsausführung|
|microsoft.web/sites/slots|FunctionExecutionLogs|Protokolle zur Funktionsausführung|

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Diagnoseprotokollen](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Streamen von Diagnoseprotokollen für Ressourcen an **Event Hubs**](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* Ändern der Diagnoseeinstellungen für Ressourcen mithilfe der Azure Monitor-REST-API ([Service Diagnostic Settings](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) (Diagnoseeinstellungen für Dienste))
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

