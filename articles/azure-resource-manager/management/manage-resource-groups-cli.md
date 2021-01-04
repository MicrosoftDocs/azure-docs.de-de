---
title: Verwalten von Ressourcengruppen – Azure CLI
description: Verwenden Sie die Azure CLI, um Ihre Ressourcengruppen über Azure Resource Manager zu verwalten. Hier wird gezeigt, wie Sie Ressourcengruppen erstellen, auflisten und löschen.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a9a4ed4ebba7f6f2470bb9e7000a899ebc26323
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185809"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Verwalten von Azure Resource Manager-Gruppen mithilfe der Azure CLI

Erfahren Sie, wie Sie mit der Azure CLI und [Azure Resource Manager](overview.md) Ihre Azure-Ressourcengruppen verwalten. Informationen zum Verwalten von Azure-Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen mithilfe der Azure CLI](manage-resources-cli.md).

Weitere Artikel zum Verwalten von Ressourcengruppen:

- [Verwalten von Azure-Ressourcengruppen mithilfe des Azure-Portals](manage-resources-portal.md)
- [Verwalten von Azure-Ressourcengruppen mithilfe von Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Was ist eine Ressourcengruppe?

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten. Im Allgemeinen fügen Sie einer Ressourcengruppe Ressourcen hinzu, die den gleichen Lebenszyklus haben, damit Sie diese einfacher als Gruppe bereitstellen, aktualisieren und löschen können.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie also fest, wo die Metadaten gespeichert werden. Aus Compliance-Gründen müssen Sie unter Umständen sicherstellen, dass Ihre Daten in einer bestimmten Region gespeichert werden.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie fest, wo die Metadaten gespeichert werden.

## <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Mit dem folgenden CLI-Befehl wird eine Ressourcengruppe erstellt.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Ressourcengruppen auflisten

Das folgende CLI-Skript listet die Ressourcengruppen unter Ihrem Abonnement auf.

```azurecli-interactive
az group list
```

So erhalten Sie eine Ressourcengruppe:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Löschen von Ressourcengruppen

Das folgende CLI-Skript löscht eine Ressourcengruppe:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Bereitstellen von Ressourcen in einer vorhandenen Ressourcengruppe

Informationen finden Sie unter [Bereitstellen von Ressourcen in einer vorhandenen Ressourcengruppe](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Bereitstellen einer Ressourcengruppe und von Ressourcen

Mithilfe einer Resource Manager-Vorlage können Sie eine Ressourcengruppe erstellen und Ressourcen in der Ressourcengruppe bereitstellen. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe und Bereitstellen von Ressourcen](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Erneute Bereitstellung bei Bereitstellungsfehlern

Dieses Feature ist auch bekannt als *Rollback bei Fehler*. Weitere Informationen finden Sie unter [Erneute Bereitstellung bei Bereitstellungsfehlern](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Verschieben in eine andere Ressourcengruppe oder ein anderes Abonnement

Sie können Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Ressourcengruppen sperren

Das Sperren verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern, z. B. ein Azure-Abonnement, eine Ressourcengruppe oder eine Ressource. 

Das folgende Skript sperrt eine Ressourcengruppe, sodass sie nicht gelöscht werden kann.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Das folgende Skript ruft alle Sperren für eine Ressourcengruppe ab:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Das folgende Skript löscht eine Sperre:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Hinzufügen von Tags zu Ressourcengruppen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportieren von Ressourcengruppen in Vorlagen

Nach dem erfolgreichen Einrichten der Ressourcengruppe sollten Sie die Resource Manager-Vorlage für die Ressourcengruppe anzeigen lassen. Das Exportieren der Vorlage hat zwei Vorteile:

- Automatisieren zukünftiger Bereitstellungen der Lösung, da die Vorlage die gesamte Infrastruktur enthält.
- Überblick über Vorlagensyntax, indem Sie sich die JavaScript Object Notation (JSON) zu Ihrer Lösung ansehen.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Das Skript zeigt die Vorlage in der Konsole an.  Kopieren Sie die JSON, und speichern Sie sie als Datei.

Die Funktion „Vorlage exportieren“ unterstützt nicht das Exportieren von Azure Data Factory-Ressourcen. Informationen zum Exportieren von Data Factory-Ressourcen finden Sie unter [Kopieren oder Klonen einer Data Factory in Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Um über das klassische Bereitstellungsmodell erstellte Ressourcen zu exportieren, müssen Sie [sie zum Resource Manager-Bereitstellungsmodell migrieren](../../virtual-machines/migration-classic-resource-manager-overview.md).

Weitere Informationen finden Sie unter [Single and multi-resource export to template in Azure portal (Exportieren von einzelnen und mehreren Ressourcen in eine Vorlage im Azure-Portal)](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Verwalten des Zugriffs auf Ressourcengruppen

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../templates/template-syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).