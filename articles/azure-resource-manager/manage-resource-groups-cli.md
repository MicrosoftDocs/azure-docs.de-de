---
title: Verwalten von Azure Resource Manager-Gruppen mithilfe der Azure CLI | Microsoft-Dokumentation
description: Verwenden Sie die Azure CLI, um Ihre Azure Resource Manager-Gruppen zu verwalten.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 9245d4c01816d26e262b1c8bd35015e2a6a9558c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550545"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Verwalten von Azure Resource Manager-Gruppen mithilfe der Azure CLI

Erfahren Sie, wie Sie mit der Azure CLI und [Azure Resource Manager](resource-group-overview.md) Ihre Azure-Ressourcengruppen verwalten. Informationen zum Verwalten von Azure-Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen mithilfe der Azure CLI](./manage-resources-cli.md).

Andere Artikel zum Verwalten von Ressourcengruppen:

- [Verwalten von Azure-Ressourcengruppen mithilfe des Azure-Portals](./manage-resources-portal.md)
- [Verwalten von Azure-Ressourcengruppen mithilfe von Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Was ist eine Ressourcengruppe?

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten. Im Allgemeinen fügen Sie einer Ressourcengruppe Ressourcen hinzu, die den gleichen Lebenszyklus haben, damit Sie diese einfacher als Gruppe bereitstellen, aktualisieren und löschen können.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie also fest, wo die Metadaten gespeichert werden. Aus Compliance-Gründen müssen Sie unter Umständen sicherstellen, dass Ihre Daten in einer bestimmten Region gespeichert werden.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie fest, wo die Metadaten gespeichert werden.

## <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Das folgende CLI-Skript erstellt eine Ressourcengruppe und zeigt dann die Ressourcengruppe an.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
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

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Bereitstellen von Ressourcen in einer vorhandenen Ressourcengruppe

Informationen finden Sie unter [Bereitstellen von Ressourcen in einer vorhandenen Ressourcengruppe](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Bereitstellen einer Ressourcengruppe und von Ressourcen

Mithilfe einer Resource Manager-Vorlage können Sie eine Ressourcengruppe erstellen und Ressourcen in der Ressourcengruppe bereitstellen. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe und Bereitstellen von Ressourcen](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Erneute Bereitstellung bei Bereitstellungsfehlern

Dieses Feature ist auch bekannt als *Rollback bei Fehler*. Weitere Informationen finden Sie unter [Erneute Bereitstellung bei Bereitstellungsfehlern](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Verschieben in eine andere Ressourcengruppe oder ein anderes Abonnement

Sie können Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Sperren von Ressourcengruppen

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

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Hinzufügen von Tags zu Ressourcengruppen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md#azure-cli).

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

Weitere Informationen finden Sie unter [Exportieren einer Ressourcengruppe](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Verwalten des Zugriffs auf Ressourcengruppen

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung](../role-based-access-control/overview.md) (Role-Based Access Control, RBAC) verwaltet. Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](./resource-group-overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](/azure/azure-resource-manager/).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).