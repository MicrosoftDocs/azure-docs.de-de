---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer – Azure-CLI | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer mithilfe der Azure-Befehlszeilenschnittstelle (CLI) konfigurieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: 1b39196c489927474c0912b316de5ff3b3dbb956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681409"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer mithilfe der Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Die folgenden Beispiele für Azure CLI-Befehle gehen von einer vorhandenen einfachen Umgebung aus. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](quick-create-cli.md)beschrieben.

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers

Um in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101* zu erstellen, führen Sie die folgenden Schritte aus:

1. Falls noch nicht geschehen, installieren und konfigurieren Sie die neueste [Azure CLI](/cli/azure/install-azure-cli), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

2. Erstellen Sie mit dem Befehl [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse für den virtuellen Computer. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

    > [!NOTE]
    > Möglicherweise benötigen Sie andere Werte für Ihre Argumente in diesem und den nachfolgenden Schritten. Dies ist von Ihrer Umgebung abhängig.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Erwartete Ausgabe:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: Name der Ressourcengruppe, in der die öffentliche IP-Adresse erstellt werden soll.
   * `--name`: Der Name der öffentlichen IP-Adresse.
   * `--location`: Azure-Region, in der die öffentliche IP-Adresse erstellt werden soll.

3. Führen Sie den Befehl [az network nic create](/cli/azure/network/nic) aus, um eine NIC mit statischer privater IP-Adresse zu erstellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Erwartete Ausgabe:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parameter:

    * `--private-ip-address`: Statische private IP-Adresse für die Netzwerkkarte.
    * `--vnet-name`: Der Name des VNET, in dem die NIC erstellt werden soll.
    * `--subnet`: Der Name des Subnetzes, in dem die NIC erstellt werden soll.

4. Führen Sie den Befehl [azure vm create](/cli/azure/vm/nic) aus, um den virtuellen Computer unter Verwendung der zuvor erstellten öffentlichen IP-Adresse und Netzwerkkarte zu erstellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Erwartete Ausgabe:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Andere Parameter als die grundlegenden Parameter von [az vm create](/cli/azure/vm).

   * `--nics`: Der Name der NIC, mit der die VM verknüpft wird.
   
Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist (z.B. beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-cli.md)). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private).

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer

Führen Sie den folgenden Befehl der Azure-Befehlszeilenschnittstelle aus, und sehen Sie sich die Werte für *Private IP alloc-method* und *Private IP address* an:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Erwartete Ausgabe:

```json
"192.168.1.101"
```

Um die spezifischen IP-Informationen der NIC für diesen virtuellen Computer anzuzeigen, fragen Sie speziell die NIC ab:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Die Ausgabe sieht etwa wie folgt aus:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer

Es ist nicht möglich, über die Azure-Befehlszeilenschnittstelle für Azure Resource Manager-Bereitstellungen eine statische private IP-Adresse von einer NIC zu entfernen. Die Voraussetzungen lauten wie folgt:
- Erstellen einer neuen NIC, die eine dynamische IP-Adresse verwendet
- Festlegen der NIC auf dem virtuellen Computer auf die neu erstellte NIC 

Um die in den vorhergehenden Befehlen verwendete Netzwerkkarte für die VM zu ändern, führen Sie die folgenden Schritte aus:

1. Führen Sie den Befehl **azure network nic create** aus, um eine neue NIC mit dynamischer IP-Zuordnung mit einer neuen IP-Adresse zu erstellen. Die Zuordnungsmethode ist **Dynamisch**, da keine IP-Adresse angegeben wird.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Erwartete Ausgabe:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Führen Sie den Befehl **azure vm set** aus, um die vom virtuellen Computer verwendete Netzwerkkarte zu ändern.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Erwartete Ausgabe:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Wenn der virtuelle Computer groß genug ist, um mehr als eine NIC zu enthalten, führen Sie den Befehl **azure network nic delete** zum Löschen der alten NIC aus.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwaltung von [privaten IP-Adressen](virtual-network-network-interface-addresses.md).
