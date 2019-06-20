---
title: 'Tutorial: Erstellen und Verwalten einer Azure-VM-Skalierungsgruppe | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell eine VM-Skalierungsgruppe erstellen und einige allgemeine Verwaltungsaufgaben ausführen, um beispielsweise eine Instanz zu starten und zu beenden oder die Kapazität der Skalierungsgruppe zu ändern.
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
ms.date: 05/18/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 694fc0ba6d59497cfc53efb6f2607bc6a7d4ad2d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728696"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Erstellen und Verwalten einer VM-Skalierungsgruppe mit Azure PowerShell

Mit einer VM-Skalierungsgruppe können Sie eine Gruppe identischer, automatisch skalierender virtueller Computer bereitstellen und verwalten. Während des Lebenszyklus einer Skalierungsgruppe müssen unter Umständen verschiedene Verwaltungsaufgaben durchgeführt werden. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer VM-Skalierungsgruppe und Herstellen einer Verbindung
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Instanzgrößen
> * Manuelles Skalieren einer Skalierungsgruppe
> * Ausführen allgemeiner Verwaltungsaufgaben für Skalierungsgruppen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor der VM-Skalierungsgruppe muss zunächst eine Ressourcengruppe erstellt werden. Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *EastUS* erstellt. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Der Ressourcengruppenname wird im gesamten Tutorial beim Erstellen oder Ändern einer Skalierungsgruppe angegeben.


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) zuerst den Benutzernamen und das Kennwort des Administrators der VM-Instanzen fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie nun mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe dauert einige Minuten.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Anzeigen der VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) wie folgt, um eine Liste mit den VM-Instanzen in einer Skalierungsgruppe anzuzeigen:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Die folgende Beispielausgabe zeigt zwei VM-Instanzen in der Skalierungsgruppe:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Wenn Sie zusätzliche Informationen zu einer bestimmten VM-Instanz anzeigen möchten, fügen Sie [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) den Parameter `-InstanceId` hinzu. Im folgenden Beispiel werden Informationen zur VM-Instanz *1* angezeigt:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Auflisten von Verbindungsinformationen
Dem Lastenausgleich, der Datenverkehr an die einzelnen VM-Instanzen weiterleitet, wird eine öffentliche IP-Adresse zugewiesen. Standardmäßig werden dem Azure-Lastenausgleich, der den Datenverkehr von Remoteverbindungen über einen bestimmten Port an die einzelnen virtuellen Computer weiterleitet, Regeln für die Netzwerkadressübersetzung (Network Address Translation, NAT) hinzugefügt. Wenn Sie eine Verbindung mit den VM-Instanzen in einer Skalierungsgruppe herstellen möchten, erstellen Sie eine Remoteverbindung mit einer zugewiesenen öffentlichen IP-Adresse und Portnummer.

Rufen Sie zuerst mit [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) das Lastenausgleichsobjekt ab, um die NAT-Ports zum Herstellen einer Verbindung mit den VM-Instanzen in einer Skalierungsgruppe aufzulisten. Zeigen Sie anschließend mit [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) die NAT-Regeln für eingehenden Datenverkehr an.


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Die folgende Beispielausgabe enthält den Instanznamen, die öffentliche IP-Adresse des Lastenausgleichs und die Portnummer, an die Datenverkehr von den NAT-Regeln weitergeleitet wird:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Der *Name* der Regel ist am Namen der VM-Instanz ausgerichtet, wie im vorherigen Befehl [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) zu sehen ist. Wenn Sie beispielsweise eine Verbindung mit der VM-Instanz *0* herstellen möchten, verwenden Sie *myScaleSet3389.0* und stellen die Verbindung mit Port *50001* her. Verwenden Sie zum Herstellen einer Verbindung mit der VM-Instanz *1* den Wert aus *myScaleSet3389.1*, und stellen Sie eine Verbindung mit Port *50002* her. Für die Verwendung von PowerShell-Remoting stellen Sie eine Verbindung mit der entsprechenden VM-Instanzregel für *TCP*-Port *5985* her.

Zeigen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) die öffentliche IP-Adresse des Lastenausgleichs an:


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Beispielausgabe:

```powershell
IpAddress
---------
52.168.121.216
```

