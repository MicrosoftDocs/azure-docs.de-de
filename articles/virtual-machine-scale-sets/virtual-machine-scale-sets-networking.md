---
title: Netzwerk für Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Konfigurationsnetzwerkeigenschaften für Azure-VM-Skalierungsgruppen.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: manayar
ms.openlocfilehash: a9141adfb1dd05efd73061379be89ddf27ab3832
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803049"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Netzwerk für Azure-VM-Skalierungsgruppen

Wenn Sie eine Azure-VM-Skalierungsgruppe über das Portal bereitstellen, werden bestimmte Standardnetzwerkeigenschaften verwendet (beispielsweise ein Azure Load Balancer mit NAT-Eingangsregeln). Dieser Artikel beschreibt die Verwendung einiger der erweiterten Netzwerkfeatures, die Sie mit Skalierungsgruppen konfigurieren können.

Alle in diesem Artikel behandelten Features können mithilfe von Azure Resource Manager-Vorlagen konfiguriert werden. Für einige Features stehen auch Beispiele für die Azure-Befehlszeilenschnittstelle und PowerShell zur Verfügung.

## <a name="accelerated-networking"></a>Beschleunigter Netzwerkbetrieb
Der beschleunigte Netzwerkbetrieb von Azure ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) für einen virtuellen Computer und somit die Verbesserung der Netzwerkleistung. Weitere Informationen zur Verwendung des beschleunigten Netzwerkbetriebs finden Sie unter den entsprechenden Abschnitten für virtuelle [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md)- oder [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)-Computer. Wenn Sie den beschleunigten Netzwerkbetrieb mit Skalierungsgruppen verwenden möchten, legen Sie in den networkInterfaceConfigurations-Einstellungen Ihrer Skalierungsgruppe die Option „EnableAcceleratedNetworking“ auf **true** fest. Beispiel:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Erstellen einer Skalierungsgruppe, die auf einen vorhandenen Azure Load Balancer verweist
Wenn eine Skalierungsgruppe über das Azure-Portal erstellt wird, wird für die meisten Konfigurationsoptionen ein neuer Load Balancer erstellt. Wenn Sie eine Skalierungsgruppe erstellen möchten, die auf einen vorhandenen Load Balancer verweisen soll, können Sie hierfür die Befehlszeilenschnittstelle verwenden. Das folgende Beispielskript erstellt einen Load Balancer und anschließend eine Skalierungsgruppe, die darauf verweist:
```bash
az network lb create \
    -g lbtest \
    -n mylb \
    --vnet-name myvnet \
    --subnet mysubnet \
    --public-ip-address-allocation Static \
    --backend-pool-name mybackendpool

az vmss create \
    -g lbtest \
    -n myvmss \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username negat \
    --ssh-key-value /home/myuser/.ssh/id_rsa.pub \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myvnet \
    --subnet mysubnet \
    --lb mylb \
    --backend-pool-name mybackendpool
```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Erstellen einer Skalierungsgruppe, die auf ein Application Gateway verweist
Um eine Skalierungsgruppe zu erstellen, die ein Application Gateway verwendet, verweisen Sie wie in dieser ARM-Vorlagenkonfiguration im Abschnitt „ipConfigurations“ Ihrer Skalierungsgruppe auf den Back-End-Adresspool des Application Gateways:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Beachten Sie, dass sich das Application Gateway im gleichen virtuellen Netzwerk wie die Skalierungsgruppe, jedoch in einem anderen Subnetz befinden muss.


## <a name="configurable-dns-settings"></a>Konfigurierbare DNS-Einstellungen
Standardmäßig werden für Skalierungsgruppen die spezifischen DNS-Einstellungen des VNETs und des Subnetzes verwendet, in dem sie erstellt wurden. Sie können die DNS-Einstellungen für eine Skalierungsgruppe allerdings direkt konfigurieren.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Erstellen einer Skalierungsgruppe mit konfigurierbaren DNS-Servern
Um mithilfe der Azure CLI eine Skalierungsgruppe mit einer benutzerdefinierten DNS-Konfiguration zu erstellen, fügen Sie dem Befehl **vmss create** das Argument **--dns-servers** und eine durch Leerzeichen getrennte Liste mit Server-IP-Adressen hinzu. Beispiel:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Um benutzerdefinierte DNS-Server in einer Azure-Vorlage zu konfigurieren, fügen Sie dem Abschnitt „networkInterfaceConfigurations“ der Skalierungsgruppe eine Eigenschaft vom Typ „dnsSettings“ hinzu. Beispiel:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Erstellen einer Skalierungsgruppe mit konfigurierbaren Domänennamen für virtuelle Computer
Um mithilfe der Befehlszeilenschnittstelle eine Skalierungsgruppe mit einem benutzerdefinierten DNS-Namen für virtuelle Computer zu erstellen, fügen Sie dem Befehl **virtual machine scale set create** das Argument **--vm-domain-name** mit dem Domänennamen als Zeichenfolge hinzu.

