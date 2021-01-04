---
title: Erstellen einer DMS-Instanz (Azure Resource Manager-Vorlage)
description: Hier wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Database Migration Service-Instanz erstellen.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963214"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Schnellstart: Erstellen einer Azure Database Migration Service-Instanz mithilfe einer ARM-Vorlage

Verwenden Sie diese Azure Resource Manager-Vorlage (ARM-Vorlage), um eine Azure Database Migration Service-Instanz bereitzustellen. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Für die ARM-Vorlage für die Azure Database Migration Service-Instanz benötigen Sie Folgendes: 

- Die aktuelle Version der [Azure CLI](/cli/azure/install-azure-cli) bzw. von [PowerShell](/powershell/scripting/install/installing-powershell). 
- Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

In der Vorlage sind drei Azure-Ressourcen definiert: 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): Erstellt das virtuelle Netzwerk. 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): Erstellt das Subnetz. 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services): Stellt eine Azure Database Migration Service-Instanz bereit. 

Weitere Azure Database Migration Service-Vorlagen finden Sie im Katalog unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Mit der Vorlage wird eine Azure Database Migration Service-Instanz erstellt. 

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe**: Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus, oder wählen Sie die Option **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen. 
    * **Region**: Der Standort, an dem die Ressourcen bereitgestellt werden.
    * **Dienstname**: Der Name des neuen Migrationsdiensts.
    * **Standort**: Der Speicherort der Ressourcengruppe (Standardwert `[resourceGroup().location]` beibehalten).
    * **VNET-Name**: Der Name des neuen virtuellen Netzwerks.
    * **Subnetzname**: Der Name des neuen Subnetzes, das dem virtuellen Netzwerk zugeordnet ist.



3. Klicken Sie auf **Überprüfen + erstellen**. Sie erhalten eine Benachrichtigung, nachdem die Azure Database Migration Service-Instanz erfolgreich bereitgestellt wurde. 


Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können die Azure CLI verwenden, um bereitgestellte Ressourcen zu überprüfen. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn sie nicht mehr benötigt wird, kann die Ressourcengruppe über die Azure-Befehlszeilenschnittstelle oder mithilfe von Azure PowerShell gelöscht werden:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nächste Schritte

Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Weitere Möglichkeiten zum Bereitstellen von Azure Database Migration Service finden Sie unter: 
- [Azure-Portal](quickstart-create-data-migration-service-portal.md)

Weitere Informationen finden Sie unter [Was ist Azure Database Migration Service?](dms-overview.md).