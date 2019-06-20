---
title: 'PowerShell-Skript: Erstellen eines benutzerdefinierten Images anhand einer VHD in Azure Lab Services | Microsoft-Dokumentation'
description: Dieses PowerShell-Skript erstellt ein benutzerdefiniertes Image anhand einer VHD-Datei in Azure Lab Services.
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
ms.openlocfilehash: 2d0cc4012adf2c17b2f7a2e769f2d666b158a8c8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160559"
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

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).
