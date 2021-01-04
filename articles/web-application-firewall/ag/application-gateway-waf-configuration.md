---
title: 'Web Application Firewall: Anforderungsgrößenlimits und Ausschlusslisten in Azure Application Gateway – Azure-Portal'
description: Dieser Artikel enthält Informationen zur Konfiguration von Anforderungsgrößenlimits und Ausschlusslisten für die Web Application Firewall (WAF) in Application Gateway mit dem Azure-Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040259"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web Application Firewall-Anforderungsgrößenlimits und Ausschlusslisten

Die Web Application Firewall (WAF) von Azure Application Gateway bietet Schutz für Webanwendungen. Dieser Artikel beschreibt die Konfiguration von WAF-Anforderungsgrößenlimits und -Ausschlusslisten. Diese Einstellungen befinden sich in der WAF-Richtlinie, die Ihrer Application Gateway-Instanz zugeordnet ist. Weitere Informationen zu WAF-Richtlinien finden Sie unter [Azure Web Application Firewall auf Azure Application Gateway](ag-overview.md) und [Erstellen von Web Application Firewall-Richtlinien für Application Gateway](create-waf-policy-ag.md).

## <a name="waf-exclusion-lists"></a>WAF-Ausschlusslisten

![Anforderungsgrößenlimits](../media/application-gateway-waf-configuration/waf-policy.png)

Mit WAF-Ausschlusslisten können Sie bestimmte Anforderungsattribute in einer WAF-Auswertung weglassen. Ein gängiges Beispiel sind von Active Directory eingefügte Token, die für Authentifizierungs- oder Kennwortfelder verwendet werden. Diese Attribute enthalten häufig Sonderzeichen, die ein falsch positives Ergebnis von den WAF-Regeln auslösen können. Ein zur WAF-Ausschlussliste hinzugefügtes Attribut wird von konfigurierten und aktiven WAF-Regeln nicht berücksichtigt. Ausschlusslisten gelten global.

Die folgenden Attribute können Ausschlusslisten nach dem Namen hinzugefügt werden. Die Werte des ausgewählten Felds werden nicht anhand von WAF-Regeln ausgewertet, ihre Namen hingegen schon (siehe Beispiel 1 unten, in dem der Wert des User-Agent-Headers von der WAF-Auswertung ausgeschlossen wird). Die Ausschlusslisten entfernen die Überprüfung des Feldwerts.

* Anforderungsheader
* Anforderungscookies
* Ein Anforderungsattributname (Argument) kann als Ausschlusselement hinzugefügt werden. Beispiele hierfür sind:

   * Name des Formularfelds
   * JSON-Entität
   * URL-Abfragezeichenfolgenargument

Sie können eine exakte Übereinstimmung für Anforderungsheader, Test, Cookies oder Abfragezeichenfolgenattribute angeben.  Oder Sie können optional teilweise Übereinstimmungen angeben. Ausschlussregeln sind global gültig und gelten für alle Seiten und Regeln.

Für Übereinstimmungskriterien werden die folgenden Operatoren unterstützt:

- **Equals** (gleich):  Dieser Operator wird für exakte Übereinstimmungen verwendet. Verwenden Sie beispielsweise zum Auswählen eines Headers namens **bearerToken** den Operator „equals“ mit dem Selektor **bearerToken**.
- **Starts with** (Beginnt mit): Dieser Operator gleicht alle Felder ab, die mit dem angegebenen Selektorwert beginnen.
- **Ends with** (Endet mit):  Dieser Operator gleicht alle Anforderungsfelder ab, die auf den angegebenen Selektorwert enden.
- **Contains** (Enthält): Dieser Operator gleicht alle Anforderungsfelder ab, die den angegebenen Selektorwert enthalten.
- **Gleich beliebige:** Dieser Operator entspricht allen angeforderten Feldern. * wird zum Selektorwert.

In allen Fällen muss beim Abgleich die Groß-/Kleinschreibung beachtet werden, und reguläre Ausdrücke sind nicht als Selektor zulässig.

