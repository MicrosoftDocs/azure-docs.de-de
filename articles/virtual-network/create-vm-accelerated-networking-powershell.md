---
title: Erstellen virtueller Azure-Computer mit beschleunigtem Netzwerkbetrieb | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen virtuellen Linux-Computer mit beschleunigtem Netzwerkbetrieb erstellen.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: ef6086afa17f1ab864d70678a6da6df2a78e0c16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190293"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb

In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Windows-Computer (VM) mit beschleunigtem Netzwerkbetrieb erstellen. Informationen zum Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](create-vm-accelerated-networking-cli.md). Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) in einer VM und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen für Hochleistung konzipierten Pfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden. Die folgende Abbildung zeigt die Kommunikation zwischen zwei VMs mit und ohne beschleunigten Netzwerkbetrieb:

![Vergleich](./media/create-vm-accelerated-networking/accelerated-networking.png)

Ohne beschleunigten Netzwerkbetrieb muss sämtlicher Netzwerkdatenverkehr zur und von der VM den Host und virtuellen Switch passieren. Der virtuelle Switch setzt alle Richtlinien für den Netzwerkdatenverkehr um, z.B. Netzwerksicherheitsgruppen, Zugriffssteuerungslisten, Isolation und andere virtualisierte Netzwerkdienste. Weitere Informationen zu virtuellen Switches finden Sie im Artikel [Übersicht über Hyper-V-Netzwerkvirtualisierung und virtuellen Switch](https://technet.microsoft.com/library/jj945275.aspx).

Mit beschleunigtem Netzwerkbetrieb gelangt der Netzwerkdatenverkehr zur Netzwerkkarte (NIC) der VM und wird dann an die VM weitergeleitet. Alle Netzwerkrichtlinien, die vom virtuellen Switch angewendet werden, werden ab sofort ausgelagert und in der Hardware angewendet. Durch das Anwenden von Richtlinien in der Hardware kann die NIC Netzwerkdatenverkehr direkt an den virtuellen Computer weiterleiten und dabei den Host und den virtuellen Switch umgehen, während gleichzeitig sämtliche Richtlinien umgesetzt werden, die sonst auf dem Host angewendet worden wären.

Die Vorteile des beschleunigten Netzwerkbetriebs gelten nur für die VM, auf der dieses Feature aktiviert ist. Die besten Ergebnisse erzielen Sie, wenn Sie dieses Feature auf mindestens zwei VMs aktivieren, die mit demselben virtuellen Azure-Netzwerk (VNet) verbunden sind. Bei der Kommunikation über VNets oder bei lokalen Verbindungen hat dieses Feature nur minimale Auswirkungen auf die Wartezeit.

## <a name="benefits"></a>Vorteile
* **Niedrigere Latenz / mehr Pakete pro Sekunde (pps):** Durch das Entfernen des virtuellen Switch aus dem Datenpfad wird die Zeit reduziert, die Pakete auf dem Host für die Verarbeitung von Richtlinien warten müssen. Gleichzeitig wird die Anzahl von Paketen erhöht, die im virtuellen Computer verarbeitet werden können.
* **Reduzierte Jitter:** Die Verarbeitung im virtuellen Switch hängt vom Umfang der anzuwendenden Richtlinien und von der Workload der CPU ab, die die Verarbeitung durchführt. Das Auslagern der Richtlinienerzwingung auf die Hardware entfernt diesen Umweg, indem die Pakete direkt an den virtuellen Computer gesendet werden. Damit werden der Host und alle Softwareinterrupts und Kontextwechsel aus der VM-Kommunikation entfernt.
* **Verringerte CPU-Auslastung:** Das Umgehen des virtuellen Switch auf dem Host führt zu weniger CPU-Auslastung für die Verarbeitung des Netzwerkdatenverkehrs.

## <a name="limitations-and-constraints"></a>Einschränkungen

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Die folgenden Distributionen werden standardmäßig aus dem Azure-Katalog unterstützt:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Unterstützte VM-Instanzen
Der beschleunigte Netzwerkbetrieb wird in den meisten universellen, computeoptimierten Instanzgrößen mit mindestens 2 vCPUs unterstützt.  Folgende Reihen werden unterstützt: D/DSv2 und F/Fs

Bei Instanzen, die Hyperthreading unterstützen, wird der beschleunigte Netzwerkbetrieb auf VM-Instanzen mit mindestens 4 vCPUs unterstützt. Folgende Reihen werden unterstützt: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms und Ms/Mmsv2.

Weitere Informationen zu VM-Instanzen finden Sie unter [Größen für virtuelle Windows-Computer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regions
Verfügbar in allen öffentlichen Azure-Regionen und in der Azure Government Cloud

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf einer ausgeführten VM
Bei einer unterstützten VM-Größe ohne aktivierten beschleunigten Netzwerkbetrieb kann das Feature nur aktiviert werden, wenn die VM beendet und ihre Zuordnung aufgehoben wird.

### <a name="deployment-through-azure-resource-manager"></a>Bereitstellung über Azure Resource Manager
Virtuelle Computer (klassisch) können nicht mit beschleunigtem Netzwerkbetrieb bereitgestellt werden.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Erstellen einer Windows-VM mit Azure „Beschleunigter Netzwerkbetrieb“
## <a name="portal-creation"></a>Erstellung über das Portal
In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Computers mit beschleunigtem Netzwerkbetrieb mithilfe von Azure PowerShell dargestellt, Sie können jedoch auch [über das Azure-Portal einen virtuellen Computer mit beschleunigtem Netzwerkbetrieb erstellen](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wenn Sie einen virtuellen Computer im Portal erstellen, wählen Sie im Blatt **Virtuellen Computer erstellen** die Registerkarte **Netzwerkbetrieb** aus.  Auf dieser Registerkarte gibt es eine Option für **Beschleunigter Netzwerkbetrieb**.  Wenn Sie ein [unterstütztes Betriebssystem](#supported-operating-systems) und eine [unterstützte VM-Größe](#supported-vm-instances) ausgewählt haben, wird diese Option automatisch auf „Ein“ gesetzt.  Wenn nicht, wird die Option für „Beschleunigter Netzwerkbetrieb“ auf „Aus“ festgelegt, und dem Benutzer wird der Grund angezeigt, warum der beschleunigte Netzwerkbetrieb nicht aktiviert wird.   
* *Hinweis:* Nur unterstützte Betriebssysteme können über das Portal aktiviert werden.  Wenn Sie ein benutzerdefiniertes Image verwenden und Ihr Image den beschleunigten Netzwerkbetrieb unterstützt, erstellen Sie Ihre VM mit CLI oder Powershell. 

Nach dem Erstellen des virtuellen Computers können Sie sich vergewissern, dass der beschleunigte Netzwerkbetrieb aktiviert ist, indem Sie den Anweisungen unter „Bestätigen der Aktivierung des beschleunigten Netzwerkbetriebs“ folgen.

## <a name="powershell-creation"></a>PowerShell-Erstellung
## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), Version 1.0.0 oder höher. Zur Ermittlung der derzeit installierten Version führen Sie `Get-Module -ListAvailable Az` aus. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, installieren Sie die aktuelle Version des Az-Moduls über den [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az). Melden Sie sich in einer PowerShell-Sitzung mithilfe von [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto an.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Zu den Parameternamen zählen z.B. *myResourceGroup*, *myNic* und *myVm*.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe. Im folgenden Beispiel wird am Standort *centralus* eine Ressourcengruppe namens *myResourceGroup* erstellt:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Erstellen Sie zunächst mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) eine Subnetzkonfiguration. Im folgenden Beispiel wird das Subnetz *mySubnet* erstellt:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) ein virtuelles Netzwerk mit dem Subnetz *mySubnet*.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie zunächst mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig) eine Netzwerksicherheitsgruppen-Regel.

```powershell
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

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) eine Netzwerksicherheitsgruppe, und weisen Sie die Sicherheitsregel *Allow-RDP-All* zu. Zusätzlich zur Regel *Allow-RDP-All* enthält die Netzwerksicherheitsgruppe noch mehrere Standardregeln. Eine Standardregel deaktiviert den gesamten über das Internet eingehenden Zugriff. Daher wird die Regel *Allow-RDP-All* der Netzwerksicherheitsgruppe zugewiesen, sodass Sie eine Remoteverbindung mit dem virtuellen Computer herstellen können, nachdem dieser erstellt wurde.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Ordnen Sie mit [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig) die Netzwerksicherheitsgruppe dem Subnetz *mySubnet* zu. Die Regel in der Netzwerksicherheitsgruppe gilt für alle Ressourcen, die im Subnetz bereitgestellt werden.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Erstellen einer Netzwerkschnittstelle mit beschleunigtem Netzwerkbetrieb
Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) eine öffentliche IP-Adresse. Wenn Sie nicht vorhaben, über das Internet auf den virtuellen Computer zuzugreifen, ist keine öffentliche IP-Adresse erforderlich – diese wird jedoch für die Durchführung der Schritte in diesem Artikel benötigt.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) eine Netzwerkschnittstelle mit beschleunigtem Netzwerkbetrieb, und weisen Sie der Netzwerkschnittstelle die öffentliche IP-Adresse zu. Das folgende Beispiel erstellt die Netzwerkschnittstelle *myNic* im Subnetz *mySubnet* des virtuellen Netzwerks *myVnet* und weist ihr die öffentliche IP-Adresse *myPublicIp*  zu:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Legen Sie Ihre VM-Anmeldeinformationen mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) auf die `$cred`-Variable fest:

```powershell
$cred = Get-Credential
```

Definieren Sie zunächst Ihren virtuellen Computer mit [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Im folgenden Beispiel wird die VM *myVM* mit einer VM-Größe definiert, die beschleunigten Netzwerkbetrieb unterstützt (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Eine Liste aller VM-Größen und -Eigenschaften finden Sie unter [Größen für virtuelle Windows-Computer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Erstellen Sie die restliche Konfiguration des virtuellen Computers mit [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) und [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Im folgenden Beispiel wird eine Windows Server 2016-VM erstellt:

```powershell
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

Fügen Sie die beiden Netzwerkschnittstellen, die Sie zuvor erstellt haben, mit [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) an:

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Erstellen Sie dann Ihren virtuellen Computer mit [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Überprüfen, ob der Treiber im Betriebssystem installiert ist

Stellen Sie nach dem Erstellen des virtuellen Computers in Azure eine Verbindung mit dem virtuellen Computer her, und vergewissern Sie sich, dass der Treiber unter Windows installiert ist.

1. Navigieren Sie in einem Internetbrowser zum [Azure-Portal](https://portal.azure.com), und melden Sie sich bei Ihrem Azure-Konto an.
2. Geben Sie oben im Azure-Portal im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *myVm* ein. Wenn **myVm** in den Suchergebnissen angezeigt wird, klicken Sie darauf. Wenn **Wird erstellt** unter der Schaltfläche **Verbinden** angezeigt wird, hat Azure das Erstellen der VM noch nicht abgeschlossen. Klicken Sie erst in der linken oberen Ecke auf **Verbinden**, nachdem **Wird erstellt** unter der Schaltfläche **Verbinden** nicht mehr angezeigt wird.
3. Geben Sie den Benutzernamen und das Kennwort ein, die Sie in [Erstellen des virtuellen Computers](#create-the-virtual-machine) verwendet haben. Wenn Sie noch nie eine Verbindung mit einer Windows-VM in Azure hergestellt haben, finden Sie unter [Herstellen der Verbindung mit dem virtuellen Computer](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine) weitere Informationen.
4. Klicken Sie mit der rechten Maustaste auf die Windows-Schaltfläche „Start“, und klicken Sie auf **Geräte-Manager**. Erweitern Sie den Knoten **Netzwerkadapter**. Vergewissern Sie sich, dass **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** (wie in der folgenden Abbildung gezeigt) angezeigt wird:

    ![Geräte-Manager](./media/create-vm-accelerated-networking/device-manager.png)

Der beschleunigte Netzwerkbetrieb ist nun für Ihre VM aktiviert.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf vorhandenen VMs
Wenn Sie eine VM ohne beschleunigten Netzwerkbetrieb erstellt haben, können Sie dieses Feature auf dieser VM aktivieren.  Die VM muss folgende Voraussetzungen erfüllen, um den beschleunigten Netzwerkbetrieb zu unterstützen (siehe auch oben):

* Die VM muss eine unterstützte Größe für den beschleunigten Netzwerkbetrieb aufweisen.
* Bei der VM muss es sich um ein unterstütztes Image aus dem Azure-Katalog (und eine unterstützte Kernelversion für Linux) handeln.
* Alle VMs in einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppe müssen beendet und ihre Zuordnung muss aufgehoben werden, bevor Sie den beschleunigten Netzwerkbetrieb in einer Netzwerkschnittstelle aktivieren können.

### <a name="individual-vms--vms-in-an-availability-set"></a>Einzelne VMs und VMs in einer Verfügbarkeitsgruppe
Beenden Sie die VM, und heben Sie ihre Zuordnung auf. Falls es sich um eine Verfügbarkeitsgruppe handelt, führen Sie diese Vorgänge für alle VMs in der Gruppe aus:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Wichtiger Hinweis: Wenn Ihre VM einzeln erstellt wurde (ohne Verfügbarkeitsgruppe), müssen Sie nur diese eine VM beenden und ihre Zuordnung aufheben, um den beschleunigten Netzwerkbetrieb zu aktivieren.  Wenn Ihre VM mit einer Verfügbarkeitsgruppe erstellt wurde, müssen alle VMs, die in dieser Verfügbarkeitsgruppe enthalten sind, beendet und deren Zuordnungen aufgehoben werden, bevor Sie den beschleunigten Netzwerkbetrieb in einer der Netzwerkschnittstellen aktivieren können. 

Aktivieren Sie dann den beschleunigten Netzwerkbetrieb in der Netzwerkschnittstelle Ihrer VM:

```azurepowershell
$nic = Get-AzureRMNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRMNetworkInterface
```

Starten Sie Ihre VM bzw. im Fall einer Verfügbarkeitsgruppe alle VMs in der Gruppe neu, und überprüfen Sie, ob der beschleunigte Netzwerkbetrieb aktiviert wurde:

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VM-Skalierungssgruppe
Eine VM-Skalierungsgruppe funktioniert etwas anders, folgt aber dem gleichen Workflow.  Beenden Sie zuerst die VMs:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Sobald alle VMs beendet sind, aktualisieren Sie die Eigenschaft für den beschleunigten Netzwerkbetrieb in der Netzwerkschnittstelle:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Beachten Sie, dass eine VM-Skalierungsgruppe VM-Upgrades hat, die Aktualisierungen entsprechend drei unterschiedlichen Einstellungen anwenden: „Automatisch“, „Parallel“ (Rolling) und „Manuell“.  In diesen Anweisungen wird die Richtlinie auf „Automatisch“ festgelegt, sodass die VM-Skalierungsgruppe die Änderungen sofort nach dem Neustart übernimmt.  So legen Sie die Einstellung auf „Automatisch“ fest, damit die Änderungen sofort übernommen werden:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Starten Sie schließlich die VM-Skalierungsgruppe neu:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Warten Sie, bis die Upgrades nach dem Neustart vollständig abgeschlossen sind. Nach dem Abschluss wird das Feature in der VM angezeigt.  (Stellen Sie sicher, dass Sie ein unterstütztes Betriebssystem und eine unterstützte VM-Größe verwenden.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändern der Größe vorhandener VMs mit beschleunigtem Netzwerkbetrieb

VMs mit aktiviertem beschleunigtem Netzwerkbetrieb können nur zu VM-Größen geändert werden, die den beschleunigten Netzwerkbetrieb unterstützen.  

Eine VM mit aktiviertem beschleunigtem Netzwerkbetrieb kann nicht in eine VM-Instanz geändert werden, die den beschleunigten Netzwerkbetrieb nicht unterstützt.  Ändern Sie die VM-Größe stattdessen in eine der folgenden VMs:

* Beenden Sie die VM, und heben Sie ihre Zuordnung auf. Falls es sich um eine VM-Skalierungsgruppe handelt, führen Sie diese Vorgänge für alle VMs in der Gruppe aus.
* Der beschleunigte Netzwerkbetrieb muss in der Netzwerkschnittstelle der VM bzw. im Fall einer Verfügbarkeits- oder VM-Skalierungsgruppe in den Netzwerkschnittstellen aller VMs deaktiviert werden.
* Sobald der beschleunigte Netzwerkbetrieb deaktiviert ist, kann die VM bzw. die Verfügbarkeits- oder VM-Skalierungsgruppe in eine neue Größe ohne Unterstützung des beschleunigten Netzwerkbetriebs geändert und neu gestartet werden.
