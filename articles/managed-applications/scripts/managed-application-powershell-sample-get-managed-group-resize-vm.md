---
title: Azure PowerShell-Skriptbeispiel – Abrufen einer verwalteten Ressourcengruppe und Ändern der Größe von virtuellen Computern | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Abrufen einer verwalteten Ressourcengruppe und Ändern der Größe von virtuellen Computern
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 9e8930c95495673c0082a82757ed6d8137900b6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171491"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Abrufen von Ressourcen in einer verwalteten Ressourcengruppe und Ändern der Größe von virtuellen Computern mit PowerShell

Dieses Skript ruft Ressourcen aus einer verwalteten Ressourcengruppe ab und ändert die Größe der virtuellen Computer in dieser Ressourcengruppe.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Bereitstellen der verwalteten Anwendung. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Auflisten der verwalteten Anwendungen. Geben Sie den Namen der Ressourcengruppe an, um die Ergebnisse einzugrenzen. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Auflisten der Ressourcen. Geben Sie eine Ressourcengruppe und den Ressourcentyp an, um die Ergebnisse einzugrenzen. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Aktualisieren der Größe eines virtuellen Computers |


## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](../overview.md).
* Weitere Informationen zu PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/get-started-azureps).
