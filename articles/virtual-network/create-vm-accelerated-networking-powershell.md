---
title: Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb – Azure PowerShell
description: Erstellen Sie einen virtuellen Windows-Computer (VM) mit beschleunigtem Netzwerkbetrieb, um seine Netzwerkleistung erheblich zu verbessern.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919968"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb mit Azure PowerShell

In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Windows-Computer (VM) mit beschleunigtem Netzwerkbetrieb erstellen.

> [!NOTE]
> Informationen zur Verwendung des beschleunigten Netzwerkbetriebs mit einem virtuellen Linux-Computer finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](create-vm-accelerated-networking-cli.md).

Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) in einer VM und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen Hochleistungspfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden. Das folgende Diagramm veranschaulicht, wie zwei VMs mit und ohne beschleunigten Netzwerkbetrieb kommunizieren:

![Kommunikation zwischen virtuellen Azure-Computern mit und ohne beschleunigten Netzwerkbetrieb](./media/create-vm-accelerated-networking/accelerated-networking.png)

Ohne beschleunigten Netzwerkbetrieb muss sämtlicher Netzwerkdatenverkehr zur und von der VM den Host und virtuellen Switch passieren. Der virtuelle Switch setzt alle Richtlinien für den Netzwerkdatenverkehr um, z.B. Netzwerksicherheitsgruppen, Zugriffssteuerungslisten, Isolation und andere virtualisierte Netzwerkdienste.

