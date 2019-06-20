---
title: Anpassen von Web Application Firewall-Regeln in Azure Application Gateway – Azure CLI
description: Dieser Artikel enthält Informationen zum Anpassen von Web Application Firewall-Regeln (WAF) in Application Gateway mit der Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: 5e364c597b8c524e95297f279003462f2d16abe1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832896"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Anpassen von Web Application Firewall-Regeln mit der Azure CLI

Die Web Application Firewall (WAF) von Azure Application Gateway bietet Schutz für Webanwendungen. Diese Schutzmaßnahmen werden durch die Kernregeln (Core Rule Set, CRS) des Open Web Application Security-Projekts (OWASP) bereitgestellt. Einige Regeln können falsche positive Ergebnisse ausgeben und den realen Datenverkehr blockieren. Aus diesem Grund bietet Application Gateway die Möglichkeit, Regelgruppen und Regeln anzupassen. Weitere Informationen zu den jeweiligen Regelgruppen und Regeln finden Sie in der [Liste der CRS-Regelgruppen und -Regeln der Web Application Firewall](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Anzeigen von Regelgruppen und Regeln

Der folgende Code veranschaulicht, wie konfigurierbare Regeln und Regelgruppen angezeigt werden.

### <a name="view-rule-groups"></a>Anzeigen von Regelgruppen

Das folgende Beispiel zeigt, wie Regelgruppen angezeigt werden:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Die folgende Ausgabe ist eine abgeschnittene Antwort aus dem vorherigen Beispiel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Anzeigen von Regeln in einer Regelgruppe

Das folgende Beispiel zeigt, wie Regeln in einer angegebenen Regelgruppe angezeigt werden:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Die folgende Ausgabe ist eine abgeschnittene Antwort aus dem vorherigen Beispiel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Deaktivieren von Regeln

Das folgende Beispiel deaktiviert die Regeln `910018` und `910017` für ein Anwendungsgateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
