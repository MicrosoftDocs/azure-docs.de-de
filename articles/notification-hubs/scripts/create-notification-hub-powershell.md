---
title: 'PowerShell-Skript: Erstellen einer Azure Notification Hub-Instanz | Microsoft-Dokumentation'
description: Dieses PowerShell-Skript erstellt eine Azure Notification Hub-Instanz.
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 93c5118962fed1bfda4d32afba942a6600664e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66172154"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Erstellen einer Azure Notification Hub-Instanz mithilfe von PowerShell

Dieses PowerShell-Beispielskript erstellt eine Azure Notification Hub-Beispielinstanz. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement:** Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können Sie den folgenden Befehl ausführen, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Erstellt einen Namespace für die Notification Hub-Instanz. |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Erstellt eine Notification Hub-Instanz. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).