Erstellen Sie eine Remoteverbindung mit Ihrer ersten VM-Instanz. Geben Sie Ihre öffentliche IP-Adresse und Portnummer der erforderlichen VM-Instanz an, wie in den vorherigen Befehlen zu sehen. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen ein, die Sie beim Erstellen der Skalierungsgruppe verwendet haben (in den Beispielbefehlen standardmäßig *azureuser* und *P\@ssw0rd!* ). Führen Sie diesen Schritt bei Verwendung von Azure Cloud Shell an einer lokalen PowerShell-Eingabeaufforderung oder über einen Remotedesktopclient aus. Im folgenden Beispiel wird eine Verbindung mit der VM-Instanz *1* hergestellt:

```powershell
mstsc /v 52.168.121.216:50001
```

Nach der Anmeldung bei der VM-Instanz können Sie nach Bedarf einige manuelle Konfigurationsänderungen vornehmen. Schließen Sie die Remoteverbindung erst einmal.


## <a name="understand-vm-instance-images"></a>Grundlegendes zu VM-Instanzimages
Im Azure Marketplace stehen zahlreiche Images zur Verfügung, die Sie zum Erstellen von VM-Instanzen verwenden können. Verwenden Sie den Befehl [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), um eine Liste mit den verfügbaren Herausgebern anzuzeigen.

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Verwenden Sie [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku), um eine Liste mit Images für einen bestimmten Herausgeber anzuzeigen. Die Imageliste kann auch nach `-PublisherName` oder `–Offer` gefiltert werden. Im folgenden Beispiel wird die Liste für alle Images mit dem Publishernamen *MicrosoftWindowsServer* und einem Angebot gefiltert, das eine Übereinstimmung für *WindowsServer* ergibt:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

In der folgenden Beispielausgabe werden alle verfügbaren Windows Server-Images angezeigt:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Wenn Sie zu Beginn des Tutorials eine Skalierungsgruppe erstellt haben, wurde ein VM-Standardimage von *Windows Server 2016 DataCenter* für die VM-Instanzen bereitgestellt. Sie können basierend auf der Ausgabe von [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) ein anderes VM-Image angeben. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Parameter `-ImageName` erstellt, um das VM-Image *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest* anzugeben. Da die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe einige Minuten dauert, müssen Sie die folgende Skalierungsgruppe nicht bereitstellen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Grundlegendes zu VM-Instanzgrößen
Eine VM-Instanzgröße (oder *SKU*) bestimmt die Menge an Computeressourcen (CPU, GPU, Arbeitsspeicher und Ähnliches), die für die VM-Instanz zur Verfügung stehen. Die Größe der VM-Instanzen in einer Skalierungsgruppe muss auf die zu erwartende Workload abgestimmt werden.

### <a name="vm-instance-sizes"></a>VM-Instanzgrößen
In der folgenden Tabelle sind gängige VM-Größen nach Anwendungsfall kategorisiert:

