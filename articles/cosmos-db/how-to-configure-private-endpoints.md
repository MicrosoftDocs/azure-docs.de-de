---
title: Konfigurieren von Azure Private Link für ein Azure Cosmos-Konto
description: Erfahren Sie, wie Sie Azure Private Link für den Zugriff auf ein Azure Cosmos-Konto über eine private IP-Adresse in einem virtuellen Netzwerk einrichten.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/18/2020
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4ba4e5f462a3cc88de5b23b32a5e749f9363e93f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081891"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurieren von Azure Private Link für ein Azure Cosmos-Konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Durch Verwendung von Azure Private Link können Sie eine Verbindung mit einem Azure Cosmos-Konto über einen privaten Endpunkt herstellen. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in einem Subnetz innerhalb Ihres virtuellen Netzwerks. Sie können dann den Zugriff auf ein Azure Cosmos-Konto über private IP-Adressen einschränken. Durch die Kombination von Private Link und eingeschränkten NSG-Richtlinien kann das Risiko der Datenexfiltration verringert werden. Weitere Informationen zu privaten Endpunkten finden Sie im Artikel zu [Azure Private Link](../private-link/private-link-overview.md).

> [!NOTE]
> Private Link verhindert nicht, dass Ihre Azure Cosmos-Endpunkte durch ein öffentliches DNS aufgelöst werden. Das Filtern eingehender Anforderungen erfolgt auf Anwendungsebene, nicht auf Transport- oder Netzwerkebene.

Private Link ermöglicht Benutzern den Zugriff auf ein Azure Cosmos-Konto innerhalb des virtuellen Netzwerks oder über ein mittels Peering verbundenes Netzwerk. Auf Ressourcen, die Private Link zugeordnet sind, kann auch lokal über privates Peering über VPN oder Azure ExpressRoute zugegriffen werden. 

Mithilfe der automatischen oder manuellen Genehmigungsmethode können Sie eine Verbindung mit einem Azure Cosmos-Konto herstellen, das über Private Link konfiguriert wurde. Weitere Informationen finden Sie in der Dokumentation zu Private Link im Abschnitt zum [Genehmigungsworkflow](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow). 

In diesem Artikel werden die Schritte zum Erstellen eines privaten Endpunkts beschrieben. Dabei wird vorausgesetzt, dass Sie die automatische Genehmigungsmethode verwenden.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Erstellen eines privaten Endpunkts über das Azure-Portal

Führen Sie die folgenden Schritte aus, um über das Azure-Portal einen privaten Endpunkt für ein vorhandenes Azure Cosmos-Konto zu erstellen:

1. Wählen Sie im Bereich **Alle Ressourcen** ein Azure Cosmos-Konto aus.

1. Wählen Sie in der Liste der Einstellungen die Option **Private Endpunktverbindungen** und dann **Privater Endpunkt** aus:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Auswahl zum Erstellen eines privaten Endpunkts über das Azure-Portal":::

1. Geben Sie im Bereich **Privaten Endpunkt erstellen – Grundlagen** folgende Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie eine Ressourcengruppe aus.|
    | **Instanzendetails** |  |
    | Name | Geben Sie einen beliebigen Namen für Ihren privaten Endpunkt ein. Wenn dieser Name vergeben ist, erstellen Sie einen eindeutigen Namen. |
    |Region| Wählen Sie die Region aus, in der Sie Private Link bereitstellen möchten. Erstellen Sie den privaten Endpunkt am gleichen Standort, an dem sich auch das virtuelle Netzwerk befindet.|
    |||
1. Klicken Sie auf **Weiter: Ressource** aus.
1. Geben Sie unter **Privaten Endpunkt erstellen – Ressource** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    |Verbindungsmethode  | Wählen Sie **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** aus. <br/><br/> Sie können dann eine Ihrer Ressourcen zum Einrichten von Private Link auswählen. Sie können aber auch eine Verbindung mit der Ressource eines anderen Benutzers herstellen, indem Sie eine Ressourcen-ID oder einen Alias verwenden, die bzw. der von diesem Benutzer für Sie freigegeben wurde.|
    | Subscription| Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.AzureCosmosDB/databaseAccounts** aus. |
    | Resource |Wählen Sie Ihr Azure Cosmos-Konto aus. |
    |Zielunterressource |Wählen Sie den Typ der zuzuordnenden Azure Cosmos DB-API aus. Standardmäßig ist für die SQL-, MongoDB- und Cassandra-API jeweils nur eine Auswahl möglich. Für die Gremlin- und die Tabellen-API können Sie auch **SQL** auswählen, da diese APIs mit der SQL-API interoperabel sind. |
    |||

