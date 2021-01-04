---
title: Leitfaden zum Bereitstellen von SQL Server auf Azure-VMs mit Azure PowerShell
description: Enthält Schritte und PowerShell-Befehle zum Erstellen eines virtuellen Azure-Computers über Images aus dem Katalog von virtuellen Computern mit SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3f51a07b274320d1cd9f12b33703d8ec7f21f49
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359658"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Bereitstellen von SQL Server auf virtuellen Azure-Computern mit Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Leitfaden behandelt die Optionen für die Verwendung von PowerShell zur Bereitstellung von SQL Server auf virtuellen Azure-Computern (VMs). Ein vereinfachtes Beispiel für Azure PowerShell anhand von Standardwerten finden Sie im [Schnellstart zum Erstellen einer SQL Server-VM mit Azure PowerShell](sql-vm-create-powershell-quickstart.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurieren Ihres Abonnements

1. Öffnen Sie PowerShell, und führen Sie den Befehl **Connect-AzAccount** aus, um den Zugriff auf Ihr Azure-Konto einzurichten.

   ```powershell
   Connect-AzAccount
   ```

1. Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

## <a name="define-image-variables"></a>Definieren von Image-Variablen

Beginnen Sie mit dem Definieren einer Reihe von Variablen, um Werte wiederzuverwenden und die Skripterstellung zu vereinfachen. Die Parameterwerte können nach Belieben geändert werden. Dabei müssen allerdings die Benennungskonventionen hinsichtlich Namenslänge und Sonderzeichen eingehalten werden.

### <a name="location-and-resource-group"></a>Ort und Ressourcengruppe

Definieren Sie den Datenbereich und die Ressourcengruppe, in der Sie die anderen VM-Ressourcen erstellen möchten.

Ändern Sie die Werte nach Bedarf, und führen Sie diese Cmdlets aus, um die Variablen zu initialisieren.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Speichereigenschaften

Definieren Sie das Speicherkonto und die Art des Speichers für den virtuellen Computer.

Ändern Sie die Werte nach Bedarf, und führen Sie das folgende Cmdlet aus, um diese Variablen zu initialisieren. Für Produktionsworkloads werden [SSD Premium-Datenträger](../../../virtual-machines/disks-types.md#premium-ssd) empfohlen.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Netzwerkeigenschaften

Definieren Sie die Eigenschaften, die vom Netzwerk auf dem virtuellen Computer verwendet werden sollen. 

- Netzwerkschnittstelle
- TCP/IP-Zuordnungsmethode
- Name des virtuellen Netzwerks
- Name des virtuellen Subnetzes
- Bereich von IP-Adressen für das virtuelle Netzwerk
- Bereich von IP-Adressen für das Subnetz
- Öffentliche Bezeichnung für die Domäne

Ändern Sie die Werte nach Bedarf, und führen Sie dieses Cmdlet aus, um die Variablen zu initialisieren.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Eigenschaften für virtuelle Computer

Definieren Sie die folgenden Eigenschaften:

- Name des virtuellen Computers
- Computername
- Größe des virtuellen Computers
- Name für den Betriebssystemdatenträger des virtuellen Computers

Ändern Sie die Werte nach Bedarf, und führen Sie dieses Cmdlet aus, um die Variablen zu initialisieren.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Auswählen eines SQL Server-Images

Definieren Sie mithilfe der folgenden Variablen das SQL Server-Image, das für den virtuellen Computer verwendet werden soll. 

1. Listen Sie zuerst mit dem Befehl `Get-AzVMImageOffer` alle SQL Server-Imageangebote auf. Dieser Befehl listet die im Azure-Portal derzeit verfügbaren Images sowie auch ältere Images auf, die nur mit PowerShell installiert werden können:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Verwenden Sie in diesem Tutorial die folgenden Variablen, um SQL Server 2017 unter Windows Server 2016 anzugeben.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Listen Sie als nächstes die verfügbaren Editionen für das Angebot auf.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Verwenden Sie in diesem Tutorial die SQL Server 2017 Developer Edition (**SQLDEV**). Die Developer Edition wird kostenlos für Test- und Entwicklungsumgebungen lizenziert – Sie bezahlen lediglich für die Kosten der VM-Ausführung.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bei Verwendung des Resource Manager-Bereitstellungsmodells wird als erstes Objekt die Ressourcengruppe erstellt. Verwenden Sie das Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), um eine Azure-Ressourcengruppe und die zugehörigen Ressourcen zu erstellen. Geben Sie die zuvor initialisierten Variablen für den Namen der Ressourcengruppe und den Speicherort an.

