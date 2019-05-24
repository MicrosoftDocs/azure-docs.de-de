---
title: Azure PowerShell-Skriptbeispiel – Skalieren der Dienstinstanz | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Skalieren der Dienstinstanz
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: c00aee73d546095b3bf73febdceaac59cf5ee687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66141167"
---
# <a name="scale-the-service-instance"></a>Skalieren der Dienstinstanz

Dieses Beispielskript skaliert die API Management-Dienstinstanz und fügt ihr eine Region hinzu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/api-management/scale-and-addregion-apim-service/scale_and_addregion_apim_service.ps1 "Scale the APIM service instance")]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview).

Weitere Azure PowerShell-Beispiele für Azure API Management finden Sie in den [PowerShell-Beispielen](../powershell-samples.md).
