---
title: Benutzerdefinierte Regeln für die Azure Web Application Firewall (WAF)
description: Dieser Artikel enthält eine Übersicht über benutzerdefinierte WAF-Regeln (Web Application Firewall) in Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 62259749e04d66d78206a0bba77ce88f2c08c82f
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688923"
---
# <a name="custom-rules-for-web-application-firewall"></a>Benutzerdefinierte Regeln für die Web Application Firewall

> [!IMPORTANT]
> Benutzerdefinierte WAF-Regeln in Azure Application Gateway befinden sich derzeit in der Public Preview-Phase. **Benutzerdefinierte Regeln stehen nur für die SKU „WAF_v2“ zur Verfügung.**
> Diese öffentliche Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Unter Umständen werden bestimmte Features nicht unterstützt, verfügen über eingeschränkte Funktionen und sind nicht an allen Azure-Standorten verfügbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die Web Application Firewall (WAF) in Azure Application Gateway enthält einen vorkonfigurierten, von der Plattform verwalteten Regelsatz, der Schutz vor verschiedensten Angriffsarten bietet. Hierzu zählen unter anderem websiteübergreifende Skripts und die Einschleusung von SQL-Befehlen. WAF-Administratoren können die Regeln des Kernregelsatzes (Core Rule Set, CRS) bei Bedarf durch selbst geschriebene Regeln erweitern. Die Regeln können angeforderten Datenverkehr auf der Grundlage von Abgleichskriterien blockieren oder zulassen.

Benutzerdefinierte Regeln ermöglichen die Erstellung eigener Regeln, die für jede Anforderung ausgewertet werden, die die WAF durchläuft. Diese Regeln haben eine höhere Priorität als die restlichen Regeln in den verwalteten Regelsätzen. Die benutzerdefinierten Regeln enthalten einen Regelnamen, eine Regelpriorität und ein Array von Abgleichsbedingungen. Sind diese Bedingungen erfüllt, erfolgt eine Aktion (Zulassen oder Blockieren).

So können beispielsweise alle Anforderungen von einer IP-Adresse im Bereich 192.168.5.4/24 blockiert werden. In dieser Regel ist der Operator *IPMatch*, „matchValues“ ist der IP-Adressbereich (192.168.5.4/24), und die Aktion ist die Blockierung des Datenverkehrs. Darüber hinaus werden Name und Priorität der Regel festgelegt.

Benutzerdefinierte Regeln unterstützen die Verwendung von Verknüpfungslogik zur Erstellung komplexerer Regeln für Ihre individuellen Sicherheitsanforderungen. Beispiel: (Bedingung 1 **und** Bedingung 2) **oder** Bedingung 3).  In diesem Beispiel soll die in der benutzerdefinierten Regel angegebene Aktion ausgeführt werden, wenn die Bedingung 1 **und** die Bedingung 2 erfüllt sind **oder** wenn die Bedingung 3 erfüllt ist.

Verschiedene Abgleichsbedingungen innerhalb der gleichen Regel sind immer durch **und** verknüpft. Beispiel: Blockieren des Datenverkehrs einer bestimmten IP-Adresse – und zwar nur bei Verwendung eines bestimmten Browsers.

Wenn Sie zwei Bedingungen mit **oder** verknüpfen möchten, müssen sich diese in unterschiedlichen Regeln befinden. Beispiel: Blockieren des Datenverkehrs einer bestimmten IP-Adresse oder Blockieren von Datenverkehr bei Verwendung eines bestimmten Browsers.

> [!NOTE]
> Es können maximal 100 benutzerdefinierte WAF-Regeln verwendet werden. Weitere Informationen zu Application Gateway-Grenzwerten finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-subscription-service-limits.md#application-gateway-limits).

In benutzerdefinierten Regeln werden genau wie in den Kernregelsätzen auch reguläre Ausdrücke unterstützt. Entsprechende Beispiele finden Sie unter [Create and use Web Application Firewall custom rules](create-custom-waf-rules.md) (Erstellen und Verwenden benutzerdefinierter Web Application Firewall-Regeln) in den Beispielen 3 und 5.

## <a name="allowing-vs-blocking"></a>Zulassen/Blockieren

Mit benutzerdefinierten Regeln lässt sich Datenverkehr ganz einfach zulassen oder blockieren. So können Sie beispielsweise sämtlichen Datenverkehr aus einem IP-Adressbereich blockieren. Mithilfe einer weiteren Regel können Sie Datenverkehr zulassen, wenn die Anforderung von einem bestimmten Browser stammt.

Wenn Sie etwas zulassen möchten, muss der Parameter `-Action` auf **Allow** festgelegt sein. Wenn Sie etwas blockieren möchten, muss der Parameter `-Action` auf **Block** festgelegt sein.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

