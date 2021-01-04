---
title: PowerShell für VNET-Endpunkte und -Regeln für Einzel- und Pooldatenbanken
description: Stellt PowerShell-Skripts zum Erstellen und Verwalten von virtuellen Dienstendpunkten für Ihre Instanzen von Azure SQL-Datenbank und Azure Synapse bereit.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: f32599c9d289c8fc5e86eb8c7b0574d9703a6dd4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792666"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>Mit PowerShell: Erstellen eines virtuellen Dienstendpunkts und einer VNet-Regel für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*VNET-Regeln* sind ein Firewallsicherheitsfeature, das steuert, ob der [logische SQL-Server](../logical-servers.md) für Ihre Datenbanken und Pool für elastische Datenbanken von [Azure SQL-Datenbank](../sql-database-paas-overview.md) oder Datenbanken in [Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Nachrichten akzeptieren, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden.

> [!IMPORTANT]
> Dieser Artikel gilt für Azure SQL-Datenbank und Azure Synapse (zuvor SQL Data Warehouse). Der Einfachheit halber bezieht sich der Begriff Azure SQL-Datenbank in diesem Artikel auf Datenbanken, die entweder zu Azure SQL-Datenbank oder Azure Synapse gehören. Dieser Artikel gilt *nicht* für Azure SQL Managed Instance, da kein zugeordneter Dienstendpunkt vorliegt.

In diesem Artikel wird ein PowerShell-Skript veranschaulicht, das die folgenden Aktionen ausführt:

1. Erstellt einen *virtuellen Dienstendpunkt* von Microsoft Azure in Ihrem Subnetz.
2. Es fügt den Endpunkt zur Firewall Ihres Servers hinzu, um eine *VNET-Regel* zu erstellen.

