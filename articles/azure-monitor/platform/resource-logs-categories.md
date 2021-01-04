---
title: 'Azure Monitor-Ressourcenprotokolle: unterstützte Dienste und Kategorien'
description: 'Referenz zu Azure Monitor: Erläuterung der unterstützten Dienste und Ereignisschemas für Azure-Ressourcenprotokolle.'
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 1fd6f07151c93b64c150f01e5c0b5c7f4cffed85
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593007"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Unterstützte Kategorien für Azure-Ressourcenprotokolle

> [!NOTE]
> Ressourcenprotokolle wurden zuvor als Diagnoseprotokolle bezeichnet. Der Name wurde im Oktober 2019 geändert, da die Typen der von Azure Monitor gesammelten Protokolle nicht mehr nur die Azure-Ressource umfassen.

[Azure Monitor-Ressourcenprotokolle](./platform-logs-overview.md) sind von Azure-Diensten ausgegebene Protokolle, die die Vorgänge der jeweiligen Dienste oder Ressourcen beschreiben. Für alle Ressourcenprotokolle, die über Azure Monitor verfügbar sind, wird ein Schema der obersten Ebene gemeinsam genutzt. Auf diese Weise kann jeder Dienst für seine eigenen Ereignisse flexibel eindeutige Eigenschaften ausgeben.

Ein Schema wird mit einer Kombination aus dem Ressourcentyp (in der `resourceId`-Eigenschaft verfügbar) und dem `category`-Element eindeutig identifiziert. Es gibt ein allgemeines Schema für alle Ressourcenprotokolle mit dienstspezifischen Feldern, die dann für verschiedene Protokollkategorien hinzugefügt werden. Weitere Informationen finden Sie unter [Allgemeines und dienstspezifisches Schema für Azure-Ressourcenprotokolle]().

## <a name="supported-log-categories-per-resource-type"></a>Unterstützte Protokollkategorien pro Ressourcentyp

Es folgt eine Liste der Arten von Protokollen, die für jeden Ressourcentyp verfügbar sind. 

