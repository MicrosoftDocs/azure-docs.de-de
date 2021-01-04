---
title: 'PowerShell: Migrieren von SQL Server zu SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure PowerShell und Azure Database Migration Service eine Migration einer SQL Server-Instanz zu einer Instanz in Azure SQL Managed Instance durchführen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: aa97fa5e92d4afe11cf8af5f7cfd3458a3c12014
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302728"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>Migrieren von SQL Server zu Azure SQL Managed Instance mit PowerShell und Azure Database Migration Service

In diesem Artikel migrieren Sie die Datenbank **Adventureworks2016**, die in einer lokalen Instanz von SQL Server 2005 (oder höher) wiederhergestellt wurde, mithilfe von Microsoft Azure PowerShell zu einer Instanz in Azure SQL Managed Instance. Mithilfe des Moduls `Az.DataMigration` in Microsoft Azure PowerShell können Sie Datenbanken aus einer SQL Server-Instanz zu einer Instanz in Azure SQL Managed Instance migrieren.

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
>
> * Erstellen Sie eine Ressourcengruppe.
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts in einer Instanz von Azure Database Migration Service.
> * Ausführen der Migration

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dieser Artikel enthält Details, wie Sie Online- und Offlinemigrationen ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen dieser Schritte benötigen Sie Folgendes:

