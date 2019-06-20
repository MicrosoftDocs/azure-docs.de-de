---
title: Erstellen einer Linux-Umgebung mit der Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Speicher, einen virtuellen Linux-Computer, ein virtuelles Netzwerk mitsamt Subnetz, einen Load Balancer, eine NIC, eine öffentliche IP-Adresse und eine Netzwerksicherheitsgruppe über die Azure-CLI von Grund auf neu erstellen.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: eb4c5897cdadecd074c2764faceeed13f4c724c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328635"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Erstellen einer vollständigen Linux-VM mit der Azure CLI
Um schnell einen virtuellen Computer (VM) in Azure zu erstellen, können Sie einen einzelnen Azure CLI-Befehl mit Standardwerten verwenden, um alle erforderlichen unterstützenden Ressourcen zu erstellen. Ressourcen wie ein virtuelles Netzwerk, eine öffentliche IP-Adresse und Regeln für Netzwerksicherheitsgruppen werden automatisch erstellt. Um mehr Kontrolle über Ihre Umgebung in der Produktionsumgebung zu erhalten, können Sie diese Ressourcen im Voraus erstellen und Ihre virtuellen Computer ihnen dann hinzufügen. Dieser Artikel führt Sie schrittweise durch das Erstellen eines virtuellen Computers und der einzelnen unterstützenden Ressourcen.

Achten Sie darauf, dass Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u.a. *myResourceGroup*, *myVnet* und *myVM*.

## <a name="create-resource-group"></a>Ressourcengruppe erstellen
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Eine Ressourcengruppe muss vor einem virtuellen Computer und unterstützenden virtuellen Netzwerkressourcen erstellt werden. Erstellen Sie die Ressourcengruppe mithilfe von [az group create](/cli/azure/group). Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Standardmäßig erfolgt die Ausgabe von Azure CLI-Befehlen in JSON (JavaScript Object Notation). Um beispielsweise die Standardausgabe in eine Liste oder Tabelle zu ändern, verwenden Sie [az configure --output](/cli/azure/reference-index). Ferner können Sie `--output` an beliebige Befehle anfügen, um eine einmalige Änderung des Ausgabeformats zu erreichen. Das folgende Beispiel zeigt die JSON-Ausgabe des Befehls `az group create`:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes
Als Nächstes erstellen Sie ein virtuelles Netzwerk in Azure und ein Subnetz, in dem Sie Ihre virtuellen Computer erstellen können. Geben Sie [az network vnet create](/cli/azure/network/vnet) an, um ein virtuelles Netzwerk namens *myVnet* mit dem Adresspräfix *192.168.0.0/16* zu erstellen. Sie können auch ein Subnetz namens *mySubnet* mit dem Adresspräfix *192.168.1.0/24* erstellen:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Die Ausgabe zeigt das Subnetz als innerhalb des virtuellen Netzwerks logisch erstellt an:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Erstellen Sie nun mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse. Mit dieser öffentlichen IP-Adresse können Sie über das Internet eine Verbindung mit Ihren VMs herstellen. Da die Standardadresse dynamisch ist, erstellen Sie einen benannten DNS-Eintrag mit dem Parameter `--domain-name-label`. Im folgenden Beispiel wird eine öffentliche IP-Adresse namens *myPublicIP* mit dem DNS-Namen *mypublicdns* erstellt. Da der DNS-Name eindeutig sein muss, geben Sie einen eigenen eindeutigen DNS-Namen an:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Ausgabe:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Übernehmen Sie eine Netzwerksicherheitsgruppe für eine virtuelle NIC oder ein Subnetz, um den ein- und ausgehenden Netzwerkdatenverkehrs Ihrer VMs zu steuern. Im folgenden Beispiel wird mithilfe von [az network nsg create](/cli/azure/network/nsg) eine Netzwerksicherheitsgruppe mit dem Namen *myNetworkSecurityGroup* erstellt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Sie definieren Regeln, die bestimmten Datenverkehr zulassen oder verweigern. Fügen Sie die eingehende Regel mit [az network nsg rule create](/cli/azure/network/nsg/rule) hinzu, um eingehende Verbindungen an Port 22 zuzulassen (um SSH zu unterstützen). Im folgenden Beispiel wird eine Regel mit dem Namen *myNetworkSecurityGroupRuleSSH* erstellt:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Um eingehende Verbindungen an Port 80 (für Webdatenverkehr) zu ermöglichen, fügen Sie eine weitere Regel für die Netzwerksicherheitsgruppe hinzu. Im folgenden Beispiel wird eine Regel mit dem Namen *myNetworkSecurityGroupRuleHTTP* erstellt:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Untersuchen Sie die Netzwerksicherheitsgruppe und die Regeln mit [az network nsg show](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Ausgabe:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Erstellen einer virtuellen Netzwerkkarte
Virtuelle Netzwerkkarten (NICs) sind programmgesteuert verfügbar, da Sie Regeln für ihre Verwendung anwenden können. Abhängig von der [VM-Größe](sizes.md) können Sie mehrere virtuelle NICs bei einem virtuellen Computer anfügen. Mit dem folgenden Befehl [az network nic create](/cli/azure/network/nic) erstellen Sie eine Netzwerkkarte namens *myNic*, die Sie Ihrer Netzwerksicherheitsgruppe zuordnen. Die öffentliche IP-Adresse *myPublicIP* ist auch der virtuellen Netzwerkkarte zugeordnet.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Ausgabe:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Erstellen einer Verfügbarkeitsgruppe
Mit Verfügbarkeitsgruppen können die virtuellen Computer auf Fehler- und Updatedomänen verteilt werden. Obwohl Sie derzeit nur eine VM erstellen können, wird empfohlen, mit Verfügbarkeitsgruppen zu arbeiten, um künftige Erweiterungen zu erleichtern. 

Durch Fehlerdomänen wird eine Gruppe virtueller Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Die innerhalb der Verfügbarkeitsgruppe konfigurierten virtuellen Computer werden standardmäßig auf bis zu drei Fehlerdomänen verteilt. Dadurch wirkt sich ein Hardwareproblem in einer dieser Fehlerdomänen nicht auf jeden virtuellen Computer aus, auf dem Ihre App ausgeführt wird.

Updatedomänen definieren Gruppen virtueller Computer und zugrunde liegender physischer Hardwarekomponenten, die gleichzeitig neu gestartet werden können. Während einer geplanten Wartung werden die Updatedomänen möglicherweise nicht der Reihe nach neu gestartet. Es wird aber jeweils nur eine Updatedomäne neu gestartet.

Azure verteilt virtuelle Computer automatisch auf die Fehler- und Updatedomänen, wenn sie in einer Verfügbarkeitsgruppe enthalten sind. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](manage-availability.md).