1. Klicken Sie auf **Weiter: Konfiguration** aus.
1. Geben Sie unter **Privaten Endpunkt erstellen – Konfiguration** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    |**Netzwerk**| |
    | Virtuelles Netzwerk| Wählen Sie Ihr virtuelles Netzwerk aus. |
    | Subnet | Wählen Sie das Subnetz aus. |
    |**Private DNS-Integration**||
    |Integration in eine private DNS-Zone |Wählen Sie **Ja** aus. <br><br/> Für die Herstellung einer privaten Verbindung mit Ihrem privaten Endpunkt benötigen Sie einen DNS-Eintrag. Es wird empfohlen, den privaten Endpunkt in eine private DNS-Zone zu integrieren. Sie können auch Ihre eigenen DNS-Server verwenden oder DNS-Einträge mithilfe der Hostdateien auf Ihren virtuellen Computern erstellen. |
    |Private DNS-Zone |Wählen Sie **privatelink.documents.azure.com** aus. <br><br/> Die private DNS-Zone wird automatisch bestimmt. Sie können sie nicht über das Azure-Portal ändern.|
    |||

1. Klicken Sie auf **Überprüfen + erstellen**. Auf der Seite **Überprüfen + erstellen** überprüft Azure Ihre Konfiguration.
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Wenn Sie Private Link für ein Azure Cosmos-Konto genehmigt haben, ist die Option **Alle Netzwerke** im Bereich **Firewall und virtuelle Netzwerke** im Azure-Portal nicht verfügbar.

Die folgende Tabelle veranschaulicht die Zuordnung zwischen verschiedenen Arten von Azure Cosmos-Konto-APIs, unterstützten Unterressourcen und entsprechenden Namen für die private Zone. Sie können auch über die SQL-API auf das Gremlin- und das Tabellen-API-Konto zugreifen, sodass für diese APIs zwei Einträge vorhanden sind.

|Art der Azure Cosmos-Konto-API  |Unterstützte Unterressourcen (oder Gruppen-IDs) |Name der privaten Zone  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabelle    |    Tabelle     |   privatelink.table.cosmos.azure.com    |
|Tabelle     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Abrufen der privaten IP-Adressen

Nach der Bereitstellung des privaten Endpunkts können Sie die IP-Adressen abfragen. Zum Anzeigen der IP-Adressen im Azure-Portal führen Sie die folgenden Schritte aus:

1. Wählen Sie **Alle Ressourcen**.
1. Suchen Sie nach dem privaten Endpunkt, den Sie zuvor erstellt haben. In diesem Fall ist das **cdbPrivateEndpoint3**.
1. Wählen Sie die Registerkarte **Übersicht** aus, um die DNS-Einstellungen und IP-Adressen anzuzeigen.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Private IP-Adressen im Azure-Portal":::

Für jeden privaten Endpunkt werden mehrere IP-Adressen erstellt:

* Eine IP-Adresse für den globalen (regionsunabhängigen) Endpunkt des Azure Cosmos-Kontos
* Eine IP-Adresse für jede Region, in der das Azure Cosmos-Konto bereitgestellt wird

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Erstellen eines privaten Endpunkts mit Azure PowerShell

Führen Sie das folgende PowerShell-Skript aus, um den privaten Endpunkt „MyPrivateEndpoint“ für ein vorhandenes Azure Cosmos-Konto zu erstellen. Ersetzen Sie die Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrieren des privaten Endpunkts in eine private DNS-Zone

Nachdem Sie den privaten Endpunkt erstellt haben, können Sie ihn mithilfe des folgenden PowerShell-Skripts in eine private DNS-Zone integrieren:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Abrufen der privaten IP-Adressen

Nach der Bereitstellung des privaten Endpunkts können Sie mit dem folgenden PowerShell-Skript die IP-Adressen und die FQDN-Zuordnung abfragen:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Erstellen eines privaten Endpunkts mit der Azure CLI

Führen Sie das folgende Azure CLI-Skript aus, um einen privaten Endpunkt namens „myPrivateEndpoint“ für ein vorhandenes Azure Cosmos-Konto zu erstellen. Ersetzen Sie die Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrieren des privaten Endpunkts in eine private DNS-Zone

Nachdem Sie den privaten Endpunkt erstellt haben, können Sie ihn mithilfe des folgenden Azure CLI-Skripts in eine private DNS-Zone integrieren:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Erstellen eines privaten Endpunkts mithilfe einer Resource Manager-Vorlage

