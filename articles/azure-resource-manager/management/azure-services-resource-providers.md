---
title: Ressourcenanbieter durch Azure-Dienste
description: Listet alle Ressourcenanbieter-Namespaces für Azure Resource Manager auf und gibt den Azure-Dienst für den jeweiligen Namespace an.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: faea6badc24d0152e94fc507cafdb15932d1ea3e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452622"
---
# <a name="resource-providers-for-azure-services"></a>Ressourcenanbieter für Azure-Dienste

In diesem Artikel wird die Zuordnung von Ressourcenanbieter-Namespaces zu Azure-Diensten aufgezeigt.

## <a name="match-resource-provider-to-service"></a>Zuordnung von Ressourcenanbieter zu Dienst

Die Ressourcenanbieter, die mit **– registriert** gekennzeichnet sind, sind standardmäßig für Ihr Abonnement registriert. Weitere Informationen finden Sie unter [Registrierung](#registration).

| Ressourcenanbieter-Namespace | Azure-Dienst |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory-Domänendienste](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService – [registriert](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure Attestation Service |
| Microsoft.Authorization – [registriert](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automation](../../automation/index.yml) |
| Microsoft.AutonomousSystems | [Autonome Systeme](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | SQL Server-Registrierung |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing – [registriert](#registration) | [Kostenverwaltung und Abrechnung](/azure/billing/) |
| Microsoft.BingMaps | [Bing Maps](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain Service](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprint](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service Certificate](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klassisches Bereitstellungsmodell für virtuelle Computer |
| Microsoft.ClassicInfrastructureMigrate | Migration vom klassischen Bereitstellungsmodell |
| Microsoft.ClassicNetwork | Klassisches Bereitstellungsmodell für virtuelle Netzwerke |
| Microsoft.ClassicStorage | Klassisches Bereitstellungsmodell für Speicher |
| Microsoft.ClassicSubscription – [registriert](#registration) | Klassisches Bereitstellungsmodell |
| Microsoft.CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft.Commerce – [registriert](#registration) | core |
| Microsoft.Compute | [Virtuelle Computer](../../virtual-machines/index.yml)<br />[Skalierungsgruppen für virtuelle Computer](../../virtual-machine-scale-sets/index.yml) |
| Microsoft.Consumption – [registriert](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Containerinstanzen](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.CostManagement – [registriert](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Kunden-Lockbox für Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Benutzerdefinierte Azure-Anbieter](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft.DataProtection | Datenschutz |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [Azure-Datenbank für PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Azure-Bereitstellungs-Manager](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Device Provisioning-Dienst](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [Lebenszyklusdienste](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | Enterprise Knowledge Graph |
| Microsoft.EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft.Features – [registriert](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA in Azure (große Instanzen)](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Dediziertes HSM von Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft.HybridNetwork  | [Private Edge Zones](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Schlüsseltresor](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logik-Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Azure-Wartung](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | Von PaaS-Diensten verwaltete virtuelle Netzwerke |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [Verwaltungsgruppen](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering – [registriert](#registration) | core |
| Microsoft.Media | [Media Services](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft.Notebooks | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | Objektspeicher |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal – [registriert](#registration) | [Azure portal](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph – [registriert](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft.Resources – [registriert](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft.SerialConsole – [registriert](#registration) | [Serielle Azure-Konsole für Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Lizenz |
| Microsoft.Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL-Datenbank](../../azure-sql/database/index.yml)<br /> [Verwaltete Azure SQL-Datenbank-Instanz](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server auf virtuellen Azure-Computern](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support – [registriert](#registration) | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | Token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure-Funktionen](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | Erweiterte Sicherheitsupdates |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registrierung

Die obigen Ressourcenanbieter, die mit **– registriert** gekennzeichnet sind, sind standardmäßig für Ihr Abonnement registriert. Wenn Sie die anderen Ressourcenanbieter nutzen möchten, müssen Sie sie [registrieren](resource-providers-and-types.md). Viele Ressourcenanbieter werden jedoch für Sie registriert, sobald Sie bestimmte Aktionen ausführen. Wenn Sie z. B. eine Ressource im Portal erstellen, registriert das Portal automatisch alle nicht registrierten Ressourcenanbieter, die benötigt werden. Wenn Sie Ressourcen über eine [Azure Resource Manager-Vorlage](../templates/overview.md) bereitstellen, werden auch alle erforderlichen Ressourcenanbieter registriert.

> [!IMPORTANT]
> Registrieren Sie einen Ressourcenanbieter nur dann, wenn Sie ihn nutzen möchten. Der Registrierungsschritt ermöglicht Ihnen, die geringstmöglichen Berechtigungen innerhalb Ihres Abonnements beizubehalten. Ein böswilliger Benutzer kann nicht registrierte Ressourcenanbieter nicht verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Ressourcenanbietern, einschließlich Registrieren eines Ressourcenanbieters, finden Sie unter [Azure-Ressourcenanbieter und -typen](resource-providers-and-types.md).