Um den Domänennamen in einer Azure-Vorlage zu konfigurieren, fügen Sie dem Abschnitt **networkInterfaceConfigurations** der Skalierungsgruppe eine Eigenschaft vom Typ **dnsSettings** hinzu. Beispiel:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Die Ausgabe für einen bestimmten VM-DNS-Namen hat das folgende Format: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Öffentliche IPv4-Adresse pro virtuellem Computer
Virtuelle Computer in Azure-Skalierungsgruppen benötigen im Allgemeinen keine eigene IP-Adresse. In den meisten Szenarien ist es wirtschaftlicher und sicherer, eine öffentliche IP-Adresse einem Load Balancer oder einem einzelnen virtuellen Computer (auch Jumpbox genannt) zuzuweisen, der dann eingehende Verbindungen nach Bedarf an virtuelle Computer in der Skalierungsgruppe weiterleitet (beispielsweise über NAT-Eingangsregeln).

In einigen Szenarien müssen virtuelle Computer in Skalierungsgruppen jedoch über eine eigene öffentliche IP-Adresse verfügen. Ein Beispiel sind etwa Spiele, bei denen eine Konsole eine direkte Verbindung mit einem virtuellen Cloudcomputer herstellen muss, der die Spielphysik verarbeitet. Ein weiteres Beispiel, bei dem virtuelle Computer regionsübergreifend gegenseitige externe Verbindungen herstellen müssen, sind verteilte Datenbanken.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Erstellen einer Skalierungsgruppe mit öffentlicher IP-Adresse pro virtuellem Computer
Um mithilfe der CLI eine Skalierungsgruppe zu erstellen, die jedem virtuellen Computer eine öffentliche IP-Adresse zuweist, fügen Sie dem Befehl **vmss create** den Parameter **--public-ip-per-vm** hinzu. 

Wenn Sie eine Skalierungsgruppe mithilfe einer Azure-Vorlage erstellen möchten, sollten Sie sich vergewissern, dass die API-Version der Ressource „Microsoft.Compute/virtualMachineScaleSets“ mindestens **2017-03-30** lautet. Fügen Sie dem Abschnitt „ipConfigurations“ für die Skalierungsgruppe dann eine JSON-Eigenschaft vom Typ **publicIpAddressConfiguration** hinzu. Beispiel:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Beispielvorlage: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Abfragen der öffentlichen IP-Adressen der virtuellen Computer in einer Skalierungsgruppe
Um mithilfe der CLI die öffentlichen IP-Adressen aufzulisten, die den virtuellen Computern in einer Skalierungsgruppe zugewiesen sind, verwenden Sie den Befehl **az vmss list-instance-public-ips**.

