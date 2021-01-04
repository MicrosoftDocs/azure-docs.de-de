---
title: 'Tutorial: Hinzufügen einer Instanz von SQL Managed Instance zu einer Failovergruppe'
titleSuffix: Azure SQL Managed Instance
description: In diesem Tutorial erfahren Sie, wie Sie eine Failovergruppe zwischen einer primären und einer sekundären Azure SQL Managed Instance erstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 92d1ce51306e846e2d842bef33bb9782da14019a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593993"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Tutorial: Hinzufügen einer SQL Managed Instance zu einer Failovergruppe
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Hinzufügen verwalteter Instanzen von Azure SQL Managed Instance zu einer Failovergruppe. In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
> - Erstellen einer primären verwalteten Instanz.
> - Erstellen einer sekundären verwalteten Instanz als Teil einer [Failovergruppe](../database/auto-failover-group-overview.md). 
> - Testen des Failovers.

  > [!NOTE]
  > - Stellen Sie beim Durcharbeiten dieses Tutorials sicher, dass Sie Ihre Ressourcen mit den [Voraussetzungen für das Einrichten von Failovergruppen für SQL Managed Instance](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets) konfigurieren. 
  > - Das Erstellen einer verwalteten Instanz kann sehr viel Zeit in Anspruch nehmen. Folglich kann es mehrere Stunden dauern, bis Sie dieses Tutorial fertiggestellt haben. Weitere Informationen zu den Bereitstellungszeiten finden Sie unter [Verwaltungsvorgänge für SQL Managed Instance](sql-managed-instance-paas-overview.md#management-operations). 
  > - Verwaltete Instanzen, die einer Failovergruppe angehören, benötigen entweder [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) oder zwei verbundene VPN-Gateways. Globales VNET-Peering wird nicht unterstützt. Dieses Tutorial beschreibt die Schritte zum Erstellen und Verbinden der VPN-Gateways. Überspringen Sie diese Schritte, wenn Sie ExpressRoute bereits konfiguriert haben. 


## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)
Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten: 

- Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), wenn Sie noch keines besitzen.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Für dieses Tutorial wird Folgendes vorausgesetzt:

- Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), wenn Sie noch keines besitzen.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Erstellen einer Ressourcengruppe und einer primären verwalteten Instanz

In diesem Schritt erstellen Sie die Ressourcengruppe und die primäre verwaltete Instanz für Ihre Failovergruppe mithilfe des Azure-Portals oder mit PowerShell. 

Stellen Sie beide verwalteten Instanzen aus Leistungsgründen in [gekoppelten Regionen](../../best-practices-availability-paired-regions.md) bereit. Verwaltete Instanzen in geografisch gekoppelten Regionen weisen eine deutlich bessere Leistung auf als Instanzen in nicht gekoppelten Regionen. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Erstellen Sie die Ressourcengruppe und die primäre verwaltete Instanz mithilfe des Azure-Portals. 

