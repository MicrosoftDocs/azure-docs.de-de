---
title: 'Tutorial: Hinzufügen eines Pools für elastische Datenbanken zu einer Failovergruppe'
description: Fügen Sie einen Pool für elastische Azure SQL-Datenbanken mit dem Azure-Portal, PowerShell oder der Azure CLI zu einer Failovergruppe hinzu.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: cdbc44158de2f24d7d33d68311979c3b8bdda85d
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593976"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Hinzufügen eines Pools für elastische Azure SQL-Datenbank-Instanzen zu einer Failovergruppe
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfigurieren Sie eine ‚Failovergruppe für einen Pool für elastische Azure SQL-Datenbanken, und testen Sie das Failover mithilfe des Azure-Portals.  In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Einzeldatenbank
> - Fügen Sie die Datenbank einem Pool für elastische Datenbanken hinzu.
> - Erstellen Sie eine [Failovergruppe](auto-failover-group-overview.md) für zwei Pools für elastische Datenbanken zwischen zwei Servern.
> - Testen des Failovers.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten:

- Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), wenn Sie noch keines besitzen.

## <a name="1---create-a-single-database"></a>1 – Erstellen einer Einzeldatenbank

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2\. Hinzufügen der Datenbank zu einem Pool für elastische Datenbanken

In diesem Schritt erstellen Sie einen Pool für elastische Datenbanken und fügen ihm Ihre Datenbank hinzu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Erstellen Sie den Pool für elastische Datenbanken mithilfe des Azure-Portals.