Verwenden Sie den Befehl _Get-AzPublicIpAddress_, um mit PowerShell öffentliche IP-Adressen für Skalierungsgruppen aufzulisten. Beispiel:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Sie können die öffentlichen IP-Adressen auch abfragen, indem Sie direkt auf die Ressourcen-ID der Konfiguration der öffentlichen IP-Adresse verweisen. Beispiel:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Die öffentlichen IP-Adressen, die virtuellen Computern in einer Skalierungsgruppe zugewiesen sind, können auch durch Abfragen des [Azure-Ressourcen-Explorers](https://resources.azure.com) oder der Azure-REST-API (ab Version **2017-03-30**) angezeigt werden.

So fragen Sie den [Azure-Ressourcen-Explorer](https://resources.azure.com) ab:

1. Öffnen Sie den [Azure-Ressourcen-Explorer](https://resources.azure.com) in einem Webbrowser.
1. Erweitern Sie *Abonnements* auf der linken Seite, indem Sie daneben auf *+* klicken. Falls sich unter *Abonnements* nur ein einzelnes Element befindet, ist der Bereich möglicherweise bereits erweitert.
1. Erweitern Sie Ihr Abonnement.
1. Erweitern Sie Ihre Ressourcengruppe.
1. Erweitern Sie *Anbieter*.
1. Erweitern Sie *Microsoft.Compute*.
1. Erweitern Sie *virtualMachineScaleSets*.
1. Erweitern Sie Ihre Skalierungsgruppe.
1. Klicken Sie auf *publicipaddresses*.

So fragen Sie die Azure-REST-API ab:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Beispielausgabe des [Azure-Ressourcen-Explorers](https://resources.azure.com) und der Azure-REST-API:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Mehrere IP-Adressen pro NIC
Jeder NIC, die an einen virtuellen Computer in einer Skalierungsgruppe angefügt ist, kann mindestens eine IP-Konfiguration zugeordnet sein. Jeder Konfiguration wird eine private IP-Adresse zugewiesen. Außerdem kann jeder Konfiguration eine öffentliche IP-Adressressource zugeordnet sein. Informationen dazu, wie viele IP-Adressen einer NIC zugewiesen werden und wie viele öffentliche IP-Adressen Sie in einem Azure-Abonnement verwenden können, finden Sie unter [Netzwerkgrenzwerte – Azure Resource Manager](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Mehrere NICs vor virtuellem Computer
Ein virtueller Computer kann je nach Größe über bis zu acht NICs verfügen. Informationen zur maximalen Anzahl von NICs pro Computer finden Sie im Artikel [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/windows/sizes.md). Alle mit einer VM-Instanz verbundenen NICs müssen eine Verbindung mit dem gleichen virtuellen Netzwerk herstellen. Die NICs können eine Verbindung mit verschiedenen Subnetzen herstellen, aber alle Subnetze müssen Teil des gleichen virtuellen Netzwerks sein.

Das folgende Beispiel ist ein Skalierungsgruppen-Netzwerkprofil mit mehreren NIC-Einträgen und mehreren öffentlichen IP-Adressen pro virtuellem Computer:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Netzwerksicherheitsgruppe und Anwendungssicherheitsgruppen pro Skalierungsgruppe
Mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md) können Sie ein- und ausgehenden Datenverkehr von Azure-Ressourcen in einem virtuellen Azure-Netzwerk unter Verwendung von Sicherheitsregeln filtern. Mithilfe von [Anwendungssicherheitsgruppen](../virtual-network/security-overview.md#application-security-groups) können Sie die Netzwerksicherheit von Azure-Ressourcen steuern und sie als Erweiterung Ihrer Anwendungsstruktur gruppieren.

Netzwerksicherheitsgruppen können direkt auf eine Skalierungsgruppe angewendet werden. Hierzu muss dem Abschnitt mit den Netzwerkschnittstellenkonfigurationen der Skalierungsgruppen-VM-Eigenschaften ein Verweis hinzugefügt werden.

Anwendungssicherheitsgruppen (ASGs) können auch direkt für eine Skalierungsgruppe angegeben werden. Hierzu muss dem Abschnitt mit den IP-Konfigurationen der Netzwerkschnittstelle in den VM-Eigenschaften der Skalierungsgruppe ein Verweis hinzugefügt werden.

Beispiel: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Verwenden Sie den Befehl `az vmss show`, um zu überprüfen, ob Ihre Netzwerksicherheitsgruppe Ihrer Skalierungsgruppe zugeordnet ist. Im folgenden Beispiel wird `--query` verwendet, um die Ergebnisse zu filtern und in der Ausgabe nur den relevanten Abschnitt anzuzeigen.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Verwenden Sie den Befehl `az vmss show`, um zu überprüfen, ob Ihre Anwendungssicherheitsgruppe Ihrer Skalierungsgruppe zugeordnet ist. Im folgenden Beispiel wird `--query` verwendet, um die Ergebnisse zu filtern und in der Ausgabe nur den relevanten Abschnitt anzuzeigen.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu virtuellen Azure-Netzwerken erhalten Sie unter [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
