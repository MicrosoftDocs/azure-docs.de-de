---
title: 'Virtueller Computer mit mehreren IP-Adressen: Azure-Befehlszeilenschnittstelle'
titlesuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie einem virtuellen Computer mit der Azure-Befehlszeilenschnittstelle (CLI) mehrere IP-Adressen zuweisen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 3b00bbb5903156da625b7caaca9b1a2cff212421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699360"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Zuweisen von mehreren IP-Adressen zu virtuellen Computern mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In diesem Artikel wird beschrieben, wie Sie über das Azure Resource Manager-Bereitstellungsmodell mithilfe der Azure CLI einen virtuellen Computer erstellen. Ressourcen, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können nicht mehrere IP-Adressen zugewiesen werden. Weitere Informationen zu den Azure-Bereitstellungsmodellen finden Sie im Artikel zum Thema [Understand deployment models (Bereitstellungsmodelle verstehen)](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Erstellen eines virtuellen Computers mit mehreren IP-Adressen

In den folgenden Schritten wird beschrieben, wie gemäß dem Szenario beispielhaft ein virtueller Computer mit mehreren IP-Adressen erstellt werden kann. Sie können die Variablenwerte in "" ändern und die IP-Adresstypen an Ihren Implementierungsbedarf anpassen. 

1. Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli), sofern noch nicht geschehen.
2. Erstellen Sie ein Paar aus einem öffentlichen und privaten SSH-Schlüssel für Linux-VMs, indem Sie die Schritte im Artikel [Erstellen eines öffentlich-privaten SSH-Schlüsselpaars für virtuelle Linux-Computer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ausführen.
3. Melden Sie sich über eine Befehlsshell mit dem Befehl `az login` an, und wählen Sie das Abonnement aus, das Sie verwenden.
4. Erstellen Sie die VM, indem Sie das folgende Skript auf einem Linux- oder Mac-Computer ausführen. Das Skript erstellt eine Ressourcengruppe, ein virtuelles Netzwerk (VNET), eine Netzwerkkarte mit drei IP-Konfigurationen und eine VM, an die die zwei Netzwerkkarten angefügt sind. Die Netzwerkkarte, die öffentliche IP-Adresse, das virtuelle Netzwerk und die VM-Ressourcen müssen sich alle am selben Standort und im selben Abonnement befinden. Wenngleich sich die Ressourcen nicht zwingend in derselben Ressourcengruppe befinden müssen, ist dies im folgenden Skript der Fall.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Zusätzlich zur VM mit einer Netzwerkkarte mit drei IP-Konfigurationen wird mit dem Skript Folgendes erstellt:

- Ein einzelner verwalteter Premium-Datenträger. Dies ist die Standardeinstellung, aber Sie können auch einen anderen Datenträgertyp für die Erstellung auswählen. Ausführliche Informationen finden Sie im Artikel [Erstellen einer Linux-VM mithilfe der Azure CLI](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Ein virtuelles Netzwerk mit einem Subnetz und zwei öffentlichen IP-Adressen. Alternativ können Sie ein *vorhandenes* Netzwerk, ein Subnetz, eine Netzwerkkarte oder öffentliche IP-Adressressourcen verwenden. Um zu erfahren, wie Sie vorhandene Netzwerkressourcen verwenden, statt zusätzliche Ressourcen zu erstellen, geben Sie `az vm create -h` ein.

Für öffentliche IP-Adressen fällt eine geringe Gebühr an. Weitere Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses) . Die Anzahl der öffentlichen IP-Adressen, die in einem Abonnement verwendet werden können, ist beschränkt. Weitere Informationen über die Einschränkungen finden Sie im Artikel zu den [Azure-Einschränkungen](../azure-subscription-service-limits.md#networking-limits) .

Nachdem die VM erstellt wurde, geben Sie den Befehl `az network nic show --name MyNic1 --resource-group myResourceGroup` ein, um die NIC-Konfiguration anzuzeigen. Geben Sie `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` ein, um eine Liste der IP-Konfigurationen anzuzeigen, die der Netzwerkkarte zugeordnet sind.

Fügen Sie die privaten IP-Adressen dem Betriebssystem des virtuellen Computers hinzu. Führen Sie dazu die Schritte für Ihr Betriebssystem im Abschnitt [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](#os-config) in diesem Artikel aus.

## <a name="add"></a>Hinzufügen von IP-Adressen zu einem virtuellen Computer

Sie können zu einer vorhandenen Azure-Netzwerkschnittstelle zusätzliche private und öffentliche IP-Adressen hinzufügen, indem Sie die nachfolgenden Schritte ausführen. Die Beispiele bauen auf dem in diesem Artikel beschriebenen [Szenario](#scenario) auf.

1. Öffnen Sie eine Befehlsshell, und führen Sie die verbleibenden Schritte in diesem Abschnitt in einer einzigen Sitzung aus. Wenn Sie die Azure CLI noch nicht installiert und konfiguriert haben, führen Sie die Schritte im Artikel [Installieren der Azure CLI](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) aus, und melden Sie sich dann mit dem Befehl `az-login` bei Ihrem Azure-Konto an.

2. Führen Sie gemäß Ihren Anforderungen die Schritte eines der folgenden Abschnitte aus:

    **Hinzufügen einer privaten IP-Adresse**
    
    Um eine private IP-Adresse zu einer Netzwerkkarte hinzuzufügen, müssen Sie mit dem folgenden Befehl eine IP-Konfiguration erstellen. Bei der statischen IP-Adresse darf es sich nicht um eine für das Subnetz verwendete Adresse handeln.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Sie können so viele Konfigurationen wie benötigt erstellen. Verwenden Sie dabei eindeutige Namen für die Konfigurationen und privaten IP-Adressen (bei Konfigurationen mit statischen IP-Adressen).

    **Hinzufügen einer öffentlichen IP-Adresse**
    
    Eine öffentliche IP-Adresse wird hinzugefügt, indem entweder eine neue oder eine vorhandene IP-Konfiguration zugeordnet wird. Führen Sie gemäß Ihren Anforderungen die Schritte des entsprechenden Abschnitts durch.

    Für öffentliche IP-Adressen fällt eine geringe Gebühr an. Weitere Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses) . Die Anzahl der öffentlichen IP-Adressen, die in einem Abonnement verwendet werden können, ist beschränkt. Weitere Informationen über die Einschränkungen finden Sie im Artikel zu den [Azure-Einschränkungen](../azure-subscription-service-limits.md#networking-limits) .

    - **Zuordnen der Ressource zu einer neuen IP-Konfiguration**
    
        Immer, wenn Sie in einer neuen IP-Konfiguration eine öffentliche IP-Adresse hinzufügen, müssen Sie auch eine private IP-Adresse hinzufügen, denn in jeder IP-Konfiguration muss es eine private IP-Adresse geben. Sie können entweder eine vorhandene öffentliche IP-Adressressource hinzufügen oder eine neue erstellen. Verwenden Sie den folgenden Befehl, um eine neue zu erstellen:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Um eine neue IP-Konfiguration mit einer statischen privaten IP-Adresse und der zugeordneten öffentlichen IP-Adressressource *myPublicIP3* zu erstellen, geben Sie den folgenden Befehl ein:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Zuordnen der Ressource zu einer vorhandenen IP-Konfiguration** Eine öffentliche IP-Adressressource kann nur einer IP-Konfiguration zugeordnet werden, der noch keiner öffentlichen IP-Adressressource zugeordnet ist. Ob einer IP-Konfiguration eine öffentliche IP-Adresse zugeordnet ist, können Sie mithilfe des folgenden Befehls ermitteln:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Zurückgegebene Ausgabe:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Da die Spalte **PublicIpAddressId** für *IpConfig-3* in der Ausgabe leer ist, ist derzeit keine öffentliche IP-Adressressource zugeordnet. Sie können IpConfig-3 eine vorhandene öffentliche IP-Adressressource zuordnen oder mit dem folgenden Befehl eine erstellen:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Geben Sie den folgenden Befehl ein, um die öffentliche IP-Adressressource der vorhandenen IP-Konfiguration namens *IPConfig-3* zuzuordnen:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Geben Sie den folgenden Befehl ein, um die privaten IP-Adressen und die IDs der öffentlichen IP-Adressressourcen, die der Netzwerkkarte zugewiesen sind, anzuzeigen:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Zurückgegebene Ausgabe: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Fügen Sie die privaten IP-Adressen hinzu, die Sie der NIC im Betriebssystem des virtuellen Computers hinzugefügt haben. Befolgen Sie dazu die Anweisungen im Abschnitt [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](#os-config) in diesem Artikel. Fügen Sie dem Betriebssystem nicht die öffentlichen IP-Adressen hinzu.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
