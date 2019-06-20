---
title: Verwenden von VM-Images zum Erstellen von Skalierungsgruppen in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure PowerShell freigegebene VM-Images erstellen können, mit denen Sie VM-Skalierungsgruppen in Azure bereitstellen können.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 055242c3118ce9d972d55cdc6a21bf623679a0c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242046"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Erstellen und Verwenden von freigegebenen Images für VM-Skalierungsgruppen mit Azure PowerShell

Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Der Katalog mit geteilten Images vereinfacht das Teilen benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. 

Der Katalog mit geteilten Images gestattet es Ihnen, Ihre benutzerdefinierten VM-Images mit anderen Personen in Ihrer Organisation, innerhalb einer oder zwischen Regionen, innerhalb eines AAD-Mandanten zu teilen. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie geteilte Images logisch gruppieren können. 

Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung (RBAC) bereitstellt. Images bieten Versionsverwaltung, und Sie können sich entschließen, jede Imageversion in eine andere Gruppe von Azure-Regionen zu replizieren. Der Katalog funktioniert nur mit verwalteten Images. 

Die Funktion „Katalog mit freigegebenen Images“ verfügt über mehrere Ressourcentypen. Wir werden diese in diesem Artikel verwenden oder erstellen:

| Resource | BESCHREIBUNG|
|----------|------------|
| **Verwaltetes Image** | Dies ist ein Basisimage, das eigenständig oder zum Erstellen einer **Imageversionen** in einem Imagekatalog verwendet werden kann. Verwaltete Images werden aus generalisierten virtuellen Computern erstellt. Ein verwaltetes Image ist ein spezieller VHD-Typ, mit dem mehrere virtuelle Computer und jetzt auch Versionen von freigegebenen Images erstellt werden können. |
| **Imagekatalog** | Wie der Azure Marketplace ist ein **Imagekatalog** ein Repository zum Verwalten und Teilen von Images, aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Abbilder sind innerhalb eines Katalogs definiert und enthalten intern Informationen über das Image und die Anforderungen für seine Verwendung. Dies schließt ein, ob das Image Windows oder Linux ist, Anmerkungen zu dieser Version und Anforderungen an den minimalen und maximalen Arbeitsspeicher. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In den folgenden Schritten wird erläutert, wie Sie eine vorhandene VM in ein wiederverwendbares benutzerdefiniertes Image umwandeln, mit dem Sie neue VM-Instanzen erstellen können.

Für das Beispiel in diesem Artikel muss ein verwaltetes Image vorhanden sein. Sie können im [Tutorial: Erstellen und Verwenden eines benutzerdefinierten Images für VM-Skalierungsgruppen mit Azure PowerShell](tutorial-use-custom-image-powershell.md) ggf. eines erstellen. Wenn das verwaltete Image einen Datenträger für Daten enthält, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten des Artikels bei Bedarf den Namen der Ressourcengruppe und des virtuellen Computers.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Erstellen einer Skalierungsgruppe aus der Version des freigegebenen Images

Erstellen Sie mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Das folgende Beispiel erstellt eine Skalierungsgruppe aus der neuen Imageversion im Rechenzentrum *USA, Süden-Mitte*. Legen Sie bei entsprechender Aufforderung Ihre eigenen Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe fest:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -Id $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Nächste Schritte

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Erstellen eines virtuellen Computers aus einer Imageversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Weitere Informationen zu Katalogen mit geteilten Images finden Sie in der [Übersicht](shared-image-galleries.md). Sollten Probleme auftreten, sehen Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](troubleshooting-shared-images.md) nach.
