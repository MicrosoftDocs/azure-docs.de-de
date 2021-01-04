---
title: Erstellen eines dedizierten SQL-Pools (vormals SQL DW) mithilfe einer Azure Resource Manager-Vorlage
description: Hier erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage einen Azure Synapse Analytics SQL-Pool erstellen.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460731"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Schnellstart: Erstellen eines dedizierten Azure Synapse Analytics-SQL-Pools (vormals SQL DW) mithilfe einer ARM-Vorlage

Mit dieser Azure Resource Manager-Vorlage (ARM-Vorlage) wird ein dedizierter SQL-Pool (vormals SQL DW) mit aktivierter Transparent Data Encryption erstellt. Ein dedizierter SQL-Pool (früher SQL DW) bezieht sich auf die Data Warehousing-Features für Unternehmen, die in Azure Synapse allgemein zur Verfügung stehen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Die Vorlage definiert eine Ressource:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie das folgende Image aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Mit dieser Vorlage wird ein dedizierter SQL-Pool (vormals SQL DW) erstellt.
   
   [![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Geben Sie die folgenden Werte ein, oder aktualisieren Sie sie:

   * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
   * **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und wählen Sie **OK** aus. Eine neue Ressourcengruppe erleichtert das Bereinigen von Ressourcen.
   * **Region**: Wählen Sie eine Region aus.  Beispiel: **USA, Mitte**.
   * **SQL Server-Name**: Übernehmen Sie den Standardnamen, oder geben Sie einen Namen für als SQL Server-Namen ein.
   * **SQL-Administratoranmeldung**: Geben Sie den Benutzernamen des Administrators für den SQL Server ein.
   * **SQL-Administratorkennwort**: Geben Sie das Administratorkennwort für den SQL Server ein.
   * **Data Warehouse-Name**: Geben Sie den Namen eines dedizierten SQL-Pools ein.
   * **Transparent Data Encryption**: Akzeptieren Sie die Standardeinstellung „Aktiviert“. 
   * **Servicelevelziel**: Akzeptieren Sie den Standardwert „DW400c“.
   * **Standort**: Akzeptieren Sie den Standardstandort der Ressourcengruppe.
   * **Überprüfen und erstellen**: Aktivieren Sie dieses Kontrollkästchen.
   * **Erstellen**: Aktivieren Sie dieses Kontrollkästchen.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können entweder das Azure-Portal nutzen, um die bereitgestellten Ressourcen zu überprüfen, oder Azure CLI oder ein Azure PowerShell-Skript, um die bereitgestellten Ressourcen aufzulisten.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

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

In dieser Schnellstartanleitung haben Sie mithilfe einer ARM-Vorlage einen dedizierten SQL-Pool (vormals SQL DW) erstellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Synapse Analytics und Azure Resource Manager finden Sie in den folgenden Artikeln:

- Lesen Sie [Eine Übersicht über Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).
- Lesen Sie weitere Informationen zu [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- [Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
