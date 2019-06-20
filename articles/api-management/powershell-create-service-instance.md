---
title: Erstellen einer Azure API Management-Instanz mit PowerShell | Microsoft-Dokumentation
description: Führen Sie die Schritte dieses Tutorials zum Erstellen einer neuen Azure API Management-Instanz aus.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: 7cd4810059199ddfc88261e27bc910331abe096d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730631"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Erstellen einer neuen Azure API Management-Dienstinstanz

Azure API Management (APIM) unterstützt Organisationen beim Veröffentlichen von APIs für externe Entwickler, Partnerentwickler und interne Entwickler, um das volle Potenzial von Daten und Diensten ausschöpfen zu können. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, geschäftlichen Erkenntnissen, Analysen, Sicherheit und Schutz. Mit APIM können Sie moderne API-Gateways für vorhandene Back-End-Dienste, die an einem beliebigen Ort gehostet werden, erstellen und verwalten. Weitere Informationen finden Sie im Thema [Übersicht](api-management-key-concepts.md).

In diesem Schnellstart werden die Schritte zum Erstellen einer neuen API Management-Instanz mit PowerShell-Skripts beschrieben. Der Schnellstart veranschaulicht das Verwenden der **Azure Cloud Shell**, die im Azure-Portal ausgeführt werden kann.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.


## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Erstellen eines API Management-Diensts

Dies ist ein langwieriger Vorgang, dessen Ausführung bis zu 15 Minuten dauern kann.

```azurepowershell-interactive
New-AzApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md)