Führen Sie dieses Cmdlet aus, um Ihre neue Ressourcengruppe zu erstellen.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Der virtuelle Computer benötigt Speicherressourcen für den Betriebssystemdatenträger sowie für die SQL Server-Daten und -Protokolldateien. Der Einfachheit halber erstellen Sie für beides einen einzelnen Datenträger. Zusätzliche Datenträger können später mithilfe des Cmdlets [Add-Azure Disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) angefügt werden, um Ihre SQL Server-Daten und -Protokolldateien auf dedizierten Datenträgern zu platzieren. Erstellen Sie mit dem Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Standardspeicherkonto in der neuen Ressourcengruppe. Geben Sie die zuvor initialisierten Variablen für den Namen des Speicherkontos, den Namen der Speicher-SKU und den Speicherort an.

Führen Sie dieses Cmdlet aus, um Ihr neues Speicherkonto zu erstellen.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Das Erstellen des Speicherkontos kann einige Minuten dauern.

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Der virtuelle Computer benötigt für die Netzwerkkonnektivität eine Reihe von Netzwerkressourcen.

* Jeder virtuelle Computer benötigt ein virtuelles Netzwerk.
* Ein virtuelles Netzwerk muss über mindestens ein definiertes Subnetz verfügen.
* Eine Netzwerkschnittstelle muss mit einer öffentlichen oder privaten IP-Adresse definiert werden.

### <a name="create-a-virtual-network-subnet-configuration"></a>Erstellen einer Subnetzkonfiguration für ein virtuelles Netzwerk

Erstellen Sie zunächst eine Subnetzkonfiguration für das virtuelle Netzwerk. Erstellen Sie für dieses Tutorial mithilfe des Cmdlets [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) ein Standardsubnetz. Geben Sie die zuvor initialisierten Variablen für den Subnetznamen und das Adresspräfix an.

> [!NOTE]
> Mit dem Cmdlet können auch weitere Eigenschaften der Subnetzkonfiguration für das virtuelle Netzwerk definiert werden. Darauf wird in diesem Tutorial jedoch nicht weiter eingegangen.

Führen Sie dieses Cmdlet aus, um die Konfiguration des virtuellen Subnetzes zu erstellen.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie als Nächstes mithilfe des Cmdlets [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) Ihr virtuelles Netzwerk in der neuen Ressourcengruppe. Geben Sie die zuvor initialisierten Variablen für Name, Speicherort und Adresspräfix an. Verwenden Sie die Sie im vorherigen Schritt definierten Subnetzkonfiguration.

Führen Sie dieses Cmdlet aus, um Ihr virtuelles Netzwerk zu erstellen.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Erstellen der öffentlichen IP-Adresse

Nachdem Ihre virtuelles Netzwerk jetzt definiert ist, müssen sie eine IP-Adresse für die Verbindung mit dem virtuellen Computer konfigurieren. Für dieses Tutorial erstellen Sie eine öffentliche IP-Adresse mit dynamischer IP-Adressierung, um Internetverbindungen zu unterstützen. Erstellen Sie mit dem Cmdlet [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) die öffentliche IP-Adresse in der neuen Ressourcengruppe. Geben Sie die zuvor initialisierten Variablen für Name, Speicherort, Zuordnungsmethode und DNS-Domänennamensbezeichnung an.

> [!NOTE]
> Mit dem Cmdlet können auch weitere Eigenschaften der öffentlichen IP-Adresse definiert werden. Darauf wird in diesem Einstiegstutorial jedoch nicht weiter eingegangen. Sie können auch eine private Adresse oder eine Adresse mit einer statischen Adresse erstellen, aber auch das wird in diesem Tutorial nicht behandelt.

Führen Sie dieses Cmdlet aus, um Ihre öffentliche IP-Adresse zu erstellen.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Erstellen der Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe, um den Datenverkehr von virtuellem Computer und SQL Server sicherzustellen.

