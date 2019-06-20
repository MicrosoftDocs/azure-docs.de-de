---
title: Erstellen und Verwalten virtueller Windows-Computer in Azure mit mehreren Netzwerkkarten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über Azure PowerShell oder mithilfe von Resource Manager-Vorlagen einen virtuellen Windows-Computer mit mehreren angefügten Netzwerkkarten erstellen und verwalten.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 14adf6e7b4b998f128c2e4a1ef146ea90469c635
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698146"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Erstellen und Verwalten eines virtuellen Windows-Computers mit mehrere Netzwerkkarten
Virtuelle Computer (VMs) in Azure können über mehrere virtuelle Netzwerkkarten (Network Interface Cards, NICs) verfügen. Ein häufiges Szenario ist das Vorhandensein unterschiedlicher Subnetze für Front-End- und Back-End-Konnektivität. Sie können mehrere NICs auf einem virtuellen Computer mehreren Subnetzen zuordnen, aber diese Subnetze müssen sich alle im gleichen virtuellen Netzwerk (VNET) befinden. In diesem Artikel erfahren Sie, wie Sie einen virtuellen Computer mit mehreren Netzwerkkarten erstellen. Außerdem erfahren Sie, wie Sie Netzwerkkarten zu einem vorhandenen virtuellen Computer hinzufügen oder davon entfernen. Verschiedene [VM-Größen](sizes.md) unterstützen eine unterschiedliche Anzahl von Netzwerkkarten, passen Sie die Größe Ihres virtuellen Computers daher entsprechend an.

## <a name="prerequisites"></a>Voraussetzungen

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u.a. *myResourceGroup*, *myVnet* und *myVM*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten (NICs)
Erstellen Sie zunächst eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *EastUs*:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Erstellen des virtuellen Netzwerks und der Subnetze
Ein häufiges Szenario ist ein virtuelles Netzwerk mit zwei oder mehr Subnetzen. Ein Subnetz kann für den Front-End-Datenverkehr, das andere für den Back-End-Datenverkehr verwendet werden. Sie verwenden mehrere Netzwerkkarten auf Ihrem virtuellen Computer, um eine Verbindung mit beiden Subnetzen herzustellen.

