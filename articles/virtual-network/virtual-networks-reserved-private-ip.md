---
title: Statische interne private IP-Adresse – Azure-VM – klassisch
description: Grundlegendes zu statischen internen IP-Adressen (DIPs) und zu deren Verwaltung
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640326"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Festlegen einer internen privaten statischen IP-Adresse mithilfe von PowerShell (klassisch)
In den meisten Fällen muss für Ihren virtuellen Computer keine statische interne IP-Adresse angegeben werden. Virtuelle Computer in einem virtuellen Netzwerk erhalten automatisch eine interne IP-Adresse aus einem von Ihnen angegebenen Bereich. In bestimmten Fällen ist die Angabe einer statischen IP-Adresse für einen bestimmten virtuellen Computer allerdings sinnvoll – beispielsweise, wenn auf Ihrem virtuellen Computer DNS ausgeführt werden oder der virtuelle Computer als Domänencontroller fungieren soll. Eine statische interne IP-Adresse bleibt erhalten, auch wenn der virtuelle Computer beendet oder seine Bereitstellung aufgehoben wird. 

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen:  [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des [Resource Manager-Bereitstellungsmodells](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Installieren des Azure PowerShell-Dienstverwaltungsmoduls

Bevor Sie die folgenden Befehle ausführen, stellen Sie sicher, dass das [Azure PowerShell-Dienstverwaltungsmodul](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) auf dem Computer installiert ist. Informationen zum Versionsverlauf des Azure PowerShell-Dienstverwaltungsmoduls finden Sie unter [Azure module in PowerShell Gallery](https://www.powershellgallery.com/packages/Azure/5.3.0) (Azure-Modul im PowerShell-Katalog).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Überprüfen der Verfügbarkeit einer bestimmten IP-Adresse
Wenn Sie beispielsweise überprüfen möchten, ob die IP-Adresse *10.0.0.7* in einem VNET namens *TestVnet* verfügbar ist, führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich den Wert für *IsAvailable* an.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Wenn Sie den obigen Befehl in einer sicheren Umgebung testen möchten, erstellen Sie unter Berücksichtigung der Richtlinien in [Erstellen eines virtuellen Netzwerks (klassisch)](virtual-networks-create-vnet-classic-pportal.md) ein VNet namens *TestVnet*, und stellen Sie dabei sicher, dass es den Adressraum *10.0.0.0/8* verwendet.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Angeben einer statischen internen IP-Adresse beim Erstellen eines virtuellen Computers
Das folgende PowerShell-Skript erstellt einen neuen Clouddienst namens *TestService*, ruft ein Image aus Azure ab, erstellt auf dessen Grundlage in dem neuen Clouddienst einen virtuellen Computer namens *TestVM*, platziert den virtuellen Computer in einem Subnetz namens *Subnet-1* und legt *10.0.0.7* als statische interne IP-Adresse für den virtuellen Computer fest:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Abrufen der Informationen zur statischen internen IP-Adresse für einen virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich die Werte für *IpAddress*an, um Informationen zur statischen internen IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Entfernen einer statischen internen IP-Adresse von einem virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, um die statische interne IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Hinzufügen einer statischen internen IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie den folgenden Befehl aus, um dem virtuellen Computer, der mit dem Skript oben erstellt wurde, eine statische interne IP-Adresse hinzuzufügen:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Nächste Schritte
[Reservierte IP](virtual-networks-reserved-public-ip.md)

[Öffentliche IP auf Instanzebene (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Reservierte IP-REST-APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)

