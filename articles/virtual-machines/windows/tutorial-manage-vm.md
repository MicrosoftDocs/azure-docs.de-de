---
title: 'Tutorial: Erstellen und Verwalten von virtuellen Windows-Computern mit Azure PowerShell | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Verwalten virtueller Windows-Computer in Azure verwenden.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6a23ca469aab582fed4b4e72f48bc2dd0b9dafe0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807788"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Tutorial: Erstellen und Verwalten von virtuellen Windows-Computern mit Azure PowerShell

Virtuelle Azure-Computer bieten eine vollständig konfigurierbare und flexible Computerumgebung. In diesem Tutorial werden grundlegende Bereitstellungsaufgaben für virtuelle Azure-Computer (Virtual Machines, VMs) behandelt – etwa das Auswählen einer VM-Größe oder eines VM-Images und das Bereitstellen eines virtuellen Computers. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers und Herstellen einer Verbindung mit ihm
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Ändern der Größe eines virtuellen Computers
> * Anzeigen und Verstehen des Status von virtuellen Computern

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit dem Befehl [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupVM* in der Region *EastUS* erstellt:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Die Ressourcengruppe wird beim Erstellen oder Ändern eines virtuellen Computers angegeben und ist im gesamten Tutorial zu sehen.

## <a name="create-a-vm"></a>Erstellen einer VM

Beim Erstellen eines virtuellen Computers stehen mehrere Optionen zur Verfügung. Hierzu zählen unter anderem Betriebssystemimage, Netzwerkkonfiguration und Administratoranmeldeinformationen. In diesem Beispiel wird ein virtueller Computer namens *myVM* mit der Standardversion von Windows Server 2016 Datacenter erstellt.

Legen Sie mit [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6) den Benutzernamen und das Kennwort für das Administratorkonto auf dem virtuellen Computer fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) den virtuellen Computer.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Herstellen einer Verbindung mit dem virtuellen Computer

Stellen Sie nach Abschluss der Bereitstellung eine Remotedesktopverbindung mit dem virtuellen Computer her.

Führen Sie die folgenden Befehle aus, um die öffentliche IP-Adresse des virtuellen Computers zurückzugeben. Notieren Sie sich diese IP-Adresse. Sie wird in einem späteren Schritt über den Browser aufgerufen, um die Webkonnektivität zu testen.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Führen Sie auf Ihrem lokalen Computer den folgenden Befehl aus, um eine Remotedesktopsitzung mit dem virtuellen Computer zu erstellen. Ersetzen Sie die IP-Adresse durch die öffentliche IP-Adresse (*publicIPAddress*) Ihres virtuellen Computers. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen ein, die Sie beim Erstellen des virtuellen Computers verwendet haben.

```powershell
mstsc /v:<publicIpAddress>
```

Wählen Sie im Fenster **Windows-Sicherheit** die Option **Weitere Optionen** und dann **Anderes Konto verwenden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie für den virtuellen Computer erstellt haben, und klicken Sie dann auf **OK**.

## <a name="understand-marketplace-images"></a>Grundlegendes zu Marketplace-Images

