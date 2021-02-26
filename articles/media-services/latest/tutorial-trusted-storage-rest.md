---
title: 'Azure Media Services: Vertrauenswürdiger Speicher'
description: In diesem Tutorial wird beschrieben, wie Sie vertrauenswürdigen Speicher für Azure Media Services aktivieren, verwaltete Identitäten für vertrauenswürdigen Speicher verwenden und für Azure-Dienste den Zugriff auf ein Speicherkonto gewähren, wenn eine Firewall oder ein VPN genutzt wird.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: b20da64c135f7e54a7f6bc74beea073140a1e0a9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100892"
---
# <a name="tutorial-media-services-trusted-storage"></a>Tutorial: Media Services: Vertrauenswürdiger Speicher

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> - Aktivieren von vertrauenswürdigem Speicher für Azure Media Services
> - Verwenden von verwalteten Identitäten für vertrauenswürdigen Speicher
> - Gewähren des Zugriffs auf ein Speicherkonto für Azure-Dienste, wenn eine Zugriffssteuerung wie eine Firewall oder ein VPN für das Netzwerk genutzt wird

Mit der 2020-05-01-API können Sie vertrauenswürdigen Speicher aktivieren, indem Sie eine verwaltete Identität einem Media Services-Konto zuordnen.

Für Media Services ist der automatische Zugriff auf Ihr Speicherkonto per Systemauthentifizierung möglich. Von Media Services wird überprüft, ob sich das Media Services-Konto und das Speicherkonto unter demselben Abonnement befinden. Darüber hinaus wird mit der rollenbasierten Zugriffssteuerung für Azure Resource Manager überprüft, ob der Benutzer, von dem die Zuordnung hinzufügt wird, Zugriff auf das Speicherkonto hat.

Falls Sie aber die Netzwerkzugriffssteuerung einsetzen möchten, um Ihr Speicherkonto zu schützen und vertrauenswürdigen Speicher zu ermöglichen, ist die Authentifizierung mit [verwalteten Identitäten](concept-managed-identities.md) erforderlich. Dadurch kann von Media Services per vertrauenswürdigem Speicherzugriff auf das Speicherkonto zugegriffen werden, das mit einer Firewall oder mit einer VNET-Einschränkung konfiguriert wurde.

## <a name="overview"></a>Übersicht

> [!IMPORTANT]
> Verwenden Sie die 2020-05-01-API für alle Anforderungen an Media Services.

Hier sind die allgemeinen Schritte zum Erstellen von vertrauenswürdigem Speicher für Media Services angegeben:

1. Erstellen Sie eine Ressourcengruppe.
1. Erstellen Sie ein Speicherkonto.
1. Fragen Sie das Speicherkonto ab, bis es bereit ist. Wenn das Speicherkonto bereit ist, wird über die Anforderung die Dienstprinzipal-ID zurückgegeben.
1. Suchen Sie nach der ID der Rolle *Mitwirkender an Storage-Blobdaten*.
1. Rufen Sie den Autorisierungsanbieter auf, und fügen Sie eine Rollenzuweisung hinzu.
1. Aktualisieren Sie das Media Services-Konto, um die Authentifizierung beim Speicherkonto über die verwaltete Identität durchzuführen.
1. Löschen Sie die Ressourcen, falls Sie sie nicht weiterverwenden und dafür das Anfallen von Gebühren vermeiden möchten.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um beginnen zu können.  Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free/) erstellen.

### <a name="get-your-tenant-id-and-subscription-id"></a>Abrufen Ihrer Mandanten-ID und Abonnement-ID

Falls Sie nicht wissen, wie Sie Ihre Mandanten-ID und Abonnement-ID erhalten, helfen Ihnen die Informationen zur Ermittlung in [diesem Artikel](how-to-set-azure-tenant.md) und [diesem Artikel](how-to-set-azure-tenant.md) weiter.

### <a name="create-a-service-principal-and-secret"></a>Erstellen eines Dienstprinzipals und eines Geheimnisses

Lesen Sie den Artikel [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](access-api-howto.md), falls Sie nicht wissen, wie Sie einen Dienstprinzipal und ein Geheimnis erstellen.

## <a name="use-a-rest-client"></a>Verwenden eines REST-Clients

Dieses Skript ist für die Verwendung mit einem REST-Client bestimmt, z. B. über Visual Studio-Codeerweiterungen.  Passen Sie es an Ihre Entwicklungsumgebung an.

## <a name="set-initial-variables"></a>Festlegen von Anfangsvariablen

Dieser Teil des Skripts ist für die Verwendung in einem REST-Client vorgesehen. Sie können Variablen in Ihrer Entwicklungsumgebung auf unterschiedliche Weise nutzen.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Abrufen eines Tokens für Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Abrufen eines Tokens für die Graph-API

Dieser Teil des Skripts ist für die Verwendung in einem REST-Client vorgesehen. Sie können Variablen in Ihrer Entwicklungsumgebung auf unterschiedliche Weise nutzen.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Abrufen der Details zu Dienstprinzipalen

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Speichern der Dienstprinzipal-ID

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Speicherkonto erstellen

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Abrufen des Status des Speicherkontos

Da es eine Weile dauert, bis das Speicherkonto bereit ist, fragt diese Anforderung dessen Status ab.  Wiederholen Sie diese Anforderung, bis das Speicherkonto bereit ist.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Abrufen der Details des Speicherkontos

Rufen Sie die Eigenschaften des Speicherkontos ab, wenn es bereit ist.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Abrufen eines Tokens für ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Erstellen eines Media Services-Kontos mit einer systemseitig zugewiesenen verwalteten Identität

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Abrufen der Definition für die Speicherblobdaten-Rolle des Speichers

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Festlegen der Speicherrollenzuweisung

Die Rollenzuweisung enthält die Information, dass der Dienstprinzipal für das Media Services-Konto über die Speicherrolle *Mitwirkender an Storage-Blobdaten* verfügt.  Dies kann eine Weile dauern, und es sollte unbedingt abgewartet werden, weil das Media Services-Konto sonst ggf. nicht richtig eingerichtet wird.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Gewähren von Umgehungszugriff auf das Speicherkonto für die verwaltete Identität

Mit diesem Vorgang wird der Zugriff von der systemseitig verwalteten Identität auf die verwaltete Identität umgestellt. Auf diese Weise ist der Zugriff auf das Speicherkonto über eine Firewall möglich, da Azure-Dienste unabhängig von IP-Zugriffsregeln (ACLs) auf das Speicherkonto zugreifen können.

Warten Sie wieder ab, bis die Rolle dem Speicherkonto zugewiesen wurde, da das Media Services-Konto sonst nicht richtig eingerichtet wird.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Aktualisieren des Media Services-Kontos für die Verwendung der verwalteten Identität

Diese Anforderung muss ggf. mehrmals wiederholt werden, weil es einige Minuten dauern kann, bis die Speicherrollenzuweisung richtig verteilt wurde.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Testen des Zugriffs

Testen Sie den Zugriff, indem Sie im Speicherkonto ein Objekt erstellen.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Löschen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht beibehalten und das Anfallen weiterer Gebühren verhindern möchten, sollten Sie sie löschen.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Medienobjekts](how-to-create-asset.md)
