---
title: Bereitstellen von SQL Managed Instance in einem Instanzpool
titleSuffix: Azure SQL Managed Instance
description: In diesem Artikel wird beschrieben, wie Sie Pools für Azure SQL Managed Instance erstellen und verwalten (Vorschau).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793108"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Bereitstellen von Azure SQL Managed Instance in einem Instanzpool
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel beschreibt ausführlich das Erstellen eines [Instanzpools](instance-pools-overview.md) und das Bereitstellen von Azure SQL Managed Instance darin. 

## <a name="instance-pool-operations"></a>Vorgänge für Instanzenpools

In der folgenden Tabelle sind die verfügbaren Vorgänge im Zusammenhang mit Instanzenpools und deren Verfügbarkeit im Azure-Portal und in PowerShell aufgeführt.

|Get-Help|Azure-Portal|PowerShell|
|:---|:---|:---|
|Erstellen eines Instanzenpools|Nein|Ja|
|Aktualisieren eines Instanzpools (begrenzte Anzahl von Eigenschaften)|Nein |Ja |
|Überprüfen von Verwendung und Eigenschaften eines Instanzpools|Nein|Ja |
|Löschen eines Instanzpools|Nein|Ja|
|Erstellen einer verwalteten Instanz innerhalb eines Instanzpools|Nein|Ja|
|Aktualisieren der Ressourcennutzung für eine verwaltete Instanz|Ja |Ja|
|Überprüfen von Nutzung und Eigenschaften einer verwalteten Instanz|Ja|Ja|
|Löschen einer verwalteten Instanz aus dem Pool|Ja|Ja|
|Erstellen einer Datenbank in einer Instanz innerhalb des Pools|Ja|Ja|
|Löschen einer Datenbank aus SQL Managed Instance|Ja|Ja|

Verfügbare [PowerShell-Befehle](/powershell/module/az.sql/):

|Cmdlet |BESCHREIBUNG |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Erstellt einen SQL Managed Instance-Pool. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Gibt Informationen über einen Instanzpool zurück. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Legt Eigenschaften für einen Instanzpool in SQL Managed Instance fest. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Entfernt einen Instanzpool in SQL Managed Instance. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Gibt Nutzungsinformationen über einen SQL Managed Instance-Pool zurück. |