Sie können eine private Verknüpfung einrichten, indem Sie einen privaten Endpunkt in einem Subnetz des virtuellen Netzwerks erstellen. Dazu verwenden Sie eine Azure Resource Manager-Vorlage.

Erstellen Sie mit dem folgenden Code die Resource Manager-Vorlage „PrivateEndpoint_template.json“. Mit dieser Vorlage wird ein privater Endpunkt für ein vorhandenes Azure Cosmos-SQL-API-Konto in einem vorhandenen virtuellen Netzwerk erstellt.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definieren der Parameterdatei für die Vorlage**

Erstellen Sie eine Parameterdatei für die Vorlage, und nennen Sie sie „PrivateEndpoint_parameters.json“. Fügen Sie in der Parameterdatei den folgenden Code ein:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Bereitstellen der Vorlage mithilfe eines PowerShell-Skripts**

Erstellen Sie mit dem folgenden Code ein PowerShell-Skript. Ersetzen Sie vor dem Ausführen des Skripts zunächst die Abonnement-ID, den Ressourcengruppennamen und andere Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

Im PowerShell-Skript kann die Variable `GroupId` nur einen Wert enthalten. Dieser Wert ist der API-Typ des Kontos. Zulässige Werte sind `Sql`, `MongoDB`, `Cassandra`, `Gremlin` und `Table`. Einige Azure Cosmos-Kontotypen sind über mehrere APIs zugänglich. Beispiel:

* Auf ein Gremlin-API-Konto kann über Gremlin-API- sowie über SQL-API-Konten zugegriffen werden.
* Auf ein Tabellen-API-Konto kann über Tabellen-API- sowie über SQL-API-Konten zugegriffen werden.

Für diese Konten müssen Sie einen privaten Endpunkt für jeden API-Typ erstellen. Der entsprechende API-Typ wird im `GroupId`-Array angegeben.

Nach der erfolgreichen Bereitstellung der Vorlage wird eine Ausgabe ähnlich der folgenden Abbildung angezeigt. Der Wert für `provisioningState` lautet `Succeeded`, wenn die privaten Endpunkte ordnungsgemäß eingerichtet sind.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Bereitstellungsausgabe für die Resource Manager-Vorlage":::

Nach der Bereitstellung der Vorlage werden die privaten IP-Adressen innerhalb des Subnetzes reserviert. Die Firewallregel für das Azure Cosmos-Konto ist so konfiguriert, dass nur Verbindungen über den privaten Endpunkt angenommen werden.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrieren des privaten Endpunkts in eine private DNS-Zone

Erstellen Sie mit dem folgenden Code die Resource Manager-Vorlage „PrivateZone_template.json“. Mit dieser Vorlage wird eine private DNS-Zone für ein vorhandenes Azure Cosmos-SQL-API-Konto in einem vorhandenen virtuellen Netzwerk erstellt.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Erstellen Sie mit dem folgenden Code die Resource Manager-Vorlage „PrivateZoneRecords_template.json“.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definieren der Parameterdatei für die Vorlage**

Erstellen Sie die beiden folgenden Parameterdateien für die Vorlage. Erstellen Sie die Datei „PrivateZone_parameters.json“. durch den folgenden Code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Erstellen Sie die Datei „PrivateZoneRecords_parameters.json“. durch den folgenden Code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Bereitstellen der Vorlage mithilfe eines PowerShell-Skripts**

Erstellen Sie mit dem folgenden Code ein PowerShell-Skript. Ersetzen Sie vor dem Ausführen des Skripts zunächst die Abonnement-ID, den Ressourcengruppennamen und andere Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurieren von benutzerdefiniertem DNS

Verwenden Sie möglichst eine private DNS-Zone innerhalb des Subnetzes, in dem Sie den privaten Endpunkt erstellt haben. Konfigurieren Sie die Endpunkte so, dass jede private IP-Adresse einem DNS-Eintrag zugeordnet ist. (Siehe `fqdns`-Eigenschaft in der zuvor gezeigten Antwort.)

Wenn Sie den privaten Endpunkt erstellen, können Sie ihn in eine private DNS-Zone in Azure integrieren. Wenn Sie stattdessen eine benutzerdefinierte DNS-Zone verwenden möchten, müssen Sie diese so konfigurieren, dass DNS-Einträge für alle privaten IP-Adressen hinzugefügt werden, die für den privaten Endpunkt reserviert sind.

## <a name="private-link-combined-with-firewall-rules"></a>Private Link in Kombination mit Firewallregeln

Folgende Fälle und Ergebnisse sind bei Verwendung von Private Link in Verbindung mit Firewallregeln möglich:

