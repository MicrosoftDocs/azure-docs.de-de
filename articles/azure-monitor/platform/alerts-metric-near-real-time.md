---
title: Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor
description: Referenz zu Supportmetriken und -protokollen für Metrikwarnungen in Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 965d1ace2afdad21a069193b508fc2b10fdf4700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64697226"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor

Azure Monitor unterstützt jetzt einen [neuen Metrikwarnungstyp](../../azure-monitor/platform/alerts-overview.md), der erhebliche Vorteile gegenüber älteren [klassischen Metrikwarnungen](../../azure-monitor/platform/alerts-classic.overview.md) aufweist. Metriken stehen für [eine umfangreiche Liste von Azure-Diensten](../../azure-monitor/platform/metrics-supported.md) zur Verfügung. Die neueren Warnungen unterstützen eine (stetig wachsende) Teilmenge der Ressourcentypen. In diesem Artikel wird diese Teilmenge aufgeführt.

Sie können auch neuere Metrikwarnungen für gängige Protokolldaten verwenden, die in einem Log Analytics-Arbeitsbereich gespeichert sind und als Metriken extrahiert wurden. Weitere Informationen finden Sie unter [Metrikwarnungen für Protokolle](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, Befehlszeilenschnittstelle, REST-Unterstützung
Derzeit können Sie neuere Metrikwarnungen nur im Azure-Portal, in der [REST-API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) oder in [Resource Manager-Vorlagen](../../azure-monitor/platform/alerts-metric-create-templates.md) erstellen. Die Konfiguration neuerer Warnungen über PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI, Version 2.0 und höher) wird in Kürze unterstützt.

## <a name="metrics-and-dimensions-supported"></a>Unterstützte Metriken und Dimensionen
Neuere Metrikwarnungen unterstützen Warnungen für Metriken mit Dimensionen. Mithilfe von Dimensionen können Sie die Metrik nach der richtigen Ebene filtern. Sie können alle unterstützten Metriken und die dazugehörigen Dimensionen unter [Azure Monitor-Metrik-Explorer](../../azure-monitor/platform/metrics-charts.md) erkunden und visualisieren.

Im Anschluss finden Sie die vollständige Liste der Azure Monitor-Metrikquellen, die von den neueren Warnungen unterstützt werden:

|Ressourcentyp  |Unterstützte Dimensionen  | Verfügbare Metriken|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-Konten](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-Konten](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuelle Computer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[Skalierungsgruppen für virtuelle Computer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| [Containergruppen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | [Verwaltete Cluster](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Ja| [Data Factorys V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ja     |[Data Factorys V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[Datenbank für MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [Datenbank für PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | –     |[IoT Hub-Metriken](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Ja     |[DPS-Metriken](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nein | [Tresore](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logik-Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Anwendungsgateways](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | –| [DNS-Zonen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | – |  [ExpressRoute-Verbindungen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (nur für Standard-SKUs)| Ja| [Lastenausgleichsmodule](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Öffentliche IP-Adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | [Traffic Manager-Profile](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja|[Log Analytics-Arbeitsbereiche](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapazitäten](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   –      |[Suchdienste](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Speicherkonten](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [Blobdienste](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Dateidienste](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Warteschlangendienste](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) und [Tabellendienste](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Ja | [App Service-Pläne](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Ja | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) und [Functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Ja | [App Service-Slots](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Nutzlast und Schema

> [!NOTE]
> Für Ihre Webhook-Integrationen können Sie auch das [allgemeine Warnungsschema](https://aka.ms/commonAlertSchemaDocs) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet. [Hier finden Sie Informationen zu den Definitionen des allgemeinen Warnungsschemas](https://aka.ms/commonAlertSchemaDefinitions).


Bei Verwendung einer ordnungsgemäß konfigurierten [Aktionsgruppe](../../azure-monitor/platform/action-groups.md) enthält der POST-Vorgang für alle neueren Metrikwarnungen die folgende JSON-Nutzlast und das folgende Schema:

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
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
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

* Erfahren Sie mehr über die [neue Oberfläche „Warnungen“](../../azure-monitor/platform/alerts-overview.md).
* Erfahren Sie mehr über [Protokollwarnungen in Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Erfahren Sie mehr über [Warnungen in Azure](../../azure-monitor/platform/alerts-overview.md).
