---
title: Azure PowerShell-Skriptbeispiel – Einrichten einer benutzerdefinierten Domäne | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Einrichten einer benutzerdefinierten Domäne
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 12/14/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 19f5016b5fb6906046875ad1ab96c892d6e10ecf
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729744"
---
# <a name="set-up-custom-domain"></a>Einrichten einer benutzerdefinierten Domäne

Dieses Beispielskript richtet eine benutzerdefinierte Domäne auf dem Proxy und dem Portalendpunkt des API Management-Diensts ein.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/api-management/setup-custom-domain/setup_custom_domain.ps1 "Set up custom domain")]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

[!INCLUDE [api-management-custom-domain](../../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview).

Weitere Azure PowerShell-Beispiele für Azure API Management finden Sie in den [PowerShell-Beispielen](../powershell-samples.md).
