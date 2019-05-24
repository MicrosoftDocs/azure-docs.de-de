---
title: 'Azure PowerShell-Skriptbeispiel: Abonnieren einer Ressourcengruppe und Filtern nach Ressource | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Abonnieren einer Ressourcengruppe und Filtern nach Ressource'
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 069762659560d1eb60ecf28267ecfa0e80a7f6e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117117"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Abonnieren von Ereignissen für eine Ressourcengruppe und Filtern nach einer Ressource mit PowerShell

Dieses Skript erstellt ein Event Grid-Abonnement für die Ereignisse für eine Ressourcengruppe. Es verwendet einen Filter, um nur Ereignisse für eine angegebene Ressource in der Ressourcengruppe abzurufen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Beispielskript: stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Beispielskript: Vorschaumodul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Für das Vorschaubeispielskript ist das Event Grid-Modul erforderlich. Führen Sie zum Installieren `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` aus.

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet den folgenden Befehl, um das Ereignisabonnement zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Erstellen Sie ein Event Grid-Abonnement. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](../overview.md).
* Weitere Informationen zu PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/get-started-azureps).
