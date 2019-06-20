---
title: Tutorial – Verwenden eines benutzerdefinierten VM-Images in einer Skalierungsgruppe mit Azure PowerShell | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure PowerShell zum Erstellen eines benutzerdefinierten VM-Images verwenden, das Sie zum Bereitstellen einer VM-Skalierungsgruppe verwenden können.
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bd605ac3a4dd3f878dd3d5b861374816243f3467
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728553"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Erstellen und Verwenden eines benutzerdefinierten Images für VM-Skalierungsgruppen mit Azure PowerShell

Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Sie können ein benutzerdefiniertes VM-Image verwenden, um die Anzahl von Aufgaben zu reduzieren, nachdem VM-Instanzen bereitgestellt wurden. Dieses benutzerdefinierte VM-Image enthält alle erforderlichen Anwendungsinstallationen oder -konfigurationen. Für alle VM-Instanzen, die in der Skalierungsgruppe erstellt werden, wird das benutzerdefinierte VM-Image verwendet, und die VM-Instanzen sind für die Bereitstellung Ihres Anwendungsdatenverkehrs bereit. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Anpassen einer VM
> * Aufheben der Bereitstellung und Generalisieren der VM
> * Erstellen eines benutzerdefinierten VM-Images von der Quell-VM
> * Bereitstellen einer Skalierungsgruppe, für die das benutzerdefinierte VM-Image verwendet wird

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Erstellen und Konfigurieren einer Quell-VM

>[!NOTE]
> In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein generalisiertes VM-Image erstellen und verwenden. Das Erstellen einer Skalierungsgruppe auf der Grundlage einer speziellen VHD wird nicht unterstützt.

Erstellen Sie zunächst mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe und dann mit [New-AzVM](/powershell/module/az.compute/new-azvm) eine VM. Diese VM wird dann als Quelle für ein benutzerdefiniertes VM-Image verwendet. Im folgenden Beispiel wird eine VM mit dem Namen *myCustomVM* in der Ressourcengruppe *myResourceGroup* erstellt. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für die VM ein:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Listen Sie zum Herstellen einer Verbindung mit dem virtuellen Computer mithilfe von [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) wie folgt die öffentliche IP-Adresse auf:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Erstellen Sie eine Remoteverbindung mit der VM. Führen Sie diesen Schritt bei Verwendung von Azure Cloud Shell an einer lokalen PowerShell-Eingabeaufforderung oder über einen Remotedesktopclient aus. Geben Sie Ihre eigene IP-Adresse aus dem vorherigen Befehl an. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen ein, die Sie im ersten Schritt beim Erstellen des virtuellen Computers verwendet haben:

```powershell
mstsc /v:<IpAddress>
```

Installieren Sie einen einfachen Webserver, um Ihre VM anzupassen. Wenn die VM-Instanz in der Skalierungsgruppe bereitgestellt wird, verfügt sie über alle erforderlichen Pakete zum Ausführen einer Webanwendung. Öffnen Sie auf dem virtuellen Computer eine lokale PowerShell-Eingabeaufforderung, und installieren Sie den IIS-Webserver wie folgt mit [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature):

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Im letzten Schritt der Vorbereitung Ihrer VM für die Nutzung als benutzerdefiniertes Image generalisieren Sie die VM. Sysprep entfernt Ihre gesamten persönlichen Kontoinformationen und -konfigurationen und setzt die VM für zukünftige Bereitstellungen in einen bereinigten Zustand zurück. Weitere Informationen finden Sie unter [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Verwenden von Sysprep: Eine Einführung).

Führen Sie zum Generalisieren der VM Sysprep aus, und richten Sie für die VM eine sofort einsatzbereite Benutzeroberfläche ein. Wenn Sie fertig sind, können Sie Sysprep anweisen, die VM herunterzufahren:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Die Remoteverbindung mit der VM wird automatisch geschlossen, wenn Sysprep den Prozess abgeschlossen hat und die VM heruntergefahren wird.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Erstellen eines benutzerdefinierten VM-Images von der Quell-VM
Die Quell-VM wird jetzt mit dem installierten IIS-Webserver angepasst. Wir erstellen das benutzerdefinierte VM-Image für die Verwendung mit einer Skalierungsgruppe.

Zum Erstellen eines Images muss die Zuordnung des virtuellen Computers aufgehoben werden. Heben Sie mit [Stop-AzVm](/powershell/module/az.compute/stop-azvm) die Zuordnung des virtuellen Computers auf. Legen Sie anschließend mit [Set-AzVm](/powershell/module/az.compute/set-azvm) den Zustand des virtuellen Computers auf „Generalisiert“ fest, um die Azure-Plattform darüber zu informieren, dass der virtuelle Computer als benutzerdefiniertes Image verwendet werden kann. Sie können nur aus einer generalisierten VM ein Image erstellen:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Es kann einige Minuten dauern, die Zuordnung der VM aufzuheben und sie zu generalisieren.

Erstellen Sie nun mit [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) und [New-AzImage](/powershell/module/az.compute/new-azimage) ein Image des virtuellen Computers. Im folgenden Beispiel wird ein Image mit dem Namen *myImage* aus Ihrer VM erstellt:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Erstellen einer Skalierungsgruppe aus dem benutzerdefinierten VM-Image
Erstellen Sie jetzt mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine Skalierungsgruppe, für die der Parameter `-ImageName` genutzt wird, um das im vorherigen Schritt erstellte VM-Image zu definieren. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Um Ihre Skalierungsgruppe in Aktion zu sehen, rufen Sie wie folgt mit [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Geben Sie die öffentliche IP-Adresse in Ihren Webbrowser ein. Die IIS-Standardwebseite wird angezeigt, wie im folgenden Beispiel dargestellt:

![IIS-Ausführung über das benutzerdefinierte VM-Image](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) die Ressourcengruppe und alle dazugehörigen Ressourcen, um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image für Ihre Skalierungsgruppen mit Azure PowerShell erstellen und verwenden:

> [!div class="checklist"]
> * Erstellen und Anpassen einer VM
> * Aufheben der Bereitstellung und Generalisieren der VM
> * Erstellen eines benutzerdefinierten VM-Images
> * Bereitstellen einer Skalierungsgruppe, für die das benutzerdefinierte VM-Image verwendet wird

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Anwendungen in Ihrer Skalierungsgruppe bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Anwendungen in Ihren Skalierungsgruppen](tutorial-install-apps-powershell.md)
