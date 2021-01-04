---
title: 'Schnellstart: Skalieren von Computeressourcen für einen dedizierten SQL-Pool (vormals SQL DW) (Azure PowerShell)'
description: Sie können Computeressourcen für einen dedizierten SQL-Pool (vormals SQL DW) mithilfe von Azure PowerShell skalieren.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 87e10740e6081431bad96daa930f61238ca495bd
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921902"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Schnellstart: Skalieren von Computeressourcen für einen dedizierten SQL-Pool (vormals SQL DW) mithilfe von Azure PowerShell

Sie können Computeressourcen für einen dedizierten SQL-Pool (vormals SQL DW) mithilfe von Azure PowerShell skalieren. [Skalieren Sie Computeressourcen auf](sql-data-warehouse-manage-compute-overview.md), um eine bessere Leistung zu erzielen, oder skalieren Sie sie ab, um Kosten einzusparen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über einen dedizierten SQL-Pool (vormals SQL DW) verfügen, den Sie skalieren können. Verwenden Sie die Anleitung unter [Erstellen und Verbinden – Portal](create-data-warehouse-portal.md), um bei Bedarf einen dedizierten SQL-Pool (vormals SQL DW) namens **mySampleDataWarehouse** zu erstellen.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Verwenden Sie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um zu ermitteln, welches Abonnement Sie verwenden.

```powershell
Get-AzSubscription
```

Falls Sie ein anderes Abonnement als das Standardabonnement verwenden müssen, führen Sie [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aus.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Suche nach Informationen zum Data Warehouse

Suchen Sie nach dem Datenbanknamen, dem Servernamen und der Ressourcengruppe für das Data Warehouse, das Sie anhalten und fortsetzen möchten.

Führen Sie die folgenden Schritte aus, um nach Informationen zu Ihrem Data Warehouse zu suchen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf der linken Navigationsseite des Azure-Portals auf **Azure Synapse Analytics (vormals SQL DW)** .
3. Wählen Sie auf der Seite **Azure Synapse Analytics (vormals SQL DW)** die Option **mySampleDataWarehouse** aus, um das Data Warehouse zu öffnen.

    ![Servername und Ressourcengruppe](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Notieren Sie sich den Namen des Data Warehouse. Dieser wird als Datenbankname verwendet. Nicht vergessen: Ein Data Warehouse ist eine Art von Datenbank. Notieren Sie außerdem den Servernamen und die Ressourcengruppe. Sie verwenden den Servernamen und Ressourcengruppennamen in den Befehlen zum Anhalten und Fortsetzen.
5. Verwenden Sie in den PowerShell-Cmdlets nur den ersten Teil des Servernamens. In der obigen Abbildung lautet der vollständige Servername „sqlpoolservername.database.windows.net“. Im PowerShell-Cmdlet wird **sqlpoolservername** als Servername verwendet.

## <a name="scale-compute"></a>Skalieren von Computeressourcen

Im dedizierten SQL-Pool (vormals SQL DW) können Sie die Menge der Computeressourcen durch das Anpassen der Data Warehouse-Einheiten erhöhen oder verringern. Mit dem [Schnellstart: Erstellen und Abfragen einer Azure SQL Data Warehouse-Instanz im Azure-Portal](create-data-warehouse-portal.md) wurde **mySampleDataWarehouse** erstellt und mit 400 DWUs initialisiert. In den folgenden Schritten werden die DWUs für **mySampleDataWarehouse** angepasst.

Um die Data Warehouse-Einheiten zu ändern, verwenden Sie das PowerShell-Cmdlet [Set AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Im folgenden Beispiel werden die Data Warehouse-Einheiten für die Datenbank **mySampleDataWarehouse**, die sich in der Ressourcengruppe **resourcegroupname** auf dem Server **sqlpoolservername** befindet, auf DW300c festgelegt.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Überprüfen des Data Warehouse-Zustands

Um den aktuellen Status des Data Warehouse zu ermitteln, verwenden Sie das PowerShell-Cmdlet [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Mit diesem Cmdlet wird der Status der Datenbank **mySampleDataWarehouse** in der Ressourcengruppe **resourcegroupname** auf dem Server **sqlpoolservername.database.windows.net** angezeigt.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Das Ergebnis ähnelt folgendem:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Den **Status** der Datenbank können Sie der Ausgabe entnehmen. In diesem Fall sehen Sie sich, dass diese Datenbank online ist.  Wenn Sie diesen Befehl ausführen, erhalten Sie als Statuswert entweder „Online“, „Wird angehalten“, „Wird fortgesetzt“, „Wird skaliert“ oder „Angehalten“.

Führen Sie den folgenden Befehl aus, um nur den Status anzuzeigen:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Nächste Schritte

Sie wissen nun, wie Sie Computeressourcen für einen dedizierten SQL-Pool (vormals SQL DW) skalieren. Weitere Informationen zum dedizierten SQL-Pool (vormals SQL DW) finden Sie im Tutorial zum Laden von Daten.

> [!div class="nextstepaction"]
>[Tutorial: Laden des Datasets „New York Taxis“](load-data-from-azure-blob-storage-using-copy.md)
