---
title: Azure PowerShell-Skriptbeispiel – Abonnieren eines Blobspeicherkontos | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Abonnieren eines Blobspeicherkontos
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: d236bcbf71915576ee31587e9abfff92c99250c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117207"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Abonnieren von Ereignissen für ein Blobspeicherkonto mit PowerShell

Dieses Skript erstellt ein Event Grid-Abonnement für die Ereignisse für ein Blobspeicherkonto.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Beispielskript: stabil

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet den folgenden Befehl, um das Ereignisabonnement zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Erstellen Sie ein Event Grid-Abonnement. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](../overview.md).
* Weitere Informationen zu PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/get-started-azureps).