* Wenn Sie keine Firewallregeln konfigurieren, kann standardmäßig der gesamte Datenverkehr auf ein Azure Cosmos-Konto zugreifen.

* Wenn Sie öffentlichen Datenverkehr oder einen Dienstendpunkt konfigurieren und private Endpunkte erstellen, werden verschiedene Arten von eingehendem Datenverkehr durch den entsprechenden Typ der Firewallregel autorisiert. Wenn ein privater Endpunkt in einem Subnetz konfiguriert ist, in dem auch der Dienstendpunkt konfiguriert ist:
  * Datenverkehr zu dem vom privaten Endpunkt zugeordneten Datenbankkonto wird über den privaten Endpunkt geleitet.
  * Datenverkehr zu anderen Datenbankkonten aus dem Subnetz wird über den Dienstendpunkt geleitet.

* Wenn Sie keinen öffentlichen Datenverkehr oder Dienstendpunkt konfigurieren und private Endpunkte erstellen, ist das Azure Cosmos-Konto nur über die privaten Endpunkte zugänglich. Wenn Sie keinen öffentlichen Datenverkehr oder einen Dienstendpunkt konfigurieren, ist das Konto nach dem Ablehnen oder Löschen aller genehmigten privaten Endpunkte für das gesamte Netzwerk offen, es sei denn, PublicNetworkAccess wurde deaktiviert (siehe Abschnitt unten).

## <a name="blocking-public-network-access-during-account-creation"></a>Blockieren des Zugriffs auf öffentliche Netzwerke während der Kontoerstellung

Wie im vorherigen Abschnitt beschrieben wurde und sofern keine bestimmten Firewallregeln festgelegt wurden, kann durch das Hinzufügen eines privaten Endpunkts nur über private Endpunkte auf Ihr Azure Cosmos-Konto zugegriffen werden. Dies bedeutet, dass das Azure Cosmos-Konto über öffentlichen Datenverkehr erreicht werden könnte, nachdem es erstellt wurde und bevor ein privater Endpunkt hinzugefügt wird. Wenn Sie sicherstellen möchten, dass der Zugriff auf öffentliche Netzwerke sogar vor der Erstellung von privaten Endpunkten deaktiviert wird, können Sie das Flag `publicNetworkAccess` während der Kontoerstellung auf `Disabled` festlegen. Beachten Sie, dass dieses Flag Vorrang vor allen IP- oder VNET-Regeln hat. Der gesamte Datenverkehr in öffentlichen und virtuellen Netzwerken wird blockiert, wenn das Flag auf `Disabled` festgelegt ist, selbst wenn die Quell-IP-Adresse oder das virtuelle Netzwerk in der Firewallkonfiguration zugelassen wurde.

Ein Beispiel für die Verwendung dieses Flags finden Sie in [dieser Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/).

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Hinzufügen privater Endpunkte zu einem vorhandenen Cosmos-Konto ohne Ausfallzeit

Wenn einem vorhandenen Konto ein privater Endpunkt hinzugefügt wird, führt dies standardmäßig zu einer kurzen Ausfallzeit von etwa 5 Minuten. Befolgen Sie die nachstehenden Anweisungen, um diese Ausfallzeit zu vermeiden:

1. Fügen Sie Ihrer Firewallkonfiguration IP- oder VNET-Regeln hinzu, um die Clientverbindungen explizit zuzulassen.
1. Warten Sie 10 Minuten, um sicherzustellen, dass das Konfigurationsupdate angewandt wurde.
1. Konfigurieren Sie den neuen privaten Endpunkt.
1. Entfernen Sie die in Schritt 1 festgelegten Firewallregeln.

## <a name="port-range-when-using-direct-mode"></a>Portbereich bei Verwendung des direkten Modus

Wenn Sie Private Link mit einem Azure Cosmos-Konto über eine Verbindung im direkten Modus verwenden, müssen Sie sicherstellen, dass der gesamte TCP-Portbereich (0 bis 65535) geöffnet ist.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualisieren eines privaten Endpunkts beim Hinzufügen oder Entfernen einer Region

Sofern Sie keine private DNS-Zonengruppe verwenden, müssen Sie beim Hinzufügen oder Entfernen von Regionen in einem Azure Cosmos-Konto die DNS-Einträge für dieses Konto hinzufügen oder entfernen. Nachdem Regionen hinzugefügt oder entfernt wurden, können Sie die private DNS-Zone des Subnetzes aktualisieren, damit diese den hinzugefügten oder entfernten DNS-Einträgen und den zugehörigen privaten IP-Adressen entspricht.