Weitere Informationen finden Sie unter [Virtuelle Dienstendpunkte für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Wenn Sie nur den *Typnamen* des virtuellen Dienstendpunkts für Azure SQL-Datenbank bewerten oder zu Ihrem Subnetz hinzufügen müssen, fahren Sie mit dem [direkten PowerShell-Skript](#a-verify-subnet-is-endpoint-ps-100) fort.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für die [`Az.Sql`-Cmdlets](/powershell/module/az.sql). Informationen zum älteren Modul finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

## <a name="major-cmdlets"></a>Wichtige Cmdlets

Dieser Artikel befasst sich überwiegend mit dem [**New-AzSqlServerVirtualNetworkRule** -Cmdlet](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule), das den Subnetzendpunkt zu Zugriffssteuerungsliste Ihres Servers hinzufügt und dadurch eine Regel erstellt.

Die folgende Liste zeigt die Abfolge von anderen *wichtigen* Cmdlets, die Sie ausführen müssen, um den Aufruf von **New-AzSqlServerVirtualNetworkRule** vorzubereiten. In diesem Artikel werden diese Aufrufe in [script 3 "Virtual network rule" (Skript 3: VNET-Regel)](#a-script-30) ausgeführt.

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Erstellt ein Subnetzobjekt.
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): Erstellt Ihr virtuelles Netzwerk und weist diesem das Subnetz zu.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Weist Ihrem Subnetz einen virtuellen Dienstendpunkt zu.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): Behält Updates bei, die für Ihr virtuelles Netzwerk vorgenommen wurden.
5. [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Sobald Ihr Subnetz ein Endpunkt ist, wird dieses als VNET-Regel zur Zugriffssteuerungsliste Ihres Servers hinzugefügt.
   - Dieses Cmdlet bietet den Parameter **-IgnoreMissingVNetServiceEndpoint** ab Azure RM PowerShell-Modul Version 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Voraussetzungen für das Ausführen von PowerShell

- Sie können sich bereits bei Azure anmelden, z. B. über das [Azure-Portal][http-azure-portal-link-ref-477t].
- Sie können bereits PowerShell-Skripts ausführen.

> [!NOTE]
> Vergewissern Sie sich, dass Dienstendpunkte für das VNet/Subnetz aktiviert sind, das Sie dem Server hinzufügen möchten, da andernfalls die VNet-Firewallregel nicht erstellt werden kann.

## <a name="one-script-divided-into-four-chunks"></a>Ein in vier Blöcke unterteiltes Skript

Das PowerShell-Beispielskript ist in eine Abfolge von kleineren Skripts unterteilt. Die Aufteilung erleichtert das Lernen und bietet Flexibilität. Die Skripts müssen in ihrer angegebenen Reihenfolge ausgeführt werden. Wenn Sie keine Zeit für das Ausführen der Skripts haben: Die tatsächliche Testausgabe wird nach Skript 4 angezeigt.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>Skript 1: Variables

Dieses erste PowerShell-Skript weist Variablen Werte zu. Die nachfolgenden Skripts hängen von diesen Variablen ab.

> [!IMPORTANT]
> Bevor Sie dieses Skript ausführen, können Sie die Werte bei Bedarf bearbeiten. Wenn Sie beispielsweise bereits über eine Ressourcengruppe verfügen, sollten Sie den Namen Ihrer Ressourcengruppe dem zugewiesenen Wert entsprechend bearbeiten.
>
> Ihr Abonnementname sollte in das Skript eingefügt werden.

### <a name="powershell-script-1-source-code"></a>Quellcode von PowerShell-Skript 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>Skript 2: Voraussetzungen

Dieses Skript bereitet Sie auf den nächsten Schritt vor, in dem die Endpunktaktion stattfindet. Dieses Skript erstellt die im Folgenden aufgelisteten Elemente für Sie, jedoch nur, wenn diese nicht bereits vorhanden sind. Sie können Skript 2 überspringen, wenn Sie sicher sind, dass diese Elemente bereits vorhanden sind:

- Azure-Ressourcengruppe
- Logischer SQL-Server

### <a name="powershell-script-2-source-code"></a>Quellcode von PowerShell-Skript 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create a server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your server already exists - $SqlDbServerName.";
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Erstellen eines Endpunkts und einer Regel

Dieses Skript erstellt ein virtuelles Netzwerk mit einem Subnetz. Das Skript weist Ihrem Subnetz dann den Endpunkttyp **Microsoft.Sql** zu. Schließlich fügt das Skript Ihr Subnetz zur Zugriffssteuerungsliste hinzu, wodurch eine Regel erstellt wird.

### <a name="powershell-script-3-source-code"></a>Quellcode von PowerShell-Skript 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL Database ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>Skript 4: Bereinigung

Dieses abschließende Skript löscht die Ressourcen, die das vorherige Skript zur Veranschaulichung erstellt hat. Das Skript fordert Sie jedoch zur Bestätigung auf, bevor Folgendes gelöscht wird:

- Logischer SQL-Server
- Azure-Ressourcengruppe

Sie können Skript 4 jederzeit nach dem Abschluss von Skript 1 ausführen.

### <a name="powershell-script-4-source-code"></a>Quellcode von PowerShell-Skript 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Überprüfen, dass Ihr Subnetz ein Endpunkt ist

Wenn Ihr Subnetz bereits zum Typnamen **Microsoft.Sql** zugewiesen wurde, ist dieses bereits ein virtueller Dienstendpunkt. Sie können das [Azure-Portal][http-azure-portal-link-ref-477t] verwenden, um eine VNET-Regel für den Endpunkt zu erstellen.

Falls Sie nicht sicher sind, ob Ihr Subnetz über den Typnamen **Microsoft.Sql** verfügt, führen Sie folgendes PowerShell-Skript aus, um diese Aktionen durchzuführen:

1. Ermitteln, ob das Subnetz über den Typnamen **Microsoft.Sql** verfügt.
2. Zuweisen des Typnamens, wenn dieser nicht vorhanden ist (optional).
    - Das Skript fordert Sie zur *Bestätigung* auf, bevor der fehlende Typname angewendet wird.

### <a name="phases-of-the-script"></a>Phasen des Skripts

Im Folgenden finden Sie die Phasen des PowerShell-Skripts:

1. Melden Sie sich bei Ihrem Azure-Konto an. Dies ist nur einmal pro PowerShell-Sitzung nötig.  Weisen Sie die Variablen zu.
2. Suchen Sie Ihr virtuelles Netzwerk und dann Ihr Subnetz.
3. Ist Ihr Subnetz als **Microsoft.Sql** -Endpunktservertyp markiert?
4. Fügen Sie Ihrem Subnetz einen virtuellen Dienstendpunkt des Typnamens **Microsoft.Sql** zu.

> [!IMPORTANT]
> Bevor Sie dieses Skript ausführen, müssen Sie die Werte bearbeiten, die den $-Variablen am Anfang des Skripts zugewiesen sind.

### <a name="direct-powershell-source-code"></a>Direkter PowerShell-Quellcode

In diesem PowerShell-Skript werden keine Aktualisierungen vorgenommen, es sei denn, Sie antworten mit „Ja“, wenn Sie zur Bestätigung aufgefordert werden. Das Skript kann den Typnamen **Microsoft.Sql** zu Ihrem Subnetz hinzufügen. Das Skript versucht dies jedoch nur, wenn das Subnetz keinen Typnamen besitzt.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/