> [!NOTE]
> Weitere Informationen und Hilfe zum Troubleshooting finden Sie unter [Problembehandlung für die Web Application Firewall (WAF) für Azure Application Gateway](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Beispiele

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die folgenden Beispiele veranschaulichen die Verwendung von Ausschlüssen.

#### <a name="example-1"></a>Beispiel 1

In diesem Beispiel möchten Sie den Header „User-Agent“ (Benutzer-Agent) ausschließen. Der Anforderungsheader „User-Agent“ (Benutzer-Agent) umfasst eine charakteristische Zeichenfolge, die es den Netzwerkprotokollpeers ermöglicht, den Anwendungstyp, das Betriebssystem, den Softwarehersteller oder die Softwareversion des anfordernden Softwarebenutzer-Agent zu ermitteln. Weitere Informationen finden Sie unter [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) (Benutzer-Agent).

Es gibt alle möglichen Gründe, die Auswertung dieses Headers zu deaktivieren. Die WAF könnte eine Zeichenfolge als schädlich einstufen. Beispielsweise den klassischen SQL-Angriff „x=x“ in einer Zeichenfolge. In einigen Fällen kann dies berechtigter Datenverkehr sein. Sie müssen diesen Header daher möglicherweise von der WAF-Auswertung ausschließen.

Das folgende Azure PowerShell-Cmdlet schließt den Header „User-Agent“ (Benutzer-Agent) von der Auswertung aus:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Beispiel 2

Dieses Beispiel schließt den Wert im Parameter *user* aus, der in der Anforderung über die URL übergeben wird. Nehmen wir beispielsweise Folgendes an: In Ihrer Umgebung enthält das Benutzerfeld üblicherweise eine Zeichenfolge, die von der WAF als schädlich eingestuft und daher von dieser blockiert wird.  Sie können in diesem Fall den Benutzerparameter ausschließen, sodass die WAF in dem Feld keine Auswertung vornimmt.

Das folgende Azure PowerShell-Cmdlet schließt den Benutzerparameter von der Auswertung aus:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Wenn also die URL `http://www.contoso.com/?user%281%29=fdafdasfda` an die WAF übergeben wird, wird die Zeichenfolge **fdafdasfda** nicht ausgewertet, der Parametername **user%281%29** dagegen schon. 

## <a name="waf-request-size-limits"></a>WAF-Anforderungsgrößenlimits



Die Web Application Firewall ermöglicht Ihnen das Konfigurieren von Anforderungsgrößenlimits mit Ober- und Untergrenzen. Die folgenden zwei Konfigurationen für Größenlimits sind verfügbar:

- Der Wert im Feld für die maximale Größe des Anforderungstexts wird in Kilobyte angegeben und bestimmt das Anforderungsgrößenlimit insgesamt mit Ausnahme von Dateiuploads. In diesem Feld können Werte zwischen 1 KB und 128 KB (jeweils einschließlich) angegeben werden. Der Standardwert für die Größe des Anforderungstexts beträgt 128 KB.
- Der Wert im Feld für das Dateiuploadlimit wird in MB angegeben und bestimmt die maximal zulässige Größe für Dateiuploads. In diesem Feld können Werte von mindestens 1 MB bis zu den folgenden Höchstwerten angegeben werden:

   - 100 MB für v1 Mittlere WAF-Gateways
   - 500 MB für v1 Große WAF-Gateways
   - 750 MB für v2 WAF-Gateways 

 Der Standardwert für die Größe von Dateiuploads beträgt 100 MB.

Die WAF bietet außerdem einen konfigurierbaren Knopf zum Aktivieren bzw. Deaktivieren der Anforderungstextüberprüfung. Standardmäßig ist die Überprüfung des Anforderungstexts aktiviert. Ist die Überprüfung des Anforderungstexts deaktiviert, wertet die WAF die Inhalte des HTTP-Nachrichtentexts nicht aus. In diesem Fall erzwingt die WAF weiterhin WAF-Regeln für Header, Cookies und den URI. Ist die Überprüfung des Anforderungstexts deaktiviert, ist das Feld für die maximale Größe des Anforderungstexts nicht verfügbar, und es kann kein Wert festgelegt werden. Wenn Sie die Überprüfung des Anforderungstexts deaktivieren, können Nachrichten, die größer als 128 KB sind, an die WAF gesendet werden, aber der Nachrichtentext wird nicht auf Sicherheitsrisiken überprüft.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren Ihrer WAF-Einstellungen können Sie sich darüber informieren, wie Sie WAF-Protokolle anzeigen. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
