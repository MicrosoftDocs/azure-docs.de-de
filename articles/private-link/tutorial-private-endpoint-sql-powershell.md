---
title: 'Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure PowerShell einen Azure SQL-Server mit einem privaten Endpunkt erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.openlocfilehash: 7448e003771bea1e235d4e0776ebce3cb1beae17
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544087"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – Azure PowerShell

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen eines Azure SQL-Servers und eines privaten Endpunkts
> * Testen der Konnektivität mit dem privaten Endpunkt für SQL Server

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein Subnetz und einen Bastion-Host. 

Der Bastion-Host wird verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen, um den privaten Endpunkt zu testen.

Erstellen Sie ein virtuelles Netzwerk und einen Bastion-Host mit:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

## <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt erstellen Sie einen virtuellen Computer zum Testen des privaten Endpunkts.

Erstellen Sie mithilfe der folgenden Befehle den virtuellen Computer:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Erstellen eines Azure SQL-Servers

In diesem Abschnitt erstellen Sie mithilfe der folgenden Befehle einen SQL-Server und eine Datenbank:

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

Erstellen Sie einen SQL-Server und eine Datenbank. Ersetzen Sie **\<sql-server-name>** durch einen eindeutigen Servernamen.

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie den privaten Endpunkt und die Verbindung mithilfe von:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

In diesem Abschnitt erstellen und konfigurieren Sie die private DNS-Zone mithilfe von:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem SQL-Server herzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) 
 
2. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

3. Wählen Sie **CreateSQLEndpointTutorial-rg** aus.

4. Wählen Sie **myVM** aus.

5. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

6. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

7. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

8. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

9. Geben Sie `nslookup <sqlserver-name>.database.windows.net` ein. Ersetzen Sie **\<sqlserver-name>** durch den Namen des SQL-Servers, den Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Als Name für den SQL-Server wird die private IP-Adresse **10.0.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.


10. Installieren Sie [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) auf **myVM**.

11. Öffnen Sie **SQL Server Management Studio**.

12. Geben Sie unter **Mit Server verbinden** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Servertyp | Wählen Sie **Datenbank-Engine** aus.|
    | Servername | Geben Sie **\<sql-server-name>.database.windows.net** ein. |
    | Authentifizierung | Wählen Sie **SQL Server-Authentifizierung** aus. |
    | Benutzername | Geben Sie den Benutzernamen ein, den Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort | Geben Sie das Kennwort ein, das Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort speichern | Wählen Sie **Ja** aus. |

13. Wählen Sie **Verbinden**.

14. Durchsuchen Sie Datenbanken im linken Menü.

15. (Optional) Erstellen Sie die Informationen unter **mysqldatabase**, bzw. rufen Sie sie ab.

16. Trennen Sie die Bastionhostverbindung mit **myVM**. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 
Wenn Sie Ihre Arbeit mit dem privaten Endpunkt, dem SQL-Server und dem virtuellen Computer abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen: 

1. Geben Sie oben im Portal im Feld **Suche** den Suchbegriff **CreateSQLEndpointTutorial-rg** ein, und wählen Sie in den Suchergebnissen dann **CreateSQLEndpointTutorial-rg** aus. 

2. Wählen Sie die Option **Ressourcengruppe löschen**. 

3. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** den Namen **CreateSQLEndpointTutorial-rg** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* Virtuellen Computer
* Azure SQL-Server mit privatem Endpunkt

Sie haben den virtuellen Computer verwendet, um die Konnektivität mit dem SQL-Server über den privaten Endpunkt auf sichere Weise zu testen.

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)