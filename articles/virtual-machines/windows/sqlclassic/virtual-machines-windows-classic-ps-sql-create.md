---
title: Erstellen eines virtuellen Computers mit SQL Server in Azure PowerShell (klassisch) | Microsoft Docs
description: Enthält Schritte und PowerShell-Skripts zum Erstellen eines virtuellen Azure-Computers über Images aus dem Katalog von virtuellen Computern mit SQL Server. In diesem Thema wird der klassische Bereitstellungsmodus verwendet.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad8b59a9290c533a3687b5ff8956d8682fb6d9e9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607831"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Bereitstellen eines virtuellen SQL Server-Computers mit Azure PowerShell (klassisch)

Dieser Artikel enthält Schritte zum Erstellen eines virtuellen Computers mit SQL Server in Azure durch Verwenden von PowerShell-Cmdlets.

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

Die Version dieses Themas zum Resource Manager finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers mit Azure PowerShell (Resource Manager)](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installieren und Konfigurieren von PowerShell:
1. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/)besuchen.
2. [Laden Sie die neuesten Azure PowerShell-Befehle herunter, und installieren Sie sie](/powershell/azure/overview).
3. Starten Sie Windows PowerShell, und verknüpfen Sie dann mithilfe des Befehls **Add-AzureAccount** PowerShell mit Ihrem Azure-Abonnement.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Bestimmen Sie Ihre Azure-Zielregion

Ihr virtueller Computer mit SQL Server wird in einem Clouddienst gehostet, der sich in einer bestimmte Azure-Region befindet. Anhand der folgenden Schritte können Sie Ihre Region, Ihr Speicherkonto und Ihren Clouddienst bestimmen, die für den Rest des Lernprogramms verwendet werden.

1. Bestimmen Sie das Rechenzentrum, das Sie zum Hosten Ihres virtuellen Computers mit SQL Server verwenden möchten. Mit dem folgenden PowerShell-Befehl wird eine Liste der verfügbaren Regionsnamen angezeigt.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Sobald Sie Ihren bevorzugten Standort ermittelt haben, legen Sie die Variable **$dcLocation** auf diese Region fest. Beispielsweise wird die Region mit dem folgenden Befehl auf „USA, Osten“ festgelegt:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Festlegen Ihres Abonnements und Speicherkontos

1. Bestimmen Sie das Azure-Abonnement, das Sie für den neuen virtuellen Computer verwenden möchten.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Weisen Sie Ihr Azure-Zielabonnement der **$subscr** -Variablen zu. Verwenden Sie dann diese Variable, um Ihr aktuelles Azure-Abonnements festzulegen.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Prüfen Sie danach auf vorhandene Speicherkonten. Das folgende Skript zeigt alle Speicherkonten an, die es in der von Ihnen ausgewählten Region gibt:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Wenn Sie ein neues Speicherkonto benötigen, erstellen Sie zunächst mit dem New-AzureStorageAccount-Befehl einen vollständig aus Kleinbuchstaben bestehenden Speicherkontonamen wie im folgenden Beispiel: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Weisen Sie der **$staccount**-Variablen den Namen des Zielspeicherkontos zu. Verwenden Sie dann **Set-AzureSubscription** , um das Abonnement und das aktuelle Speicherkonto festzulegen.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Auswählen eines Image für einen virtuellen Computer mit SQL Server

1. Ermitteln Sie im Katalog die Liste der verfügbaren Images für virtuelle Computer mit SQL Server. Jedes dieser Images hat eine **ImageFamily** -Eigenschaft, die mit "SQL" beginnt. Die folgende Abfrage zeigt die Familie der Images an, die für Sie verfügbar sind und in denen SQL Server vorinstalliert ist.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Wenn Sie die Imagefamilie für virtuelle Computer suchen, finden Sie möglicherweise mehrere veröffentlichte Images in dieser Familie. Verwenden Sie das folgende Skript, um den Namen des Images zu ermitteln, das für die von Ihnen ausgewählte Imagefamilie das zuletzt veröffentlichte Image für virtuelle Computer ist (z.B. **SQL Server 2016 RTM Enterprise on Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

Erstellen Sie schließlich den virtuellen Computer PowerShell:

1. Erstellen Sie einen Clouddienst, um den neuen virtuellen Computer zu hosten. Sie können stattdessen auch einen vorhandenen Clouddienst verwenden. Erstellen Sie eine neue Variable **$svcname** mit dem kurzen Namen des Clouddiensts.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Geben Sie den Namen für den virtuellen Computer sowie dessen Größe an. Weitere Informationen über Größen von virtuellen Computern finden Sie unter [Größen für virtuelle Azure-Computer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Geben Sie den Namen und das Kennwort des lokalen Administratorkontos an.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Führen Sie das folgende Skript aus, um den virtuellen Computer zu erstellen:

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Zusätzliche Erläuterungen sowie Konfigurationsoptionen finden Sie im Abschnitt **Erstellen des Befehlssatzes** in [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>PowerShell-Beispielskript

Das folgende Skript ist ein Beispiel für ein vollständiges Skript, das einen virtuellen **SQL Server 2016 RTM Enterprise on Windows Server 2012 R2**-Computer erstellt. Wenn Sie dieses Skript verwenden, müssen Sie die anfänglichen Variablen entsprechend den vorherigen Schritten in diesem Thema anpassen.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Verbinden mit Remotedesktop

1. Erstellen Sie die RDP-Dateien im Ordner „Dokumente“ des aktuellen Benutzers, damit diese virtuellen Computer gestartet werden können, um die Einrichtung abzuschließen:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Starten Sie die RDP-Datei im Verzeichnis „Dokumente“. Stellen Sie über die früher bereitgestellten Administrator-Anmeldeinformationen (Benutzername und Kennwort) eine Verbindung her (ist Ihr Benutzername z. B. „VMAdmin“, geben Sie „\VMAdmin“ als Benutzer ein, und stellen Sie das Kennwort bereit).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Konfigurieren des virtuellen Computers mit SQL Server für Remotezugriff

Nachdem Sie sich über Remotedesktop bei dem Computer angemeldet haben, konfigurieren Sie SQL Server entsprechend den Anleitungen unter [Schritte zum Konfigurieren von SQL Server-Konnektivität in einer Azure-VM](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Nächste Schritte

Weitere Anleitungen zur Bereitstellung von virtuellen Computern mit PowerShell finden Sie in der [Dokumentation über virtuelle Computer](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

In vielen Fällen ist der nächste Schritt, die Datenbanken auf diese neue SQL Server-VM zu migrieren. Anleitungen zur Datenbankmigration finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Wenn Sie auch wissen möchten, wie Sie das Azure-Portal zum Erstellen von virtuellen SQL-Computern verwenden können, lesen Sie [Bereitstellen eines virtuellen Computers mit SQL Server im Azure-Portal](../sql/virtual-machines-windows-portal-sql-server-provision.md). Beachten Sie, dass das Tutorial, das Sie durch das Portal führt, die VMs mithilfe des empfohlenen Resource Manager-Modells anstelle des klassischen, in diesem PowerShell-Thema verwendeten Modells erstellt.

Zusätzlich zu diesen Ressourcen wird empfohlen, dass Sie sich die [anderen Themen im Zusammenhang mit Ausführen von SQL Server auf virtuellen Azure Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md)ansehen.
