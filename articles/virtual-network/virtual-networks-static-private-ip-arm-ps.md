---
title: Erstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse – Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von PowerShell einen virtuellen Computer mit einer statischen privaten IP-Adresse erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685332"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Erstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können einen virtuellen Computer (VM) mit einer statischen privaten IP-Adresse erstellen. Weisen Sie eine statische private IP-Adresse anstelle einer dynamischen Adresse zu, wenn Sie auswählen möchten, welche Adresse aus einem Subnetz einem virtuellen Computer zugewiesen wird. Erfahren Sie mehr über [statische private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Um eine dynamische private IP-Adresse, die einem vorhandenen virtuellen Computer zugewiesen ist, in eine statische Adresse zu ändern oder mit öffentlichen IP-Adressen zu arbeiten, lesen Sie [Hinzufügen, Ändern oder Entfernen von IP-Adressen](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Auf dem lokalen Computer oder mithilfe von Azure Cloud Shell können Sie die folgenden Schritte durchführen. Um den lokalen Computer zu verwenden, stellen Sie sicher, dass [Azure PowerShell installiert ist](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Klicken Sie zum Verwenden von Azure Cloud Shell in der oberen rechten Ecke eines beliebigen Befehlsfelds auf **Testen**. Cloud Shell meldet Sie in Azure an.

1. Wenn Sie Cloud Shell bereits verwenden, fahren Sie mit Schritt 2 fort. Öffnen Sie eine Befehlssitzung, und melden Sie sich mit `Connect-AzAccount` in Azure an.
2. Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region „USA, Osten“ erstellt:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Erstellen Sie mit den Befehlen [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) und [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) eine Subnetzkonfiguration und ein virtuelles Netzwerk:

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Erstellen Sie eine Netzwerkschnittstelle im virtuellen Netzwerk, und weisen Sie der Netzwerkschnittstelle mit den Befehlen [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) und [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) eine private IP-Adresse aus dem Subnetz zu:

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Erstellen Sie mit [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig) eine VM-Konfiguration, und erstellen Sie dann mit [New-AzVM](/powershell/module/az.Compute/New-azVM) den virtuellen Computer. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Sie können dem Betriebssystem zwar Einstellungen für private IP-Adressen hinzufügen, dies wird jedoch erst nach dem Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) empfohlen.
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Um aus dem Internet auf die VM zugreifen zu können, müssen Sie ihr eine öffentliche IP-Adresse zuweisen. Sie können auch eine dynamische private IP-Adresszuweisung in eine statische Zuweisung ändern. Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von IP-Adressen](virtual-network-network-interface-addresses.md). Darüber hinaus empfiehlt es sich, den Netzwerkdatenverkehr auf Ihren virtuellen Computer beschränken, indem Sie der Netzwerkschnittstelle, dem Subnetz, in dem Sie die Netzwerkschnittstelle erstellt haben, oder beiden eine Netzwerksicherheitsgruppe zuordnen. Weitere Informationen finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
- Erfahren Sie mehr über das Erstellen von VMs unter [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