Zur Verwendung von PowerShell [installieren Sie die neueste Version von PowerShell Core](/powershell/scripting/install/installing-powershell#powershell), und befolgen Sie die Anweisungen zum [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

Verwenden Sie für Vorgänge für Instanzen innerhalb von Pools sowie für Einzelinstanzen die [Standardbefehle für verwaltete Instanzen](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances). Wenn diese Befehle für eine Instanz in einem Pool verwendet werden, muss allerdings der *Name des Instanzenpools* angegeben werden.

## <a name="deployment-process"></a>Bereitstellungsprozess

Um eine verwaltete Instanz in einem Instanzpool bereitzustellen, müssen Sie zuerst den Instanzpool bereitstellen. Dies ist ein einmaliger Vorgang mit langer Ausführungszeit, dessen Dauer die gleiche ist wie beim Bereitstellen einer [einzelnen Instanz, die in einem leeren Subnetz erstellt wird](sql-managed-instance-paas-overview.md#management-operations). Anschließend können Sie eine verwaltete Instanz im Pool bereitstellen, was ein relativ schneller Vorgang ist, der in der Regel nicht mehr als fünf Minuten dauert. Der Instanzenpoolparameter muss als Teil dieses Vorgangs explizit angegeben werden.

In der öffentlichen Vorschauversion werden beide Aktionen nur unter Verwendung von PowerShell und Azure Resource Manager-Vorlagen unterstützt. Das Azure-Portal steht dafür derzeit nicht zur Verfügung.

Nach dem Bereitstellen einer verwalteten Instanz in einem Pool *können* Sie über das Azure-Portal die zugehörigen Eigenschaften auf der Seite mit den Tarifen ändern.

## <a name="create-a-virtual-network-with-a-subnet"></a>Erstellen eines virtuellen Netzwerks mit einem Subnetz 

Informationen zum Platzieren mehrerer Instanzenpools innerhalb desselben virtuellen Netzwerks finden Sie in den folgenden Artikeln:

- [Bestimmen der VNET-/Subnetzgröße für eine Azure SQL Managed Instance](vnet-subnet-determine-size.md).
- Erstellen Sie ein neues virtuelles Netzwerk und Subnetz mithilfe der [Vorlage im Azure-Portal](virtual-network-subnet-create-arm-template.md), oder befolgen Sie die Anweisungen zum [Vorbereiten eines vorhandenen virtuellen Netzwerks](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Erstellen eines Instanzenpools 

Nach Ausführen der vorherigen Schritte können Sie einen Instanzenpool erstellen.

Für Instanzenpools gelten die folgenden Einschränkungen:

- In der Public Preview sind nur die Optionen „Universell“ und „Gen5“ verfügbar.
- Der Poolname darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten, und er darf nicht mit einem Bindestrich beginnen.
- Wenn Sie den Azure-Hybridvorteil nutzen möchten, wird er auf Ebene des Instanzpools angewandt. Sie können den Lizenztyp während der Poolerstellung festlegen oder jederzeit nach der Erstellung aktualisieren.

> [!IMPORTANT]
> Das Bereitstellen eines Instanzenpools ist ein zeitintensiver Vorgang und dauert ungefähr 4,5 Stunden.

So rufen Sie Netzwerkparameter ab:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

So erstellen Sie einen Instanzenpool

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Da die Bereitstellung eines Instanzenpools ein zeitintensiver Vorgang ist, müssen Sie warten, bis er abgeschlossen ist, bevor Sie die nachfolgenden Schritte in diesem Artikel ausführen.

## <a name="create-a-managed-instance"></a>Erstellen einer verwalteten Instanz

Nach der erfolgreichen Bereitstellung des Instanzpools kann nun eine verwaltete Instanz darin erstellt werden.

Führen Sie zum Erstellen einer verwalteten Instanz den folgenden Befehl aus:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Das Bereitstellen einer Instanz innerhalb eines Pools dauert einige Minuten. Nachdem die erste Instanz erstellt wurde, können weitere Instanzen erstellt werden:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Erstellen einer Datenbank 

Zum Erstellen und Verwalten von Datenbanken in einer verwalteten Instanz, die sich in einem Pool befindet, verwenden Sie die Befehle für Einzelinstanzen.

So erstellen Sie eine Datenbank innerhalb einer verwalteten Instanz

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Abrufen der Poolnutzung 
 
So rufen Sie eine Liste von Instanzen innerhalb eines Pools ab

```powershell
$instancePool | Get-AzSqlInstance
```


So rufen Sie die Ressourcennutzung des Pools ab

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


So rufen Sie die detaillierte Nutzungsübersicht über den Pool und die darin enthaltenen Instanzen ab

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

So listen Sie die Datenbanken in einer Instanz auf

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Pro Pool (nicht pro Instanz) können maximal 100 Datenbanken erstellt werden.


## <a name="scale"></a>Skalieren 


Nachdem Sie eine verwaltete Instanz mit Datenbanken gefüllt haben, erreichen Sie möglicherweise Instanzengrenzwerte in Bezug auf die Speicherung oder Leistung. Wenn in diesem Fall die Poolverwendung nicht überschritten wurde, können Sie die Instanz skalieren.
Die Skalierung einer verwalteten Instanz innerhalb eines Pools dauert einige Minuten. Voraussetzung für die Skalierung sind verfügbare virtuelle Kerne und verfügbarer Speicher auf Ebene des Instanzenpools.

So aktualisieren Sie die Anzahl der virtuellen Kerne und die Speichergröße

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


So aktualisieren Sie nur die Speichergröße

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Verbinden 

Zum Herstellen einer Verbindung mit einer verwalteten Instanz in einem Pool sind die folgenden zwei Schritte erforderlich:

1. [Aktivieren des öffentlichen Endpunkts für die Instanz](#enable-the-public-endpoint)
2. [Hinzufügen einer Eingangsregel zur Netzwerksicherheitsgruppe (NSG)](#add-an-inbound-rule-to-the-network-security-group)

Nach Ausführung beider Schritte können Sie unter Verwendung der Adresse, des Ports und der Anmeldeinformationen des öffentlichen Endpunkts, die bei der Erstellung der Instanz angegeben wurden, eine Verbindung mit der Instanz herstellen. 

### <a name="enable-the-public-endpoint"></a>Aktivieren des öffentlichen Endpunkts

Die Aktivierung des öffentlichen Endpunkts für eine Instanz kann über das Azure-Portal oder den folgenden PowerShell-Befehl erfolgen:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Auch dieser Parameter kann bei der Erstellung der Instanz festgelegt werden.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Hinzufügen einer Eingangsregel zur Netzwerksicherheitsgruppe 

Dieser Schritt kann über das Azure-Portal oder mithilfe von PowerShell-Befehlen ausgeführt werden. Er kann jederzeit erfolgen, nachdem das Subnetz für die verwaltete Instanz vorbereitet wurde.

Ausführliche Informationen finden Sie unter [Zulassen von Datenverkehr auf dem öffentlichen Endpunkt in der Netzwerksicherheitsgruppe](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Verschieben einer vorhandenen Einzelinstanz in einen Pool
 
Das Verschieben von Instanzen in und aus einem Pool ist eine der Einschränkungen der Public Preview. Eine Problemumgehung basiert auf der Point-in-Time-Wiederherstellung von Datenbanken einer Instanz außerhalb eines Pools in einer Instanz, die sich bereits in einem Pool befindet. 

Beide Instanzen müssen sich im selben Abonnement und derselben Region befinden. Die regions- und abonnementübergreifende Wiederherstellung wird derzeit nicht unterstützt.

Dieser Vorgang bringt eine gewisse Ausfallzeit mit sich.

So verschieben Sie vorhandene Datenbanken

1. Halten Sie Workloads in der verwalteten Instanz an, von der aus die Migration durchgeführt wird.
2. Generieren Sie Skripts zum Erstellen von Systemdatenbanken, und führen Sie sie in der Instanz aus, die sich im Instanzenpool befindet.
3. Führen Sie eine Point-in-Time-Wiederherstellung der einzelnen Datenbanken der Einzelinstanz in der Instanz im Pool durch.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Richten Sie in der Anwendung einen Verweis auf die neue Instanz ein, und setzen Sie die zugehörigen Workloads fort.

Wiederholen Sie bei mehreren Datenbanken den Vorgang für jede Datenbank.


## <a name="next-steps"></a>Nächste Schritte

- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](../database/features-comparison.md).
- Weitere Informationen zur VNET-Konfiguration finden Sie unter [Konfigurieren eines VNET für SQL Managed Instance](connectivity-architecture-overview.md).
- Eine Schnellstartanleitung, in der eine verwaltete Instanz erstellt und eine Datenbank aus einer Sicherungsdatei wiederhergestellt wird, finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](instance-create-quickstart.md).
- Ein Tutorial zur Verwendung des Azure Database Migration Service für die Migration finden Sie unter [SQL Managed Instance-Migration mithilfe des Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Informationen zur erweiterten Überwachung der Datenbankleistung von SQL Managed Instance mit integrierten Problembehandlungsfunktionen finden Sie unter [Überwachen von Azure SQL-Datenbank mithilfe von Azure SQL-Analyse (Vorschauversion)](../../azure-monitor/insights/azure-sql.md).
- Preisinformationen finden Sie auf der Seite mit den [Preisen für SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).