1. Wählen Sie im linken Menü im Azure-Portal die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann `Azure SQL` in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen. 
1. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen zu den verschiedenen Datenbanken anzeigen, indem Sie auf der Kachel **Datenbanken** die Option **Details anzeigen** auswählen.
1. Wählen Sie auf der Kachel **Verwaltete SQL-Instanzen** die Option **Erstellen** aus. 

    ![Auswählen von SQL Managed Instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Auf der Seite **Verwaltete Azure SQL-Datenbank-Instanz erstellen** auf der Registerkarte **Grundlagen**:
    1. Wählen Sie unter **Projektdetails** Ihr **Abonnement** aus der Dropdownliste aus, und wählen Sie dann **Neu erstellen** für die Ressourcengruppe aus. Geben Sie einen Namen für Ihre Ressourcengruppe ein, etwa `myResourceGroup`. 
    1. Geben Sie unter **Details der verwalteten SQL-Instanz** den Namen Ihrer verwalteten Instanz und die Region an, in der Sie die verwaltete Instanz bereitstellen möchten. Belassen Sie die Standardwerte für **Compute und Speicher**. 
    1. Geben Sie unter **Administratorkonto** eine Administratoranmeldung an (z.B. `azureuser`) sowie ein komplexes Administratorkennwort. 

    ![Erstellen einer primären verwalteten Instanz](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Belassen Sie die restlichen Einstellungen bei den Standardwerten, und wählen Sie **Überprüfen und erstellen** aus, um Ihre Einstellungen für SQL Managed Instance zu überprüfen. 
1. Wählen Sie **Erstellen** aus, um die primäre verwaltete Instanz zu erstellen. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie die Ressourcengruppe und die primäre verwaltete Instanz mithilfe von PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Azure-Ressourcengruppe.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt eine Netzwerksicherheitsgruppe. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Erstellt eine Routingtabelle |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualisiert eine Subnetzkonfiguration für ein virtuelles Netzwerk  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ruft eine Netzwerksicherheitsgruppe ab |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Fügt einer Netzwerksicherheitsgruppe eine Netzwerksicherheits-Konfigurationsregel hinzu |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualisiert eine Netzwerksicherheitsgruppe  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Fügt einer Routingtabelle Routen hinzu |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualisiert eine Routingtabelle  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Erstellt eine verwaltete Instanz.  |

---

## <a name="create-secondary-virtual-network"></a>Erstellen eines sekundären virtuellen Netzwerks

Wenn Sie das Azure-Portal zum Erstellen der verwalteten Instanz verwenden, müssen Sie das virtuelle Netzwerk separat erstellen, da die Anforderung besteht, dass das Subnetz der primären und sekundären verwalteten Instanz keine überlappenden Bereiche aufweisen darf. Wenn Sie PowerShell zum Konfigurieren der verwalteten Instanz verwenden, fahren Sie mit Schritt 3 fort. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Führen Sie die folgenden Schritte aus, um den Subnetzbereich Ihres primären virtuellen Netzwerks zu überprüfen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie das virtuelle Netzwerk für Ihre primäre Instanz aus.  
2. Wählen Sie unter **Einstellungen** die Option **Subnetze**  aus, und notieren Sie sich den **Adressbereich**. Der Subnetzadressbereich des virtuellen Netzwerks für die sekundäre verwaltete Instanz darf keine Überschneidung mit diesem Adressbereich aufweisen. 


   ![Primäres Subnetz](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Führen Sie die folgenden Schritte aus, um ein virtuelles Netzwerk zu erstellen:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen**, und suchen Sie nach *virtuelles Netzwerk*. 
1. Wählen Sie die von Microsoft veröffentlichte Option **Virtuelles Netzwerk** aus, und klicken Sie dann auf der nächsten Seite auf **Erstellen**. 
1. Füllen Sie die Pflichtfelder aus, um das virtuelle Netzwerk für die sekundäre verwaltete Instanz zu konfigurieren, und wählen Sie dann **Erstellen** aus. 

   Die folgende Tabelle enthält die erforderlichen Werte für das sekundäre virtuelle Netzwerk:

    | **Feld** | Wert |
    | --- | --- |
    | **Name** |  Der Name des virtuellen Netzwerks, das von der sekundären verwalteten Instanz verwendet werden soll, z.B `vnet-sql-mi-secondary`. |
    | **Adressraum** | Der Adressraum für Ihr virtuelles Netzwerk, z.B. `10.128.0.0/16`. | 
    | **Abonnement** | Das Abonnement, in dem sich Ihre primäre verwaltete Instanz und die Ressourcengruppe befinden. |
    | **Region** | Der Standort, an dem Ihre sekundäre verwaltete Instanz bereitgestellt wird. |
    | **Subnetz** | Der Name für Ihr Subnetz. `default` wird standardmäßig bereitgestellt. |
    | **Adressbereich**| Der Adressbereich für das Subnetz. Dieser muss sich von dem Subnetzadressbereich unterscheiden, der vom virtuellen Netzwerk der primären verwalteten Instanz verwendet wird, z.B. `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Werte des sekundären virtuellen Netzwerks](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Dieser Schritt ist nur erforderlich, wenn Sie das Azure-Portal zum Bereitstellen von SQL Managed Instance verwenden. Wenn Sie PowerShell verwenden, fahren Sie mit Schritt 3 fort. 

---

## <a name="create-a-secondary-managed-instance"></a>Erstellen einer sekundären verwalteten Instanz
In diesem Schritt erstellen Sie eine sekundäre verwaltete Instanz im Azure-Portal, durch die auch die Netzwerkverbindung zwischen den beiden verwalteten Instanzen konfiguriert wird. 

Die zweite verwaltete Instanz muss die folgenden Bedingungen erfüllen:
- Sie muss leer sein. 
- Sie muss über ein anderes Subnetz und einen anderen IP-Adressbereich als die primäre verwaltete Instanz verfügen. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Erstellen Sie die sekundäre verwaltete Instanz mithilfe des Azure-Portals. 

1. Wählen Sie im linken Menü im Azure-Portal die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann `Azure SQL` in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen. 
1. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen zu den verschiedenen Datenbanken anzeigen, indem Sie auf der Kachel **Datenbanken** die Option **Details anzeigen** auswählen.
1. Wählen Sie auf der Kachel **Verwaltete SQL-Instanzen** die Option **Erstellen** aus. 

    ![Auswählen von SQL Managed Instance](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Füllen Sie auf der Registerkarte **Grundlagen** der Seite **Verwaltete Azure SQL-Datenbank-Instanz erstellen** die Pflichtfelder aus, um die sekundäre verwaltete Instanz zu konfigurieren. 

   Die folgende Tabelle enthält die erforderlichen Werte für die sekundäre verwaltete Instanz:
 
    | **Feld** | Wert |
    | --- | --- |
    | **Abonnement** |  Das Abonnement, in dem sich Ihre primäre verwaltete Instanz befindet. |
    | **Ressourcengruppe**| Die Ressourcengruppe, in der sich Ihre primäre verwaltete Instanz befindet. |
    | **Name der verwalteten Instanz** | Der Name der neuen sekundären verwalteten Instanz, z. B. `sql-mi-secondary`.  | 
    | **Region**| Der Standort für die sekundäre verwaltete Instanz.  |
    | **Administratoranmeldung für verwaltete Instanz** | Die Anmeldung, die Sie für Ihre neue sekundäre verwaltete Instanz verwenden möchten, z.B. `azureuser`. |
    | **Kennwort** | Ein komplexes Kennwort, das von der Administratoranmeldung für die neue sekundäre verwaltete Instanz verwendet wird.  |
    | &nbsp; | &nbsp; |

1. Wählen Sie auf der Registerkarte **Netzwerk** als **Virtuelles Netzwerk** das virtuelle Netzwerk aus, das Sie für die sekundäre verwaltete Instanz erstellt haben.

   ![Netzwerk der sekundären verwalteten Instanz](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Wählen Sie unter der Registerkarte **Weitere Einstellungen** für **Georeplikation** die Angabe **Ja** aus, um die _Verwendung als sekundäre Failoverinstanz_ anzugeben. Wählen Sie die primäre verwaltete Instanz aus der Dropdownliste aus. 
    
   Stellen Sie sicher, dass die Sortierung und die Zeitzone mit der primären verwalteten Instanz übereinstimmen. Die in diesem Tutorial erstellte primäre verwaltete Instanz verwendet den Standardwert `SQL_Latin1_General_CP1_CI_AS`-Sortierung und die Zeitzone `(UTC) Coordinated Universal Time`. 

   ![Netzwerkeinstellungen für die sekundäre verwaltete Instanz](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Wählen Sie **Überprüfen und erstellen** aus, um die Einstellungen für die sekundäre verwaltete Instanz zu überprüfen. 
1. Wählen Sie **Erstellen** aus, um die sekundäre verwaltete Instanz zu erstellen. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie die sekundäre verwaltete Instanz mithilfe von PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Azure-Ressourcengruppe.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt eine Netzwerksicherheitsgruppe. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Erstellt eine Routingtabelle |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualisiert eine Subnetzkonfiguration für ein virtuelles Netzwerk  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ruft eine Netzwerksicherheitsgruppe ab |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Fügt einer Netzwerksicherheitsgruppe eine Netzwerksicherheits-Konfigurationsregel hinzu |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualisiert eine Netzwerksicherheitsgruppe  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Fügt einer Routingtabelle Routen hinzu |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualisiert eine Routingtabelle  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Erstellt eine verwaltete Instanz.  |

---

## <a name="create-a-primary-gateway"></a>Erstellen eines primären Gateways 

Damit zwei verwaltete Instanzen an einer Failovergruppe teilnehmen können, muss zwischen den virtuellen Netzwerken der beiden verwalteten Instanzen ExpressRoute oder ein Gateway konfiguriert sein, um die Netzwerkkommunikation zuzulassen. Wenn Sie sich dafür entscheiden, [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) zu konfigurieren, anstatt zwei VPN-Gateways zu verbinden, fahren Sie mit [Schritt 7](#create-a-failover-group) fort.  

In diesem Artikel wird beschrieben, wie Sie die beiden VPN-Gateways erstellen und verbinden. Sie können aber mit dem Erstellen der Failovergruppe fortfahren, wenn Sie stattdessen ExpressRoute konfiguriert haben. 

> [!NOTE]
> Die SKU des Gateways wirkt sich auf die Durchsatzleistung aus. In diesem Tutorial wird ein Gateway mit der einfachsten SKU (`HwGw1`) bereitgestellt. Stellen Sie eine höhere SKU (beispielsweise `VpnGw3`) bereit, um einen höheren Durchsatz zu erzielen. Informationen zu allen verfügbaren Optionen finden Sie unter [Gateway-SKUs](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Erstellen Sie das Gateway für das virtuelle Netzwerk der primären verwalteten Instanz mithilfe des Azure-Portals. 


1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie die Ressource **Virtuelles Netzwerk** für Ihre primäre verwaltete Instanz aus. 
1. Wählen Sie unter **Einstellungen **die Option **Subnetze** aus, und wählen Sie dann ein neues** Gatewaysubnetz** aus. Lassen Sie die Standardwerte unverändert. 

   ![Hinzufügen eines Gateways für die primäre verwaltete Instanz](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Nachdem das Subnetzgateway erstellt wurde, wählen Sie im linken Navigationsbereich **Ressource erstellen** aus, und geben Sie dann `Virtual network gateway` in das Suchfeld ein. Wählen Sie die Ressource **Virtuelles Netzwerkgateway** aus, die von **Microsoft** veröffentlicht wurde. 

   ![Erstellen eines neuen Gateways für das virtuelle Netzwerk](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Füllen Sie die Pflichtfelder aus, um das Gateway ihrer primären verwalteten Instanz zu konfigurieren. 

   Die folgende Tabelle enthält die erforderlichen Werte das Gateway für die primäre verwaltete Instanz:
 
    | **Feld** | Wert |
    | --- | --- |
    | **Abonnement** |  Das Abonnement, in dem sich Ihre primäre verwaltete Instanz befindet. |
    | **Name** | Der Name für das Gateway des virtuellen Netzwerks, z.B. `primary-mi-gateway`. | 
    | **Region** | Die Region, in der sich die primäre verwaltete Instanz befindet. |
    | **Gatewaytyp** | Wählen Sie **VPN** aus. |
    | **VPN-Typ** | Wählen Sie **Routenbasiert** aus. |
    | **SKU**| Lassen Sie den Standardwert `VpnGw1` unverändert. |
    | **Virtuelles Netzwerk**| Wählen Sie das virtuelle Netzwerk aus, das in Abschnitt 2 erstellt wurde, z.B. `vnet-sql-mi-primary`. |
    | **Öffentliche IP-Adresse**| Wählen Sie **Neu erstellen**. |
    | **Name der öffentlichen IP-Adresse**| Geben Sie einen Namen für die IP-Adresse ein, z.B. `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Belassen Sie die anderen Werte als Standardwerte, und wählen Sie dann **Überprüfen und erstellen** aus, um die Einstellungen für Ihr virtuelles Netzwerkgateway zu überprüfen.

   ![Primäre Gatewayeinstellungen](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Wählen Sie **Erstellen** aus, um das neue Gateway für Ihr virtuelles Netzwerk zu erstellen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie das Gateway für das virtuelle Netzwerk der primären verwalteten Instanz mithilfe von PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Erstellt eine IP-Konfiguration für ein Gateway des virtuellen Netzwerks. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein Gateway für das virtuelle Netzwerk. |


---


## <a name="create-secondary-gateway"></a>Erstellen eines sekundären Gateways 
In diesem Schritt erstellen Sie das Gateway für das virtuelle Netzwerk der sekundären verwalteten Instanz mithilfe des Azure-Portals. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Wiederholen Sie im Azure-Portal die Schritte im vorherigen Abschnitt, um das Subnetz des virtuellen Netzwerks und das Gateway für die sekundäre verwaltete Instanz zu erstellen. Füllen Sie die Pflichtfelder aus, um das Gateway für die sekundäre verwaltete Instanz zu konfigurieren. 

   Die folgende Tabelle enthält die erforderlichen Werte für das Gateway für die sekundäre verwaltete Instanz:

   | **Feld** | Wert |
   | --- | --- |
   | **Abonnement** |  Das Abonnement, in dem sich Ihre sekundäre verwaltete Instanz befindet. |
   | **Name** | Der Name für das Gateway des virtuellen Netzwerks, z.B. `secondary-mi-gateway`. | 
   | **Region** | Die Region, in der sich die sekundäre verwaltete Instanz befindet. |
   | **Gatewaytyp** | Wählen Sie **VPN** aus. |
   | **VPN-Typ** | Wählen Sie **Routenbasiert** aus. |
   | **SKU**| Lassen Sie den Standardwert `VpnGw1` unverändert. |
   | **Virtuelles Netzwerk**| Wählen Sie das virtuelle Netzwerk für die sekundäre verwaltete Instanz aus, z. B. `vnet-sql-mi-secondary`. |
   | **Öffentliche IP-Adresse**| Wählen Sie **Neu erstellen**. |
   | **Name der öffentlichen IP-Adresse**| Geben Sie einen Namen für die IP-Adresse ein, z.B. `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Sekundäre Gatewayeinstellungen](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie das Gateway für das virtuelle Netzwerk der sekundären verwalteten Instanz mithilfe von PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Erstellt eine IP-Konfiguration für ein Gateway des virtuellen Netzwerks. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein Gateway für das virtuelle Netzwerk. |

---


## <a name="connect-the-gateways"></a>Verbinden der Gateways
In diesem Schritt erstellen Sie eine bidirektionale Verbindung zwischen den beiden Gateways der beiden virtuellen Netzwerke. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Verbinden Sie die beiden Gateways mithilfe des Azure-Portals. 


1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** aus.
1. Geben Sie `connection` in das Suchfeld ein, und drücken Sie die EINGABETASTE, um die Suche zu starten. Sie gelangen zur Ressource **Verbindung**, die von Microsoft veröffentlicht wurde.
1. Wählen Sie **Erstellen** aus, um die Verbindung zu erstellen. 
1. Wählen Sie auf der Seite **Grundlagen** die folgenden Werte und dann **OK** aus. 
    1. Wählen Sie für **Verbindungstyp** den Eintrag `VNet-to-VNet` aus. 
    1. Wählen Sie in der Dropdownliste Ihr Abonnement aus. 
    1. Wählen Sie die Ressourcengruppe für SQL Managed Instance in der Dropdownliste aus. 
    1. Wählen Sie den Speicherort der primären verwalteten Instanz aus der Dropdownliste aus. 
1. Wählen Sie auf der Seite **Einstellungen** die folgenden Werte aus, oder geben Sie die Werte ein, und klicken Sie dann auf **OK**:
    1. Wählen Sie das primäre Netzwerkgateway für **Erstes Gateway für virtuelle Netzwerke** aus, z.B. `primaryGateway`.  
    1. Wählen Sie das sekundäre Netzwerkgateway für **Zweites Gateway für virtuelle Netzwerke** aus, z.B. `secondaryGateway`. 
    1. Aktivieren Sie das Kontrollkästchen neben **Bidirektionale Konnektivität einrichten**. 
    1. Übernehmen Sie entweder den Standardnamen für die primäre Verbindung, oder ändern Sie ihn in einen Namen Ihrer Wahl. 
    1. Geben Sie einen **Gemeinsam verwendeter Schlüssel (PSK)** für die Verbindung an, z.B. `mi1m2psk`. 
    1. Klicken Sie auf **OK**, um die Einstellungen zu speichern. 

    ![Erstellen einer Gatewayverbindung](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Überprüfen Sie auf der Seite **Überprüfen und erstellen** die Einstellungen für die bidirektionale Verbindung, und wählen Sie dann **OK** aus, um die Verbindung zu erstellen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verbinden Sie die beiden Gateways mithilfe von PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

In diesem Teil des Tutorials wird das folgende PowerShell-Cmdlet verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Erstellt eine Verbindung zwischen den beiden Gateways der virtuellen Netzwerke   |

---


## <a name="create-a-failover-group"></a>Erstellen einer Failovergruppe
In diesem Schritt erstellen Sie die Failovergruppe und fügen ihr beide verwalteten Instanzen hinzu. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Erstellen Sie die Failovergruppe mithilfe des Azure-Portals. 


1. Wählen Sie im linken Menü im [Azure-Portal](https://portal.azure.com) die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann `Azure SQL` in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen. 
1. Wählen Sie die primäre verwaltete Instanz aus, die Sie im ersten Abschnitt erstellt haben, z. B. `sql-mi-primary`. 
1. Navigieren Sie unter **Einstellungen** zu **Instanzfailovergruppen**, und wählen Sie dann **Gruppe hinzufügen** aus, um die Seite **Instanzfailovergruppe** zu öffnen. 

   ![Hinzufügen einer Failovergruppe](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Geben Sie auf der Seite **Instanzfailovergruppe** den Namen Ihrer Failovergruppe ein, z. B. `failovergrouptutorial`. Wählen Sie dann in der Dropdownliste die sekundäre verwaltete Instanz aus, z. B. `sql-mi-secondary`. Wählen Sie **Erstellen** aus, um die Failovergruppe zu erstellen. 

   ![Erstellen einer Failovergruppe](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Nachdem die Bereitstellung der Failovergruppe abgeschlossen wurde, gelangen Sie erneut auf die Seite **Failovergruppe**. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Erstellen Sie die Failovergruppe mithilfe von PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In diesem Teil des Tutorials wird das folgende PowerShell-Cmdlet verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Erstellt eine neue Azure SQL Managed Instance-Failovergruppe.  |


---


## <a name="test-failover"></a>Testfailover
In diesem Schritt führen Sie ein Failover für Ihre Failovergruppe auf dem sekundären Server und anschließend ein Failback mit dem Azure-Portal aus. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Testen Sie ein Failover über das Azure-Portal. 


1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer _sekundären_ verwalteten Instanz, und wählen Sie unter „Einstellungen“ die Option **Instanzfailovergruppen** aus. 
1. Überprüfen Sie, welche verwaltete Instanz die primäre Instanz ist und welche verwaltete Instanz die sekundäre Instanz ist. 
1. Wählen Sie **Failover** aus, und klicken Sie dann in der Warnung zu TDS-Sitzungen, die getrennt werden, auf **Ja**. 

   ![Ausführen des Failovers der Failovergruppe](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Überprüfen Sie, welche verwaltete Instanz die primäre und welche die sekundäre ist. Wenn das Failover erfolgreich ausgeführt wurde, sollten die beiden Instanzen die Rollen getauscht haben. 

   ![Verwaltete Instanzen haben nach einem Failover die Rollen getauscht](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Navigieren Sie zur neuen _sekundären_ verwalteten Instanz, und wählen Sie erneut **Failover** aus, um ein Failback der primären Instanz auf die primäre Rolle durchzuführen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testen Sie das Failovers mithilfe von PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Stellen Sie die Failovergruppe auf dem primären Server wieder her:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ruft die Azure SQL Managed Instance-Failovergruppen ab oder listet sie auf.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Führt ein Failover einer Azure SQL Managed Instance-Failovergruppe aus. | 

---



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Bereinigen Sie Ressourcen, indem Sie zuerst die verwalteten Instanzen, dann den virtuellen Cluster, dann alle verbleibenden Ressourcen und schließlich die Ressourcengruppe löschen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe. 
1. Wählen Sie die verwaltete(n) Instanz(en) und dann **Löschen** aus. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus. Dieser Vorgang kann im Hintergrund einige Zeit in Anspruch nehmen. Bis der Vorgang abgeschlossen ist, können Sie den *virtuellen Cluster* oder andere abhängige Ressourcen nicht löschen. Überwachen Sie den Löschvorgang auf der Registerkarte **Aktivität**, um zu bestätigen, dass die verwaltete Instanz gelöscht wurde. 
1. Nachdem die verwaltete Instanz gelöscht wurde, löschen Sie den *virtuellen Cluster*, indem Sie ihn in der Ressourcengruppe auswählen und dann **Löschen** auswählen. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus. 
1. Löschen Sie alle verbleibenden Ressourcen. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus. 
1. Löschen Sie die Ressourcengruppe, indem Sie die Option **Ressourcengruppe löschen** auswählen, geben Sie den Namen der Ressourcengruppe (`myResourceGroup`) ein, und wählen Sie dann **Löschen** aus. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Die Ressourcengruppe muss zweimal entfernt werden. Wenn Sie die Ressourcengruppe zum ersten Mal entfernen, werden die verwalteten Instanzen und die virtuellen Cluster entfernt, es wird jedoch die Fehlermeldung `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` ausgegeben. Führen Sie den Befehl Remove-AzResourceGroup ein zweites Mal aus, um alle übrigen Ressourcen sowie die Ressourcengruppe zu entfernen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

In diesem Teil des Tutorials wird das folgende PowerShell-Cmdlet verwendet:

| Get-Help | Notizen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe |

---

## <a name="full-script"></a>Vollständiges Skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Azure-Ressourcengruppe.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt eine Netzwerksicherheitsgruppe. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Erstellt eine Routingtabelle |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualisiert eine Subnetzkonfiguration für ein virtuelles Netzwerk  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ruft eine Netzwerksicherheitsgruppe ab |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Fügt einer Netzwerksicherheitsgruppe eine Netzwerksicherheits-Konfigurationsregel hinzu |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualisiert eine Netzwerksicherheitsgruppe  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Fügt einer Routingtabelle Routen hinzu |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualisiert eine Routingtabelle  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Erstellt eine verwaltete Instanz.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Gibt Informationen zu Azure SQL Managed Instance zurück. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Erstellt eine IP-Konfiguration für ein Gateway des virtuellen Netzwerks. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein Gateway für das virtuelle Netzwerk. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Erstellt eine Verbindung zwischen den beiden Gateways der virtuellen Netzwerke   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Erstellt eine neue SQL Managed Instance-Failovergruppe.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ruft die Azure SQL Managed Instance-Failovergruppen ab oder listet sie auf.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Führt ein Failover einer Azure SQL Managed Instance-Failovergruppe aus. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Es sind keine Skripts für das Azure-Portal verfügbar.

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Failovergruppe zwischen zwei verwalteten Instanzen konfiguriert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> - Erstellen einer primären verwalteten Instanz.
> - Erstellen einer sekundären verwalteten Instanz als Teil einer [Failovergruppe](../database/auto-failover-group-overview.md). 
> - Testen des Failovers.

Fahren Sie mit dem nächsten Schnellstart fort, um zu erfahren, wie Sie eine Verbindung mit Ihrer Instanz von SQL Managed Instance herstellen und wie Sie eine Datenbank in Ihrer Instanz von SQL Managed Instance wiederherstellen: 

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit SQL Managed Instance](connect-vm-instance-configure.md)
> [Wiederherstellen einer Datenbank in SQL Managed Instance](restore-sample-database-quickstart.md)


