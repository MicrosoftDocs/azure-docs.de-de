---
title: 'PowerShell-Skript: Festlegen zulässiger VM-Größen in Azure Lab Services | Microsoft-Dokumentation'
description: Dieser Artikel enthält ein PowerShell-Beispielskript, das zulässige VM-Größen in Azure Lab Services festlegt.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998130"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Festlegen zulässiger VM-Größen in Azure Lab Services mithilfe von PowerShell

Dieses PowerShell-Beispielskript legt zulässige Größen virtueller Computer (VMs) in Azure Lab Services fest.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Lab.** Das Skript erfordert ein vorhandenes Lab. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| Find-AzResource | Dient zum Suchen nach Ressourcen basierend auf den angegebenen Parametern. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Dient zum Abrufen von Ressourcen. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Dient zum Ändern einer Ressource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Dient zum Erstellen einer Ressource. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).