Erstellen Sie mit [az vm availability-set create](/cli/azure/vm/availability-set) eine Verfügbarkeitsgruppe für Ihre VM. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *myAvailabilitySet* erstellt:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Die Ausgabe gibt Fehler- und Updatedomänen an:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Erstellen einer VM
Sie haben die Netzwerkressourcen zur Unterstützung von über das Internet erreichbaren virtuellen Computern erstellt. Nun erstellen Sie eine VM, die Sie mit einem SSH-Schlüssel schützen. In diesem Beispiel wird eine Ubuntu-VM basierend auf dem aktuellen LTS-Stand erstellt. Mithilfe von [az vm image list](/cli/azure/vm/image) können Sie, wie unter [Suchen von Azure-VM-Images](cli-ps-findimage.md) beschrieben, weitere Images finden.

Legen Sie einen SSH-Schlüssel für die Authentifizierung fest. Wenn Sie nicht über ein öffentliches SSH-Schlüsselpaar verfügen, können Sie [eines erstellen](mac-create-ssh-keys.md) oder den Parameter `--generate-ssh-keys` angeben, damit es für Sie erstellt wird. Wenn Sie bereits über ein Schlüsselpaar verfügen, verwendet dieser Parameter in `~/.ssh` die vorhandenen Schlüssel.

Erstellen Sie die VM, indem Sie alle Ressourcen und Informationen mit dem Befehl [az vm create](/cli/azure/vm) zusammenbringen. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Stellen Sie per SSH eine Verbindung mit Ihrer VM mit dem DNS-Eintrag her, den Sie angegeben haben, als Sie die öffentliche IP-Adresse erstellt haben. Dieser `fqdn`-Wert wird in der Ausgabe angezeigt, nachdem Sie Ihre VM erstellt haben:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ausgabe:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Sie können NGINX installieren und den Datenverkehr zur VM anzeigen. Installieren Sie NGINX wie folgt:

```bash
sudo apt-get install -y nginx
```

Um die standardmäßige NGINX-Website in Aktion zu sehen, öffnen Sie Ihren Webbrowser, und geben Sie Ihren FQDN ein:

![NGINX-Standardwebsite für Ihren virtuellen Computer](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportieren als Vorlage
Was ist, wenn Sie nun eine weitere Entwicklungsumgebung mit den gleichen Parametern oder eine entsprechende Produktionsumgebung erstellen möchten? Der Resource Manager verwendet die JSON-Vorlagen, die alle Parameter für Ihre Umgebung definieren. Sie erstellen ganze Umgebungen durch Verweisen auf diese JSON-Vorlage. Sie können [JSON-Vorlagen manuell erstellen](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder eine vorhandene Umgebung exportieren, um eine JSON-Vorlage zu erstellen. Verwenden Sie [az group export](/cli/azure/group) wie folgt, um Ihre Ressourcengruppe zu exportieren:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Dieser Befehl erstellt die `myResourceGroup.json`-Datei in Ihrem aktuellen Arbeitsverzeichnis. Wenn Sie eine Umgebung anhand dieser Vorlage erstellen, werden Sie aufgefordert, alle Ressourcennamen anzugeben. Sie können diese Namen in Ihre Vorlagendatei eintragen, indem Sie den Parameter `--include-parameter-default-value` dem Befehl `az group export` hinzufügen. Bearbeiten Sie die JSON-Vorlage zum Angeben der Ressourcennamen, oder [erstellen Sie eine Datei „parameters.json“](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), die die Ressourcennamen angibt.

Verwenden Sie zum Erstellen einer Umgebung aus Ihrer Vorlage [az group deployment create](/cli/azure/group/deployment) wie folgt:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Möglicherweise möchten Sie [weitere Informationen zum Bereitstellen von Vorlagen](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)lesen. Erfahren Sie, wie Sie Umgebungen unter Verwendung der Parameterdatei schrittweise aktualisieren und von einem zentralen Speicherort aus auf Vorlagen zugreifen.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt beginnen, mit mehreren Netzwerkkomponenten und VMs zu arbeiten. Sie können diese Beispielumgebung nutzen, um Ihre Anwendung mithilfe der hier eingeführten zentralen Komponenten zu erstellen.