`$BlockRule` im obigen Code ist der folgenden benutzerdefinierten Regel in Azure Resource Manager zugeordnet:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Diese benutzerdefinierte Regel enthält einen Namen, eine Priorität, eine Aktion und das Array von Abgleichsbedingungen, die erfüllt sein müssen, damit die Aktion ausgeführt wird. Die einzelnen Felder werden in den folgenden Feldbeschreibungen ausführlicher erläutert. Beispiele für benutzerdefinierte Regeln finden Sie unter [Create and use Web Application Firewall custom rules](create-custom-waf-rules.md) (Erstellen und Verwenden benutzerdefinierter Web Application Firewall-Regeln).

## <a name="fields-for-custom-rules"></a>Felder für benutzerdefinierte Regeln

### <a name="name-optional"></a>Name (optional)

Der Name der Regel. Dieser Name wird in den Protokollen angezeigt.

### <a name="priority-required"></a>Priorität (erforderlich)

- Bestimmt die Auswertungsreihenfolge der Regeln. Je niedriger der Wert, desto früher wird die Regel ausgewertet.
Muss für alle benutzerdefinierten Regeln eindeutig sein. Eine Regel mit der Priorität 100 wird vor einer Regel mit der Priorität 200 ausgewertet.

### <a name="rule-type-required"></a>Regeltyp (erforderlich)

Muss derzeit **MatchRule** lauten.

### <a name="match-variable-required"></a>Abgleichsvariable (erforderlich)

Muss eine der folgenden Variablen sein:

- RemoteAddr: IP-Adresse/Hostname der Remotecomputerverbindung
- RequestMethod: HTTP-Anforderungsmethode (GET, POST, PUT, DELETE usw.)
- QueryString: Variable im URI
- PostArgs: Im POST-Text gesendete Argumente
- RequestUri: URI der Anforderung
- RequestHeaders: Header der Anforderung
- RequestBody: Text der Anforderung
- RequestCookies: Cookies der Anforderung

### <a name="selector-optional"></a>Selektor (optional)

Beschreibt das Feld der matchVariable-Auflistung. Lautet die Abgleichsvariable (matchVariable) beispielsweise „RequestHeaders“, kann der Selektor etwa auf den Header *User-Agent* festgelegt werden.

### <a name="operator-required"></a>Operator (erforderlich)

Muss einer der folgenden Operatoren sein:

- IPMatch: Wird nur verwendet, wenn die Abgleichsvariable *RemoteAddr* lautet.
- Equals: Die Eingabe entspricht dem Abgleichswert (MatchValue).
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- RegEx

### <a name="negate-condition-optional"></a>Negationsbedingung (optional)

Negiert die aktuelle Bedingung.

### <a name="transform-optional"></a>Transformation (optional)

Eine Zeichenfolgenliste mit Namen von Transformationen, die vor dem Abgleich ausgeführt werden sollen. Mögliche Transformationen:

- Lowercase
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Abgleichswerte (erforderlich)

Liste mit Werten für den Abgleich (verknüpft durch *oder*). Dabei kann es sich beispielsweise um IP-Adressen oder um andere Zeichenfolgen handeln. Das Wertformat hängt vom vorherigen Operator ab.

### <a name="action-required"></a>Aktion (erforderlich)

- Allow: Autorisiert die Transaktion. Alle nachfolgenden Regeln werden übersprungen. Die angegebene Anforderung wird also der Zulassungsliste hinzugefügt. Nach erfolgreichem Abgleich findet keine weitere Auswertung mehr statt, und die Anforderung wird an den Back-End-Pool gesendet. Regeln in der Zulassungsliste werden für keine weiteren benutzerdefinierten oder verwalteten Regeln ausgewertet.
- Block: Blockiert die Transaktion auf der Grundlage von *SecDefaultAction* (Erkennungs-/Schutzmodus). Nachdem die Anforderung ausgewertet und der Blockierungsliste hinzugefügt wurde, findet keine weitere Auswertung mehr statt, und die Anforderung wird blockiert. Nachfolgende Anforderungen, die die gleichen Bedingungen erfüllen, werden nicht ausgewertet und einfach blockiert. 
- Log: Ermöglicht der Regel das Schreiben in das Protokoll sowie die Auswertung der restlichen Regeln. Nachfolgende benutzerdefinierte Regeln werden gemäß ihrer Priorität ausgewertet – gefolgt von den verwalteten Regeln.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit benutzerdefinierten Regeln vertraut gemacht haben, können Sie [Ihre eigenen benutzerdefinierten Regeln erstellen](create-custom-waf-rules.md).
