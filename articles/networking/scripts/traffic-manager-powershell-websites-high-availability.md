---
title: 'Azure PowerShell-Skriptbeispiel: Weiterleiten von Datenverkehr für Hochverfügbarkeit von Anwendungen | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Weiterleiten von Datenverkehr für Hochverfügbarkeit von Anwendungen'
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: georgewallace
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 1086fe6d656db9450d84fd6971a271775f54687d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156925"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Weiterleiten von Datenverkehr für Hochverfügbarkeit von Anwendungen

Dieses Skript erstellt eine Ressourcengruppe, zwei App Service-Pläne, zwei Web-Apps, ein Traffic Manager-Profil und zwei Traffic Manager-Endpunkte. Traffic Manager leitet Datenverkehr zur Anwendung an eine Region weiter, die als primäre Region gilt, und an die sekundäre Region, wenn die Anwendung in der primären Region nicht verfügbar ist. Vor dem Ausführen des Skripts müssen Sie die Werte von MyWebApp, MyWebAppL1 und MyWebAppL2 in Werte ändern, die innerhalb von Azure eindeutig sind. Nach dem Ausführen des Skripts können Sie in der primären Region mit der URL „mywebapp.trafficmanager.net“ auf die App zugreifen.

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine Web-App, ein Traffic Manager-Profil und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Erstellt einen App Service-Plan. Dies ist wie eine Serverfarm für die Azure-Web-App. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Erstellt eine Azure-Web-App im App Service-Plan. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Erstellt eine Azure-Web-App im App Service-Plan. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Erstellt ein Azure Traffic Manager-Profil. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Fügt einem Azure Traffic Manager-Profil einen Endpunkt hinzu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview).

Zusätzliche Netzwerk-PowerShell-Skriptbeispiele finden Sie unter [Azure-Dokumentation für Netzwerke – Übersicht](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
