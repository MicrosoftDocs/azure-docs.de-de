---
title: Erstellen und Verwenden von benutzerdefinierten v2-Regeln
titleSuffix: Azure Web Application Firewall
description: Dieser Artikel enthält Informationen dazu, wie Sie benutzerdefinierte Regeln für die Web Application Firewall (WAF) v2 in Azure Application Gateway erstellen.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: b14bd60ab744be5a1735abc073f32f8ebc3e1ab1
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301698"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>Erstellen und Verwenden von benutzerdefinierten Regeln für Web Application Firewall v2 auf Application Gateway

Web Application Firewall (WAF) v2 auf Azure Application Gateway bietet Schutz für Webanwendungen. Dieser Schutz wird über die Kernregeln (Core Rule Set, CRS) des Open Web Application Security-Projekts (OWASP) bereitgestellt. In einigen Fällen müssen Sie unter Umständen eigene benutzerdefinierte Regeln erstellen, um Ihre jeweiligen Anforderungen zu erfüllen. Weitere Informationen zu benutzerdefinierten WAF-Regeln finden Sie unter [Custom rules for Web Application Firewall v2](custom-waf-rules-overview.md) (Benutzerdefinierte Regeln für Web Application Firewall v2).

Dieser Artikel enthält einige Beispiele für benutzerdefinierte Regeln, die Sie erstellen und mit Ihrer WAF v2 nutzen können. Informationen zur Bereitstellung einer WAF mit einer benutzerdefinierten Regel mithilfe von Azure PowerShell finden Sie unter [Configure Web Application Firewall v2 with a custom rule using Azure PowerShell](configure-waf-custom-rules.md) (Konfigurieren der Web Application Firewall v2 mit einer benutzerdefinierten Regel per Azure PowerShell).

Die in diesem Artikel gezeigten JSON-Codeausschnitte wurden von einer [ApplicationGatewayWebApplicationFirewallPolicies](/azure/templates/microsoft.network/applicationgatewaywebapplicationfirewallpolicies)-Ressource abgeleitet.

>[!NOTE]
> Wenn Ihr Anwendungsgateway nicht auf die WAF-Ebene zurückgreift, wird die Option zum Aktualisieren des Anwendungsgateways auf die WAF-Ebene im rechten Bereich angezeigt.

![Aktivieren der WAF][fig1]

## <a name="example-1"></a>Beispiel 1

Sie wissen, dass es einen Bot mit dem Namen *evilbot* gibt, den Sie daran hindern möchten, Ihre Website zu „crawlen“. In diesem Fall sperren Sie den Benutzer-Agent *evilbot* in den Anforderungsheadern.

Logik: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Hier ist der entsprechende JSON-Code angegeben:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Informationen zum Anzeigen einer WAF, die mit dieser benutzerdefinierten Regel bereitgestellt wurde, finden Sie unter [Configure Web Application Firewall v2 with a custom rule using Azure PowerShell](configure-waf-custom-rules.md) (Konfigurieren von Web Application Firewall v2 mit einer benutzerdefinierten Regel per Azure PowerShell).

### <a name="example-1a"></a>Beispiel 1a

Dasselbe erreichen Sie auch mit einem regulären Ausdruck:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Entsprechender JSON-Code:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>Beispiel 2

Sie möchten den Datenverkehr aus den USA mit dem GeoMatch-Operator zulassen:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow
```

Entsprechender JSON-Code:

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Allow",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "GeoMatch",
            "matchValues": [
              "US"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>Beispiel 3

Sie möchten alle Anforderungen von IP-Adressen im Bereich 198.168.5.0/24 blockieren.

In diesem Beispiel blockieren Sie den gesamten Datenverkehr, der aus einem IP-Adressbereich stammt. Der Name der Regel lautet *myrule1*, und die Priorität ist auf 10 festgelegt.

Logik: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Hier ist der entsprechende JSON-Code angegeben:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

Zugehörige CRS-Regel: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>Beispiel 4

In diesem Beispiel möchten Sie den Benutzer-Agent *evilbot* und den Datenverkehr im Bereich 192.168.5.0/24 blockieren. Hierzu können Sie zwei separate Übereinstimmungsbedingungen erstellen und in dieselbe Regel einfügen. So wird sichergestellt, dass die Anforderung blockiert wird, wenn *evilbot* im Header des Benutzers-Agents **und** IP-Adressen aus dem Bereich 192.168.5.0/24 abgeglichen werden.

Logik: p **and** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Hier ist der entsprechende JSON-Code angegeben:

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>Beispiel 5

In diesem Beispiel möchten Sie Datenverkehr blockieren, wenn sich die Anforderung entweder außerhalb des IP-Adressbereichs *192.168.5.0/24* befindet oder die Zeichenfolge des Benutzer-Agents nicht *chrome* lautet (der Benutzer also nicht den Chrome-Browser verwendet). Da bei dieser Logik **or** verwendet wird, befinden sich die beiden Bedingungen in separaten Regeln. Dies ist im folgenden Beispiel dargestellt. Sowohl für *myrule1* als auch für *myrule2* muss sich eine Übereinstimmung ergeben, damit der Datenverkehr blockiert wird.

Logik: **not** (p **and** q) = **not** p **or not** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

Entsprechender JSON-Code:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>Beispiel 6

Sie möchten eine benutzerdefinierte SQLI-Einheit blockieren. Da für die Logik hier **or** verwendet wird und alle Werte in *RequestUri* enthalten sind, können alle *MatchValues* in einer durch Kommas getrennten Liste angeordnet sein.

Logik: p **or** q **or** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Entsprechender JSON-Code:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Alternativer Azure PowerShell-Code:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Entsprechender JSON-Code:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre benutzerdefinierten Regeln erstellt haben, können Sie sich darüber informieren, wie Sie Ihre WAF-Protokolle anzeigen. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/create-custom-waf-rules/1.png