1. Erstellen Sie zuerst eine Netzwerksicherheitsgruppen-Regel für das Remotedesktopprotokoll (RDP), um RDP-Verbindungen zuzulassen.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurieren Sie eine Netzwerksicherheitsgruppen-Regel, die Datenverkehr an TCP-Port 1433 zulässt. So werden Verbindungen zu SQL Server über das Internet ermöglicht.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Erstellen Sie die Netzwerksicherheitsgruppe.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Erstellen der Netzwerkschnittstelle

Nun können Sie die Netzwerkschnittstelle für Ihren virtuellen Computer erstellen. Erstellen Sie mit dem Cmdlet [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) die Netzwerkschnittstelle in Ihrer neuen Ressourcengruppe. Geben Sie den Namen, Speicherort, das Subnetz und die öffentliche IP-Adresse an, die zuvor Sie definiert haben.

Führen Sie dieses Cmdlet aus, um Ihre Netzwerkschnittstelle zu erstellen.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurieren eines VM-Objekts

Nachdem die Speicher- und Netzwerkressourcen jetzt definiert sind, können Sie Computeressourcen für den virtuellen Computer definieren.

- Geben Sie die Größe des virtuellen Computers und verschiedene Betriebssystemeigenschaften an.
- Geben Sie die zuvor erstellte Netzwerkschnittstelle an.
- Definieren Sie Blobspeicher.
- Legen Sie den Betriebssystem-Datenträger fest.

### <a name="create-the-vm-object"></a>Erstellen des VM-Objekts

Legen Sie zunächst die Größe des virtuellen Computers fest. Geben Sie für dieses Tutorial DS13 an. Erstellen Sie mit dem Cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) ein konfigurierbares VM-Objekt. Geben Sie die zuvor initialisierten Variablen für Name und Größe an.

Führen Sie dieses Cmdlet aus, um das VM-Objekt zu erstellen.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Erstellen eines Anmeldeinformationsobjekts zum Speichern von Name und Kennwort für die lokalen Administratoranmeldeinformationen

Bevor Sie die Betriebssystemeigenschaften für den virtuellen Computer festlegen können, müssen Sie die Anmeldeinformationen für das lokale Administratorkonto als sichere Zeichenfolge angeben. Verwenden Sie hierzu das Cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

Führen Sie das folgende Cmdlet aus. Sie müssen den Namen und das Kennwort des lokalen Administrators der VM im PowerShell-Anforderungsfenster für Anmeldeinformationen eingeben.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Festlegen der Betriebssystemeigenschaften für den virtuellen Computer

Nun können Sie die Betriebssystemeigenschaften des virtuellen Computers mit dem Cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) festlegen.

- Legen Sie „Windows“ als Art des Betriebssystems fest.
- Legen Sie fest, dass der [Agent für virtuelle Computer](../../../virtual-machines/extensions/agent-windows.md) installiert werden muss.
- Legen Sie fest, dass das Cmdlet automatische Aktualisierungen ermöglicht.
- Geben Sie die zuvor initialisierten Variablen für den Namen des virtuellen Computers, den Computernamen und die Anmeldeinformationen an.

Führen Sie dieses Cmdlet aus, um die Betriebssystemeigenschaften für Ihren virtuellen Computer festzulegen.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Hinzufügen der Netzwerkschnittstelle zum virtuellen Computer

Verwenden Sie dann das Cmdlet [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface), um die Netzwerkschnittstelle mit der zuvor definierten Variable hinzuzufügen.

Führen Sie dieses Cmdlet aus, um die Netzwerkschnittstelle für Ihren virtuellen Computer festzulegen.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Festlegen des Blobspeicherorts für den Datenträger des virtuellen Computers

Legen Sie im nächsten Schritt mithilfe der zuvor definierten Variablen den Blobspeicherort für den VM-Datenträger fest.

Führen Sie dieses Cmdlet aus, um den Blobspeicherort festzulegen.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Festlegen der Eigenschaften des Betriebssystemdatenträgers für den virtuellen Computer

Legen Sie als Nächstes mit dem Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) die Eigenschaften des Betriebssystemdatenträgers für den virtuellen Computer fest. 

- Geben Sie an, dass das Betriebssystem für den virtuellen Computer auf einem Image basiert.
- Legen Sie eine schreibgeschützte Zwischenspeicherung fest (da SQL Server auf dem selben Datenträger installiert wird).
- Geben Sie die zuvor initialisierten Variablen für den Namen des virtuellen Computers und den Betriebssystem-Datenträger an.

