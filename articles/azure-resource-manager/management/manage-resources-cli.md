---
title: 'Verwalten von Ressourcen: Azure CLI'
description: Verwenden Sie die Azure CLI und Azure Resource Manager zum Verwalten Ihrer Ressourcen. Hier wird gezeigt, wie Sie Ressourcen bereitstellen und löschen.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6672fd41dbd199940d663c4f7e1cb7f3b1936ce9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185775"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Verwalten von Azure-Ressourcen mithilfe der Azure CLI

Erfahren Sie, wie Sie mit der Azure CLI und [Azure Resource Manager](overview.md) Ihre Azure-Ressourcen verwalten. Informationen zum Verwalten von Ressourcengruppen finden Sie unter [Verwalten von Azure-Ressourcengruppen mithilfe der Azure CLI](manage-resource-groups-cli.md).

Andere Artikel zum Verwalten von Ressourcen:

- [Verwalten von Azure-Ressourcen über das Azure-Portal](manage-resources-portal.md)
- [Verwalten von Azure-Ressourcen mithilfe von Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Bereitstellen von Ressourcen in einer vorhandenen Ressourcengruppe

Sie können Azure-Ressourcen direkt mithilfe der Azure CLI bereitstellen oder eine Resource Manager-Vorlage zum Erstellen von Azure-Ressourcen bereitstellen.

### <a name="deploy-a-resource"></a>Bereitstellen einer Ressource

Das folgende Skript erstellt ein Speicherkonto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Bereitstellen einer Vorlage

Das folgende Skript stellt eine Schnellstartvorlage zum Erstellen eines Speicherkontos bereit. Weitere Informationen finden Sie unter [Quickstart: Erstellen von Azure Resource Manager-Vorlagen mit Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Bereitstellen einer Ressourcengruppe und von Ressourcen

Sie können eine Ressourcengruppe erstellen und Ressourcen in der Gruppe bereitstellen. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe und Bereitstellen von Ressourcen](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Bereitstellen von Ressourcen für mehrere Abonnements oder Ressourcengruppen

In der Regel stellen Sie alle Ressourcen in der Vorlage als einzelne Ressourcengruppe bereit. Es gibt jedoch Szenarien, bei denen Sie eine Reihe von Ressourcen zwar gemeinsam, aber in verschiedenen Ressourcengruppen oder Abonnements bereitstellen möchten. Weitere Informationen finden Sie unter [Bereitstellen von Azure-Ressourcen für mehrere Abonnements oder Ressourcengruppen](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Löschen von Ressourcen

Das folgende Skript zeigt, wie ein Speicherkonto gelöscht wird.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](delete-resource-group.md).

## <a name="move-resources"></a>Verschieben von Ressourcen

Das folgende Skript zeigt, wie ein Speicherkonto aus einer Ressourcengruppe in eine andere Ressourcengruppe verschoben werden kann.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Sperren von Ressourcen

Das Sperren verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern, z. B. ein Azure-Abonnement, eine Ressourcengruppe oder eine Ressource. 

Das folgende Skript sperrt ein Speicherkonto, damit das Konto nicht gelöscht werden kann.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Das folgende Skript ruft alle Sperren für ein Speicherkonto ab:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Das folgende Skript löscht eine Sperre für ein Speicherkonto:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](lock-resources.md).

## <a name="tag-resources"></a>Markieren von Ressourcen

Das Markieren hilft Ihnen, Ihre Ressourcengruppe und Ressourcen logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Zugriff auf Ressourcen verwalten

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../templates/template-syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).