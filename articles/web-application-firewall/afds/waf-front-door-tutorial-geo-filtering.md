---
title: Konfigurieren einer Web Application Firewall-Richtlinie zur Geofilterung für Azure Front Door Service
description: In diesem Tutorial erfahren Sie, wie Sie eine Richtlinie für die Geofilterung erstellen und Ihrem vorhandenen Front Door-Front-End-Host zuordnen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 479b1d8ed1f4238486bb78e33a6139463578dbba
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563306"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Einrichten einer WAF-Richtlinie für Geofilterung für Ihre Front Door-Instanz

In diesem Tutorial wird veranschaulicht, wie Sie mit Azure PowerShell eine Beispielrichtlinie für die Geofilterung erstellen und sie Ihrem vorhandenen Front Door-Front-End-Host zuordnen. Diese Beispielrichtlinie für die Geofilterung blockiert Anforderungen aus allen Ländern/Regionen, mit Ausnahme der USA.

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Einrichtung einer Geofilterungsrichtlinie beginnen, müssen Sie zunächst Ihre PowerShell-Umgebung einrichten und ein Front Door-Profil erstellen.
### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](/powershell/azure/) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Herstellen einer Verbindung mit Azure über ein interaktives Anmeldedialogfeld

```
Install-Module -Name Az
Connect-AzAccount
```
Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist. Führen Sie den folgenden Befehl aus, und öffnen Sie PowerShell erneut.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installieren des Moduls „Az.FrontDoor“ 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Erstellen eines Front Door-Profils

Gehen Sie zum Erstellen eines Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) vor.

## <a name="define-geo-filtering-match-condition"></a>Definieren der Übereinstimmungsbedingung für die Geofilterung

Erstellen Sie eine exemplarische Übereinstimmungsbedingung, durch die Anforderungen ausgewählt werden, die nicht aus den USA stammen. Verwenden Sie dazu [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) in den Parametern, wenn Sie eine Übereinstimmungsbedingung erstellen. Zweistellige Länder-/Regionscodes zur Zuordnung von Ländern/Regionen werden in [Was ist Geofilterung in einer Domäne für Azure Front Door?](waf-front-door-geo-filtering.md) bereitgestellt.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Hinzufügen der Übereinstimmungsbedingung für die Geofilterung zu einer Regel mit Aktion und Priorität

Erstellen Sie anschließend unter Verwendung von [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) das CustomRule-Objekt `nonUSBlockRule` basierend auf der Übereinstimmungsbedingung, einer Aktion und einer Priorität.  Ein CustomRule-Objekt kann über mehrere MatchCondition-Elemente verfügen.  In diesem Beispiel ist „Action“ auf „Block“ und „Priority“ auf „1“ (höchste Priorität) festgelegt.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Hinzufügen von Regeln zu einer Richtlinie

Suchen Sie mithilfe von `Get-AzResourceGroup` nach dem Namen der Ressourcengruppe, die das Front Door-Profil enthält. Erstellen Sie als Nächstes mithilfe von [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) ein Richtlinienobjekt vom Typ `geoPolicy` mit `nonUSBlockRule` in der angegebenen Ressourcengruppe, die das Front Door-Profil enthält. Geben Sie einen eindeutigen Namen für die Georichtlinie an. 

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß den Anweisungen im Artikel [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) erstellt haben. Ersetzen Sie im folgenden Beispiel den Richtliniennamen *geoPolicyAllowUSOnly* durch einen eindeutigen Richtliniennamen.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Verknüpfen der WAF-Richtlinie mit einem Front Door-Front-End-Host

Verknüpfen Sie das WAF-Richtlinienobjekt mit dem vorhandenen Front Door-Front-End-Host, und aktualisieren Sie die Front Door-Eigenschaften. 

Rufen Sie hierzu zunächst mithilfe von [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) Ihr Front Door-Objekt ab. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Legen Sie als Nächstes mithilfe von [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) die Eigenschaft „WebApplicationFirewallPolicyLink“ des Front-Ends auf die Ressourcen-ID von `geoPolicy`fest.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Die Eigenschaft „WebApplicationFirewallPolicyLink“ muss nur einmal festgelegt werden, um eine WAF-Richtlinie mit einem Front Door-Front-End-Host zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf den Front-End-Host angewendet.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Azure Web Application Firewall finden Sie [hier](../overview.md).
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](../../frontdoor/quickstart-create-front-door.md).