Der Azure Marketplace bietet zahlreiche Images, die zum Erstellen eines neuen virtuellen Computers verwendet werden können. In den vorherigen Schritten wurde ein virtueller Computer mit dem Windows Server 2016 Datacenter-Image erstellt. In diesem Schritt wird der Marketplace mithilfe des PowerShell-Moduls nach weiteren Windows-Images durchsucht, die ebenfalls als Grundlage für neue virtuelle Computer verwendet werden können. Dieser Prozess umfasst die Suche nach dem Herausgeber, dem Angebot, der SKU und optional einer Versionsnummer zur [Identifizierung](cli-ps-findimage.md#terminology) des Images.

Führen Sie den Befehl [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) aus, um eine Liste mit Imageherausgebern abzurufen:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Führen Sie den Befehl [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) aus, um eine Liste mit Imageangeboten abzurufen. Mit diesem Befehl wird die zurückgegebene Liste nach dem angegebenen Herausgeber (`MicrosoftWindowsServer`) gefiltert:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Die Ergebnisse sehen in etwa wie folgt aus: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

Mit dem Befehl [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) wird dann nach dem Herausgeber und dem Angebotsnamen gefiltert und eine Liste mit Imagenamen zurückgegeben.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Die Ergebnisse sehen in etwa wie folgt aus: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Mithilfe dieser Informationen kann ein virtueller Computer mit einem spezifischen Image bereitgestellt werden. In diesem Beispiel wird ein virtueller Computer mit der neuesten Version eines Images vom Typ „Windows Server 2016 mit Containern“ bereitgestellt.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Mit dem Parameter `-AsJob` wird die VM als Hintergrundaufgabe erstellt, sodass die PowerShell-Aufforderungen für Sie zurückgegeben werden. Sie können die Details von Hintergrundaufträgen mit dem `Get-Job`-Cmdlet anzeigen.

## <a name="understand-vm-sizes"></a>Grundlegendes zu VM-Größen

Die Größe eines virtuellen Computers bestimmt die Menge an Computeressourcen (CPU, GPU und Arbeitsspeicher), die für den virtuellen Computer zur Verfügung gestellt werden. Virtuelle Computer müssen mit einer Größe erstellt werden, die für die erwartete Workload angemessen ist. Bei einer Zunahme der Workload kann die Größe eines vorhandenen virtuellen Computers auch geändert werden.

### <a name="vm-sizes"></a>VM-Größen

In der folgenden Tabelle sind Größen in Anwendungsfällen kategorisiert.  

| Type                     | Gängige Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Allgemeiner Zweck](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Entwicklung und Tests, kleine bis mittlere Anwendungen und Datenlösungen.  |
| [Computeoptimiert](sizes-compute.md)   | Fsv2            | Hohes Verhältnis von CPU zu Arbeitsspeicher. Geeignet für Anwendungen, Network Appliances und Batch-Prozesse mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Hohes Verhältnis von Speicher zu Kern. Hervorragend geeignet für relationale Datenbanken, mittlere bis große Caches und In-Memory-Analysen.                 |
| [Speicheroptimiert](sizes-storage.md)      | Lsv2, Ls                | Datenträgerdurchsatz und -E/A auf hohem Niveau. Ideal für Big Data sowie SQL- und NoSQL-Datenbanken.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, NDv2, ND   | Spezialisierte virtuelle Computer für aufwendiges Grafikrendering und aufwendige Videobearbeitung.       |
| [Hohe Leistung](sizes-hpc.md) | H        | Unsere virtuellen Computer mit den leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen. |

### <a name="find-available-vm-sizes"></a>Ermitteln der verfügbaren VM-Größen

Eine Liste der in einer bestimmten Region verfügbaren VM-Größen können Sie mit dem Befehl [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) abrufen.

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Ändern der Größe eines virtuellen Computers

Nach der Bereitstellung eines virtuellen Computers kann dessen Größe geändert werden, um die Ressourcenzuordnung zu erhöhen oder zu verringern.

Prüfen Sie vor der Größenänderung eines virtuellen Computers, ob die gewünschte Größe im aktuellen VM-Cluster verfügbar ist. Mit dem Befehl [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) wird eine Liste der Größen zurückgegeben.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Wenn die gewünschte Größe verfügbar ist, kann die Größe des virtuellen Computers im eingeschalteten Zustand geändert werden, er muss jedoch während des Vorgangs neu gestartet werden.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Sollte die gewünschte Größe im aktuellen Cluster nicht verfügbar sein, muss die Zuordnung des virtuellen Computers aufgehoben werden, damit die Größenänderung erfolgen kann. Beim Aufheben der Zuordnung eines virtuellen Computers werden alle Daten auf dem temporären Datenträger entfernt, und die öffentliche IP-Adresse ändert sich, sofern keine statische IP-Adresse verwendet wird.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Betriebszustände von virtuellen Computern

Ein virtueller Azure-Computer kann einen von mehreren Betriebszuständen aufweisen. 


| Betriebszustand | BESCHREIBUNG
|----|----|
| Wird gestartet | Der virtuelle Computer wird gestartet. |
| Wird ausgeführt | Der virtuelle Computer wird ausgeführt. |
| Wird beendet | Der virtuelle Computer wird beendet. |
| Beendet | Die VM wurde beendet. Für virtuelle Computer mit beendetem Zustand fallen weiterhin Computegebühren an.  |
| Zuordnung wird aufgehoben | Die Zuordnung des virtuellen Computers wird aufgehoben. |
| Zuordnung aufgehoben | Gibt an, dass der virtuelle Computer aus dem Hypervisor entfernt wurde, auf der Steuerungsebene jedoch weiterhin verfügbar ist. Im Zustand `Deallocated` fallen für virtuelle Computer keine Computegebühren an. |
| - | Der Betriebszustand des virtuellen Computers ist nicht bekannt. |


Verwenden Sie zum Abrufen des Zustands eines bestimmten virtuellen Computers den Befehl [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Achten Sie darauf, dass Sie einen gültigen Namen für einen virtuellen Computer und eine Ressourcengruppe angeben.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Die Ausgabe sieht in etwa wie folgt aus:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Verwaltungsaufgaben

Während der Lebensdauer eines virtuellen Computers können Sie Verwaltungsaufgaben wie das Starten, Beenden oder Löschen eines virtuellen Computers ausführen. Darüber hinaus empfiehlt es sich, Skripts zum Automatisieren von wiederkehrenden oder komplexen Aufgaben zu erstellen. Mit Azure PowerShell können viele allgemeine Verwaltungsaufgaben über die Befehlszeile oder in Skripts ausgeführt werden.

### <a name="stop-a-vm"></a>Anhalten eines virtuellen Computers

Verwenden Sie [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm), um einen virtuellen Computer zu beenden und seine Zuordnung aufzuheben:

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Soll der virtuelle Computer bereitgestellt bleiben, verwenden Sie den Parameter „-StayProvisioned“.

### <a name="start-a-vm"></a>Starten eines virtuellen Computers

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Ressourcengruppe löschen

Beim Löschen einer Ressourcengruppe werden alle Elemente innerhalb dieser Ressourcengruppe gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zur grundlegenden Erstellung und Verwaltung von virtuellen Computern erhalten, darunter:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers und Herstellen einer Verbindung mit ihm
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Ändern der Größe eines virtuellen Computers
> * Anzeigen und Verstehen des Status von virtuellen Computern

Im nächsten Tutorial erhalten Sie Informationen zu VM-Datenträgern.  

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von VM-Datenträgern](./tutorial-manage-data-disk.md)
