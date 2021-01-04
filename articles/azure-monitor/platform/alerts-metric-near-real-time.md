---
title: Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor
description: Referenz zu Supportmetriken und -protokollen für Metrikwarnungen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/3/2020
ms.subservice: alerts
ms.openlocfilehash: dfcc1f15ebec1337cfa7d484319c7b942ff052b2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548238"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor

Azure Monitor unterstützt jetzt einen [neuen Metrikwarnungstyp](./alerts-overview.md), der erhebliche Vorteile gegenüber älteren [klassischen Metrikwarnungen](./alerts-classic.overview.md) aufweist. Metriken stehen für [eine umfangreiche Liste von Azure-Diensten](./metrics-supported.md) zur Verfügung. Die neueren Warnungen unterstützen eine (stetig wachsende) Teilmenge der Ressourcentypen. In diesem Artikel wird diese Teilmenge aufgeführt.

Sie können auch neuere Metrikwarnungen für gängige Protokolldaten verwenden, die in einem Log Analytics-Arbeitsbereich gespeichert sind und als Metriken extrahiert wurden. Weitere Informationen finden Sie unter [Metrikwarnungen für Protokolle](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, Befehlszeilenschnittstelle, REST-Unterstützung
Derzeit können Sie neuere Metrikwarnungen nur im Azure-Portal, in der [REST-API](/rest/api/monitor/metricalerts/) oder in [Resource Manager-Vorlagen](./alerts-metric-create-templates.md) erstellen. Die Konfiguration neuerer Warnungen über PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI, Version 2.0 und höher) wird in Kürze unterstützt.

## <a name="metrics-and-dimensions-supported"></a>Unterstützte Metriken und Dimensionen
Neuere Metrikwarnungen unterstützen Warnungen für Metriken mit Dimensionen. Mithilfe von Dimensionen können Sie die Metrik nach der richtigen Ebene filtern. Sie können alle unterstützten Metriken und die dazugehörigen Dimensionen unter [Azure Monitor-Metrik-Explorer](./metrics-charts.md) erkunden und visualisieren.

Im Folgenden finden Sie die vollständige Liste der Azure Monitor-Metrikquellen, die von den neueren Warnungen unterstützt werden:

