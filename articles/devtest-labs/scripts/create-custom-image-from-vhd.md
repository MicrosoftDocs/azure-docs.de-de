---
title: 'PowerShell: Erstellen eines benutzerdefinierten Images anhand einer VHD-Datei in Azure Lab Services'
description: Dieses PowerShell-Skript erstellt ein benutzerdefiniertes Image anhand einer VHD-Datei in Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: d22e6e1d226e826bf114a0fdb378879b828d9b4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022233"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Erstellen eines benutzerdefinierten Images anhand einer VHD-Datei in Azure Lab Services mithilfe von PowerShell

Dieses PowerShell-Beispielskript erstellt ein benutzerdefiniertes Image aus einer VHD-Datei in Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Lab.** Das Skript erfordert ein vorhandenes Lab. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Dient zum Abrufen von Ressourcen. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Ruft die Zugriffsschlüssel für ein Azure-Speicherkonto ab. |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Fügt einer Ressourcengruppe eine Azure-Bereitstellung hinzu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).
