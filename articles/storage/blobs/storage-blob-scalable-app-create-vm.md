---
title: Erstellen eines virtuellen Computers und eines Speicherkontos für eine skalierbare Anwendung in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen virtuellen Computer bereitstellen, auf dem eine skalierbare Anwendung mit Azure Blob Storage ausgeführt werden kann.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 38fd62eff663c7714acf00afe3ffa559c1eeb7e0
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729096"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Erstellen eines virtuellen Computers und eines Speicherkontos für eine skalierbare Anwendung

Dieses Tutorial ist der erste Teil einer Serie. In diesem Tutorial wird gezeigt, wie Sie eine Anwendung bereitstellen, in der große Mengen von Zufallsdaten mit einem Azure-Speicherkonto hochgeladen und heruntergeladen werden. Am Ende des Tutorials haben Sie eine Konsolenanwendung, die auf einem virtuellen Computer ausgeführt wird und in der Sie große Datenmengen aus einem Speicherkonto herunterladen oder in dieses Konto hochladen können.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Speicherkonto erstellen
> * Erstellen eines virtuellen Computers
> * Konfigurieren einer benutzerdefinierten Skripterweiterung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version Az 0.7 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
 
In dem Beispiel werden 50 große Dateien in einen BlOB-Container in einem Azure Storage-Konto hochgeladen. Ein Speicherkonto stellt einen eindeutigen Namespace zum Speichern Ihrer Azure Storage-Datenobjekte sowie für den Zugriff darauf bereit. Erstellen Sie mit dem Befehl [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) ein Speicherkonto in der von Ihnen erstellten Ressourcengruppe.

Ersetzen Sie im folgenden Befehl den Platzhalter `<blob_storage_account>` durch Ihren eigenen global eindeutigen Namen für das Blob Storage-Konto.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie eine VM-Konfiguration. Diese Konfiguration umfasst die beim Bereitstellen der VM verwendeten Einstellungen, z.B. ein Image des virtuellen Computers, Größe und Authentifizierungskonfiguration. Wenn Sie diesen Schritt ausführen, werden Sie aufgefordert, Anmeldeinformationen einzugeben. Die Werte, die Sie eingeben, werden als Benutzername und Kennwort für den virtuellen Computer konfiguriert.

Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer.

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Bereitstellen der Konfiguration

Für dieses Tutorial sind einige Komponenten erforderlich, die auf dem virtuellen Computer installiert werden müssen. Die benutzerdefinierte Skripterweiterung wird verwendet, um ein PowerShell-Skript auszuführen, in dem die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Installieren von .NET Core 2.0
> * Installieren von Chocolatey
> * Installieren von Git
> * Klonen des Beispielrepositorys
> * Wiederherstellen der NuGet-Pakete
> * Erstellen von 50 1-GB-Dateien mit Zufallsdaten

Führen Sie das folgende Cmdlet aus, um die Konfiguration des virtuellen Computers abzuschließen. Für diesen Schritt werden kann 5 bis 15 Minuten benötigt.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil dieser Reihe haben Sie erfahren, wie Sie ein Speicherkonto erstellen, einen virtuellen Computer bereitstellen und den virtuellen Computer mit den erforderlichen Komponenten konfigurieren, wozu Folgendes gehört:

> [!div class="checklist"]
> * Speicherkonto erstellen
> * Erstellen eines virtuellen Computers
> * Konfigurieren einer benutzerdefinierten Skripterweiterung

Fahren Sie mit dem vierten Teil der Reihe fort, in dem Sie große Datenmengen in ein Speicherkonto hochladen, wozu Sie exponentielle Wiederholung und Parallelismus verwenden.

> [!div class="nextstepaction"]
> [Paralleles Hochladen großer Mengen von Zufallsdaten in ein Azure Storage-Konto](storage-blob-scalable-app-upload-files.md)