|Ressourcentyp  |Unterstützte Dimensionen |Warnungen mit mehreren Ressourcen| Verfügbare Metriken|
|---------|---------|-----|----------|
|Microsoft.Aadiam/azureADMetrics | Ja | Nein | |
|Microsoft.ApiManagement/service | Ja | Nein | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Ja | Nein | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Ja | Nein | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Ja| Nein | [Automation-Konten](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | Nein | Nein | |
|Microsoft.Batch/batchAccounts | Ja | Nein | [Batch-Konten](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Ja | Ja | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nein | Nein | [Cloud Services (klassisch)](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | Nein | Nein | [Virtuelle Computer (klassisch)](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Ja | Nein | [Speicherkonten (klassisch)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Ja | Nein | |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Ja | Nein | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Ja | Nein | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Ja | Nein | |
|Microsoft.CognitiveServices/accounts | Ja | Nein | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Ja | Ja<sup>1</sup> | [Virtuelle Computer](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nein |[Skalierungsgruppen für virtuelle Computer](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Ja| Nein | [Containergruppen](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Nein | Nein | [Containerregistrierungen](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Ja | Nein | [Verwaltete Cluster](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Ja| Nein | [Data Factorys V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Ja | Nein | [Data Factorys V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Ja | Nein | |
|Microsoft.DBforMariaDB/servers | Nein | Nein | [DB for MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Nein | Nein |[Datenbank für MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | Ja | Nein | |
|Microsoft.DBforPostgreSQL/servers | Nein | Nein | [Datenbank für PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Nein | Nein | [DB for PostgreSQL V2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/singleservers | Nein | Nein | [DB for PostgreSQL (Einzelserver)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | Ja | Nein |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Ja | Nein | [Device Provisioning Service-Instanzen](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | Ja | Nein | |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nein | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Ja | Nein | [Event Grid-Domänen](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | Ja | Nein | [Event Grid-Systemthemen](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Ja | Nein | [Event Grid-Themen](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Ja| Nein | [Event Hubs-Cluster](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Ja| Nein | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Ja | Nein | [HDInsight-Cluster](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Ja | Nein | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Ja |Ja |[Tresore](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Ja |Nein |[Data Explorer-Cluster](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Ja | Nein |[Integrationsdienstumgebungen](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Nein | Nein |[Logik-Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Ja | Nein | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Ja | Nein | [Maps-Konten](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Nein | Nein | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Ja | Nein | [Media Services-Streamingendpunkte](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Ja | Ja | [Azure NetApp-Kapazitätspools](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Ja | Ja | [Azure NetApp-Volumes](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Ja | Nein | [Anwendungsgateways](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Ja | Nein | [Firewalls](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Nein | Nein | [DNS-Zonen](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ja | Nein |[ExpressRoute-Verbindungen](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Ja | Nein |[ExpressRoute Direct](./metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (nur für Standard-SKUs)| Ja| Nein | [Lastenausgleichsmodule](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| Nein | Nein | |
|Microsoft.Network/privateEndpoints| Nein | Nein | |
|Microsoft.Network/privateLinkServices| Nein | Nein |
|Microsoft.Network/publicipaddresses | Nein | Nein |[Öffentliche IP-Adressen](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nein | [Traffic Manager-Profile](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | Nein | [Log Analytics-Arbeitsbereiche](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | Ja | Nein | [Peerings](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | Ja | Nein | [Peeringdienste](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Nein | Nein | [Kapazitäten](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Ja | Nein | [Relays](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Nein | Nein | [Suchdienste](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Ja | Nein | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Nein | Ja | [SQL Managed Instance](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Nein | Ja | [SQL-Datenbanken](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Nein | Ja | [Pools für elastische SQL-Datenbanken](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Ja | Nein | [Speicherkonten](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ja| Nein | [Blobdienste](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Dateidienste](./metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Warteschlangendienste](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) und [Tabellendienste](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | Ja | Nein | |
|Microsoft.StorageSync/storageSyncServices | Ja | Nein | [Speichersynchronisierungsdienste](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Ja | Nein | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Ja | Nein | [Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft.Synapse/workspaces/bigDataPools | Ja | Nein | [Apache Spark-Pools in Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft.Synapse/workspaces/sqlPools | Ja | Nein | [SQL-Pools von Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft.VMWareCloudSimple/virtualMachines | Ja | Nein | [Virtuelle CloudSimple-Computer](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Ja | Nein | [Pools mit mehreren Rollen für die App Service-Umgebung](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Ja | Nein | [Workerpools für die App Service-Umgebung](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Nein | [App Service-Pläne](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Nein | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) und [Functions](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ja | Nein | [App Service-Slots](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> Für Netzwerkmetriken (Eingehender Netzwerkverkehr gesamt, Ausgehender Netzwerkverkehr gesamt, Eingehende Datenflüsse, Ausgehende Datenflüsse, Maximale Erstellungsrate für eingehende Datenflüsse, Maximale Erstellungsrate für ausgehende Datenflüsse) sowie benutzerdefinierte Metriken virtueller Computer nicht unterstützt.

## <a name="payload-schema"></a>Nutzlast und Schema

> [!NOTE]
> Für Ihre Webhook-Integrationen können Sie auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet. [Hier finden Sie Informationen zu den Definitionen des allgemeinen Warnungsschemas](./alerts-common-schema-definitions.md).


Bei Verwendung einer ordnungsgemäß konfigurierten [Aktionsgruppe](./action-groups.md) enthält der POST-Vorgang für alle neueren Metrikwarnungen die folgende JSON-Nutzlast und das folgende Schema:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [neue Oberfläche „Warnungen“](./alerts-overview.md).
* Erfahren Sie mehr über [Protokollwarnungen in Azure](./alerts-unified-log.md).
* Erfahren Sie mehr über [Warnungen in Azure](./alerts-overview.md).
