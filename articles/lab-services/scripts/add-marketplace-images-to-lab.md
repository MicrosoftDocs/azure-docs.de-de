---
title: 'PowerShell-Skript: Hinzufügen eines Marketplace-Images zu einem Lab in Azure DevTest Labs | Microsoft-Dokumentation'
description: Dieses PowerShell-Skript fügt einem Lab in Azure DevTest Labs ein Marketplace-Image hinzu.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: e099a29a198d43bf8d00487ab45e2648479aedbe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160600"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Hinzufügen eines Marketplace-Images zu einem Lab in Azure DevTest Labs mithilfe von PowerShell

Dieses PowerShell-Beispielskript fügt einem Lab in Azure DevTest Labs ein Marketplace-Image hinzu. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Lab.** Das Skript erfordert ein vorhandenes Lab. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| Find-AzResource | Dient zum Suchen nach Ressourcen basierend auf den angegebenen Parametern. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Dient zum Abrufen von Ressourcen. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Dient zum Ändern einer Ressource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Dient zum Erstellen einer Ressource. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).