Einige Kategorien werden möglicherweise nur für bestimmte Ressourcentypen unterstützt. Wenn Sie der Meinung sind, dass eine Ressource fehlt, lesen Sie die ressourcenspezifische Dokumentation. Beispielsweise sind Kategorien des Typs „Microsoft.Sql/servers/databases“ nicht für alle Datenbanktypen verfügbar. Weitere Informationen finden Sie unter den [Informationen zur SQL-Datenbank-Diagnoseprotokollierung](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Wenn Sie noch etwas vermissen, können Sie unten in diesem Artikel einen GitHub-Kommentar öffnen.

## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|Category|Anzeigename der Kategorie|
|---|---|
|Signin|Signin|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Category|Anzeigename der Kategorie|
|---|---|
|Engine|Engine|
|Dienst|Dienst|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Category|Anzeigename der Kategorie|
|---|---|
|GatewayLogs|Protokolle im Zusammenhang mit dem ApiManagement-Gateway|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Category|Anzeigename der Kategorie|
|---|---|
|ApplicationConsole|Anwendungskonsole|
|SystemLogs|Systemprotokolle|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Category|Anzeigename der Kategorie|
|---|---|
|JobLogs|Auftragsprotokolle|
|JobStreams|Auftragsdatenströme|
|DscNodeStatus|DSC-Knotenstatus|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Category|Anzeigename der Kategorie|
|---|---|
|ServiceLog|Dienstprotokolle|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Category|Anzeigename der Kategorie|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Category|Anzeigename der Kategorie|
|---|---|
|BlockchainApplication|Blockchainanwendung|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Category|Anzeigename der Kategorie|
|---|---|
|BlockchainApplication|Blockchainanwendung|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Category|Anzeigename der Kategorie|
|---|---|
|WebApplicationFirewallLogs|Web Application Firewall-Protokolle|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Category|Anzeigename der Kategorie|
|---|---|
|AzureCdnAccessLog|Azure CDN-Zugriffsprotokoll|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Category|Anzeigename der Kategorie|
|---|---|
|CoreAnalytics|Ruft die Metriken des Endpunkts ab, z. B. Bandbreite, ausgehender Datenverkehr usw.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Category|Anzeigename der Kategorie|
|---|---|
|Regelflussereignis der Netzwerksicherheitsgruppe|Regelflussereignis der Netzwerksicherheitsgruppe|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Category|Anzeigename der Kategorie|
|---|---|
|Audit|Überwachungsprotokolle|
|RequestResponse|Anforderungs- und Antwortprotokolle|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Category|Anzeigename der Kategorie|
|---|---|
|ContainerRegistryLoginEvents|Anmeldeereignisse|
|ContainerRegistryRepositoryEvents|RepositoryEvent-Protokolle|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Category|Anzeigename der Kategorie|
|---|---|
|cluster-autoscaler|Automatische Kubernetes-Clusterskalierung|
|kube-apiserver|Kubernetes-API-Server|
|kube-audit|Kubernetes-Überwachung|
|kube-controller-manager|Kubernetes-Controller-Manager|
|kube-scheduler|Kubernetes-Scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Category|Anzeigename der Kategorie|
|---|---|
|AuditLogs|Überwachungsprotokolle für MiniRP-Aufrufe|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Category|Anzeigename der Kategorie|
|---|---|
|accounts|Databricks-Konten|
|clusters|Databricks-Cluster|
|dbfs|Databricks-Dateisystem|
|instancePools|Instanzenpools|
|jobs|Databricks-Aufträge|
|Notebook|Databricks-Notebook|
|secrets|Databricks-Geheimnisse|
|sqlPermissions|Databricks-SQL-Berechtigungen|
|ssh|Databricks-SSH|
|Arbeitsbereich|Databricks-Arbeitsbereich|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Category|Anzeigename der Kategorie|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Category|Anzeigename der Kategorie|
|---|---|
|ActivityRuns|Pipeline-Aktivitätsausführungsprotokoll|
|PipelineRuns|Pipelineausführungsprotokoll|
|TriggerRuns|Triggerausführungsprotokoll|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Category|Anzeigename der Kategorie|
|---|---|
|Audit|Überwachungsprotokolle|
|Requests|Anforderungsprotokolle|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Category|Anzeigename der Kategorie|
|---|---|
|MySqlAuditLogs|MariaDB-Überwachungsprotokolle|
|MySqlSlowLogs|MariaDB-Serverprotokolle|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Category|Anzeigename der Kategorie|
|---|---|
|MySqlAuditLogs|MySQL-Überwachungsprotokolle|
|MySqlSlowLogs|MySQL Server-Protokolle|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Category|Anzeigename der Kategorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des PostgreSQL-Abfragespeichers|
|QueryStoreWaitStatistics|Wartestatistik des PostgreSQL-Abfragespeichers|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Category|Anzeigename der Kategorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Category|Anzeigename der Kategorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Category|Anzeigename der Kategorie|
|---|---|
|Prüfpunkt|Prüfpunkt|
|Fehler|Fehler|
|Verwaltung|Verwaltung|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Category|Anzeigename der Kategorie|
|---|---|
|Prüfpunkt|Prüfpunkt|
|Verbindung|Verbindung|
|Fehler|Fehler|
|HostRegistration|HostRegistration|
|Verwaltung|Verwaltung|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Category|Anzeigename der Kategorie|
|---|---|
|Prüfpunkt|Prüfpunkt|
|Fehler|Fehler|
|Feed|Feed|
|Verwaltung|Verwaltung|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Category|Anzeigename der Kategorie|
|---|---|
|C2DCommands|C2D-Befehle|
|C2DTwinOperations|C2D-Zwillingsvorgänge|
|Configurations|Configurations|
|Verbindungen|Verbindungen|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Geräteidentitätsvorgänge|
|DeviceStreams|Gerätestreams (Vorschau)|
|DeviceTelemetry|Gerätetelemetrie|
|DirectMethods|Direkte Methoden|
|DistributedTracing|Verteilte Ablaufverfolgung (Vorschauversion)|
|FileUploadOperations|Dateiuploadvorgänge|
|JobsOperations|Auftragsvorgänge|
|Routen|Routen|
|TwinQueries|Zwillingsabfragen|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Category|Anzeigename der Kategorie|
|---|---|
|DeviceOperations|Gerätevorgänge|
|ServiceOperations|Dienstoperationen|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Category|Anzeigename der Kategorie|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Category|Anzeigename der Kategorie|
|---|---|
|AuditEvent|AuditEvent-Protokoll|
|DataIssue|DataIssue-Protokoll|
|Requests|Konfigurationsprotokoll|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Category|Anzeigename der Kategorie|
|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|
|PublishFailures|Protokolle für Veröffentlichungsfehler|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Category|Anzeigename der Kategorie|
|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Category|Anzeigename der Kategorie|
|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|
|PublishFailures|Protokolle für Veröffentlichungsfehler|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Category|Anzeigename der Kategorie|
|---|---|
|ArchiveLogs|Archivprotokolle|
|AutoScaleLogs|Protokolle zur automatischen Skalierung|
|CustomerManagedKeyUserLogs|Protokolle für von Kunden verwaltete Schlüssel|
|EventHubVNetConnectionEvent|VNet/IP-Filterung-Verbindungsprotokolle|
|KafkaCoordinatorLogs|Kafka-Koordinatorprotokolle|
|KafkaUserErrorLogs|Kafka-Benutzerfehlerprotokolle|
|OperationalLogs|Betriebsprotokolle|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Category|Anzeigename der Kategorie|
|---|---|
|AuditLogs|Überwachungsprotokolle|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Category|Anzeigename der Kategorie|
|---|---|
|AutoscaleEvaluations|Bewertungen der Autoskalierung|
|AutoscaleScaleActions|Skalierungsaktionen der Autoskalierung|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Category|Anzeigename der Kategorie|
|---|---|
|AppAvailabilityResults|Verfügbarkeitsergebnisse|
|AppBrowserTimings|Browserzeiten|
|AppDependencies|Abhängigkeiten|
|AppEvents|Events|
|AppExceptions|Ausnahmen|
|AppMetrics|Metriken|
|AppPageViews|Seitenaufrufe|
|AppPerformanceCounters|Leistungsindikatoren|
|AppRequests|Requests|
|AppSystemEvents|Systemereignisse|
|AppTraces|Traces|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|Category|Anzeigename der Kategorie|
|---|---|
|Audit|Audit|
|Ausgehende Daten|Ausgehende Daten|
|Eingehende Daten|Eingehende Daten|
|Bei Betrieb|Bei Betrieb|
|Trace|Trace|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Category|Anzeigename der Kategorie|
|---|---|
|AuditEvent|Überwachungsprotokolle|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Category|Anzeigename der Kategorie|
|---|---|
|FailedIngestion|Fehlgeschlagene Erfassungsvorgänge|
|SucceededIngestion|Erfolgreiche Erfassungsvorgänge|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|Category|Anzeigename der Kategorie|
|---|---|
|IntegrationAccountTrackingEvents|Integrationskonto –Nachverfolgen von Ereignissen|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Category|Anzeigename der Kategorie|
|---|---|
|WorkflowRuntime|Diagnoseereignisse zur Workflowlaufzeit|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Category|Anzeigename der Kategorie|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Category|Anzeigename der Kategorie|
|---|---|
|KeyDeliveryRequests|Schlüsselübermittlungsanforderungen|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Category|Anzeigename der Kategorie|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway-Zugriffsprotokoll|
|ApplicationGatewayFirewallLog|Application Gateway-Firewallprotokoll|
|ApplicationGatewayPerformanceLog|Application Gateway-Leistungsprotokoll|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Category|Anzeigename der Kategorie|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall-Anwendungsregel|
|AzureFirewallNetworkRule|Azure Firewall-Netzwerkregel|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Category|Anzeigename der Kategorie|
|---|---|
|BastionAuditLogs|Bastion-Überwachungsprotokolle|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Category|Anzeigename der Kategorie|
|---|---|
|PeeringRouteLog|Routentabellenprotokolle zum Peering|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Category|Anzeigename der Kategorie|
|---|---|
|FrontdoorAccessLog|Frontdoor-Zugriffsprotokoll|
|FrontdoorWebApplicationFirewallLog|Frontdoor-Web Application Firewall-Protokoll|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Category|Anzeigename der Kategorie|
|---|---|
|LoadBalancerAlertEvent|Load Balancer-Warnereignisse|
|LoadBalancerProbeHealthStatus|Integritätsstatus der Load Balancer-Stichprobe|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Category|Anzeigename der Kategorie|
|---|---|
|NetworkSecurityGroupEvent|Ereignis der Netzwerksicherheitsgruppe|
|NetworkSecurityGroupRuleCounter|Regelzähler der Netzwerksicherheitsgruppe|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Category|Anzeigename der Kategorie|
|---|---|
|DDoSMitigationFlowLogs|Datenflussprotokolle von Entscheidungen zur DDoS-Risikominderung|
|DDoSMitigationReports|Berichte zur DDoS-Risikominderung|
|DDoSProtectionNotifications|DDoS-Schutz-Benachrichtigungen|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Category|Anzeigename der Kategorie|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager-Testintegritätsergebnisse (Ereignis)|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Category|Anzeigename der Kategorie|
|---|---|
|GatewayDiagnosticLog|Gatewaydiagnoseprotokolle|
|IKEDiagnosticLog|IKE-Diagnoseprotokolle|
|P2SDiagnosticLog|P2S-Diagnoseprotokolle|
|RouteDiagnosticLog|Routendiagnoseprotokolle|
|TunnelDiagnosticLog|Tunneldiagnoseprotokolle|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Category|Anzeigename der Kategorie|
|---|---|
|VMProtectionAlerts|VM-Schutz-Warnungen|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Category|Anzeigename der Kategorie|
|---|---|
|Engine|Engine|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Category|Anzeigename der Kategorie|
|---|---|
|AddonAzureBackupAlerts|Add-On: Azure Backup-Warnungsdaten|
|AddonAzureBackupJobs|Add-On: Azure Backup-Auftragsdaten|
|AddonAzureBackupPolicy|Add-On: Azure Backup-Richtliniendaten|
|AddonAzureBackupProtectedInstance|Add-On: Azure Backup-Daten für geschützte Instanz|
|AddonAzureBackupStorage|Add-On: Azure Backup-Speicherdaten|
|AzureBackupReport|Azure Backup-Berichtsdaten|
|AzureSiteRecoveryEvents|Azure Site Recovery-Ereignisse|
|AzureSiteRecoveryJobs|Azure Site Recovery-Aufträge|
|AzureSiteRecoveryProtectedDiskDataChurn|Datenänderungen auf mit Azure Site Recovery geschützten Datenträgern|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-Wiederherstellungspunkte|
|AzureSiteRecoveryReplicatedItems|Replizierte Azure Site Recovery-Elemente|
|AzureSiteRecoveryReplicationDataUploadRate|Uploadrate für Azure Site Recovery-Replikationsdaten|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery-Replikationsstatistiken|
|CoreAzureBackup|Kern: Azure Backup-Daten|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Category|Anzeigename der Kategorie|
|---|---|
|HybridConnectionsEvent|HybridConnections Events|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Category|Anzeigename der Kategorie|
|---|---|
|OperationLogs|Vorgangsprotokolle|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Category|Anzeigename der Kategorie|
|---|---|
|OperationalLogs|Betriebsprotokolle|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Category|Anzeigename der Kategorie|
|---|---|
|AllLogs|Protokolle für Azure SignalR Service|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Category|Anzeigename der Kategorie|
|---|---|
|DevOpsOperationsAudit|Überwachungsprotokolle für DevOps-Vorgänge|
|ResourceUsageStats|Nutzungsstatistiken zu Ressourcen|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Category|Anzeigename der Kategorie|
|---|---|
|Errors|Errors|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|
|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|
|SQLInsights|SQL-Informationen|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Category|Anzeigename der Kategorie|
|---|---|
|AutomaticTuning|Automatische Optimierung|
|Blöcke|Blöcke|
|DatabaseWaitStatistics|Wartestatistik der Datenbank|
|Deadlocks|Deadlocks|
|DevOpsOperationsAudit|Überwachungsprotokolle für DevOps-Vorgänge|
|DmsWorkers|DMS-Worker|
|Errors|Errors|
|ExecRequests|Ausführungsanforderungen|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|
|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|
|RequestSteps|Anforderungsschritte|
|SQLInsights|SQL-Informationen|
|SqlRequests|SQL-Anforderungen|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|
|Zeitlimits|Zeitlimits|
|Wartevorgänge|Wartevorgänge|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Category|Anzeigename der Kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Category|Anzeigename der Kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Category|Anzeigename der Kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Category|Anzeigename der Kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Category|Anzeigename der Kategorie|
|---|---|
|Erstellen|Erstellen|
|Ausführung|Ausführung|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Category|Anzeigename der Kategorie|
|---|---|
|AppServiceEnvironmentPlatformLogs|Plattformprotokolle der App Service-Umgebung|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Category|Anzeigename der Kategorie|
|---|---|
|AppServiceAppLogs|App Service-Anwendungsprotokolle|
|AppServiceAuditLogs|Zugriffsüberwachungsprotokolle|
|AppServiceConsoleLogs|App Service-Konsolenprotokolle|
|AppServiceFileAuditLogs|Überwachungsprotokolle für Website-Inhaltsänderungen|
|AppServiceHTTPLogs|HTTP-Protokolle|
|FunctionAppLogs|Funktionsanwendungsprotokolle|
|ScanLogs|Protokolle für Antivirenscans|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots


|Category|Anzeigename der Kategorie|
|---|---|
|AppServiceAppLogs|App Service-Anwendungsprotokolle|
|AppServiceAuditLogs|Zugriffsüberwachungsprotokolle|
|AppServiceConsoleLogs|App Service-Konsolenprotokolle|
|AppServiceFileAuditLogs|Überwachungsprotokolle für Website-Inhaltsänderungen|
|AppServiceHTTPLogs|HTTP-Protokolle|
|FunctionAppLogs|Funktionsanwendungsprotokolle|
|ScanLogs|Protokolle für Antivirenscans|


## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Ressourcenprotokollen](./platform-logs-overview.md)
* [Streamen von Ressourcenprotokollen an **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändern der Diagnoseeinstellungen für Ressourcenprotokolle mithilfe der Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings)
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