Angenommen, Sie stellen ein Azure Cosmos-Konto in drei Regionen bereit: „USA, Westen“, „USA, Mitte“ und „Europa, Westen“. Wenn Sie einen privaten Endpunkt für das Konto erstellen, werden vier private IP-Adressen im Subnetz reserviert. Es gibt eine IP-Adresse für jede der drei Regionen, und es gibt eine IP-Adresse für den globalen/regionsunabhängigen Endpunkt.

Später fügen Sie dem Azure Cosmos-Konto möglicherweise eine neue Region hinzu (z. B. „USA, Osten“). Nach dem Hinzufügen der neuen Region müssen Sie einen entsprechenden DNS-Eintrag zu Ihrer privaten DNS-Zone oder zu Ihrem benutzerdefinierten DNS hinzufügen.

Beim Entfernen einer Region können die gleichen Schritte verwendet werden. Nach dem Entfernen der Region müssen Sie den entsprechenden DNS-Eintrag aus Ihrer privaten DNS-Zone oder Ihrem benutzerdefinierten DNS entfernen.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Bei Verwendung von Private Link mit einem Azure Cosmos-Konto gelten die folgenden Einschränkungen:

* Es können nicht mehr als 200 private Endpunkte in einem einzelnen Azure Cosmos-Konto vorhanden sein.

* Wenn Sie Private Link mit einem Azure Cosmos-Konto über eine Verbindung im direkten Modus verwenden, können Sie lediglich das TCP-Protokoll verwenden. Das HTTP-Protokoll wird derzeit nicht unterstützt.

* Wenn Sie die Azure Cosmos DB-API für MongoDB-Konten verwenden, wird ein privater Endpunkt nur für Konten unter Serverversion 3.6 unterstützt (d. h. Konten mit Endpunkt im Format `*.mongo.cosmos.azure.com`). Private Link wird nicht für Konten unter Serverversion 3.2 unterstützt (d. h. Konten mit Endpunkt im Format `*.documents.azure.com`). Zur Verwendung von Private Link sollten Sie alte Konten zur neuen Version migrieren.

* Wenn Sie eine Azure Cosmos DB-API für ein MongoDB-Konto verwenden, das Private Link beinhaltet, funktionieren einige Tools oder Bibliotheken unter Umständen nicht, da sie automatisch den Parameter `appName` aus der Verbindungszeichenfolge entfernen. Dieser Parameter ist erforderlich, um über einen privaten Endpunkt eine Verbindung mit dem Konto herzustellen. Einige Tools wie Visual Studio Code entfernen diesen Parameter nicht aus der Verbindungszeichenfolge und sind daher kompatibel.

* Einem Netzwerkadministrator muss mindestens die Berechtigung `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` im Azure Cosmos-Kontobereich erteilt werden, damit er automatisch genehmigte private Endpunkte erstellen kann.

### <a name="limitations-to-private-dns-zone-integration"></a>Einschränkungen bei der Integration für eine private DNS-Zone

Sofern Sie keine private DNS-Zonengruppe verwenden, werden die DNS-Einträge in der privaten DNS-Zone nicht automatisch entfernt, wenn Sie einen privaten Endpunkt löschen oder eine Region aus dem Azure Cosmos-Konto entfernen. Entfernen Sie die DNS-Einträge manuell, bevor Sie einen der folgenden Schritte ausführen:

* Hinzufügen eines neuen privaten Endpunkts, der mit dieser privaten DNS-Zone verknüpft ist
* Hinzufügen einer neuen Region zu einem Datenbankkonto mit privaten Endpunkten, die mit dieser privaten DNS-Zone verknüpft sind

Wenn Sie die DNS-Einträge nicht bereinigen, können unerwartete Probleme mit der Datenebene auftreten. Zu diesen Problemen gehören Datenausfälle in Regionen, die nach dem Entfernen des privaten Endpunkts oder dem Entfernung der Region hinzugefügt wurden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Sicherheitsfunktionen in Azure Cosmos DB finden Sie in folgenden Artikeln:

* Informationen zum Konfigurieren einer Firewall für Azure Cosmos DB finden Sie unter [Firewallunterstützung](how-to-configure-firewall.md).

* Informationen zum Konfigurieren eines VNET-Dienstendpunkts für Ihr Azure Cosmos-Konto finden Sie unter [Konfigurieren des Zugriffs über virtuelle Netzwerke](how-to-configure-vnet-service-endpoint.md).

* Weitere Informationen zu Private Link finden Sie in der Dokumentation zu [Azure Private Link](../private-link/private-link-overview.md).
