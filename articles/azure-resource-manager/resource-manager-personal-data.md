---
title: Persönliche Daten und Azure Resource Manager | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie persönliche Daten verwalten, die mit Azure Resource Manager-Vorgängen verknüpft sind.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: db4e1b8705b879fd5716763869462bafdf1f905c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128468"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Verwalten von mit Azure Resource Manager verknüpften persönlichen Daten

Um vertrauliche Informationen zu schützen, löschen Sie alle persönlichen Daten, die Sie in Bereitstellungen, Ressourcengruppen oder Tags angegeben haben. Mithilfe der Vorgänge in Azure Resource Manager können Sie persönliche Daten verwalten, die Sie in Bereitstellungen, Ressourcengruppen oder Tags bereitgestellt haben.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Löschen von persönlichen Daten im Bereitstellungsverlauf

Für Bereitstellungen werden von Resource Manager Parameterwerte und Statusmeldungen im Bereitstellungsverlauf gespeichert. Diese Werte bleiben enthalten, bis Sie die Bereitstellung aus dem Verlauf löschen. Um festzustellen, ob Sie persönliche Daten für diese Werte angegeben haben, listen Sie die Bereitstellungen auf. Wenn Sie persönliche Daten gefunden haben, löschen Sie die Bereitstellungen aus dem Verlauf.

Verwenden Sie diese Referenzen als Hilfe, um **Bereitstellungen** im Verlauf aufzulisten:

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup) (Nach Ressourcengruppe gruppieren)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Verwenden Sie diese Referenzen als Hilfe, um **Bereitstellungen** aus dem Verlauf zu löschen:

* [Löschen](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Löschen von persönlichen Daten in Ressourcengruppennamen

Der Name der Ressourcengruppe wird gespeichert, bis Sie die Ressourcengruppe löschen. Um festzustellen, ob Sie persönliche Daten in den Namen angegeben haben, listen Sie die Ressourcengruppen auf. Wenn Sie persönliche Daten gefunden haben, [verschieben Sie die Ressourcen](resource-group-move-resources.md) in eine neue Ressourcengruppe und löschen die Ressourcengruppe mit persönlichen Daten im Namen.

Verwenden Sie diese Referenzen als Hilfe, um **Ressourcengruppen** aufzulisten:

* [Liste](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Verwenden Sie diese Referenzen als Hilfe, um **Ressourcengruppen** zu löschen:

* [Löschen](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Löschen von persönlichen Daten in Tags

Tagnamen und -werte werden gespeichert, bis Sie das Tag löschen oder ändern. Um festzustellen, ob Sie persönliche Daten in den Tags angegeben haben, listen Sie die Tags auf. Wenn Sie persönliche Daten gefunden haben, löschen Sie die Tags.

Verwenden Sie diese Referenzen als Hilfe, um **Tags** aufzulisten:

* [Liste](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

Verwenden Sie diese Referenzen als Hilfe, um **Tags** zu löschen:

* [Löschen](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Nächste Schritte
* Einen Überblick über Azure Resource Manager erhalten Sie in der [Übersicht über Azure Resource Manager](resource-group-overview.md).