* [SQL Server 2016 oder höher](https://www.microsoft.com/sql-server/sql-server-downloads) (beliebige Edition).
* Eine lokale Kopie der **AdventureWorks2016**-Datenbank, die [hier](/sql/samples/adventureworks-install-configure?view=sql-server-2017) zum Download verfügbar ist.
* Aktivieren Sie das TCP/IP-Protokoll, das in einer SQL Server Express-Installation standardmäßig deaktiviert ist. Aktivieren Sie das TCP/IP-Protokoll anhand des Artikels [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.
* Eine Instanz in Azure SQL Managed Instance. Sie können eine Instanz in Azure SQL Managed Instance erstellen, indem Sie die Anweisungen im Artikel [Erstellen einer Instanz in Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) befolgen.
* Laden Sie den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595), Version 3.3 oder höher, herunter, und installieren Sie ihn.
* Ein Microsoft Azure Virtual Network, das mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurde, das dem Azure Database Migration Service entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) Site-to-Site-Konnektivität für Ihre lokalen Quellserver bereitstellt.
* Eine abgeschlossene Bewertung der lokalen Datenbank und Schemamigration mithilfe von Data Migration Assistant wie im Artikel [Durchführen einer SQL Server-Migrationsbewertung](/sql/dma/dma-assesssqlonprem) beschrieben.
* Laden Sie das `Az.DataMigration`-Modul (Version 0.7.2 oder höher) aus dem PowerShell-Katalog mit dem [PowerShell-Cmdlet „Install-Module“](/powershell/module/powershellget/Install-Module?view=powershell-5.1) herunter und installieren Sie es.
* Stellen Sie sicher, dass die für die Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldeinformationen über [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-Berechtigungen verfügen.
* Stellen Sie sicher, dass die für die Verbindung mit der Zielinstanz in Azure SQL Managed Instance verwendeten Anmeldeinformationen über die CONTROL DATABASE-Berechtigung für die Zieldatenbanken in Azure SQL Managed Instance verfügen.

    > [!IMPORTANT]
    > Für Onlinemigrationen müssen Sie bereits Ihre Azure Active Directory-Anmeldeinformationen eingerichtet haben. Weitere Informationen finden Sie im Artikel [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Anmelden bei Ihrem Microsoft Azure-Abonnement

Melden Sie sich bei Ihrem Azure-Abonnement mithilfe der PowerShell an. Weitere Informationen finden Sie im Artikel [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *USA, Osten* erstellt.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Erstellen einer Instanz von Azure Database Migration Service

Mit dem Cmdlet `New-AzDataMigrationService` können Sie eine neue Instanz von Azure Database Migration Service erstellen.
Dieses Cmdlet erwartet die folgenden erforderlichen Parameter:

* *ResourceGroupName*: Mit dem Befehl [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) können Sie wie oben gezeigt eine Azure-Ressourcengruppe erstellen und deren Namen als Parameter bereitstellen.
* *ServiceName*: Die Zeichenfolge, die dem gewünschten eindeutigen Dienstnamen für Azure Database Migration Service entspricht.
* *Standort*. Gibt den Standort des Diensts an. Geben Sie einen Azure-Rechenzentrumsstandort an, z. B. „USA, Westen“ oder „Asien, Südosten“.
* *Sku*: Dieser Parameter entspricht dem Sku-Namen des DMS. Derzeit unterstützte Sku-Namen sind *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *VirtualSubnetId*: Sie können das Cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) verwenden, um ein Subnetz zu erstellen.

Im folgenden Beispiel wird ein Dienst mit dem Namen *MyDMS* in der Ressourcengruppe *MyDMSResourceGroup*, die sich in der Region *USA, Osten* befindet, mit einem virtuellen Netzwerk namens *MyVNET* und einem Subnetz namens *MySubnet* erstellt.

> [!IMPORTANT]
> Der folgende Codeausschnitt ist für eine Offlinemigration, die keine Instanz von Azure Database Migration Service auf Grundlage einer Premium-SKU erfordert. Für eine Onlinemigration muss der Wert des Parameters „-Sku“ eine Premium-SKU umfassen.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem Sie eine Instanz von Azure Database Migration Service erstellt haben, erstellen Sie ein Migrationsprojekt. Ein Azure Database Migration Service-Projekt erfordert Verbindungsinformationen sowohl für die Quell- als auch die Zielinstanz sowie eine Liste der Datenbanken, die Sie als Teil des Projekts migrieren möchten.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Erstellen eines Objekts mit Datenbankverbindungsinformationen für die Quell- und Zielverbindung

Mit dem Cmdlet `New-AzDmsConnInfo`, das die folgenden Parameter erwartet, können Sie ein Objekt mit Datenbankverbindungsinformationen erstellen:

* *ServerType*: Der Typ der angeforderten Datenbankverbindung, z. B. „SQL“, „Oracle“ oder „MySQL“. Verwenden Sie „SQL“ für SQL Server und Azure SQL.
* *DataSource*: Der Name oder die IP-Adresse einer SQL Server-Instanz oder einer Azure SQL-Datenbankinstanz.
* *AuthType*: Der Authentifizierungstyp für die Verbindung. Dieser kann „SqlAuthentication“ oder „WindowsAuthentication“ sein.
* *TrustServerCertificate*: Mit diesem Parameter wird ein Wert festgelegt, der angibt, ob der Kanal verschlüsselt wird, während das Durchlaufen der Zertifikatskette zum Überprüfen der Vertrauensstellung umgangen wird. Der Wert kann `$true` oder `$false` lauten.

Im folgenden Beispiel wird ein Objekt mit Verbindungsinformationen für eine SQL Server-Quellinstanz namens *MySourceSQLServer* mithilfe der SQL-Authentifizierung erstellt:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Das nächste Beispiel zeigt die Erstellung von Verbindungsinformationen für eine Instanz von Azure SQL Managed Instance namens „targetmanagedinstance“:

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Bereitstellen von Datenbanken für das Migrationsprojekt

Erstellen Sie eine Liste von `AzDataMigrationDatabaseInfo`-Objekten, in der Datenbanken als Teil des Azure Database Migration Service-Projekts angegeben sind, die als Parameter für die Erstellung des Projekts bereitgestellt werden können. Sie können das Cmdlet `New-AzDataMigrationDatabaseInfo` verwenden, um `AzDataMigrationDatabaseInfo` zu erstellen.

Im folgenden Beispiel wird das Projekt `AzDataMigrationDatabaseInfo` für die Datenbank **AdventureWorks2016** erstellt und der Liste hinzugefügt, die als Parameter für die Projekterstellung bereitgestellt wird.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Erstellen eines Projektobjekts

Abschließend können Sie mit `New-AzDataMigrationProject` ein Azure Database Migration Service-Projekt mit Namen *MyDMSProject* erstellen, das sich in *USA, Osten* befindet, und die zuvor erstellte Quell- und Zielverbindung sowie die Liste der zu migrierenden Datenbanken hinzufügen.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Erstellen und Starten einer Migrationsaufgabe

Als Nächstes erstellen Sie eine Azure Database Migration Service-Aufgabe und starten diese. Diese Aufgabe erfordert außer den Informationen, die bereits durch das als Voraussetzung erstellte Projekt bereitgestellt werden, die Anmeldeinformationen sowohl für die Quell- als auch die Zielverbindung sowie die Liste der zu migrierenden Datenbanktabellen.

### <a name="create-credential-parameters-for-source-and-target"></a>Erstellen von Parametern für Anmeldeinformationen für Quelle und Ziel

Erstellen Sie Sicherheitsanmeldeinformationen für die Verbindung als ein [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0)-Objekt.

Das folgende Beispiel zeigt die Erstellung von *PSCredential*-Objekten sowohl für die Quell- als auch die Zielverbindung, wobei Kennwörter als Zeichenfolgenvariablen *$sourcePassword* und *$targetPassword* bereitgestellt werden.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Erstellen eines Sicherungs-FileShare-Objekts

Erstellen Sie nun ein FileShare-Objekt, das die lokale SMB-Netzwerkfreigabe darstellt, in die Azure Database Migration Service mit dem Cmdlet `New-AzDmsFileShare` die Quelldatenbanksicherungen ausführen kann.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Erstellen eines ausgewählten Datenbankobjekts

Der nächste Schritt besteht darin, die Quell- und Zieldatenbanken mithilfe des Cmdlets `New-AzDmsSelectedDB` auszuwählen.

Das folgende Beispiel zeigt das Migrieren eines Singletons aus SQL Server zu einer Instanz in Azure SQL Managed Instance:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Wenn eine komplette SQL Server-Instanz per Lift & Shift zu einer Instanz in Azure SQL Managed Instance migriert werden muss, wird unten eine Schleife bereitgestellt, um alle Datenbanken aus der Quelle zu erfassen. Geben Sie im folgenden Beispiel für „$Server“, „$SourceUserName“ und „$SourcePassword“ die Details Ihrer SQL Server-Quelle an.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS-URI für Azure-Speichercontainer

Erstellen Sie eine Variable, die den SAS-URI enthält, mit dem für den Azure Database Migration Service der Zugriff auf den Speicherkontocontainer gewährt wird, in den der Dienst die Sicherungsdateien hochlädt.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Die Verwendung eines SAS-Tokens auf Kontoebene wird von Azure Database Migration Service nicht unterstützt. Sie müssen einen SAS-URI für den Speicherkontocontainer verwenden. [Erfahren Sie, wie der SAS-URI für Blobcontainer abgerufen wird](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Zusätzliche Konfigurationsanforderungen

Es gibt einige zusätzliche Anforderungen, die Sie berücksichtigen müssen, die aber variieren, je nachdem, ob Sie eine Offline- oder Onlinemigration durchführen.

#### <a name="offline-migrations"></a>Offlinemigrationen

Führen Sie nur für Offlinemigrationen die folgenden zusätzlichen Konfigurationsaufgaben durch.

* **Auswählen von Anmeldungen**. Erstellen Sie eine Liste der zu migrierenden Anmeldungen, wie im folgenden Beispiel gezeigt:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Zurzeit unterstützt Azure Database Migration Service nur das Migrieren von SQL-Anmeldungen.

* **Auswählen von Agent-Aufträgen**. Erstellen Sie eine Liste der zu migrierenden Agent-Aufträge, wie im folgenden Beispiel gezeigt:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Zurzeit unterstützt Azure Database Migration Service nur Aufträge mit T-SQL-Subsystemauftragsschritten.

#### <a name="online-migrations"></a>Onlinemigrationen

Führen Sie nur für Onlinemigrationen die folgenden zusätzlichen Konfigurationsaufgaben durch.

* **Konfigurieren der Azure Active Directory-App**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurieren der Speicherressource**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Erstellen und Starten der Migrationsaufgabe

Verwenden Sie das Cmdlet `New-AzDataMigrationTask` zum Erstellen und Starten einer Migrationsaufgabe.

#### <a name="specify-parameters"></a>Angeben von Parametern

##### <a name="common-parameters"></a>Allgemeine Parameter

Unabhängig davon, ob Sie eine Offline- oder Onlinemigration durchführen, erwartet das Cmdlet `New-AzDataMigrationTask` die folgenden Parameter:

* *TaskType*: Als Typ der Migrationsaufgabe, die für die Migration von SQL Server zu Azure SQL Managed Instance zu erstellen ist, wird *MigrateSqlServerSqlDbMi* erwartet. 
* *ResourceGroupName*: Name der Azure-Ressourcengruppe, in der die Aufgabe erstellt werden soll.
* *ServiceName*: Instanz von Azure Database Migration Service, in der die Aufgabe erstellt werden soll.
* *ProjectName*: Name des Azure Database Migration Service-Projekts, in dem die Aufgabe erstellt werden soll. 
* *TaskName*: Name der zu erstellenden Aufgabe. 
* *SourceConnection*. Das „AzDmsConnInfo“-Objekt, das die SQL Server-Quellverbindung darstellt.
* *TargetConnection*. Das „AzDmsConnInfo“-Objekt, das die Zielverbindung zu Azure SQL Managed Instance darstellt.
* *SourceCred*: Das [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0)-Objekt für die Verbindung mit dem Quellserver.
* *TargetCred*: Das [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0)-Objekt für die Verbindung mit dem Zielserver.
* *SelectedDatabase*: Das „AzDataMigrationSelectedDB“-Objekt, das die Quell- und Zieldatenbankzuordnung darstellt.
* *BackupFileShare*. FileShare-Objekt, das die lokale Netzwerkfreigabe darstellt, auf die Azure Database Migration Service die Quelldatenbanksicherungen übertragen kann.
* *BackupBlobSasUri*. Der SAS-URI, mit dem für den Azure Database Migration Service der Zugriff auf den Speicherkontocontainer gewährt wird, in den der Dienst die Sicherungsdateien hochlädt. Erfahren Sie, wie der SAS-URI für Blobcontainer abgerufen wird.
* *SelectedLogins*. Liste der ausgewählten zu migrierenden Anmeldungen.
* *SelectedAgentJobs*. Liste der ausgewählten zu migrierenden Agent-Aufträge.

##### <a name="additional-parameters"></a>Zusätzliche Parameter

Das Cmdlet `New-AzDataMigrationTask` erwartet außerdem Parameter, die für den Typ der von Ihnen ausgeführten Migration, offline oder online, eindeutig sind.

* **Offlinemigrationen**. Für Offlinemigrationen erwartet das Cmdlet `New-AzDataMigrationTask` außerdem die folgenden Parameter:

  * *SelectedLogins*. Liste der ausgewählten zu migrierenden Anmeldungen.
  * *SelectedAgentJobs*. Liste der ausgewählten zu migrierenden Agent-Aufträge.

* **Onlinemigrationen**. Für Onlinemigrationen erwartet das Cmdlet `New-AzDataMigrationTask` außerdem die folgenden Parameter.

* *AzureActiveDirectoryApp*. Active Directory-Anwendung.
* *StorageResourceID*. Ressourcen-ID des Speicherkontos.

#### <a name="create-and-start-an-offline-migration-task"></a>Erstellen und Starten einer Offlinemigrationsaufgabe

Im folgenden Beispiel wird eine Offlinemigrationsaufgabe mit dem Namen **myDMSTask** erstellt und gestartet:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Erstellen und Starten einer Onlinemigrationsaufgabe

Im folgenden Beispiel wird eine Onlinemigrationsaufgabe mit dem Namen **myDMSTask** erstellt und gestartet:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Überwachen der Migration

Um die Migration zu überwachen, führen Sie die folgenden Aufgaben aus.

1. Konsolidieren Sie alle Migrationsdetails in eine Variablen namens „$CheckTask“.

    Verwenden Sie zum Kombinieren der Migrationsdetails wie Eigenschaften, Zustand und der Migration zugeordnete Datenbankinformationen den folgenden Codeausschnitt:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Verwenden Sie die Variable `$CheckTask`, um den aktuellen Zustand der Migrationsaufgabe abzurufen.

    Um die Variable `$CheckTask` zum Abrufen des aktuellen Zustands der Migrationsaufgabe zu verwenden, können Sie die Ausführung der Migrationsaufgabe überwachen, indem Sie die Zustandseigenschaft der Aufgabe abfragen, wie es im folgenden Beispiel gezeigt wird:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Durchführen der Übernahme (nur Onlinemigrationen)

Bei einer Onlinemigration wird eine vollständige Sicherung und Wiederherstellung der Datenbank durchgeführt, woraufhin die Arbeit mit der Wiederherstellung der im BackupFileShare gespeicherten Transaktionsprotokolle fortgesetzt wird.

Wenn die Datenbank in Azure SQL Managed Instance mit den neuen Daten aktualisiert wird und mit der Quelldatenbank synchronisiert ist, können Sie eine Übernahme durchführen.

Im folgende Beispiel wird die Übernahmemigration abgeschlossen. Benutzer rufen diesen Befehl nach ihrem eigenen Ermessen auf.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Löschen der Instanz von Azure Database Migration Service

Nach Abschluss der Migration können Sie die Azure Database Migration Service-Instanz löschen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zu zusätzlichen Migrationsszenarien (Quelle/Ziel-Paare) finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/) von Microsoft.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).