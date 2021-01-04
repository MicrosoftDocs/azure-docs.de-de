---
title: Konfigurieren von benutzerdefinierten Antworten für Web Application Firewall (WAF) mit Azure Front Door
description: Erfahren Sie, wie Sie einen benutzerdefinierten Antwortcode und eine Meldung für den Fall konfigurieren, dass eine Anforderung durch WAF blockiert wird.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563680"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Konfigurieren einer benutzerdefinierten Antwort für Azure Web Application Firewall (WAF)

Wenn WAF aufgrund einer übereinstimmenden Regel eine Anforderung blockiert, wird standardmäßig ein 403-Statuscode mit der Meldung **Die Anforderung wurde blockiert** zurückgegeben. Die Standardmeldung enthält auch die Nachverfolgungs-Verweiszeichenfolge, mit der eine Verknüpfung mit [Protokolleinträgen](./waf-front-door-monitor.md) für die Anforderung abgerufen werden kann.  Sie können einen benutzerdefinierten Antwortstatuscode und eine benutzerdefinierte Meldung mit einer Verweiszeichenfolge für Ihren Anwendungsfall konfigurieren. In diesem Artikel wird beschrieben, wie Sie eine benutzerdefinierte Antwortseite für den Fall konfigurieren, dass eine Anforderung durch WAF blockiert wird.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Konfigurieren des benutzerdefinierten Antwortstatuscodes und der Nachricht über das Portal

Sie können einen benutzerdefinierten Antwortstatuscode und -text unter „Richtlinieneinstellungen“ im WAF-Portal konfigurieren.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF-Richtlinieneinstellungen":::

Im obigen Beispiel haben wir den Antwortcode 403 beibehalten und eine kurze Meldung mit dem Inhalt „Bitte kontaktieren Sie uns“ konfiguriert, wie in der nachstehenden Abbildung dargestellt:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Beispiel für eine benutzerdefinierte Antwort":::

„{{azure-ref}}“ fügt die eindeutige Verweiszeichenfolge in den Antworttext ein. Der Wert stimmt mit dem Feld TrackingReference in den Protokollen `FrontdoorAccessLog` und `FrontdoorWebApplicationFirewallLog` überein.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Konfigurieren des benutzerdefinierten Antwortstatuscodes und der Nachricht über PowerShell

### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung

Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](/powershell/azure/) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

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

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Hier erstellen wir eine Ressourcengruppe mithilfe von [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Erstellen einer neuen WAF-Richtlinie mit benutzerdefinierter Antwort 

Im nachstehenden Beispiel wird mit [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) eine neue WAF-Richtlinie erstellt, in der der benutzerdefinierte Statuscode auf 405 und die Meldung auf **You are blocked** (Sie wurden blockiert) festgelegt wird.

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
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Web Application Firewall mit Azure Front Door](../afds/afds-overview.md).