1. Definieren Sie mit [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) zwei virtuelle Netzwerksubnetze. Das folgende Beispiel definiert die Subnetze für *mySubnetFrontEnd* und *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Erstellen Sie mit [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Ihr virtuelles Netzwerk und die Subnetze. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* erstellt:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Erstellen von mehreren Netzwerkkarten
Erstellen Sie mit [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) zwei Netzwerkkarten. Fügen Sie eine Netzwerkkarte an das Front-End-Subnetz und die andere an das Back-End-Subnetz an. Im folgenden Beispiel werden zwei Netzwerkkarten namens *myNic1* und *myNic2* erstellt:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

In der Regel erstellen Sie auch eine [Netzwerksicherheitsgruppe](../../virtual-network/security-overview.md), um den Netzwerkdatenverkehr zur VM zu filtern, und einen [Lastenausgleich](../../load-balancer/load-balancer-overview.md) zum Verteilen des Datenverkehrs auf mehrere VMs.

### <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers
Beginnen Sie jetzt damit, Ihre VM-Konfiguration zu erstellen. Jede VM-Größe weist eine maximale Anzahl von Netzwerkkarten auf, die Sie einem virtuellen Computer hinzufügen können. Weitere Informationen finden Sie unter [Windows-VM-Größen](sizes.md) .

1. Legen Sie Ihre VM-Anmeldeinformationen wie folgt auf die Variable `$cred` fest:

    ```powershell
    $cred = Get-Credential
    ```

2. Definieren Sie Ihren virtuellen Computer mit [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). Im folgenden Beispiel wird ein virtueller Computer mit dem Namen *myVM* definiert und eine VM-Größe verwendet, die mehr als zwei Netzwerkkarten unterstützt (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Erstellen Sie die restliche Konfiguration des virtuellen Computers mit [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) und [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). Im folgenden Beispiel wird eine Windows Server 2016-VM erstellt:

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

4. Fügen Sie die beiden Netzwerkkarten, die Sie zuvor erstellt haben, mit [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) an:

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Erstellen Sie mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Ihren virtuellen Computer:

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Fügen Sie Routen für sekundäre NICs zum Betriebssystem hinzu. Führen Sie dazu die Schritte unter [Erstellen und Verwalten eines virtuellen Windows-Computers mit mehreren Netzwerkkarten](#configure-guest-os-for-multiple-nics) aus.

## <a name="add-a-nic-to-an-existing-vm"></a>Hinzufügen einer Netzwerkkarte auf einem vorhandenen virtuellen Computer
Wenn Sie einem vorhandenen virtuellen Computer eine virtuelle Netzwerkkarte hinzufügen möchten, heben Sie die Zuordnung des virtuellen Computers auf, fügen Sie die virtuelle Netzwerkkarte hinzu, und starten Sie anschließend den virtuellen Computer. Verschiedene [VM-Größen](sizes.md) unterstützen eine unterschiedliche Anzahl von Netzwerkkarten, passen Sie die Größe Ihres virtuellen Computers daher entsprechend an. Bei Bedarf können Sie die [Größe eines virtuellen Computers ändern](resize-vm.md).

1. Heben Sie mit [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) die Zuordnung des virtuellen Computers auf. Im folgenden Beispiel wird die Zuordnung für den virtuellen Computer *myVM* in *myResourceGroup* aufgehoben:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Rufen Sie die vorhandene Konfiguration des virtuellen Computers mit [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ab. Im folgenden Beispiel werden Informationen für den virtuellen Computer namens *myVM* in der Ressourcengruppe mit dem Namen *myResourceGroup* abgerufen:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Im folgenden Beispiel wird mit [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) eine virtuelle Netzwerkkarte mit dem Namen *myNic3* erstellt, die an *mySubnetBackEnd* angefügt ist. Die virtuelle Netzwerkkarte wird dann mit [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) an den virtuellen Computer mit dem Namen *myVM* in *myResourceGroup* angefügt:

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primäre virtuelle Netzwerkkarten
    Eine der Netzwerkkarten auf einem virtuellen Computer mit mehreren Netzwerkkarten muss primär sein. Wenn eine der vorhandenen virtuellen Netzwerkkarten auf dem virtuellen Computer bereits als primär festgelegt wurde, können Sie diesen Schritt überspringen. Im folgenden Beispiel wird davon ausgegangen, dass auf einem virtuellen Computer zwei virtuelle Netzwerkkarten vorhanden sind und Sie die erste Netzwerkkarte (`[0]`) als primäre Netzwerkkarte hinzufügen möchten:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Starten Sie den virtuellen Computer mit [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Fügen Sie Routen für sekundäre NICs zum Betriebssystem hinzu. Führen Sie dazu die Schritte unter [Erstellen und Verwalten eines virtuellen Windows-Computers mit mehreren Netzwerkkarten](#configure-guest-os-for-multiple-nics) aus.

## <a name="remove-a-nic-from-an-existing-vm"></a>Entfernen einer Netzwerkkarte von einem vorhandenen virtuellen Computer
Wenn Sie von einem vorhandenen virtuellen Computer eine virtuelle Netzwerkkarte entfernen möchten, heben Sie die Zuordnung des virtuellen Computers auf, entfernen Sie die virtuelle Netzwerkkarte, und starten Sie anschließend den virtuellen Computer.

1. Heben Sie mit [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) die Zuordnung des virtuellen Computers auf. Im folgenden Beispiel wird die Zuordnung für den virtuellen Computer *myVM* in *myResourceGroup* aufgehoben:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Rufen Sie die vorhandene Konfiguration des virtuellen Computers mit [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ab. Im folgenden Beispiel werden Informationen für den virtuellen Computer namens *myVM* in der Ressourcengruppe mit dem Namen *myResourceGroup* abgerufen:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Rufen Sie die Informationen zur zu entfernenden Netzwerkkarte mit [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) ab. Im folgenden Beispiel werden Informationen über *myNic3*abgerufen.

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Entfernen Sie die Netzwerkkarte mit [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface), und aktualisieren Sie anschließend den virtuellen Computer mit [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). Im folgenden Beispiel wird *myNic3* (im vorherigen Schritt durch `$nicId` abgerufen) entfernt:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Starten Sie den virtuellen Computer mit [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Erstellen Sie mehrere Netzwerkkarten mit Vorlagen
Azure Resource Manager-Vorlagen bieten eine Möglichkeit, während der Bereitstellung mehrere Instanzen einer Ressource zu erstellen – z.B. mehrere Netzwerkkarten. Resource Manager-Vorlagen verwenden deklarative JSON-Dateien zum Definieren Ihrer Umgebung. Weitere Informationen finden Sie in der [Übersicht über den Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Mithilfe von *copy* können Sie die Anzahl der zu erstellenden Instanzen angeben:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Weitere Informationen finden Sie unter [Erstellen mehrerer Instanzen mithilfe von *copy*](../../resource-group-create-multiple.md). 

Sie können einem Ressourcennamen auch mithilfe von `copyIndex()` eine Nummer anfügen. Daraufhin können Sie *myNic1*, *MyNic2* usw. erstellen. Der folgende Code veranschaulicht das Anfügen des Indexwerts:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Hier finden Sie auch ein vollständiges Beispiel für das [Erstellen von mehreren Netzwerkkarten mithilfe von Resource Manager-Vorlagen](../../virtual-network/template-samples.md).

Fügen Sie Routen für sekundäre NICs zum Betriebssystem hinzu. Führen Sie dazu die Schritte unter [Erstellen und Verwalten eines virtuellen Windows-Computers mit mehreren Netzwerkkarten](#configure-guest-os-for-multiple-nics) aus.

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurieren von Gastbetriebssystem für mehrere Netzwerkadapter

Azure weist der ersten (primären) Netzwerkschnittstelle, die an den virtuellen Computer angefügt ist, ein Standardgateway zu. Azure weist zusätzlichen (sekundären) Netzwerkschnittstellen, die an einen virtuellen Computer angefügt sind, kein Standardgateway zu. Daher können Sie standardmäßig nicht mit Ressourcen außerhalb des Subnetzes kommunizieren, in dem sich eine sekundäre Netzwerkschnittstelle befindet. Sekundäre Netzwerkschnittstellen können jedoch mit Ressourcen außerhalb ihres Subnetzes kommunizieren. Die Schritte zum Aktivieren der Kommunikation unterscheiden sich allerdings je nach Betriebssystem.

1. Führen Sie an einer Windows-Eingabeaufforderung den Befehl `route print` aus, der für einen virtuellen Computer mit zwei angefügten Netzwerkschnittstellen eine Ausgabe ähnlich der folgenden zurückgibt:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    In diesem Beispiel ist **Microsoft Hyper-V Network Adapter #4** (Schnittstelle 7) die sekundäre Netzwerkschnittstelle, der kein Standardgateway zugeordnet ist.

2. Führen Sie an einer Eingabeaufforderung den Befehl `ipconfig` aus, um zu prüfen, welche IP-Adresse der sekundären Netzwerkschnittstelle zugewiesen ist. In diesem Beispiel ist 192.168.2.4 der Schnittstelle 7 zugewiesen. Für die sekundäre Netzwerkschnittstelle wird keine Standardgateway-Adresse zurückgegeben.

3. Um den gesamten Datenverkehr für Adressen außerhalb des Subnetzes der sekundären Netzwerkschnittstelle an das Gateway des Subnetzes zu leiten, führen Sie den folgenden Befehl aus:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Die Gatewayadresse für das Subnetz ist die erste IP-Adresse (mit der Endung .1) im für das Subnetz definierten Adressbereich. Wenn Sie nicht den gesamten Datenverkehr außerhalb des Subnetzes weiterleiten möchten, können Sie stattdessen einzelne Routen zu bestimmten Zielen hinzufügen. Wenn Sie beispielsweise nur den Datenverkehr von der sekundären Netzwerkschnittstelle an das Netzwerk 192.168.3.0 weiterleiten möchten, geben Sie den folgenden Befehl ein:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Um beispielsweise eine erfolgreiche Kommunikation mit einer Ressource im Netzwerk 192.168.3.0 zu bestätigen, geben Sie den folgenden Befehl ein, um 192.168.3.4 über die Schnittstelle 7 (192.168.2.4) zu pingen:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Möglicherweise müssen Sie ICMP über die Windows-Firewall des Geräts öffnen, das Sie mit dem folgenden Befehl pingen:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Um zu bestätigen, dass die hinzugefügte Route in der Routentabelle enthalten ist, geben Sie den Befehl `route print` ein, der eine Ausgabe ähnlich dem folgenden Text liefert:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Die mit *192.168.1.1* unter **Gateway** aufgeführte Route ist die Route, die standardmäßig für die primäre Netzwerkschnittstelle vorhanden ist. Die Route mit *192.168.2.1* unter **Gateway** ist die von Ihnen hinzugefügte Route.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die [Größen für virtuelle Windows-Computer](sizes.md), wenn Sie einen virtuellen Computer mit mehreren Netzwerkkarten erstellen. Achten Sie auf die maximale Anzahl von Netzwerkkarten, die von jeder VM-Größe unterstützt wird. 


