---
title: 'Schnellstart: Erstellen einer VM-Skalierungsgruppe mit Azure PowerShell | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie mit Azure CLI 2.0 schnell eine VM-Skalierungsgruppe erstellen können.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2018
ms.author: cynthn
ms.openlocfilehash: 2e9f850183c0e5ee5ab883848e7b02afbfde2bae
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728970"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Schnellstart: Erstellen einer VM-Skalierungsgruppe mit Azure PowerShell



Mit einer VM-Skalierungsgruppe können Sie eine Gruppe identischer, automatisch skalierender virtueller Computer bereitstellen und verwalten. Sie können die Anzahl virtueller Computer in der Skalierungsgruppe manuell skalieren oder basierend auf der Ressourcennutzung, z.B. CPU-Auslastung, Speicherbedarf oder Netzwerkdatenverkehr, Regeln für die automatische Skalierung definieren. Daraufhin wird der Datenverkehr durch einen Azure-Lastenausgleich auf die VM-Instanzen in der Skalierungsgruppe verteilt. In dieser Schnellstartanleitung erstellen Sie eine VM-Skalierungsgruppe und stellen eine Beispielanwendung mit Azure PowerShell bereit.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Erstellen Sie mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, für die das Plattformimage *Windows Server 2016 Datacenter* verwendet wird. Die Azure-Netzwerkressourcen für virtuelles Netzwerk, öffentliche IP-Adresse und Lastenausgleich werden automatisch erstellt. Bei entsprechender Aufforderung können Sie Ihre eigenen Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe festlegen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="deploy-sample-application"></a>Bereitstellen der Beispielanwendung
Installieren Sie eine einfache Webanwendung, um Ihre Skalierungsgruppe zu testen. Die benutzerdefinierte Skripterweiterung von Azure wird zum Herunterladen und Ausführen eines Skripts verwendet, das IIS auf den VM-Instanzen installiert. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/windows/extensions-customscript.md).

Verwenden Sie die benutzerdefinierte Skripterweiterung, um einen einfachen IIS-Webserver zu installieren. Wenden Sie die benutzerdefinierte Skripterweiterung, mit der IIS installiert wird, wie folgt an:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Zulassen von Datenverkehr für die Anwendung

 Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) und [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe, um den Zugriff auf die einfache Webanwendung zuzulassen. Weitere Informationen finden Sie unter [Netzwerk für Azure-VM-Skalierungsgruppen](virtual-machine-scale-sets-networking.md).

 ```azurepowershell-interactive
 # Get information about the scale set
 $vmss = Get-AzVmss `
             -ResourceGroupName "myResourceGroup" `
             -VMScaleSetName "myScaleSet"

 #Create a rule to allow traffic over port 80
 $nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
   -Name myFrontendNSGRule `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 200 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 80 `
   -Access Allow

 #Create a network security group and associate it with the rule
 $nsgFrontend = New-AzNetworkSecurityGroup `
   -ResourceGroupName  "myResourceGroup" `
   -Location EastUS `
   -Name myFrontendNSG `
   -SecurityRules $nsgFrontendRule

 $vnet = Get-AzVirtualNetwork `
   -ResourceGroupName  "myResourceGroup" `
   -Name myVnet

 $frontendSubnet = $vnet.Subnets[0]

 $frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
   -VirtualNetwork $vnet `
   -Name mySubnet `
   -AddressPrefix $frontendSubnet.AddressPrefix `
   -NetworkSecurityGroup $nsgFrontend

 Set-AzVirtualNetwork -VirtualNetwork $vnet

 # Update the scale set and apply the Custom Script Extension to the VM instances
 Update-AzVmss `
     -ResourceGroupName "myResourceGroup" `
     -Name "myScaleSet" `
     -VirtualMachineScaleSet $vmss
 ```

## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Greifen Sie über einen Webbrowser auf die Beispielwebanwendung zu, um Ihre Skalierungsgruppe in Aktion zu sehen. Rufen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab. Im folgenden Beispiel wird die IP-Adresse angezeigt, die in der Ressourcengruppe *myResourceGroup* erstellt wurde:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einen Webbrowser ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Ausführen der IIS-Website](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) können Sie wie folgt die Ressourcengruppe, die Skalierungsgruppe und alle dazugehörigen Ressourcen entfernen, sofern diese nicht mehr benötigt werden. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie eine einfache Skalierungsgruppe erstellt und die benutzerdefinierte Skripterweiterung verwendet, um einen einfachen IIS-Webserver auf den VM-Instanzen zu installieren. Im nächsten Tutorial erfahren Sie mehr über die Erstellung und Verwaltung von Azure-VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Azure-VM-Skalierungsgruppen](tutorial-create-and-manage-powershell.md)
