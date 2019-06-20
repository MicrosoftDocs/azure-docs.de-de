---
title: Verschieben eines virtuellen Computers (klassisch) oder einer Cloud Services-Rolleninstanz in ein anderes Subnetz – Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuelle Computer (klassisch) und Cloud Services-Rolleninstanzen mithilfe von PowerShell in ein anderes Subnetz verschieben.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640394"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Verschieben eines virtuellen Computers (klassisch) oder einer Cloud Services-Rolleninstanz in ein anderes Subnetz mithilfe von PowerShell
Sie können PowerShell verwenden, um Ihre virtuellen Computer (klassisch) zwischen Subnetzen im gleichen virtuellen Netzwerk (VNet) zu verschieben. Rolleninstanzen können anstatt mit PowerShell auch durch Bearbeiten der CSCFG-Datei verschoben werden.

> [!NOTE]
> In diesem Artikel wird nur das Verschieben von virtuellen Computern erläutert, die über das klassische Bereitstellungsmodell bereitgestellt wurden.
> 
> 

Gründe für das Verschieben virtueller Computer in ein anderes Subnetz Die Subnetzmigration ist sinnvoll, wenn das ältere Subnetz zu klein ist und aufgrund von vorhandenen virtuellen Computer in diesem Subnetz nicht erweitert werden kann. In diesem Fall können Sie ein neues, größeres Subnetz erstellen und die virtuellen Computer zum neuen Subnetz migrieren. Nach Abschluss der Migration können Sie das alte leere Subnetz löschen.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Verschieben eines virtuellen Computers in ein anderes Subnetz
Führen Sie zum Verschieben eines virtuellen Computers das PowerShell-Cmdlet „Set-AzureSubnet“ aus. Verwenden Sie dabei das unten angegebene Beispiel als Vorlage. Im folgenden Beispiel verschieben wir „TestVM“ vom aktuellen Subnetz in Subnetz-2. Bearbeiten Sie das Beispiel Ihrer Umgebung entsprechend. Hinweis: Jedes Mal, wenn Sie das Update-AzureVM-Cmdlet als Teil einer Prozedur ausführen, wird der virtuelle Computer im Rahmen des Aktualisierungsprozesses neu gestartet.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Wenn Sie eine statische interne private IP-Adresse für Ihren virtuellen Computer angegeben haben, müssen Sie diese Einstellung deaktivieren, bevor Sie den virtuellen Computer in ein neues Subnetz verschieben können. Verwenden Sie in diesem Fall Folgendes:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>So verschieben Sie eine Rolleninstanz in ein anderes Subnetz
Bearbeiten Sie zum Verschieben einer Rolleninstanz die CSCFG-Datei. Im folgenden Beispiel verschieben wir „Role0“ im virtuellen Netzwerk *VNETName* vom aktuellen Subnetz in *Subnetz-2*. Da die Rolleninstanz bereits bereitgestellt wurde, ändern Sie einfach den Subnetznamen in „Subnetz-2“. Bearbeiten Sie das Beispiel Ihrer Umgebung entsprechend.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
