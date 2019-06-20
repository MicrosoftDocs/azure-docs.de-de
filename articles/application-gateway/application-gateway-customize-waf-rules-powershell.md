---
title: Anpassen von Web Application Firewall-Regeln in Azure Application Gateway – PowerShell
description: Dieser Artikel enthält Informationen zum Anpassen von Web Application Firewall-Regeln (WAF) in Application Gateway mit PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831451"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Anpassen von Web Application Firewall-Regeln über PowerShell

Die Web Application Firewall (WAF) von Azure Application Gateway bietet Schutz für Webanwendungen. Diese Schutzmaßnahmen werden durch die Kernregeln (Core Rule Set, CRS) des Open Web Application Security-Projekts (OWASP) bereitgestellt. Einige Regeln können falsche positive Ergebnisse ausgeben und den realen Datenverkehr blockieren. Aus diesem Grund bietet Application Gateway die Möglichkeit, Regelgruppen und Regeln anzupassen. Weitere Informationen zu den jeweiligen Regelgruppen und Regeln finden Sie in der [Liste der CRS-Regelgruppen und -Regeln der Web Application Firewall](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Anzeigen von Regelgruppen und Regeln

Der folgende Code veranschaulicht, wie Regeln und Regelgruppen angezeigt werden, die in einem WAF-fähigen Anwendungsgateway konfigurierbar sind.

### <a name="view-rule-groups"></a>Anzeigen von Regelgruppen

Das folgende Beispiel zeigt, wie Regelgruppen angezeigt werden:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Die folgende Ausgabe ist eine abgeschnittene Antwort aus dem vorherigen Beispiel:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Deaktivieren von Regeln

Das folgende Beispiel deaktiviert die Regeln `911011` und `911012` für ein Anwendungsgateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Obligatorische Regeln

Die folgende Liste enthält die Bedingungen, die dazu führen, dass WAF die Anforderung im Präventionsmodus blockiert (im Erkennungsmodus werden sie als Ausnahmen protokolliert). Diese können nicht konfiguriert oder deaktiviert werden:

* Fehler beim Analysieren des Anforderungstexts führen dazu, dass die Anforderung blockiert wird, sofern die Textüberprüfung nicht deaktiviert ist (XML, JSON, Formulardaten).
* Die Datenlänge des Anforderungstexts (ohne Dateien) überschreitet das konfigurierte Limit.
* Der Anforderungstext (einschließlich Dateien) überschreitet den Grenzwert.
* Ein interner Fehler ist in der WAF-Engine aufgetreten.

CRS 3.x-spezifisch:

* Anomaliebewertung für Eingang überschritt den Schwellenwert

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren Ihrer deaktivierten Regeln können Sie sich darüber informieren, wie Sie WAF-Protokolle anzeigen. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
