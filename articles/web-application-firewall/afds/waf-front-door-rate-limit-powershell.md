---
title: Konfigurieren einer WAF-Regel zur Ratenbegrenzung für Front Door – Azure PowerShell
description: Erfahren Sie, wie Sie eine Ratenbegrenzungsregel für einen vorhandenen Front Door-Endpunkt konfigurieren.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 23b893bad591af5f1e923b68e8d30453f859792b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563476"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurieren einer Web Application Firewall-Regel zur Ratenbegrenzung mit Azure PowerShell
Die Azure-WAF-Ratenbegrenzungsregel (Web Application Firewall) für Azure Front Door steuert die Anzahl der Anforderungen, die von Clients in einem Zeitraum von einer Minute zulässig sind.
Dieser Artikel zeigt das Konfigurieren einer WAF-Ratenbegrenzungsregel mithilfe von Azure PowerShell, die steuert, wie viele Anforderungen von Clients an eine Webanwendung, die */promo* in der URL enthält, zulässig sind.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!NOTE]
> Ratenbegrenzungen werden auf jede Client-IP-Adresse angewendet. Wenn mehrere Clients von unterschiedlichen IP-Adressen aus auf Ihre Front Door zugreifen, werden auf diese jeweils eigene Ratenbegrenzungen angewendet.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit der Einrichtung einer Richtlinie für ein Ratenlimit beginnen, müssen Sie zunächst Ihre PowerShell-Umgebung einrichten und ein Front Door-Profil erstellen.
### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](/powershell/azure/) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Herstellen einer Verbindung mit Azure über ein interaktives Anmeldedialogfeld
```
Connect-AzAccount

```
Stellen Sie vor dem Installieren des Front Door-Moduls sicher, dass die aktuelle Version von PowerShellGet installiert ist. Führen Sie den folgenden Befehl aus, und öffnen Sie PowerShell erneut.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installieren des Moduls „Az.FrontDoor“ 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Erstellen eines Front Door-Profils
Gehen Sie zum Erstellen eines Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) vor.

## <a name="define-url-match-conditions"></a>Definieren von URL-Übereinstimmungsbedingungen
Definieren Sie eine URL-Übereinstimmungsbedingung (URL enthält „/promo“) mit [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Das folgende Beispiel überprüft Übereinstimmungen mit */promo* im Wert der Variable *RequestUri*:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Erstellen einer benutzerdefinierten Ratenbegrenzungsregel
Legen Sie eine Ratenbegrenzung mit [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) fest. Im folgenden Beispiel wird die Begrenzung auf 1.000 festgelegt. Anforderungen über 1.000 von einem Client an die Aktionsseite („/promo“) in einem Zeitraum von einer Minute werden blockiert, bis die nächste Minute beginnt.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurieren einer Sicherheitsrichtlinie

Suchen Sie mithilfe von `Get-AzureRmResourceGroup` nach dem Namen der Ressourcengruppe, die das Front Door-Profil enthält. Konfigurieren Sie als Nächstes mithilfe von [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) eine Sicherheitsrichtlinie mit einer Ratenbegrenzungsregel in der angegebenen Ressourcengruppe, die das Front Door-Profil enthält.

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß den Anweisungen im Artikel [Schnellstart: Erstellen Sie eine „Front Door“](../../frontdoor/quickstart-create-front-door.md) mithilfe von [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) erstellt haben.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Verknüpfen der Richtlinie mit einem Front Door-Front-End-Host
Verknüpfen Sie das Sicherheitsrichtlinienobjekt mit einem vorhandenen Front Door-Front-End-Host, und aktualisieren Sie die Front Door-Eigenschaften. Rufen Sie zunächst mithilfe des Befehls [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) das Front Door-Objekt ab.
Legen Sie dann mit dem Befehl [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) die Eigenschaft *WebApplicationFirewallPolicyLink* des Front-Ends auf die *Ressourcen-ID* des im vorherigen Schritt erstellten „$ratePolicy“-Objekts fest. 

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß den Anweisungen im Artikel [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) erstellt haben. Ersetzen Sie im folgenden Beispiel „$frontDoorName“ durch den Namen Ihres Front Door-Profils. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Die Eigenschaft *WebApplicationFirewallPolicyLink* muss nur einmal festgelegt werden, um eine Sicherheitsrichtlinie mit einem Front Door-Front-End-Host zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Front Door](../../frontdoor/front-door-overview.md)