1. Wählen Sie im linken Menü im Azure-Portal die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann „Azure SQL“ in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen.
1. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen zu den verschiedenen Datenbanken anzeigen, indem Sie auf der Kachel „Datenbanken“ die Option „Details anzeigen“ auswählen.
1. Wählen Sie **Pool für elastische Datenbanken** aus dem Dropdownmenü **Ressourcentyp** auf der Kachel **SQL-Datenbanken** aus. Wählen Sie **Erstellen** aus, um den Pool für elastische Datenbanken zu erstellen.

    ![Auswählen des Pools für elastische Datenbanken](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurieren Sie den Pool für elastische Datenbanken mit den folgenden Werten:
   - **Name**: Geben Sie einen eindeutigen Namen für den Pool für elastische Datenbanken an, wie z.B. `myElasticPool`.
   - **Abonnement**: Wählen Sie in der Dropdownliste Ihr Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie `myResourceGroup` aus der Dropdownliste aus. Das ist die Ressourcengruppe, die Sie im Abschnitt 1 erstellt haben.
   - **Server**: Wählen Sie den in Abschnitt 1 erstellten Server aus der Dropdownliste aus.  

       ![Erstellen eines neuen Servers für den Pool für elastische Datenbanken](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute und Speicher**: Wählen Sie **Pool für elastische Datenbanken konfigurieren** aus, um Ihren Computespeicher zu konfigurieren, und fügen Sie anschließend Ihre Einzeldatenbank zu Ihrem Pool für elastische Datenbanken hinzu. Belassen Sie auf der Registerkarte **Pooleinstellungen** die Standardeinstellung von Gen5 mit 2 virtuellen Kernen und 32 GB bei.

1. Wählen Sie auf der Seite **Konfigurieren** die Registerkarte **Datenbanken** und dann **Datenbank hinzufügen** aus. Wählen Sie die in Abschnitt 1 erstellte Datenbank und dann **Anwenden** aus, um sie zu Ihrem Pool für elastische Datenbanken hinzuzufügen. Wählen Sie erneut **Anwenden** aus, um die Einstellungen für Ihren Pool anzuwenden und die Seite **Konfigurieren** zu schließen.

    ![Hinzufügen einer Datenbank zu einem Pool für elastische Datenbanken](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Klicken Sie auf **Bewerten + erstellen**, um die Einstellung für den Pool für elastische Datenbanken anzuzeigen, und wählen Sie **Erstellen** aus, um Ihren Pool für elastische Datenbanken zu erstellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie die Pools für elastische Datenbanken und den sekundären Server mithilfe von PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken für eine Azure SQL-Datenbank.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken. |

---

## <a name="3---create-the-failover-group"></a>3 – Erstellen der Failovergruppe

In diesem Schritt erstellen Sie eine [Failovergruppe](auto-failover-group-overview.md) zwischen einem vorhandenen Server und einem neuen Server in einer anderen Region. Anschließend fügen Sie dann der Failovergruppe den Pool für elastische Datenbanken hinzu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Erstellen Sie die Failovergruppe mithilfe des Azure-Portals.

1. Wählen Sie im linken Menü im [Azure-Portal](https://portal.azure.com) die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann „Azure SQL“ in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen.
1. Wählen Sie den Pool für elastische Datenbanken aus, den Sie im vorherigen Abschnitt erstellt haben, beispielsweise `myElasticPool`.
1. Wählen Sie im Bereich **Übersicht** unter **Servername** den Namen des Servers aus, um die Einstellungen für den Server zu öffnen.
  
    ![Öffnen des Servers für den Pool für elastische Datenbanken](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Wählen Sie **Failovergruppen** im Bereich **Einstellungen** aus, und klicken Sie dann auf **Gruppe hinzufügen**, um eine neue Failovergruppe zu erstellen.

    ![Hinzufügen einer neuen Failovergruppe](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Wählen Sie auf der Seite **Failovergruppe** die folgenden Werte aus bzw. geben Sie sie ein, und wählen Sie dann **Erstellen**:
    - **Name der Failovergruppe**: Geben Sie einen eindeutigen Namen für die Failovergruppe ein, z.B. `failovergrouptutorial`.
    - **Sekundärer Server**: Wählen Sie die Option  *zum Konfigurieren der erforderlichen Einstellungen* aus, und wählen Sie dann **Neuen Server erstellen** aus. Alternativ können Sie auch einen bereits vorhandenen Server als sekundären Server auswählen. Nachdem Sie die folgenden Werte für den neuen sekundären Server eingegeben haben, wählen Sie **Auswählen** aus.
        - **Servername**: Geben Sie einen eindeutigen Namen für den sekundären Server ein, z.B. `mysqlsecondary`.
        - **Serveradministratoranmeldung**: Geben Sie Folgendes ein: `azureuser`
        - **Kennwort**: Geben Sie ein komplexes Kennwort ein, das die Anforderungen für Kennwörter erfüllt.
        - **Standort**: Wählen Sie in der Dropdownliste einen Standort aus, z. B. `East US`. Dieser Standort darf nicht mit dem Standort des primären Servers übereinstimmen.

       > [!NOTE]
       > Die Einstellungen für Serveranmeldung und Firewall müssen jedoch mit denen Ihres primären Servers übereinstimmen.

       ![Erstellen eines sekundären Servers für die Failovergruppe](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Wählen Sie **Datenbank in der Gruppe** aus, und wählen Sie anschließend den in Abschnitt 2 erstellten Pool für elastische Datenbanken aus. Es wird eine Warnung angezeigt, in der Sie aufgefordert werden, einen Pool für elastische Datenbanken auf dem sekundären Server zu erstellen. Wählen Sie die Warnung aus, und klicken Sie dann auf **OK**, um den Pool für elastische Datenbanken auf dem sekundären Server zu erstellen. 

   ![Hinzufügen eines Pools für elastische Datenbanken zu einer Failovergruppe](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Wählen Sie **Auswählen** aus, um die Einstellungen für den Pool für elastische Datenbanken auf die Failovergruppe anzuwenden, und klicken Sie dann auf **Erstellen**, um Ihre Failovergruppe zu erstellen. Durch das Hinzufügen des Pool für elastische Datenbanken zur Failovergruppe wird automatisch der Georeplikationsprozess gestartet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie die Failovergruppe mithilfe von PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server, der Datenbanken und Pools für elastische Datenbanken hostet. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Erstellt eine Firewallregel für einen Server |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken für eine Azure SQL-Datenbank-Instanz|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Erstellt eine neue Failovergruppe. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Fügt einer Failovergruppe mindestens eine Azure SQL-Datenbank hinzu. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft Azure SQL-Datenbank-Failovergruppen ab oder listet diese auf. |

---

## <a name="4---test-failover"></a>4 – Testen des Failovers

In diesem Schritt führen Sie ein Failover für Ihre Failovergruppe auf dem sekundären Server und anschließend ein Failback mit dem Azure-Portal aus.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testen Sie das Failover Ihrer Failovergruppe mithilfe des Azure-Portals.

1. Wählen Sie im linken Menü im [Azure-Portal](https://portal.azure.com) die Option **Azure SQL** aus. Wenn **Azure SQL** nicht in der Liste aufgeführt wird, wählen Sie **Alle Dienste** aus, und geben Sie dann „Azure SQL“ in das Suchfeld ein. (Optional:) Wählen Sie den Stern neben **Azure SQL** aus, um die Option als Favorit zu markieren und als Element im linken Navigationsbereich hinzuzufügen.
1. Wählen Sie den Pool für elastische Datenbanken aus, den Sie im vorherigen Abschnitt erstellt haben, beispielsweise `myElasticPool`.
1. Wählen Sie unter **Servername** den Namen des Servers aus, um die Einstellungen für diesen Server zu öffnen.

    ![Öffnen des Servers für den Pool für elastische Datenbanken](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Wählen Sie **Failovergruppen** im Bereich **Einstellungen** aus, und wählen Sie dann die in Abschnitt 2 erstellte Failovergruppe aus.
  
   ![Auswählen der Failovergruppe aus dem Portal](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Überprüfen Sie, welcher Server der primäre und welcher der sekundäre ist.
1. Wählen Sie im Aufgabenbereich **Failover** aus, um ein Failover für die Failovergruppe mit dem Pool für elastische Datenbanken durchzuführen.
1. Wählen Sie in der Meldung, dass die TDS-Sitzungen getrennt werden, **Ja** aus.

   ![Failover für die Failovergruppe, die Ihre Datenbank enthält](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Überprüfen Sie, welcher Server der primäre und welcher der sekundäre ist. Wenn das Failover erfolgreich ausgeführt wurde, sollten die beiden Server die Rollen getauscht haben.
1. Wählen Sie erneut **Failover** aus, um die Failovergruppe wieder auf die ursprünglichen Einstellungen zurückzusetzen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testen Sie das Failover Ihrer Failovergruppe mithilfe von PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

Führen Sie ein Failover für Ihre Failovergruppe auf den sekundären Server und anschließend ein Failback mithilfe von PowerShell aus.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

In diesem Teil des Tutorials werden die folgenden PowerShell-Cmdlets verwendet:

| Get-Help | Notizen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft Azure SQL-Datenbank-Failovergruppen ab oder listet diese auf. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Führt ein Failover für eine Azure SQL-Datenbank-Failovergruppe aus. |

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie die Ressourcen, indem Sie die Ressourcengruppe löschen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe.
1. Wählen Sie **Ressourcengruppe löschen** aus, um alle Ressourcen in der Gruppe sowie die Ressourcengruppe selbst zu löschen.
1. Geben Sie den Namen der Ressourcengruppe `myResourceGroup` in das Textfeld ein, und wählen Sie dann **Löschen**, um die Ressourcengruppe zu löschen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bereinigen Sie Ihre Ressourcen mithilfe von PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In diesem Teil des Tutorials wird das folgende PowerShell-Cmdlet verwendet:

| Get-Help | Notizen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe. |

---

> [!IMPORTANT]
> Wenn Sie die Ressourcengruppe beibehalten, die sekundäre Datenbank aber löschen möchten, entfernen Sie sie aus der Failovergruppe, bevor Sie sie löschen. Wenn eine sekundäre Datenbank vor dem Entfernen aus der Failovergruppe gelöscht wird, kann dies zu unvorhersehbarem Verhalten führen.

## <a name="full-script"></a>Vollständiges Skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server, der Datenbanken und Pools für elastische Datenbanken hostet. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Erstellt eine Firewallregel für einen Server |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Datenbank. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken für eine Azure SQL-Datenbank.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Erstellt eine neue Failovergruppe. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ruft mindestens eine Datenbank in SQL-Datenbank ab |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Fügt einer Failovergruppe mindestens eine Azure SQL-Datenbank hinzu. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft Azure SQL-Datenbank-Failovergruppen ab oder listet diese auf. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Führt ein Failover für eine Azure SQL-Datenbank-Failovergruppe aus. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe. |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Es sind keine Skripts für das Azure-Portal verfügbar.

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einer Failovergruppe einen Pool für elastische Azure SQL-Datenbanken hinzugefügt und das Failover getestet. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> - Erstellen einer Einzeldatenbank
> - Fügen Sie die Datenbank in einen Pool für elastische Datenbanken ein.
> - Erstellen Sie eine [Failovergruppe](auto-failover-group-overview.md) für zwei Pools für elastische Datenbanken zwischen zwei Servern.
> - Testen des Failovers.

Fahren Sie mit dem nächsten Tutorial zur Migration mithilfe von DMS fort.

> [!div class="nextstepaction"]
> [Tutorial: Migrieren von SQL Server zu einer Pooldatenbank mit DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