> [!NOTE]
> Weitere Informationen zu virtuellen Switches finden Sie unter [Virtueller Hyper-V-Switch](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Mit beschleunigtem Netzwerkbetrieb gelangt der Netzwerkdatenverkehr zur Netzwerkkarte (NIC) der VM und wird dann an die VM weitergeleitet. Alle Netzwerkrichtlinien, die vom virtuellen Switch angewendet werden, werden ab sofort ausgelagert und in der Hardware angewendet. Da die Richtlinie in der Hardware angewendet wird, kann die NIC den Netzwerkdatenverkehr direkt an die VM weiterleiten. Die NIC umgeht den Host und den virtuellen Switch, während sie die gesamte Richtlinie, die sie im Host angewendet hat, beibehält.

Die Vorteile des beschleunigten Netzwerkbetriebs gelten nur für die VM, auf der dieses Feature aktiviert ist. Die besten Ergebnisse erzielen Sie, wenn Sie dieses Feature auf mindestens zwei VMs aktivieren, die mit demselben virtuellen Azure-Netzwerk verbunden sind. Bei der Kommunikation über virtuelle Netzwerke oder bei lokalen Verbindungen hat dieses Feature nur minimale Auswirkungen auf die Wartezeit.

## <a name="benefits"></a>Vorteile

- **Niedrigere Latenz/mehr Pakete pro Sekunde (pps)** : Wenn Sie den virtuellen Switch aus dem Datenpfad entfernen, werden die Zeitpakete, die für die Richtlinienverarbeitung auf dem Host aufgewendet werden, entfernt. Außerdem wird die Anzahl der Pakete erhöht, die innerhalb der VM verarbeitet werden können.

- **Reduzierte Jitter**: Die Verarbeitung virtueller Switches hängt vom Umfang der anzuwendenden Richtlinien ab. Es hängt auch von der Workload der CPU ab, die die Verarbeitung durchführt. Durch die Abladung der Richtlinienerzwingung auf die Hardware entfällt dieser Umweg, indem Pakete direkt an den virtuellen Computer gesendet werden. Das Abladen entfernt auch die Kommunikation zwischen Host und virtuellem Computer, alle Softwareinterrupts und alle Kontextwechsel.

- **Verringerte CPU-Auslastung**: Das Umgehen des virtuellen Switch auf dem Host führt zu weniger CPU-Auslastung für die Verarbeitung des Netzwerkdatenverkehrs.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgenden Distributionen werden direkt aus dem Azure-Katalog unterstützt:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Einschränkungen

### <a name="supported-vm-instances"></a>Unterstützte VM-Instanzen

Der beschleunigte Netzwerkbetrieb wird in den meisten universellen, computeoptimierten Instanzgrößen mit mindestens 2 vCPUs unterstützt. Bei Instanzen, die Hyperthreading unterstützen, wird der beschleunigte Netzwerkbetrieb auf VM-Instanzen mit mindestens 4 vCPUs unterstützt. 

Informationen zur Unterstützung für beschleunigten Netzwerkbetrieb finden Sie in der Dokumentation zu den einzelnen [VM-Größen](../virtual-machines/sizes.md). 

### <a name="custom-images"></a>Benutzerdefinierte Images

Wenn Sie ein benutzerdefiniertes Image verwenden und dieses den beschleunigten Netzwerkbetrieb unterstützt, stellen Sie sicher, dass Sie über die erforderlichen Treiber verfügen, damit Mellanox ConnectX-3- und ConnectX-4 LX-Netzwerkadapter in Azure unterstützt werden.

### <a name="regions"></a>Regions

Beschleunigter Netzwerkbetrieb ist in allen globalen Azure-Regionen und in Azure Government-Cloud verfügbar.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf einer ausgeführten VM

Bei einer unterstützten VM-Größe ohne aktivierten beschleunigten Netzwerkbetrieb kann das Feature nur aktiviert werden, wenn die VM beendet und ihre Zuordnung aufgehoben wird.

### <a name="deployment-through-azure-resource-manager"></a>Bereitstellung über Azure Resource Manager

Virtuelle Computer (klassisch) können nicht mit beschleunigtem Netzwerkbetrieb bereitgestellt werden.

## <a name="vm-creation-using-the-portal"></a>VM-Erstellung über das Portal

In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Computers mit beschleunigtem Netzwerkbetrieb mithilfe von Azure PowerShell dargestellt, Sie können jedoch auch [über das Azure-Portal einen virtuellen Computer](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mit beschleunigtem Netzwerkbetrieb erstellen. Wenn Sie eine VM im Portal erstellen, wählen Sie auf der Seite **Virtuellen Computer erstellen** die Registerkarte **Netzwerk** aus. Auf dieser Registerkarte gibt es eine Option für **Beschleunigter Netzwerkbetrieb**. Wenn Sie ein [unterstütztes Betriebssystem](#supported-operating-systems) und eine [unterstützte VM-Größe](#supported-vm-instances) ausgewählt haben, wird diese Option automatisch auf **Ein** festgelegt. Andernfalls ist die Option auf **Aus** festgelegt und Azure zeigt den Grund an, warum sie nicht aktiviert werden kann.

> [!NOTE]
> Nur unterstützte Betriebssysteme können über das Portal aktiviert werden. Wenn Sie ein benutzerdefiniertes Image verwenden und Ihr Image den beschleunigten Netzwerkbetrieb unterstützt, erstellen Sie Ihre VM mit CLI oder PowerShell. 

Nachdem Sie den virtuellen Computer erstellt haben, können Sie überprüfen, ob der beschleunigte Netzwerkbetrieb aktiviert ist. Befolgen Sie diese Anweisungen:

1. Rufen Sie das [Azure-Portal](https://portal.azure.com) auf, um Ihre VMs zu verwalten. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie in der Liste virtueller Computer den neuen virtuellen Computer aus.

3. Klicken Sie in der VM-Menüleiste auf **Netzwerk**.

In den Informationen zur Netzwerkschnittstelle zeigt das Portal neben der Bezeichnung **Beschleunigter Netzwerkbetrieb** entweder **Deaktiviert** oder **Aktiviert** für den Status des beschleunigten Netzwerkbetriebs an.

## <a name="vm-creation-using-powershell"></a>VM-Erstellung mithilfe von PowerShell

Bevor Sie fortfahren, installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps) Version 1.0.0 oder höher. Zur Ermittlung der derzeit installierten Version führen Sie `Get-Module -ListAvailable Az` aus. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, installieren Sie die aktuelle Version des Az-Moduls über den [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az). Melden Sie sich in einer PowerShell-Sitzung mithilfe von [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto an.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Zu den Parameternamen zählen z.B. *myResourceGroup*, *myNic* und *myVm*.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe. Der folgende Befehl erstellt am Standort *centralus* eine Ressourcengruppe namens *myResourceGroup*:

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) eine Subnetzkonfiguration. Der folgende Befehl erstellt ein Subnetz namens *mySubnet*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) ein virtuelles Netzwerk mit dem Subnetz *mySubnet*.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

1. Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig) eine Netzwerksicherheitsgruppen-Regel.

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) eine Netzwerksicherheitsgruppe, und weisen Sie die Sicherheitsregel *Allow-RDP-All* zu. Neben der Regel *Allow-RDP-All* enthält die Netzwerksicherheitsgruppe noch mehrere Standardregeln. Eine Standardregel deaktiviert den gesamten eingehenden Zugriff aus dem Internet. Nach der Erstellung wird die Regel *Allow-RDP-All* der Netzwerksicherheitsgruppe zugewiesen, sodass Sie eine Remoteverbindung mit dem virtuellen Computer herstellen können.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Ordnen Sie mit [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig) die Netzwerksicherheitsgruppe dem Subnetz *mySubnet* zu. Die Regel in der Netzwerksicherheitsgruppe gilt für alle Ressourcen, die im Subnetz bereitgestellt werden.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Erstellen einer Netzwerkschnittstelle mit beschleunigtem Netzwerkbetrieb

1. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) eine öffentliche IP-Adresse. Eine öffentliche IP-Adresse ist nicht erforderlich, wenn Sie nicht beabsichtigen, über das Internet auf den virtuellen Computer zuzugreifen. Es ist jedoch erforderlich, die Schritte in diesem Artikel auszuführen.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) eine Netzwerkschnittstelle mit beschleunigtem Netzwerkbetrieb, und weisen Sie der Netzwerkschnittstelle die öffentliche IP-Adresse zu. Das folgende Beispiel erstellt die Netzwerkschnittstelle *myNic* im Subnetz *mySubnet* des virtuellen Netzwerks *myVnet* und weist ihr die öffentliche IP-Adresse *myPublicIp* zu:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Erstellen eines virtuellen Computers und Anfügen der Netzwerkschnittstelle

1. Legen Sie Ihre VM-Anmeldeinformationen mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) auf die Variable `$cred` fest, wodurch Sie zur Anmeldung aufgefordert werden:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Definieren Sie Ihren virtuellen Computer mit [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Der folgende Befehl definiert die VM *myVM* mit einer VM-Größe, die beschleunigten Netzwerkbetrieb unterstützt (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Eine Liste aller VM-Größen und -Eigenschaften finden Sie unter [Größen für virtuelle Windows-Computer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Erstellen Sie die restliche Konfiguration des virtuellen Computers mit [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) und [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Der folgende Befehl erstellt eine Windows Server 2016-VM:

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. Fügen Sie die beiden Netzwerkschnittstellen, die Sie zuvor erstellt haben, mit [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) an:

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) Ihren virtuellen Computer.

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Bestätigen der Installation des Ethernet-Controllers auf dem virtuellen Windows-Computer

Stellen Sie nach dem Erstellen des virtuellen Computers in Azure eine Verbindung mit dem virtuellen Computer her, und vergewissern Sie sich, dass der Ethernet-Controller unter Windows installiert ist.

1. Rufen Sie das [Azure-Portal](https://portal.azure.com) auf, um Ihre VMs zu verwalten. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie in der Liste virtueller Computer den neuen virtuellen Computer aus.

3. Wenn auf der VM-Übersichtsseite der **Status** der VM als **Wird erstellt** aufgeführt ist, warten Sie, bis Azure die Erstellung der VM abgeschlossen hat. Der **Status** wird in **Wird ausgeführt** geändert, nachdem die VM-Erstellung abgeschlossen ist.

4. Wählen Sie auf der Symbolleiste der VM-Übersicht die Optionen **Verbinden** > **RDP** > **RDP-Datei herunterladen** aus.

5. Öffnen Sie die RDP-Datei, und melden Sie sich dann bei der VM mit den Anmeldeinformationen an, die Sie im Abschnitt [Erstellen eines virtuellen Computers und Anfügen der Netzwerkschnittstelle](#create-a-vm-and-attach-the-network-interface) eingegeben haben. Wenn Sie noch nie eine Verbindung mit einer Windows-VM in Azure hergestellt haben, finden Sie unter [Herstellen der Verbindung mit dem virtuellen Computer](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine) weitere Informationen.

6. Nachdem die Remotedesktopsitzung für Ihren virtuellen Computer angezeigt wird, klicken Sie mit der rechten Maustaste auf die Windows-Schaltfläche „Start“, und wählen Sie **Geräte-Manager** aus.

7. Erweitern Sie im Fenster **Geräte-Manager** den Knoten **Netzwerkadapter**.

8. Vergewissern Sie sich, dass **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** (wie in der folgenden Abbildung gezeigt) angezeigt wird:

    ![Mellanox ConnectX-3 Virtual Function Ethernet Adapter, neuer Netzwerkadapter für beschleunigten Netzwerkbetrieb, Geräte-Manager](./media/create-vm-accelerated-networking/device-manager.png)

Der beschleunigte Netzwerkbetrieb ist nun für Ihre VM aktiviert.

> [!NOTE]
> Wenn der Mellanox-Adapter nicht startet, öffnen Sie in der Remotedesktopsitzung eine Administratoreingabeaufforderung, und geben Sie den folgenden Befehl ein:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf vorhandenen VMs

Wenn Sie eine VM ohne beschleunigten Netzwerkbetrieb erstellt haben, können Sie dieses Feature auf einer vorhandenen VM aktivieren. Die VM muss folgende Voraussetzungen erfüllen, um den beschleunigten Netzwerkbetrieb zu unterstützen (siehe auch oben):

* Die VM muss eine unterstützte Größe für den beschleunigten Netzwerkbetrieb aufweisen.
* Bei der VM muss es sich um ein unterstütztes Image aus dem Azure-Katalog (und eine unterstützte Kernelversion für Linux) handeln.
* Alle VMs in einer Verfügbarkeitsgruppe oder einer VM-Skalierungsgruppe müssen beendet oder freigegeben werden, bevor Sie den beschleunigten Netzwerkbetrieb auf einer beliebigen NIC aktivieren.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Einzelne VMs und VMs in einer Verfügbarkeitsgruppe

1. Beenden Sie die VM oder heben Sie ihre Zuordnung auf. Falls es sich um eine Verfügbarkeitsgruppe handelt, führen Sie diese Vorgänge für alle VMs in der Gruppe aus:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Wenn Sie eine VM einzeln erstellen (ohne Verfügbarkeitsgruppe), müssen Sie nur diese eine VM beenden oder ihre Zuordnung aufheben, um den beschleunigten Netzwerkbetrieb zu aktivieren. Wenn Ihre VM mit einer Verfügbarkeitsgruppe erstellt wurde, müssen Sie alle in der Verfügbarkeitsgruppe enthaltenen VMs stoppen oder ihre Zuordnung aufheben, bevor Sie den beschleunigten Netzwerkbetrieb auf einer der NICs aktivieren, sodass die VMs auf einem Cluster landen, der den beschleunigten Netzwerkbetrieb unterstützt. Die Anforderung zum Beenden oder Aufheben der Zuordnung ist nicht erforderlich, wenn Sie den beschleunigten Netzwerkbetrieb deaktivieren, da Cluster, die den beschleunigten Netzwerkbetrieb unterstützen, auch mit NICs funktionieren, die keinen beschleunigten Netzwerkbetrieb verwenden.

2. Aktivieren Sie den beschleunigten Netzwerkbetrieb für die Netzwerkschnittstelle Ihrer VM:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Starten Sie Ihre VM bzw. im Fall einer Verfügbarkeitsgruppe alle VMs in der Gruppe neu, und überprüfen Sie, ob der beschleunigte Netzwerkbetrieb aktiviert wurde:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>VM-Skalierungsgruppe

Eine VM-Skalierungsgruppe unterscheidet sich geringfügig, aber sie folgt dem gleichen Workflow.

1. Beenden Sie die VMs:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Aktualisieren Sie die Eigenschaft für den beschleunigten Netzwerkbetrieb unter der Netzwerkschnittstelle:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Legen Sie die angewandten Updates auf „Automatisch“ fest, damit die Änderungen sofort übernommen werden:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Eine VM-Skalierungsgruppe verfügt über VM-Upgrades, die Aktualisierungen entsprechend drei unterschiedlichen Einstellungen anwenden: „Automatisch“, „Parallel“ (Rolling) und „Manuell“. In diesen Anweisungen wird die Richtlinie auf „Automatisch“ festgelegt, sodass die VM-Skalierungsgruppe die Änderungen sofort nach dem Neustart übernimmt.

4. Starten Sie die Skalierungsgruppe neu:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Warten Sie nach dem Neustart, bis die Upgrades abgeschlossen sind. Nachdem die Upgrades durchgeführt wurden, wird die virtuelle Funktion (VF) innerhalb der VM angezeigt. Stellen Sie sicher, dass Sie ein unterstütztes Betriebssystem und eine unterstützte VM-Größe verwenden.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändern der Größe vorhandener VMs mit beschleunigtem Netzwerkbetrieb

Wenn für eine VM der beschleunigte Netzwerkbetrieb aktiviert ist, können Sie die Größe nur auf eine VM ändern, die den beschleunigten Netzwerkbetrieb unterstützt.  

Eine VM mit aktiviertem beschleunigtem Netzwerkbetrieb kann nicht in eine VM-Instanz geändert werden, die den beschleunigten Netzwerkbetrieb nicht unterstützt. Ändern Sie die VM-Größe stattdessen in eine der folgenden VMs:

1. Beenden Sie den virtuellen Computer, oder heben Sie seine Zuordnung auf. Beenden Sie für eine Verfügbarkeitsgruppe oder eine Skalierungsgruppe alle VMs in der Verfügbarkeitsgruppe oder der Skalierungsgruppe, oder heben Sie ihre Zuordnung auf.

2. Deaktivieren Sie den beschleunigten Netzwerkbetrieb für die Netzwerkschnittstelle der VM. Deaktivieren Sie für eine Verfügbarkeitsgruppe oder einer Skalierungsgruppe den beschleunigten Netzwerkbetrieb auf den NICs aller VMs in der Verfügbarkeitsgruppe oder der Skalierungsgruppe.

3. Nachdem Sie den beschleunigten Netzwerkbetrieb deaktiviert haben, verschieben Sie die VM, die Verfügbarkeitsgruppe oder die Skalierungsgruppe auf eine neue Größe, die den beschleunigten Netzwerkbetrieb nicht unterstützt, und starten Sie sie dann neu.