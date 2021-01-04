---
title: Konfigurieren von benutzerdefinierten WAF-Regeln und eines Standardregelsatzes für Azure Front Door
description: Erfahren Sie, wie Sie eine WAF-Richtlinie mit benutzerdefinierten und verwalteten Regeln für einen vorhandenen Front Door-Endpunkt konfigurieren.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563408"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurieren einer Web Application Firewall-Richtlinie mit Azure PowerShell

Die Azure Web Application Firewall-Richtlinie (WAF) definiert die Überprüfungen, die erforderlich sind, wenn eine Anforderung in Front Door eingeht.
In diesem Artikel wird erläutert, wie Sie eine WAF-Richtlinie konfigurieren, die einige benutzerdefinierte Regeln umfasst und in der ein per Azure verwalteter Standardregelsatz aktiviert ist.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Einrichtung einer Richtlinie für ein Ratenlimit beginnen, müssen Sie zunächst Ihre PowerShell-Umgebung einrichten und ein Front Door-Profil erstellen.

### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung

Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](/powershell/azure/) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

#### <a name="sign-in-to-azure"></a>Anmelden bei Azure

```
Connect-AzAccount

```
Stellen Sie vor dem Installieren des Front Door-Moduls sicher, dass Sie die aktuelle Version von PowerShellGet installiert haben. Führen Sie den folgenden Befehl aus, und öffnen Sie PowerShell erneut.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installieren des Moduls „Az.FrontDoor“ 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Erstellen eines Front Door-Profils

Gehen Sie zum Erstellen eines Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) vor.

## <a name="custom-rule-based-on-http-parameters"></a>Benutzerdefinierte Regel basierend auf HTTP-Parametern

Im folgenden Beispiel wird veranschaulicht, wie eine benutzerdefinierte Regel mit zwei Übereinstimmungsbedingungen mithilfe von [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) konfiguriert wird. Die Anforderungen stammen von einer angegebenen Website, die vom Verweiser definierten wird. Die Abfragezeichenfolge enthält nicht „password“. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Benutzerdefinierte Regel basierend auf HTTP-Anforderungsmethode

Erstellen Sie mithilfe von [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) wie folgt eine Regel, mit der die „PUT“-Methode blockiert wird:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Erstellen einer benutzerdefinierten Regel basierend auf Größenbeschränkung

Im folgenden Beispiel wird mithilfe von Azure PowerShell eine Regel erstellt, die Anforderungen mit einer URL mit mehr als 100 Zeichen blockiert:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Hinzufügen eines verwalteten Standardregelsatzes

Im folgenden Beispiel wird mithilfe von Azure PowerShell ein verwalteter Standardregelsatz erstellt:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurieren einer Sicherheitsrichtlinie

Suchen Sie mithilfe von `Get-AzResourceGroup` nach dem Namen der Ressourcengruppe, die das Front Door-Profil enthält. Konfigurieren Sie als Nächstes mithilfe von [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) eine Sicherheitsrichtlinie mit den in den vorherigen Schritten erstellten Regeln in der angegebenen Ressourcengruppe, die das Front Door-Profil enthält.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Verknüpfen der Richtlinie mit einem Front Door-Front-End-Host

Verknüpfen Sie das Sicherheitsrichtlinienobjekt mit einem vorhandenen Front Door-Front-End-Host, und aktualisieren Sie die Front Door-Eigenschaften. Rufen Sie zunächst mithilfe von [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) das Front Door-Objekt ab.
Legen Sie dann mithilfe von [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) die Eigenschaft *WebApplicationFirewallPolicyLink* des Front-Ends auf die *Ressourcen-ID* des im vorherigen Schritt erstellten „$myWAFPolicy$“-Objekts fest. 

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß den Anweisungen im Artikel [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md) erstellt haben. Ersetzen Sie im folgenden Beispiel „$frontDoorName“ durch den Namen Ihres Front Door-Profils. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Die Eigenschaft *WebApplicationFirewallPolicyLink* muss nur einmal festgelegt werden, um eine Sicherheitsrichtlinie mit einem Front Door-Front-End-Host zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Front Door](../../frontdoor/front-door-overview.md) 
- Weitere Informationen zu [WAF mit Front Door](afds-overview.md)