Führen Sie dieses Cmdlet aus, um die Eigenschaften des Betriebssystem-Datenträgers für Ihren virtuellen Computer festzulegen.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Angeben des Plattformimage für den virtuellen Computer

Im letzten Konfigurationsschritt wird das Plattformimage für den virtuellen Computer angegeben. Für dieses Tutorial verwenden Sie das aktuelle SQL Server 2016 CTP-Image. Verwenden Sie das Cmdlet [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage), um dieses Image mit den zuvor definierten Variablen zu verwenden.

Führen Sie dieses Cmdlet aus, um das Plattformimage für Ihren virtuellen Computer festzulegen.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Erstellen des virtuellen SQL-Computers

Nach Abschluss der Konfigurationsschritte können Sie den virtuellen Computer nun erstellen. Verwenden Sie das Cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm), um den virtuellen Computer mithilfe der zuvor definierten Variablen zu erstellen.

> [!TIP]
> Das Erstellen des virtuellen Computers kann einige Minuten dauern.

Führen Sie dieses Cmdlet aus, um Ihren virtuellen Computer zu erstellen.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Der virtuelle Computer wird erstellt.

> [!NOTE]
> Wenn Sie eine Fehlermeldung bezüglich der Startdiagnose erhalten haben, können Sie sie ignorieren. Für die Startdiagnose wird ein Standardspeicherkonto erstellt, da es sich bei dem angegebenen Speicherkonto für den Datenträger des virtuellen Computers um ein Premium-Speicherkonto handelt.

## <a name="install-the-sql-iaas-agent"></a>Installieren des SQL-IaaS-Agents

SQL Server-VMs unterstützen mit der [Erweiterung für SQL Server-IaaS-Agent](sql-server-iaas-agent-extension-automate-management.md) automatische Verwaltungsfunktionen. Führen Sie nach der Erstellung des virtuellen Computers den Befehl [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) aus, um Ihre SQL Server-VM mit der Erweiterung zu registrieren. Geben Sie den Lizenztyp für Ihre SQL Server-VM an, und wählen Sie über den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) eine Lizenz mit nutzungsbasierter Bezahlung oder eine BYOL-Lizenz (Bring Your Own License) aus. Weitere Informationen zur Lizenzierung finden Sie unter [Lizenzierungsmodell](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```

Es gibt drei Möglichkeiten, VMs mit der Erweiterung zu registrieren: 
- [Automatisch für alle aktuellen und zukünftigen VMs in einem Abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuell für eine einzelne VM](sql-agent-extension-manually-register-single-vm.md)
- [Manuell für mehrere VMs in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="stop-or-remove-a-vm"></a>Beenden oder Entfernen eines virtuellen Computers

Wenn der virtuelle Computer nicht kontinuierlich ausgeführt werden muss, können Sie ihn beenden, wenn er nicht verwendet wird, um unnötige Kosten zu vermeiden. Mit dem folgenden Befehl wird der virtuelle Computer beendet, bleibt aber für eine spätere Verwendung verfügbar.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Mit dem Befehl **Remove-AzResourceGroup** können Sie alle Ressourcen, die der VM zugeordnet sind, endgültig entfernen. Dadurch wird auch der virtuelle Computer endgültig gelöscht. Seien Sie daher vorsichtig, wenn Sie diesen Befehl verwenden.

## <a name="example-script"></a>Beispielskript

Das folgende Skript enthält das vollständige PowerShell-Skript für dieses Tutorial. Es wird davon ausgegangen, dass Sie das zu verwendende Azure-Abonnement bereits mit den Befehlen **Connect-AzAccount** und **Select-AzSubscription** eingerichtet haben.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Nächste Schritte

Nach der Erstellung des virtuellen Computers haben Sie folgende Möglichkeiten:

- Verbindung mit diesem virtuellen Computer über RDP herstellen
- Konfigurieren Sie SQL Server-Einstellungen im Portal für Ihren virtuellen Computer, einschließlich:
   - [Speichereinstellungen](storage-configuration.md) 
   - [Automatisierte Verwaltungsaufgaben](sql-server-iaas-agent-extension-automate-management.md)
- [Konfigurieren von Konnektivität](ways-to-connect-to-sql.md)
- Verbinden von Clients und Anwendungen mit der neuen SQL Server-Instanz