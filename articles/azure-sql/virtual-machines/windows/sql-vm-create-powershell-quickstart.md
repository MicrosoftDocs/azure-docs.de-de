---
title: Erstellen von SQL Server auf einem virtuellen Windows-Computer mit Azure-PowerShell | Microsoft-Dokumentation
description: In diesem Tutorial wird gezeigt, wie Azure PowerShell zum Erstellen eines virtuellen Windows-Computers mit SQL Server 2017 verwendet wird.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356836"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Schnellstart: Erstellen von SQL Server auf einem virtuellen Windows-Computer mit Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure PowerShell einen virtuellen SQL Server-Computer (VM) erstellen.

> [!TIP]
> - Sie erfahren, wie Sie schnell einen virtuellen SQL-Computer bereitstellen und eine Verbindung damit herstellen. Weitere Informationen zu anderen Azure PowerShell-Optionen für die Erstellung virtueller SQL-Computer finden Sie im [Bereitstellungshandbuch für virtuelle SQL Server-Computer mit Azure PowerShell](create-sql-vm-powershell.md).
> - Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Einrichten eines Azure-Abonnements

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Abrufen von Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurieren von PowerShell

1. Öffnen Sie PowerShell, und führen Sie den Befehl **Connect-AzAccount** aus, um den Zugriff auf Ihr Azure-Konto einzurichten.

   ```powershell
   Connect-AzAccount
   ```

1. Wenn das Anmeldefenster angezeigt wird, geben Sie Ihre Anmeldeinformationen ein. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Definieren Sie eine Variable mit einem eindeutigen Ressourcengruppennamen. Dieser Name wird der Einfachheit halber bei den übrigen Befehlen in dieser Schnellstartanleitung als Grundlage für andere Ressourcennamen verwendet.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Definieren Sie einen Standort einer Azure-Zielregion für alle VM-Ressourcen.

   ```powershell
   $Location = "East US"
   ```

1. Erstellen Sie die Ressourcengruppe.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Konfigurieren der Netzwerkeinstellungen

1. Erstellen Sie ein virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse. Diese Ressourcen dienen dazu, dem virtuellen Computer Netzwerkkonnektivität bereitzustellen und ihn mit dem Internet zu verbinden.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Erstellen Sie eine Netzwerksicherheitsgruppe. Konfigurieren Sie Regeln, um Remotedesktop- und SQL Server-Verbindungen zuzulassen.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Erstellen Sie die Netzwerkschnittstelle.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Erstellen des virtuellen SQL-Computers

1. Definieren Sie Ihre Anmeldeinformationen für den virtuellen Computer. Der Benutzername lautet „azureadmin“. Denken Sie daran, \<password> zu ändern, bevor Sie den Befehl ausführen.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Erstellen Sie ein VM-Konfigurationsobjekt, und erstellen Sie dann den virtuellen Computer. Der folgende Befehl erstellt einen virtuellen Computer mit SQL Server 2017 Developer Edition unter Windows Server 2016:

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Die Erstellung des virtuellen Computers dauert einige Minuten.

## <a name="register-with-sql-vm-rp"></a>Registrieren bei SQL-VM-RP 

Um die Portalintegration und die SQL-VM-Features nutzen zu können, müssen Sie sich bei der [Erweiterung für den SQL-IaaS-Agent](sql-agent-extension-manually-register-single-vm.md) registrieren.

Damit der vollständige Funktionsumfang zur Verfügung steht, müssen Sie sich im vollständigen Modus bei der Erweiterung registrieren. Dadurch wird jedoch der SQL Server-Dienst neu gestartet. Die empfohlene Vorgehensweise ist daher, sich im Lightweight-Modus zu registrieren und dann während eines Wartungsfensters auf den vollständigen Modus zu aktualisieren. 

Registrieren Sie die SQL Server-VM zunächst im Modus „Lightweight“: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Führen Sie dann während eines Wartungsfensters ein Upgrade auf den vollständigen Modus durch: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Herstellen einer Remotedesktopverbindung mit dem virtuellen Computer

1. Rufen Sie mithilfe des folgenden Befehls die öffentliche IP-Adresse für den neuen virtuellen Computer ab:

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Übergeben Sie die zurückgegebene IP-Adresse als Befehlszeilenparameter an **mstsc**, um eine Remotedesktopsitzung mit dem neuen virtuellen Computer zu starten:

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, entscheiden Sie sich für die Eingabe von Anmeldeinformationen für ein anderes Konto. Geben Sie den Benutzernamen mit einem vorangestellten umgekehrten Schrägstrich (Beispiel: `\azureadmin`) sowie das Kennwort ein, das Sie zuvor in dieser Schnellstartanleitung festgelegt haben.

## <a name="connect-to-sql-server"></a>Verbindung mit SQL Server herstellen

1. Starten Sie nach der Anmeldung bei der Remotedesktopsitzung **SQL Server Management Studio 2017** über das Startmenü.

1. Behalten Sie im Dialogfeld **Mit Server verbinden** die Standardeinstellungen bei. Der Servername ist der Name des virtuellen Computers. Die Authentifizierung ist auf **Windows-Authentifizierung** festgelegt. Wählen Sie **Verbinden**.

Sie sind nun lokal mit SQL Server verbunden. Falls Sie eine Remoteverbindung herstellen möchten, müssen Sie die [Konnektivität konfigurieren](ways-to-connect-to-sql.md) (entweder über das Azure-Portal oder manuell).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn der virtuelle Computer nicht kontinuierlich ausgeführt werden muss, können Sie ihn beenden, wenn er nicht verwendet wird, um unnötige Kosten zu vermeiden. Mit dem folgenden Befehl wird der virtuelle Computer beendet, bleibt aber für eine spätere Verwendung verfügbar.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Mit dem Befehl **Remove-AzResourceGroup** können Sie alle Ressourcen, die der VM zugeordnet sind, endgültig entfernen. Dadurch wird auch der virtuelle Computer endgültig gelöscht. Seien Sie daher vorsichtig, wenn Sie diesen Befehl verwenden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie unter Verwendung von Azure PowerShell einen virtuellen Computer mit SQL Server 2017 erstellt. Weitere Informationen zum Migrieren Ihrer Daten zu dem neuen SQL Server-Computer finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md)
