---
title: Vorlagenfunktionen – Ressourcen
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Abrufen von Werten zu Ressourcen verwendet werden können.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 3b5a2642ebbabac61e46f4d9b0a2dfec9610a673
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902662"
---
# <a name="resource-functions-for-arm-templates"></a>Ressourcenfunktionen für ARM-Vorlagen

Resource Manager stellt die folgenden Funktionen für das Abrufen von Ressourcenwerten in Ihren ARM-Vorlagen (Azure Resource Manager) bereit:

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [pickZones](#pickzones)
* [providers](#providers)
* [Referenz](#reference)
* [Ressourcengruppe](#resourcegroup)
* [Ressourcen-ID](#resourceid)
* [Abonnement](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Informationen zum Abrufen von Werten aus Parametern, Variablen oder der aktuellen Bereitstellung finden Sie unter [Funktionen für Bereitstellungswerte](template-functions-deployment.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Gibt die Ressourcen-ID für eine [Erweiterungsressource](../management/extension-resource-types.md) zurück. Hierbei handelt es sich um einen Ressourcentyp, der auf eine andere Ressource angewendet wird, um deren Funktionen zu erweitern.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |Zeichenfolge |Die Ressourcen-ID für die Ressource, auf die die Erweiterungsressource angewendet wird. |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein |Zeichenfolge |Nächstes Ressourcennamensegment, sofern erforderlich. |

Fügen Sie weitere Ressourcennamen als Parameter hinzu, wenn der Ressourcentyp mehrere Segmente enthält.

### <a name="return-value"></a>Rückgabewert

Das Standardformat der Ressourcen-ID, die von dieser Funktion zurückgegeben wird, ist wie folgt:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Das „scope“-Segment ist je nach erweiterter Ressource unterschiedlich.

Wenn die Erweiterungsressource auf eine **Ressource** angewendet wird, hat die zurückgegebene Ressourcen-ID das folgende Format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf eine **Ressourcengruppe** angewendet wird, ist das Format wie folgt:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf ein **Abonnement** angewendet wird, ist das Format wie folgt:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf eine **Verwaltungsgruppe** angewendet wird, ist das Format wie folgt:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Beispiel für „extensionResourceId“

Im folgenden Beispiel wird die Ressourcen-ID für eine Ressourcengruppensperre zurückgegeben.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Eine benutzerdefinierte, für eine Verwaltungsgruppe bereitgestellte Richtliniendefinition wird als Erweiterungsressource implementiert. Stellen Sie die folgende Vorlage für eine Verwaltungsgruppe bereit, um eine Richtlinie zu erstellen und zuzuweisen.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Ein Beispiel für die Bereitstellung einer integrierten Richtliniendefinition finden Sie unter [tenantResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Die Syntax für diese Funktion variiert je nach dem Namen der Auflistungsvorgänge. Jede Implementierung gibt Werte für den Ressourcentyp zurück, der einen Auflistungsvorgang unterstützt. Der Name des Vorgangs muss mit `list` beginnen. Häufig werden `listKeys`, `listKeyValue` und `listSecrets` verwendet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Eindeutiger Bezeichner für die Ressource. |
| apiVersion |Ja |Zeichenfolge |API-Version eines Ressourcen-Laufzeitstatus. In der Regel im Format **jjjj-mm-tt**. |
| functionValues |Nein |Objekt (object) | Ein Objekt, das über Werte für die Funktion verfügt. Geben Sie dieses Objekt nur für Funktionen an, die den Empfang eines Objekts mit Parameterwerten unterstützen – z.B. **listAccountSas** für ein Speicherkonto. Ein Beispiel für die Übergabe von Funktionswerten wird in diesem Artikel gezeigt. |

### <a name="valid-uses"></a>Gültige Verwendungen

Die list-Funktionen können in den Eigenschaften einer Ressourcendefinition verwendet werden. Verwenden Sie keine list-Funktionen, die vertrauliche Informationen im Ausgabeabschnitt einer Vorlage offenlegen. Ausgabewerte werden im Bereitstellungsverlauf gespeichert und könnten von einem böswilligen Benutzer abgerufen werden.

Wenn sie mit [Eigenschafteniteration](copy-properties.md) verwendet werden, können Sie die Listenfunktionen für `input` verwenden, weil der Ausdruck der Ressourceneigenschaft zugewiesen wird. Sie können sie nicht mit `count` verwenden, weil die Anzahl bestimmt werden muss, bevor die Listenfunktion aufgelöst wird.

### <a name="implementations"></a>Implementierungen

Die Verwendungsmöglichkeiten von list* werden in der folgenden Tabelle gezeigt.

| Ressourcentyp | Funktionsname |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2020-04-01/notebookworkspaces/listconnectioninfo) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listauthkeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Um zu bestimmen, welche Ressourcentypen einen list-Vorgang aufweisen, stehen die folgenden Optionen zur Verfügung:

* Zeigen Sie die [REST-API-Vorgänge](/rest/api/) für einen Ressourcenanbieter an, und suchen Sie nach List-Vorgängen. Speicherkonten weisen z. B. den [listKeys-Vorgang](/rest/api/storagerp/storageaccounts) auf.
* Verwenden Sie das PowerShell-Cmdlet [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). Im folgenden Beispiel werden alle List-Vorgänge für Speicherkonten abgerufen:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Verwenden Sie den folgenden Azure-CLI-Befehl, um nur die Listenvorgänge zu filtern:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt ist abhängig von der list-Funktion, die Sie verwenden, unterschiedlich. Die Funktion listKeys für ein Speicherkonto wird z.B. im folgenden Format zurückgegeben:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere list-Funktionen weisen andere Rückgabeformate auf. Um das Format einer Funktion anzuzeigen, geben Sie es im Abschnitt „outputs“ wie in der Beispielvorlage dargestellt an.

### <a name="remarks"></a>Bemerkungen

Geben Sie die Ressource entweder mithilfe des Ressourcennamens oder der [resourceId-Funktion](#resourceid) an. Wenn Sie eine list-Funktion in der gleichen Vorlage verwenden, die auch die referenzierte Ressource bereitstellt, verwenden Sie den Ressourcennamen.

Bei Verwendung einer **list**-Funktion mit einer Ressource mit bedingter Bereitstellung wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die **list**-Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie die **if**-Funktion, um sicherzustellen, dass die Funktion nur ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „list“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der [if](template-functions-logical.md#if)-Funktion.

### <a name="list-example"></a>list-Beispiel

Im folgenden Beispiel wird listKeys beim Festlegen eines Werts für [Bereitstellungsskripts](deployment-script-template.md) verwendet.

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

Das nächste Beispiel zeigt eine list-Funktion, die einen Parameter verwendet. In diesem Fall handelt es sich um die Funktion **listAccountSas**. Übergeben Sie ein Objekt für die Ablaufzeit. Die Ablaufzeit muss in der Zukunft liegen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Bestimmt, ob ein Ressourcentyp Zonen für eine Region unterstützt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| providerNamespace | Ja | Zeichenfolge | Der Ressourcenanbieternamespace für den Ressourcentyp, der auf Zonenunterstützung überprüft werden soll. |
| resourceType | Ja | Zeichenfolge | Der Ressourcentyp, der auf Zonenunterstützung überprüft werden soll. |
| location | Ja | Zeichenfolge | Die Region, die auf Zonenunterstützung überprüft werden soll. |
| numberOfZones | Nein | integer | Die Anzahl der zurückzugebenden logischen Zonen. Der Standardwert ist 1. Die Anzahl muss eine positive ganze Zahl zwischen 1 und 3 sein.  Verwenden Sie 1 für Ressourcen mit nur einer Zone. Für Ressourcen mit mehreren Zonen muss der Wert kleiner als oder gleich der Anzahl der unterstützten Zonen sein. |
| offset | Nein | integer | Der Offset von der beginnenden logischen Zone. Die Funktion gibt einen Fehler zurück, wenn Offset plus numberOfZones die Anzahl der unterstützten Zonen überschreitet. |

### <a name="return-value"></a>Rückgabewert

Ein Array mit den unterstützten Zonen. Wenn Sie die Standardwerte für Offset und numberOfZones verwenden, geben ein Ressourcentyp und eine Region, die Zonen unterstützen, das folgende Array zurück:

```json
[
    "1"
]
```

Wenn der `numberOfZones`-Parameter auf 3 festgelegt ist, wird Folgendes zurückgegeben:

```json
[
    "1",
    "2",
    "3"
]
```

Wenn der Ressourcentyp oder die Region keine Zonen unterstützt, wird ein leeres Array zurückgegeben.

```json
[
]
```

### <a name="pickzones-example"></a>Beispiel für pickZones

Die folgende Vorlage zeigt drei Ergebnisse für die Verwendung der pickZones-Funktion.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

Die Ausgabe aus den vorherigen Beispielen gibt drei Arrays zurück.

| Name | type | Wert |
| ---- | ---- | ----- |
| Unterstützt | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

Sie können anhand der Antwort von pickZones bestimmen, ob NULL für Zonen bereitgestellt werden soll oder virtuelle Computer verschiedenen Zonen zugewiesen werden sollen. Im folgenden Beispiel wird ein Wert für die Zone auf der Verfügbarkeit von Zonen basierend festgelegt.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> „Loops“ (Schleifen) und „copyIndex()“ sind noch nicht implementiert.  Siehe unter [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) (Schleifen).

---

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

Gibt Informationen zu einem Ressourcenanbieter und den von ihm unterstützten Ressourcentypen zurück. Wenn Sie keinen Ressourcentyp angeben, gibt die Funktion alle unterstützten Typen für den Ressourcenanbieter zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |Zeichenfolge |Namespace des Anbieters |
| resourceType |Nein |Zeichenfolge |Der Ressourcentyp innerhalb des angegebenen Namespace. |

### <a name="return-value"></a>Rückgabewert

Jeder unterstützte Typ wird im folgenden Format zurückgegeben:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

Die Arraysortierung der zurückgegebenen Werte ist dabei nicht garantiert.

### <a name="providers-example"></a>providers-Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) zeigt die Nutzungsweise der Anbieterfunktion:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

Für den Ressourcenanbieter **Microsoft.Web** und den Ressourcentyp **sites** wird im vorherigen Beispiel ein Objekt im folgenden Format zurückgegeben:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Referenz

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Gibt ein Objekt zurück, das den Laufzeitstatus einer Ressource darstellt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Name oder eindeutiger Bezeichner einer Ressource Wenn Sie auf eine Ressource in der aktuellen Vorlage verweisen, stellen Sie nur den Ressourcennamen als Parameter bereit. Wenn Sie auf eine zuvor bereitgestellte Ressource verweisen oder der Name der Ressource nicht eindeutig ist, geben Sie die Ressourcen-ID an. |
| apiVersion |Nein |Zeichenfolge |API-Version der angegebenen Ressource. **Dieser Parameter ist erforderlich, wenn die Ressource nicht innerhalb derselben Vorlage bereitgestellt wird.** In der Regel im Format **jjjj-mm-tt**. Informationen zu gültigen API-Versionen für Ihre Ressource finden Sie in der [Vorlagenreferenz](/azure/templates/). |
| 'Full' |Nein |Zeichenfolge |Ein Wert, der angibt, ob das vollständige Ressourcenobjekt zurückgegeben werden soll. Wird `'Full'` nicht angegeben, wird nur das Eigenschaftenobjekt der Ressource zurückgegeben. Das vollständige Objekt enthält Werte wie die Ressourcen-ID und den Standort. |

### <a name="return-value"></a>Rückgabewert

Jeder Ressourcentyp gibt andere Eigenschaften für die Verweisfunktion zurück. Die Funktion gibt kein einzelnes vordefiniertes Format zurück. Darüber hinaus variiert der zurückgegebene Wert auch je nach dem Wert des `'Full'`-Arguments. Um die Eigenschaften für einen Ressourcentyp anzuzeigen, geben Sie das Objekt wie im Beispiel gezeigt im Abschnitt „outputs“ zurück.

### <a name="remarks"></a>Bemerkungen

Die Verweisfunktion ruft den Runtime-Status einer zuvor bereitgestellten Ressource oder einer in der aktuellen Vorlage bereitgestellten Ressource ab. Dieser Artikel zeigt Beispiele für beide Szenarios.

Sie verwenden in der Regel die Funktion **Verweis**, um einen bestimmten Wert aus einem Objekt zurückzugeben, wie z.B. den Blob-Endpunkt-URI oder den vollständig qualifizierten Domänennamen.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Verwenden Sie `'Full'`, wenn Sie Ressourcenwerte benötigen, die nicht Teil des Eigenschaftsschemas sind. Rufen Sie zum Festlegen von Schlüsseltresor-Zugriffsrichtlinien beispielsweise die Identitätseigenschaften für einen virtuellen Computer ab.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Gültige Verwendungen

Die Verweisfunktion kann nur in den Eigenschaften einer Ressourcendefinition und im Abschnitt „outputs“ einer Vorlage oder Bereitstellung verwendet werden. Wenn sie mit [Eigenschafteniteration](copy-properties.md) verwendet wird, können Sie die Verweisfunktion für `input` verwenden, weil der Ausdruck der Ressourceneigenschaft zugewiesen wird.

Sie können die Verweisfunktion nicht verwenden, um den Wert der `count`-Eigenschaft in einer Kopierschleife festzulegen. Sie können damit andere Eigenschaften in der Schleife festlegen. Der Verweis ist für die count-Eigenschaft gesperrt, da diese Eigenschaft vor dem Auflösen der Verweisfunktion bestimmt werden muss.

Wenn Sie die reference-Funktion oder eine beliebige list*-Funktion im Abschnitt „outputs“ einer geschachtelten Vorlage verwenden möchten, müssen Sie die ```expressionEvaluationOptions``` festlegen, um die Auswertung des [inneren Bereichs](linked-templates.md#expression-evaluation-scope-in-nested-templates) oder eine verknüpfte anstelle einer geschachtelten Vorlage zu verwenden.

Bei Verwendung der **reference**-Funktion mit einer Ressource mit bedingter Bereitstellung wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird.  Es wird eine Fehlermeldung angezeigt, wenn die **reference**-Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie die **if**-Funktion, um sicherzustellen, dass die Funktion nur ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „reference“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der [if](template-functions-logical.md#if)-Funktion.

### <a name="implicit-dependency"></a>Implizite Abhängigkeit

Mithilfe der Referenzfunktion deklarieren Sie implizit, dass eine Ressource von einer anderen abhängt, wenn die referenzierte Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird und Sie anhand des Ressourcennamens (nicht der Ressourcen-ID) auf die Ressource verweisen. Die dependsOn-Eigenschaft muss nicht zusätzlich verwendet werden. Die Funktion wird erst dann ausgewertet, wenn die Ressource, auf die verwiesen wird, die Bereitstellung abgeschlossen hat.

### <a name="resource-name-or-identifier"></a>Ressourcenname oder -bezeichner

Wenn Sie auf eine Ressource verweisen, die in der gleichen Vorlage bereitgestellt wird, geben Sie den Namen der Ressource an.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

Wenn Sie auf eine Ressource verweisen, die nicht in derselben Vorlage bereitgestellt wird, geben Sie die Ressourcen-ID und die `apiVersion` an.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

Um Mehrdeutigkeiten in Bezug auf die Ressource zu vermeiden, auf die Sie verweisen, können Sie eine vollqualifizierte Ressourcen-ID angeben.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

Wenn Sie einen vollqualifizierten Verweis auf eine Ressource erstellen, ist die Reihenfolge für die Kombination von Segmenten von Typ und Name nicht einfach eine Verkettung beider Werte. Verwenden Sie stattdessen nach dem Namespace eine Folge von *Typ-Name*-Paaren, beginnend mit dem am wenigsten spezifischen bis zum spezifischsten:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Beispiel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` ist richtig `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` ist nicht richtig

Um die Erstellung von Ressourcen-IDs zu vereinfachen, verwenden Sie die in diesem Dokument beschriebenen `resourceId()`-Funktionen anstelle der `concat()`-Funktion.

### <a name="get-managed-identity"></a>Abrufen einer verwalteten Identität

[Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sind [Erweiterungsressourcentypen](../management/extension-resource-types.md), die implizit für einige Ressourcen erstellt werden. Da die verwaltete Identität nicht explizit in der Vorlage definiert ist, müssen Sie auf die Ressource verweisen, auf die die Identität angewendet wird. Verwenden Sie `Full`, um alle Eigenschaften, einschließlich der implizit erstellten Identität, abzurufen.

Das Muster lautet:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Um z. B. die Prizipal-ID für eine verwaltete Identität abzurufen, die auf einen virtuellen Computer angewandt wird, verwenden Sie:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Um die Mandanten-ID für eine verwaltete Identität abzurufen, die auf eine VM-Skalierungsgruppe angewandt wird, verwenden Sie:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>reference-Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) stellt eine Ressource bereit und verweist auf diese Ressource.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

Im vorherigen Beispiel werden die beiden Objekt zurückgegeben. Das Eigenschaftenobjekt hat das folgende Format:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Das vollständige Objekt hat das folgende Format:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verweist auf ein Speicherkonto, das nicht in dieser Vorlage bereitgestellt wird. Das Speicherkonto ist bereits in demselben Abonnement vorhanden.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Gibt ein Objekt zurück, das die aktuelle Ressourcengruppe darstellt.

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Die Eigenschaft **managedBy** wird nur für Ressourcengruppen zurückgegeben, die von einem anderen Dienst verwaltete Ressourcen enthalten. Bei verwalteten Anwendungen, Databricks und AKS ist der Wert der Eigenschaft die Ressourcen-ID der Verwaltungsressource.

### <a name="remarks"></a>Bemerkungen

Die Funktion `resourceGroup()` kann nicht in einer Vorlage verwendet werden, die [auf der Abonnementebene bereitgestellt](deploy-to-subscription.md) wird. Sie kann nur in Vorlagen verwendet werden, die in einer Ressourcengruppe bereitgestellt werden. Sie können die `resourceGroup()`-Funktion in einer [verknüpften oder geschachtelten Vorlage (mit innerem Bereich)](linked-templates.md) verwenden, deren Ziel eine Ressourcengruppe ist, auch wenn die übergeordnete Vorlage im Abonnement bereitgestellt wurde. In diesem Szenario wird die verknüpfte oder geschachtelte Vorlage auf Ressourcengruppenebene bereitgestellt. Weitere Informationen zum Einrichten einer Ressourcengruppe als Ziel in einer Bereitstellung auf Ressourcenebene finden Sie unter [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](./deploy-to-resource-group.md).

Die Funktion „resourceGroup“ wird häufig verwendet, um Ressourcen am gleichen Speicherort wie die Ressourcengruppe zu erstellen. Das folgende Beispiel nutzt den Speicherort der Ressourcengruppe als einen Parameterstandardwert.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Die Funktion „resourceGroup“ kann auch verwendet werden, um Tags aus einer Ressourcengruppe auf eine Ressource anzuwenden. Weitere Informationen finden Sie unter [Apply tags from resource group](../management/tag-resources.md#apply-tags-from-resource-group) (Anwenden von Tags aus einer Ressourcengruppe).

Wenn Sie für die Bereitstellung in mehreren Ressourcengruppen geschachtelte Vorlagen verwenden, können Sie den Bereich zum Auswerten der resourceGroup-Funktion angeben. Weitere Informationen finden Sie unter [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](./deploy-to-resource-group.md).

### <a name="resource-group-example"></a>resourceGroup-Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) gibt die Eigenschaften der Ressourcengruppe zurück.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

Im vorherigen Beispiel wird ein Objekt im folgenden Format zurückgegeben:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner einer Ressource zurück. Diese Funktion wird verwendet, wenn der Ressourcenname zweideutig ist oder nicht innerhalb der gleichen Vorlage zur Verfügung gestellt wird. Das Format des zurückgegebenen Bezeichners variiert abhängig davon, ob die Bereitstellung im Bereich einer Ressourcengruppe, eines Abonnements, einer Verwaltungsgruppe oder eines Mandanten erfolgt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nein |Zeichenfolge (im GUID-Format) |Der Standardwert ist das aktuelle Abonnement. Geben Sie diesen Wert an, wenn Sie eine Ressource in einem anderen Abonnement abrufen möchten. Geben Sie diesen Wert nur an, wenn die Bereitstellung im Bereich einer Ressourcengruppe oder eines Abonnements erfolgt. |
| resourceGroupName |Nein |Zeichenfolge |Der Standardwert ist die aktuelle Ressourcengruppe. Geben Sie diesen Wert an, wenn Sie eine Ressource in einer anderen Ressourcengruppe abrufen möchten. Geben Sie diesen Wert nur an, wenn die Bereitstellung im Bereich einer Ressourcengruppe erfolgt. |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein |Zeichenfolge |Nächstes Ressourcennamensegment, sofern erforderlich. |

Fügen Sie weitere Ressourcennamen als Parameter hinzu, wenn der Ressourcentyp mehrere Segmente enthält.

### <a name="return-value"></a>Rückgabewert

Wenn die Vorlage im Bereich einer Ressourcengruppe bereitgestellt wird, wird die Ressourcen-ID im folgenden Format zurückgegeben:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Die resourceId-Funktion kann auch für andere Bereitstellungsbereiche verwendet werden. Das Format der ID ändert sich jedoch.

Wenn Sie die resourceId-Funktion beim Bereitstellen für ein Abonnement verwenden, wird die Ressourcen-ID im folgenden Format zurückgegeben:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Wenn Sie die resourceId-Funktion beim Bereitstellen für eine Verwaltungsgruppe oder für einen Mandanten verwenden, wird die Ressourcen-ID im folgenden Format zurückgegeben:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Um Verwechslungen zu vermeiden, empfiehlt es sich, „resourceId“ bei der Arbeit mit Ressourcen, die für das Abonnement, für die Verwaltungsgruppe oder für den Mandanten bereitgestellt werden, nicht zu verwenden. Verwenden Sie stattdessen die für den jeweiligen Bereich vorgesehene ID-Funktion.

Verwenden Sie für [Ressourcen auf Abonnementebene](deploy-to-subscription.md) die Funktion [subscriptionResourceId](#subscriptionresourceid).

Verwenden Sie für [Ressourcen auf Verwaltungsgruppenebene](deploy-to-management-group.md) die Funktion [extensionResourceId](#extensionresourceid), um auf eine Ressource zu verweisen, die als Erweiterung einer Verwaltungsgruppe implementiert ist. Benutzerdefinierte Richtliniendefinitionen, die für eine Verwaltungsgruppe bereitgestellt werden, sind beispielsweise Erweiterungen der Verwaltungsgruppe. Verwenden Sie die Funktion [tenantResourceId](#tenantresourceid), um auf Ressourcen zu verweisen, die für den Mandanten bereitgestellt wurden, aber in Ihrer Verwaltungsgruppe verfügbar sind. Integrierte Richtliniendefinitionen werden beispielsweise als Ressourcen auf Mandantenebene implementiert.

Verwenden Sie für [Ressourcen auf Mandantenebene](deploy-to-tenant.md) die Funktion [tenantResourceId](#tenantresourceid). Verwenden Sie „tenantResourceId“ für integrierte Richtliniendefinitionen, da diese auf der Mandantenebene implementiert werden.

### <a name="remarks"></a>Bemerkungen

Die Anzahl der Parameter, die Sie angeben, hängt davon ab, ob es sich bei der Ressource um eine übergeordnete oder untergeordnete Ressource handelt und ob sich die Ressource im gleichen Abonnement oder in der gleichen Ressourcengruppe befindet.

Um die Ressourcen-ID für eine übergeordnete Ressource im gleichen Abonnement und in der gleichen Ressourcengruppe abzurufen, geben Sie den Typ und den Namen der Ressource an.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Um die Ressourcen-ID für eine untergeordnete Ressource abzurufen, achten Sie auf die Anzahl der Segmente im Ressourcentyp. Geben Sie einen Ressourcennamen für jedes Segment des Ressourcentyps an. Der Name des Segments entspricht der Ressource, die für den jeweiligen Teil der Hierarchie vorhanden ist.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Um die Ressourcen-ID für eine Ressource im gleichen Abonnement, jedoch in einer anderen Ressourcengruppe abzurufen, geben Sie den Ressourcengruppennamen an.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Um die Ressourcen-ID für eine Ressource in einem anderen Abonnement und in einer anderen Ressourcengruppe abzurufen, geben Sie die Abonnement-ID und den Ressourcengruppennamen an.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Sie müssen diese Funktion oft nutzen, wenn Sie ein Speicherkonto oder einen virtuellen Computer in einer alternativen Ressourcengruppe verwenden. Das folgende Beispiel zeigt, wie eine Ressource von einer externen Ressourcengruppe leicht verwendet werden kann:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>resourceId-Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) gibt die Ressourcen-ID für ein Speicherkonto in der Ressourcengruppe zurück:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>Abonnement

`subscription()`

Gibt Details zum Abonnement für die aktuelle Bereitstellung zurück.

### <a name="return-value"></a>Rückgabewert

Die Funktion gibt das folgende Format zurück:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Bemerkungen

Wenn Sie für die Bereitstellung in mehreren Abonnements geschachtelte Vorlagen verwenden, können Sie den Bereich zum Auswerten der subscription-Funktion angeben. Weitere Informationen finden Sie unter [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](./deploy-to-resource-group.md).

### <a name="subscription-example"></a>subscription-Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) zeigt die im Abschnitt „outputs“ abgerufene subscription-Funktion.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner für eine Ressource zurück, die auf Abonnementebene bereitgestellt wird.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nein |Zeichenfolge (im GUID-Format) |Der Standardwert ist das aktuelle Abonnement. Geben Sie diesen Wert an, wenn Sie eine Ressource in einem anderen Abonnement abrufen möchten. |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein |Zeichenfolge |Nächstes Ressourcennamensegment, sofern erforderlich. |

Fügen Sie weitere Ressourcennamen als Parameter hinzu, wenn der Ressourcentyp mehrere Segmente enthält.

### <a name="return-value"></a>Rückgabewert

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Bemerkungen

Mit dieser Funktion können Sie die Ressourcen-ID für Ressourcen abrufen, die [im Abonnement bereitgestellt werden](deploy-to-subscription.md) und nicht in einer Ressourcengruppe. Die zurückgegebene ID unterscheidet sich dadurch von dem Wert, der von der Funktion [resourceId](#resourceid) zurückgegeben wird, dass kein Ressourcengruppenwert enthalten ist.

### <a name="subscriptionresourceid-example"></a>Beispiel für „subscriptionResourceId“

Mit der folgenden Vorlage wird eine integrierte Rolle zugewiesen. Sie können diese entweder in einer Ressourcengruppe oder einem Abonnement bereitstellen. Hierbei wird die Funktion „subscriptionResourceId“ verwendet, um die Ressourcen-ID für integrierte Rollen abzurufen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner für eine Ressource zurück, die auf Mandantenebene bereitgestellt wird.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein |Zeichenfolge |Nächstes Ressourcennamensegment, sofern erforderlich. |

Fügen Sie weitere Ressourcennamen als Parameter hinzu, wenn der Ressourcentyp mehrere Segmente enthält.

### <a name="return-value"></a>Rückgabewert

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Bemerkungen

Mit dieser Funktion können Sie die Ressourcen-ID für eine Ressource abrufen, die für den Mandanten bereitgestellt wird. Die zurückgegebene ID unterscheidet sich dadurch von den Werten, die von anderen Ressourcen-ID-Funktionen zurückgegeben werden, dass keinen Ressourcengruppen- oder Abonnementwerte enthalten sind.

### <a name="tenantresourceid-example"></a>Beispiel für „tenantResourceId“

Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Verwenden Sie die tenantResourceId-Funktion, um eine Richtlinienzuweisung bereitzustellen, die auf eine integrierte Richtliniendefinition verweist.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](template-syntax.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](copy-resources.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](deploy-powershell.md).