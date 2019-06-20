---
title: Azure Resource Manager-Vorlagenfunktionen – Ressourcen | Microsoft Docs
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Abrufen von Werten zu Ressourcen verwendet werden können.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/21/2019
ms.author: tomfitz
ms.openlocfilehash: dcad4b988f37d46a0b843fbf905e18011bc4e313
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990755"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Ressourcenfunktionen für Azure Resource Manager-Vorlagen

Der Ressourcen-Manager stellt die folgenden Funktionen zum Abrufen von Ressourcenwerten bereit:

* [list*](#list)
* [providers](#providers)
* [Referenz](#reference)
* [Ressourcengruppe](#resourcegroup)
* [Ressourcen-ID](#resourceid)
* [Abonnement](#subscription)

Informationen zum Abrufen von Werten aus Parametern, Variablen oder der aktuellen Bereitstellung finden Sie unter [Funktionen für Bereitstellungswerte](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Die Syntax für diese Funktion variiert je nach dem Namen der Auflistungsvorgänge. Jede Implementierung gibt Werte für den Ressourcentyp zurück, der einen Auflistungsvorgang unterstützt. Der Name des Vorgangs muss mit `list` beginnen. Häufig werden `listKeys` und `listSecrets` verwendet. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Eindeutiger Bezeichner für die Ressource. |
| apiVersion |Ja |Zeichenfolge |API-Version eines Ressourcen-Laufzeitstatus. In der Regel im Format **jjjj-mm-tt**. |
| functionValues |Nein |object | Ein Objekt, das über Werte für die Funktion verfügt. Geben Sie dieses Objekt nur für Funktionen an, die den Empfang eines Objekts mit Parameterwerten unterstützen – z.B. **listAccountSas** für ein Speicherkonto. Ein Beispiel für die Übergabe von Funktionswerten wird in diesem Artikel gezeigt. | 

### <a name="implementations"></a>Implementierungen

Die Verwendungsmöglichkeiten von list* werden in der folgenden Tabelle gezeigt.

| Ressourcentyp | Funktionsname |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listPolicies](/rest/api/containerregistry/registries/listpolicies) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | listKeys |
| Microsoft.MachineLearningServices/workspaces | listKeys |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
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

### <a name="remarks"></a>Anmerkungen

Geben Sie die Ressource entweder mithilfe des Ressourcennamens oder der [resourceId-Funktion](#resourceid) an. Wenn Sie eine list-Funktion in der gleichen Vorlage verwenden, die auch die referenzierte Ressource bereitstellt, verwenden Sie den Ressourcennamen.

Bei Verwendung einer **list**-Funktion mit einer Ressource mit bedingter Bereitstellung wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die **list**-Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie die **if**-Funktion, um sicherzustellen, dass die Funktion nur ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „list“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der [if](resource-group-template-functions-logical.md#if)-Funktion.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) zeigt, wie die Primär- und Sekundärschlüssel von einem Speicherkonto im Abschnitt „outputs“ zurückgegeben werden können. Es wird auch ein SAS-Token für das Speicherkonto zurückgegeben. 

Um das SAS-Token abzurufen, übergeben Sie ein Objekt für die Ablaufzeit. Die Ablaufzeit muss in der Zukunft liegen. Dieses Beispiel soll Ihnen zeigen, wie Sie die Listenfunktionen verwenden können. In der Regel würden Sie das SAS-Token eher in einem Ressourcenwert verwenden, statt es als Ausgabewert zurückzugeben. Ausgabewerte werden im Bereitstellungsverlauf gespeichert und sind nicht sicher.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

Gibt Informationen zu einem Ressourcenanbieter und den von ihm unterstützten Ressourcentypen zurück. Wenn Sie keinen Ressourcentyp angeben, gibt die Funktion alle unterstützten Typen für den Ressourcenanbieter zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) zeigt die Nutzungsweise der Anbieterfunktion:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Name oder eindeutiger Bezeichner einer Ressource |
| apiVersion |Nein |Zeichenfolge |API-Version der angegebenen Ressource. Schließen Sie diesen Parameter ein, wenn die Ressource nicht innerhalb der gleichen Vorlage bereitgestellt wird. In der Regel im Format **jjjj-mm-tt**. |
| 'Full' |Nein |Zeichenfolge |Ein Wert, der angibt, ob das vollständige Ressourcenobjekt zurückgegeben werden soll. Wird `'Full'` nicht angegeben, wird nur das Eigenschaftenobjekt der Ressource zurückgegeben. Das vollständige Objekt enthält Werte wie die Ressourcen-ID und den Standort. |

### <a name="return-value"></a>Rückgabewert

Jeder Ressourcentyp gibt andere Eigenschaften für die Verweisfunktion zurück. Die Funktion gibt kein einzelnes vordefiniertes Format zurück. Darüber hinaus variiert der zurückgegebene Wert abhängig davon, ob Sie das vollständige Objekt angegeben haben. Um die Eigenschaften für einen Ressourcentyp anzuzeigen, geben Sie das Objekt wie im Beispiel gezeigt im Abschnitt „outputs“ zurück.

### <a name="remarks"></a>Anmerkungen

Die Verweisfunktion ruft den Runtime-Status einer zuvor bereitgestellten Ressource oder einer in der aktuellen Vorlage bereitgestellten Ressource ab. Dieser Artikel zeigt Beispiele für beide Szenarios. Wenn Sie auf eine Ressource in der aktuellen Vorlage verweisen, stellen Sie nur den Ressourcennamen als Parameter bereit. Wenn Sie auf eine zuvor bereitgestellte Ressource verweisen, geben Sie die Ressourcen-ID und eine API-Version für die Ressource an. Sie können gültige API-Versionen für Ihre Ressource im [Vorlagenverweis](/azure/templates/) ermitteln.

Die Verweisfunktion kann nur in den Eigenschaften einer Ressourcendefinition und im Abschnitt „outputs“ einer Vorlage oder Bereitstellung verwendet werden. Wenn sie mit [Eigenschafteniteration](resource-group-create-multiple.md#property-iteration) verwendet wird, können Sie die Verweisfunktion für `input` verwenden, weil der Ausdruck der Ressourceneigenschaft zugewiesen wird. Sie können sie nicht mit `count` verwenden, weil die Anzahl bestimmt werden muss, bevor die Verweisfunktion aufgelöst wird.

Mithilfe der Referenzfunktion deklarieren Sie implizit, dass eine Ressource von einer anderen abhängt, wenn die referenzierte Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird und Sie anhand des Ressourcennamens (nicht der Ressourcen-ID) auf die Ressource verweisen. Die dependsOn-Eigenschaft muss nicht zusätzlich verwendet werden. Die Funktion wird erst dann ausgewertet, wenn die Ressource, auf die verwiesen wird, die Bereitstellung abgeschlossen hat.

Bei Verwendung der **reference**-Funktion mit einer Ressource mit bedingter Bereitstellung wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird.  Es wird eine Fehlermeldung angezeigt, wenn die **reference**-Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie die **if**-Funktion, um sicherzustellen, dass die Funktion nur ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „reference“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der [if](resource-group-template-functions-logical.md#if)-Funktion.

Um die Eigenschaftennamen und Werte für einen Ressourcentyp anzuzeigen, erstellen Sie eine Vorlage, die das Objekt im Abschnitt „outputs“ zurückgibt. Wenn Sie über eine Ressource dieses Typs verfügen, gibt Ihre Vorlage das Objekt zurück, ohne neue Ressourcen bereitzustellen. 

Sie verwenden in der Regel die Funktion **Verweis**, um einen bestimmten Wert aus einem Objekt zurückzugeben, wie z.B. den Blob-Endpunkt-URI oder den vollständig qualifizierten Domänennamen.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Verwenden Sie `'Full'`, wenn Sie Ressourcenwerte benötigen, die nicht Teil des Eigenschaftsschemas sind. Rufen Sie zum Festlegen von Schlüsseltresor-Zugriffsrichtlinien beispielsweise die Identitätseigenschaften für einen virtuellen Computer ab.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
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

Das vollständige Beispiel der vorherigen Vorlage finden Sie unter [Windows to Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json) (Windows und Key Vault). Ein ähnliches Beispiel ist für [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json) verfügbar.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) stellt eine Ressource bereit und verweist auf diese Ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
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

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>Ressourcengruppe

`resourceGroup()`

Gibt ein Objekt zurück, das die aktuelle Ressourcengruppe darstellt. 

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Anmerkungen

Die Funktion `resourceGroup()` kann nicht in einer Vorlage verwendet werden, die [auf der Abonnementebene bereitgestellt](deploy-to-subscription.md) wird. Sie kann nur in Vorlagen verwendet werden, die in einer Ressourcengruppe bereitgestellt werden.

Die Funktion „resourceGroup“ wird häufig verwendet, um Ressourcen am gleichen Speicherort wie die Ressourcengruppe zu erstellen. Das folgende Beispiel nutzt den Speicherort der Ressourcengruppe, um einer Website den Speicherort zuzuweisen.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) gibt die Eigenschaften der Ressourcengruppe zurück.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Im vorherigen Beispiel wird ein Objekt im folgenden Format zurückgegeben:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Gibt den eindeutigen Bezeichner einer Ressource zurück. Diese Funktion wird verwendet, wenn der Ressourcenname zweideutig ist oder nicht innerhalb der gleichen Vorlage zur Verfügung gestellt wird. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nein |Zeichenfolge (im GUID-Format) |Der Standardwert ist das aktuelle Abonnement. Geben Sie diesen Wert an, wenn Sie eine Ressource in einem anderen Abonnement abrufen möchten. |
| resourceGroupName |Nein |Zeichenfolge |Der Standardwert ist die aktuelle Ressourcengruppe. Geben Sie diesen Wert an, wenn Sie eine Ressource in einer anderen Ressourcengruppe abrufen möchten. |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein |Zeichenfolge |Nächstes Ressourcen-Namensegment, wenn die Ressource geschachtelt ist. |

### <a name="return-value"></a>Rückgabewert

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmerkungen

Bei Verwendung mit einer [Bereitstellung auf Abonnementebene](deploy-to-subscription.md) kann die Funktion `resourceId()` nur die ID der auf dieser Ebene bereitgestellten Ressourcen abrufen. Sie können beispielsweise die ID einer Richtlinien- oder Rollendefinition, nicht aber die ID eines Speicherkontos abrufen. Für Bereitstellungen in einer Ressourcengruppe gilt das Gegenteil. Hier kann die Ressourcen-ID von auf der Abonnementebene bereitgestellten Ressourcen nicht abgerufen werden.

Welche Parameterwerte Sie angeben, hängt davon ab, ob sich die Ressource in der gleichen Abonnement- und Ressourcengruppe befindet wie die aktuelle Bereitstellung. Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in derselben Abonnement- und Ressourcengruppe Folgendes:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in derselben Abonnementgruppe, aber einer anderen Ressourcengruppe Folgendes:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in einer anderen Abonnement- und Ressourcengruppe Folgendes:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für eine Datenbank in einer anderen Ressourcengruppe Folgendes:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID einer Ressource auf Abonnementebene bei der Bereitstellung im Abonnementkontext Folgendes:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Sie müssen diese Funktion oft nutzen, wenn Sie ein Speicherkonto oder einen virtuellen Computer in einer alternativen Ressourcengruppe verwenden. Das folgende Beispiel zeigt, wie eine Ressource von einer externen Ressourcengruppe leicht verwendet werden kann:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) gibt die Ressourcen-ID für ein Speicherkonto in der Ressourcengruppe zurück:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| sameRGOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Zeichenfolge | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

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

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) zeigt die im Abschnitt „outputs“ abgerufene subscription-Funktion. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](resource-group-linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](resource-group-template-deploy.md).

