---
title: Azure PowerShell-Skriptbeispiel – Erstellen eines Log Analytics-Arbeitsbereichs | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen eines Log Analytics-Arbeitsbereichs
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129171"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Erstellen eines Log Analytics-Arbeitsbereichs mit PowerShell

Mithilfe dieses Skripts verfügen Sie rasch über einen einsatzbereiten Azure Log Analytics-Arbeitsbereich, den Sie benötigen, wenn Sie damit beginnen möchten, Daten zu erfassen, zu analysieren und zu bearbeiten.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript wird mithilfe der folgenden Befehle ein neuer Log Analytics-Arbeitsbereich in Ihrem Abonnement erstellt. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Ruft Informationen zu einem vorhandenen Arbeitsbereich ab. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Erstellt einen Arbeitsbereich in der angegebenen Ressourcengruppe und am angegebenen Ort. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

