---
title: Konfigurieren einer benutzerdefinierten Antwort für Web Application Firewall in Azure Front Door
description: Erfahren Sie, wie Sie einen benutzerdefinierten Antwortcode und eine Meldung für den Fall konfigurieren, dass eine Anforderung durch Web Application Firewall (WAF) blockiert wird.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: tyao;kumud
ms.openlocfilehash: d6d73055abe972cd3b6fee253b6bdb2b082ceca8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242984"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurieren einer benutzerdefinierten Antwort für Azure Web Application Firewall

Wenn Azure Web Application Firewall (WAF) mit Azure Front Door eine Anforderung aufgrund einer übereinstimmenden Regel blockiert, wird standardmäßig ein 403-Statuscode mit der Meldung **Die Anforderung wird blockiert** zurückgegeben. In diesem Artikel wird beschrieben, wie Sie einen benutzerdefinierten Antwortstatuscode und eine Antwortmeldung für den Fall konfigurieren, dass eine Anforderung durch WAF blockiert wird.

## <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Herstellen einer Verbindung mit Azure über einen interaktiven Anmeldedialog
```
Connect-AzAccount
Install-Module -Name Az
```
Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist. Führen Sie den folgenden Befehl aus, und öffnen Sie PowerShell erneut.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installieren des Moduls „Az.FrontDoor“ 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. In diesem Beispiel erstellen Sie mithilfe von [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) eine Ressourcengruppe.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Erstellen einer neuen WAF-Richtlinie mit benutzerdefinierter Antwort 

Im folgenden Beispiel wird eine neue WAF-Richtlinie erstellt, in der der Statuscode auf 405 und die Meldung auf **You are blocked** (Sie wurden blockiert) festgelegt wird. mit [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Mithilfe von [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy) können Sie den benutzerdefinierten Antwortcode oder die Einstellungen des Antworttext einer vorhandenen WAF-Richtlinie ändern.

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Front Door](front-door-overview.md)