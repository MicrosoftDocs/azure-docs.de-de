---
title: Tutorial – Installieren von Anwendungen in einer Skalierungsgruppe mit Azure PowerShell | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure PowerShell zum Installieren von Anwendungen in VM-Skalierungsgruppen mit der benutzerdefinierten Skripterweiterung verwenden.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4c6216f3dff3cbf4c8d838810c4dd786f1c34ec1
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728653"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Installieren von Anwendungen in VM-Skalierungsgruppen mit Azure PowerShell

Zum Ausführen von Anwendungen auf VM-Instanzen in einer Skalierungsgruppe müssen Sie zuerst die Anwendungskomponenten und erforderlichen Dateien installieren. In einem vorherigen Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image erstellen und verwenden, um Ihre VM-Instanzen bereitzustellen. Dieses benutzerdefinierte Image umfasste manuelle Anwendungsinstallationen und -konfigurationen. Sie können die Installation von Anwendungen auch per Skalierungsgruppe automatisieren, nachdem die einzelnen VM-Instanzen bereitgestellt wurden, oder eine Anwendung aktualisieren, die bereits in einer Skalierungsgruppe ausgeführt wird. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Automatisches Installieren von Anwendungen in Ihrer Skalierungsgruppe
> * Verwenden der benutzerdefinierten Skripterweiterung von Azure
> * Aktualisieren einer ausgeführten Anwendung in einer Skalierungsgruppe

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Was ist die benutzerdefinierte Skripterweiterung von Azure?
Die benutzerdefinierte Skripterweiterung lädt Skripts auf Azure-VMs herunter und führt sie aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden.

Die Erweiterung für benutzerdefinierte Skripts ist in Azure Resource Manager-Vorlagen integriert. Sie kann auch mit der Azure CLI, mit Azure PowerShell, mit dem Azure-Portal oder der REST-API verwendet werden. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/windows/extensions-customscript.md).

Wenn Sie die benutzerdefinierte Skripterweiterung in Aktion sehen möchten, können Sie eine Skalierungsgruppe erstellen, mit der der IIS-Webserver installiert und der Hostname einer VM-Instanz der Skalierungsgruppe ausgegeben wird. Mit der Definition der benutzerdefinierten Skripterweiterung wird ein Beispielskript von GitHub heruntergeladen, die erforderlichen Pakete werden installiert, und anschließend wird der Hostname der VM-Instanz auf eine einfache HTML-Seite geschrieben.


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Erstellen Sie nun mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80. Er lässt außerdem Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985 zu. Bei entsprechender Aufforderung können Sie Ihre eigenen Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe festlegen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="create-custom-script-extension-definition"></a>Erstellen der Definition für die benutzerdefinierte Skripterweiterung
In Azure PowerShell wird eine Hashtabelle zum Speichern der herunterzuladenden Tabelle und des auszuführenden Befehls verwendet. Im folgenden Beispiel wird ein Beispielskript von GitHub verwendet. Erstellen Sie zunächst wie folgt dieses Konfigurationsobjekt:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Wenden Sie nun mit [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension) die benutzerdefinierte Skripterweiterung an. Das zuvor definierte Konfigurationsobjekt wird an die Erweiterung übergeben. Verwenden Sie [Update-AzVmss](/powershell/module/az.compute/update-azvmss), um die Erweiterung auf den VM-Instanzen zu aktualisieren und auszuführen.


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Jede VM-Instanz in der Skalierungsgruppe wird heruntergeladen und führt das Skript von GitHub aus. In einem komplexeren Beispiel können auch mehrere Anwendungskomponenten und Dateien installiert werden. Wenn die Skalierungsgruppe zentral hochskaliert wird, wenden die neuen VM-Instanzen automatisch dieselbe Definition der benutzerdefinierten Skripterweiterung an und installieren die erforderliche Anwendung.


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
Um Ihren Webserver in Aktion zu sehen, rufen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Load Balancers ab. Im folgenden Beispiel wird die IP-Adresse angezeigt, die in der Ressourcengruppe *myResourceGroup* erstellt wurde:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einen Webbrowser ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Einfache Webseite in IIS](media/tutorial-install-apps-powershell/running-iis.png)

Lassen Sie den Webbrowser geöffnet, damit Sie im nächsten Schritt die aktualisierte Version sehen können.


## <a name="update-app-deployment"></a>Aktualisieren der App-Bereitstellung
Während des Lebenszyklus einer Skalierungsgruppe müssen Sie ggf. eine aktualisierte Version Ihrer Anwendung bereitstellen. Mit der benutzerdefinierten Skripterweiterung können Sie auf ein aktualisiertes Bereitstellungsskript verweisen und die Erweiterung dann erneut auf Ihre Skalierungsgruppe anwenden. Als die Skalierungsgruppe in einem vorherigen Schritt erstellt wurde, wurde `-UpgradePolicyMode` auf *Automatic* festgelegt. Mit dieser Einstellung können die VM-Instanzen in der Skalierungsgruppe aktualisiert werden, und die aktuelle Version Ihrer Anwendung kann angewendet werden.

Erstellen Sie eine neue Konfigurationsdefinition mit dem Namen *customConfigv2*. Diese Definition führt eine aktualisierte *v2*-Version des Anwendungsinstallationsskripts aus:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Aktualisieren Sie die Konfiguration der benutzerdefinierten Skripterweiterung auf die VM-Instanzen in Ihrer Skalierungsgruppe. Die Definition *customConfigv2* wird verwendet, um die aktualisierte Version der Anwendung anzuwenden:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Alle VM-Instanzen in der Skalierungsgruppe werden automatisch mit der aktuellen Version der Beispielwebseite aktualisiert. Aktualisieren Sie die Webseite in Ihrem Browser, um die aktualisierte Version anzuzeigen:

![Aktualisierte Webseite in IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) die Ressourcengruppe und alle dazugehörigen Ressourcen, um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie Anwendungen in Ihrer Skalierungsgruppe mit Azure PowerShell automatisch installieren und aktualisieren:

> [!div class="checklist"]
> * Automatisches Installieren von Anwendungen in Ihrer Skalierungsgruppe
> * Verwenden der benutzerdefinierten Skripterweiterung von Azure
> * Aktualisieren einer ausgeführten Anwendung in einer Skalierungsgruppe

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Skalierungsgruppe automatisch skalieren.

> [!div class="nextstepaction"]
> [Automatisches Skalieren Ihrer Skalierungsgruppen](tutorial-autoscale-powershell.md)