| Type                     | Gängige Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Allgemeiner Zweck](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Entwicklung und Tests, kleine bis mittlere Anwendungen und Datenlösungen.  |
| [Computeoptimiert](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Hohes Verhältnis von CPU zu Arbeitsspeicher. Geeignet für Anwendungen, Network Appliances und Batch-Prozesse mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Hohes Verhältnis von Speicher zu Kern. Hervorragend geeignet für relationale Datenbanken, mittlere bis große Caches und In-Memory-Analysen.                 |
| [Speicheroptimiert](../virtual-machines/windows/sizes-storage.md)      | Ls                | Datenträgerdurchsatz und -E/A auf hohem Niveau. Ideal für Big Data sowie SQL- und NoSQL-Datenbanken.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | Spezialisierte virtuelle Computer für aufwendiges Grafikrendering und aufwendige Videobearbeitung.       |
| [Hohe Leistung](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Unsere virtuellen Computer mit den leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen. 

### <a name="find-available-vm-instance-sizes"></a>Suchen nach verfügbaren VM-Instanzgrößen
Eine Liste mit den in einer bestimmten Region verfügbaren VM-Instanzgrößen können Sie mit dem Befehl [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) abrufen. 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

Die Ausgabe ähnelt dem folgenden verkürzten Beispiel, das die Ressourcen zeigt, die den einzelnen VM-Größen zugewiesen sind:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Bei der Erstellung der Skalierungsgruppe zu Beginn des Tutorials wurde für die VM-Instanzen die VM-Standard-SKU *Standard_DS1_v2* angegeben. Auf Grundlage der Ausgabe von [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) können Sie eine andere VM-Instanzgröße angeben. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Parameter `-VmSize` erstellt, um die VM-Instanzgröße *Standard_F1* anzugeben. Da die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe einige Minuten dauert, müssen Sie die folgende Skalierungsgruppe nicht bereitstellen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändern der Kapazität einer Skalierungsgruppe
Beim Erstellen einer Skalierungsgruppe haben Sie zwei VM-Instanzen angefordert. Wenn Sie die Anzahl von VM-Instanzen in der Skalierungsgruppe erhöhen oder verringern möchten, können Sie die Kapazität manuell ändern. Die Skalierungsgruppe erstellt oder entfernt die erforderliche Anzahl von VM-Instanzen und konfiguriert anschließend den Lastenausgleich für die Verteilung des Datenverkehrs.

Erstellen Sie zunächst mit [Get-AzVmss](/powershell/module/az.compute/get-azvmss) ein Skalierierungsgruppenobjekt, und geben Sie dann einen neuen Wert für `sku.capacity` an. Verwenden Sie zum Anwenden der Kapazitätsänderung [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Im folgenden Beispiel wird die Anzahl von VM-Instanzen in der Skalierungsgruppe auf *3* festgelegt:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Die Aktualisierung der Skalierungsgruppenkapazität dauert ein paar Minuten. Verwenden Sie [Get-AzVmss](/powershell/module/az.compute/get-azvmss), um die Anzahl von Instanzen anzuzeigen, die jetzt in der Skalierungsgruppe enthalten sind:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

In der folgenden Beispielausgabe ist zu sehen, dass die Kapazität der Skalierungsgruppe jetzt *3* beträgt:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Allgemeine Verwaltungsaufgaben
Sie können nun eine Skalierungsgruppe erstellen, Verbindungsinformationen auflisten und eine Verbindung mit VM-Instanzen herstellen. Sie haben gelernt, wie Sie ein anderes Betriebssystemimage für Ihre VM-Instanzen verwenden, eine andere VM-Größe auswählen oder manuell die Instanzenanzahl skalieren. Im Rahmen der alltäglichen Verwaltung kann es vorkommen, dass Sie die VM-Instanzen in der Skalierungsgruppe beenden, starten oder neu starten müssen.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Beenden von VM-Instanzen in einer Skalierungsgruppe und Aufheben ihrer Zuordnung
Verwenden Sie [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), um virtuelle Computer in einer Skalierungsgruppe zu beenden. Mit dem Parameter `-InstanceId` können Sie die zu beendenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe beendet. Im folgenden Beispiel wird die Instanz *1* beendet:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Standardmäßig wird die Zuordnung beendeter VMs aufgehoben, woraufhin keine Computegebühren mehr anfallen. Wenn Sie möchten, dass die VM in einem bereitgestellten Zustand verbleibt, nachdem sie beendet wurde, fügen Sie dem vorhergehenden Befehl den Parameter `-StayProvisioned` hinzu. Für beendete virtuelle Computer, die bereitgestellt bleiben, fallen die üblichen Computegebühren an.

### <a name="start-vm-instances-in-a-scale-set"></a>Starten von VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [Start-AzVmss](/powershell/module/az.compute/start-azvmss), um virtuelle Computer in einer Skalierungsgruppe zu starten. Mit dem Parameter `-InstanceId` können Sie die zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe gestartet. Im folgenden Beispiel wird die Instanz *1* gestartet:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Neustarten von VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss), um virtuelle Computer in einer Skalierungsgruppe neu zu starten. Mit dem Parameter `-InstanceId` können Sie die neu zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe neu gestartet. Im folgenden Beispiel wird die Instanz *1* neu gestartet:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie eine Ressourcengruppe löschen, werden auch alle darin enthaltenen Ressourcen (wie VM-Instanzen, das virtuelle Netzwerk und die Datenträger) gelöscht. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie mit Azure PowerShell einige grundlegende Erstellungs- und Verwaltungsaufgaben für Skalierungsgruppen durchführen:

> [!div class="checklist"]
> * Erstellen einer VM-Skalierungsgruppe und Herstellen einer Verbindung
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Manuelles Skalieren einer Skalierungsgruppe
> * Ausführen allgemeiner Verwaltungsaufgaben für Skalierungsgruppen

Im nächsten Tutorial erhalten Sie Informationen zu Datenträgern für Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Verwenden von Datenträgern mit Skalierungsgruppen](tutorial-use-disks-powershell.md)
