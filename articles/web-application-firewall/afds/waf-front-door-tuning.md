---
title: Optimieren von Web Application Firewall (WAF) für Azure Front Door
description: In diesem Artikel erfahren Sie, wie Sie WAF für Front Door optimieren.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 4c710792dd7966fad76b33954fdf7c2253cf18f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488237"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Optimieren von Web Application Firewall (WAF) für Azure Front Door
 
Der von Azure verwaltete Standardregelsatz basiert auf dem [OWASP Core Rule Set (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) (OWASP-Kernregelsatz) und ist für den sofortigen Einsatz konzipiert. Häufig wird davon ausgegangen, dass WAF-Regeln entsprechend den spezifischen Anforderungen der Anwendung oder Organisation, die WAF verwendet, angepasst werden müssen. Dies lässt sich im Allgemeinen durch Definieren von Regelausschlüssen, Erstellen von benutzerdefinierten Regeln und sogar durch Deaktivieren von Regeln erreichen, die möglicherweise zu Problemen oder falsch positiven Ergebnissen führen. Ihnen stehen einige Möglichkeiten zur Verfügung, wenn Anforderungen, die Ihre Web Application Firewall (WAF) durchlaufen sollten, blockiert werden.

Lesen Sie zunächst die Dokumente [Übersicht über WAF für Front Door](afds-overview.md) und [WAF-Richtlinie für Front Door](waf-front-door-create-portal.md). Stellen Sie außerdem sicher, dass Sie die [Überwachung und Protokollierung von WAF](waf-front-door-monitor.md) aktiviert haben. In diesen Artikeln wird die Funktionsweise von WAF und WAF-Regelsätzen sowie der Zugriff auf WAF-Protokolle erläutert.
 
## <a name="understanding-waf-logs"></a>Grundlegendes zu WAF-Protokollen
 
Der Zweck von WAF-Protokollen ist es, jede Anforderung anzuzeigen, die von der WAF abgeglichen oder blockiert wird. Es handelt sich um eine Sammlung aller ausgewerteten Anforderungen, die abgeglichen oder blockiert werden. Wenn Sie feststellen, dass die WAF eine Anforderung blockiert, bei der das nicht geschehen sollte (ein falsch positives Ergebnis), stehen Ihnen einige Möglichkeiten zur Verfügung. Nehmen Sie zunächst eine Eingrenzung vor, und suchen Sie nach der betreffenden Anforderung. Bei Bedarf können Sie [eine benutzerdefinierte Antwortnachricht so konfigurieren](./waf-front-door-configure-custom-response-code.md), dass sie das Feld `trackingReference` enthält, um das Ereignis einfach zu identifizieren und eine Protokollabfrage für den spezifischen Wert auszuführen. Durchsuchen Sie die Protokolle, um den jeweiligen URI, den Zeitstempel oder die Client-IP-Adresse der Anforderung zu ermitteln. Wenn Sie die zugehörigen Protokolleinträge gefunden haben, können Sie mit Maßnahmen für die False Positives beginnen. 
 
Angenommen, Sie haben einen berechtigten Datenverkehr, der die Zeichenfolge `1=1` enthält und die WAF durchlaufen soll. Die Anforderung sieht wie folgt aus:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Beim Ausführen der Anforderung blockiert die WAF Datenverkehr, der die Zeichenfolge *1=1* in einem Parameter oder Feld enthält. Diese Zeichenfolge steht häufig mit einem Angriff durch Einschleusung von SQL-Befehlen in Verbindung. Sie können die Protokolle durchsuchen und den Zeitstempel der Anforderung sowie die Regeln, die eine Blockierung/Übereinstimmung ergaben, ermitteln.
 
Im folgenden Beispiel sehen Sie sich ein `FrontdoorWebApplicationFirewallLog`-Protokoll an, das aufgrund einer Regelübereinstimmung generiert wurde.
 
Im Feld „requestUri“ können Sie sehen, dass die Anforderung spezifisch für `/api/Feedbacks/` durchgeführt wurde. Weiter unten sehen Sie im Feld „ruleName“ die Regel-ID `942110`. Wenn Sie die Regel-ID kennen, können Sie im [offiziellen Repository des OWASP ModSecurity-Kernregelsatzes](https://github.com/coreruleset/coreruleset) nach dieser [Regel-ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) suchen, um den zugehörigen Code zu überprüfen und genau zu verstehen, worauf diese Regel zutrifft. 
 
Beim Überprüfen des Felds `action` sehen Sie, dass diese Regel so festgelegt ist, dass Anforderungen beim Abgleich blockiert werden. Und tatsächlich wurde die Anforderung von WAF blockiert, da `policyMode` auf `prevention` festgelegt ist. 
 
Sehen Sie sich nun die Informationen im Feld `details` an. Hier werden die Informationen zu `matchVariableName` und `matchVariableValue` angezeigt. Sie können dadurch erkennen, dass diese Regel ausgelöst wurde, da *1=1* im Feld `comment` der Web-App eingegeben wurde.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Es ist auch sinnvoll, die Zugriffsprotokolle zu überprüfen, um Ihr Wissen über ein bestimmtes WAF-Ereignis zu erweitern. Im Folgenden überprüfen Sie das Protokoll `FrontdoorAccessLog`, das als Reaktion auf das obige Ereignis generiert wurde.
 
Dabei handelt es sich um verwandte Protokolle, da bei ihnen der Wert `trackingReference` identisch ist. Unter den verschiedenen Feldern, die einen allgemeinen Einblick geben, z. B. `userAgent` und `clientIP`, soll auf die Felder `httpStatusCode` und `httpStatusDetails` aufmerksam gemacht werden. Hier kann festgestellt werden, dass der Client eine HTTP 403-Antwort erhalten hat, wodurch sich bestätigt, dass diese Anforderung verweigert und blockiert wurde. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Beheben von False Positives
 
Um eine fundierte Entscheidung zum Umgang mit einem falsch positiven Ergebnis zu treffen, müssen Sie sich mit den von Ihrer Anwendung verwendeten Technologien vertraut machen. Angenommen, es ist kein SQL-Server in Ihrem Technologiestapel vorhanden, und Sie erhalten falsch positive Ergebnisse im Zusammenhang mit diesen Regeln. Durch Deaktivieren dieser Regeln wird nicht unbedingt die Sicherheit geschwächt. 

Mit diesen Informationen und dem Wissen, dass Regel 942110 eine Übereinstimmung mit der Zeichenfolge `1=1` ergab, gibt es einige Möglichkeiten, um zu verhindern, dass diese legitime Anforderung blockiert wird:
 
* Verwenden von Ausschlusslisten
  * Weitere Informationen zu Ausschlusslisten finden Sie unter [Web Application Firewall (WAF) mit Ausschlusslisten des Front Door-Diensts](waf-front-door-exclusion.md). 
* Ändern von WAF-Aktionen
  * Weitere Informationen zu den Aktionen, die ausgeführt werden können, wenn eine Anforderung mit den Bedingungen einer Regel übereinstimmt, finden Sie unter [WAF-Aktionen](afds-overview.md#waf-actions).
* Verwenden benutzerdefinierter WAF-Regeln
  * Weitere Informationen zu benutzerdefinierten Regeln finden Sie unter [Benutzerdefinierte Regeln für Web Application Firewall mit Azure Front Door](waf-front-door-custom-rules.md).
* Deaktivieren von Regeln 

> [!TIP]
> Wenn Sie einen Ansatz wählen, um legitime Anforderungen über WAF zuzulassen, versuchen Sie, diesen so eng wie möglich zu fassen. Beispielsweise ist es besser, eine Ausschlussliste zu verwenden, als eine Regel vollständig zu deaktivieren.

### <a name="using-exclusion-lists"></a>Verwenden von Ausschlusslisten

Ein Vorteil der Verwendung einer Ausschlussliste besteht darin, dass nur die Übereinstimmungsvariable, die ausgeschlossen werden soll, nicht mehr für die jeweilige Anforderung überprüft wird. Das heißt, Sie können zwischen spezifischen Anforderungsheadern, Anforderungscookies, Argumenten für Abfragezeichenfolgen oder POST-Argumenten für Anforderungstext wählen, die ausgeschlossen werden sollen, wenn eine bestimmte Bedingung erfüllt ist, anstatt die gesamte Anforderung von der Überprüfung auszuschließen. Die anderen nicht angegebenen Variablen der Anforderung werden weiterhin normal überprüft.
 
Beachten Sie, dass es sich bei den Ausschlüssen um eine globale Einstellung handelt. Das bedeutet, dass der konfigurierte Ausschluss für den gesamten Datenverkehr gilt, der WAF durchläuft, nicht nur für eine bestimmte Web-App oder einen URI. Dies kann beispielsweise ein Problem sein, wenn *1=1* eine gültige Anforderung im Text für eine bestimmte Web-App ist, nicht aber für andere Web-Apps mit der gleichen WAF-Richtlinie. Wenn es sinnvoll ist, unterschiedliche Ausschlusslisten für verschiedene Anwendungen zu verwenden, sollten Sie verschiedene WAF-Richtlinien für die einzelnen Anwendungen verwenden und auf das Front-End der jeweiligen Anwendung anwenden.
 
Wenn Sie Ausschlusslisten für verwaltete Regeln konfigurieren, können Sie auswählen, ob alle Regeln innerhalb eines Regelsatzes, alle Regeln innerhalb einer Regelgruppe oder einzelne Regeln ausgeschlossen werden sollen. Eine Ausschlussliste kann mit [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), der [Azure-Befehlszeilenschnittstelle](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), der [REST-API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate) oder im Azure-Portal konfiguriert werden.

* Ausschlüsse auf Regelebene
  * Die Anwendung von Ausschlüssen auf Regelebene bedeutet, dass die angegebenen Ausschlüsse nur für die jeweilige einzelne Regel nicht analysiert werden, jedoch durch alle anderen Regeln im Regelsatz weiterhin analysiert werden. Dies ist die präziseste Ebene für Ausschlüsse. Damit können Sie den verwalteten Regelsatz basierend auf den Informationen optimieren, die Sie bei der Problembehandlung eines Ereignisses in den WAF-Protokollen finden.
* Ausschlüsse auf Regelgruppenebene
  * Die Anwendung von Ausschlüssen auf Regelgruppenebene bedeutet, dass die angegebenen Ausschlüsse für die entsprechende Gruppe von Regeltypen nicht analysiert werden. Die Auswahl von *SQLI* als ausgeschlossene Regelgruppe gibt beispielsweise an, dass die definierten Anforderungsausschlüsse zwar durch keine der SQLI-spezifischen Regeln, aber durch Regeln in anderen Gruppen überprüft werden, z. B. *PHP*, *RFI* oder *XSS*. Diese Art des Ausschlusses kann nützlich sein, wenn sichergestellt ist, dass die Anwendung nicht anfällig für bestimmte Arten von Angriffen ist. Beispielsweise können bei einer Anwendung, die keine SQL-Datenbanken enthält, alle *SQLI*-Regeln ausgeschlossen werden, ohne dass dies die zugehörige Sicherheitsstufe beeinträchtigt.
* Ausschlüsse auf Regelsatzebene 
  * Die Anwendung von Ausschlüssen auf Regelsatzebene bedeutet, dass die angegebenen Ausschlüsse für keine der Sicherheitsregeln analysiert werden, die im entsprechenden Regelsatz verfügbar sind. Dabei handelt es sich um einen umfassenden Ausschluss, der mit Vorsicht verwendet werden sollte.

Im Beispiel wird ein Ausschluss auf der präzisesten Ebene ausgeführt (Anwenden des Ausschlusses auf eine einzelne Regel). Dabei soll die Übereinstimmungsvariable **Request body post args name** ausgeschlossen werden, die `comment` enthält. Dies ist offensichtlich, da Sie die Details der Übereinstimmungsvariablen im Firewallprotokoll sehen können: `"matchVariableName": "PostParamValue:comment"`. Das Attribut ist `comment`. Einige andere Möglichkeiten zum Auffinden dieses Attributnamens finden Sie unter [Suchen von Anforderungsattributnamen](#finding-request-attribute-names).

![Ausschlussregeln](../media/waf-front-door-tuning/exclusion-rules.png)

![Regelausschluss für eine bestimmte Regel](../media/waf-front-door-tuning/exclusion-rule.png)

Gelegentlich gibt es Fälle, in denen bestimmte Parameter auf eine möglicherweise nicht intuitive Weise an die WAF übergeben werden. Es gibt beispielsweise ein Token, das übergeben wird, wenn die Authentifizierung mithilfe von Azure Active Directory erfolgt. Dieses Token (`__RequestVerificationToken`) wird normalerweise als Anforderungscookie übergeben. In einigen Fällen, in denen Cookies deaktiviert sind, wird dieses Token jedoch auch als POST-Argument der Anforderung übergeben. Aus diesem Grund müssen Sie sicherstellen, dass `__RequestVerificationToken` für `RequestCookieNames` und für `RequestBodyPostArgsNames` in der Ausschlussliste hinzugefügt wird, um False Positives für Azure AD-Token zu beheben.

Ausschlüsse für einen Feldnamen (*Selektor*) bedeuten, dass der Wert nicht mehr von WAF ausgewertet wird. Der Feldname selbst wird jedoch weiterhin ausgewertet, und in seltenen Fällen kann er mit einer WAF-Regel übereinstimmen und eine Aktion auslösen.

![Regelausschluss für einen Regelsatz](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Ändern von WAF-Aktionen

Eine andere Möglichkeit, das Verhalten von WAF-Regeln zu bearbeiten, besteht darin, die Aktion auszuwählen, die ausgeführt wird, wenn eine Anforderung den Bedingungen einer Regel entspricht. Folgende Aktionen sind verfügbar: [Allow, Block, Log und Redirect](afds-overview.md#waf-actions) (Zulassen, Blockieren, Protokollieren und Umleiten).

In diesem Beispiel wurde die Standardaktion *Block* (Blockieren) für die Regel 942110 in die Aktion *Log* (Protokollieren) geändert. Dadurch wird die Anforderung von WAF protokolliert und die Auswertung der gleichen Anforderung für die verbleibenden Regeln mit niedrigerer Priorität fortgesetzt.

![WAF-Aktionen](../media/waf-front-door-tuning/actions.png)

Nach dem Ausführen der gleichen Anforderung können Sie auf die Protokolle zugreifen und sehen, dass diese Anforderung mit der Regel-ID 942110 übereinstimmt und dass im Feld `action_s` nun *Log* (Protokollieren) anstelle von *Block* (Blockieren) angegeben ist. Anschließend haben Sie die Protokollabfrage erweitert, um die `trackingReference_s`-Informationen einzufügen und festzustellen, was mit der Anforderung sonst noch passiert ist.

![Protokoll mit mehreren Regelübereinstimmungen](../media/waf-front-door-tuning/actions-log.png)

Interessanterweise tritt Millisekunden nach der Verarbeitung der Regel-ID 942110 eine andere SQLI-Regelübereinstimmung auf. Dieselbe Anforderung entsprach der Regel-ID 942310, und dieses Mal wurde die Standardaktion *Block* (Blockieren) ausgelöst.

Ein weiterer Vorteil der Verwendung der Aktion *Log* (Protokollieren) während der WAF-Optimierung oder Problembehandlung besteht darin, dass Sie ermitteln können, ob mehrere Regeln in einer spezifischen Regelgruppe übereinstimmen und eine bestimmte Anforderung blockieren. Sie können dann die Ausschlüsse auf der entsprechenden Ebene erstellen, d. h. auf der Regel- oder Regelgruppenebene. 

### <a name="using-custom-rules"></a>Verwenden benutzerdefinierter Regeln

Nachdem Sie ermittelt haben, wodurch eine WAF-Regelübereinstimmung verursacht wird, können Sie mithilfe von benutzerdefinierten Regeln die WAF-Reaktion auf das Ereignis anpassen. Benutzerdefinierte Regeln werden vor verwalteten Regeln verarbeitet, sie können mehrere Bedingungen enthalten, und die zugehörigen Aktionen können [Allow, Deny, Log oder Redirect](afds-overview.md#waf-actions) (Zulassen, Verweigern, Protokollieren oder Umleiten) sein. Bei einer Regelübereinstimmung wird die Verarbeitung der WAF-Engine beendet. Das bedeutet, dass andere benutzerdefinierte Regeln mit niedrigerer Priorität und verwaltete Regeln nicht mehr ausgeführt werden.

Im folgenden Beispiel wurde eine benutzerdefinierte Regel mit zwei Bedingungen erstellt. Mit der ersten Bedingung wird der Wert `comment` im Anforderungstext gesucht. Mit der zweiten Bedingung wird der Wert `/api/Feedbacks/` im Anforderungs-URI gesucht.

Durch Verwendung einer benutzerdefinierten Regel können Sie bei der Optimierung Ihrer WAF-Regeln und beim Umgang mit False Positives am präzisesten vorgehen. In diesem Fall werden keine Aktionen ausgeführt, die nur auf dem Wert `comment` des Anforderungstexts basieren, der in mehreren Sites oder Apps mit derselben WAF-Richtlinie vorhanden sein kann. Durch Einfügen einer weiteren Bedingung für die Übereinstimmung mit einem bestimmten Anforderungs-URI (`/api/Feedbacks/`) wird sichergestellt, dass diese benutzerdefinierte Regel auch wirklich für den überprüften expliziten Anwendungsfall gilt. Dadurch wird sichergestellt, dass derselbe Angriff, wenn er unter verschiedenen Bedingungen ausgeführt wird, dennoch von der WAF-Engine überprüft und verhindert wird.

![Log](../media/waf-front-door-tuning/custom-rule.png)

Beim Überprüfen des Protokolls können Sie sehen, dass das Feld `ruleName_s` den Namen der erstellten benutzerdefinierten Regel enthält: `redirectcomment`. Im Feld `action_s` sehen Sie, dass für dieses Ereignis die Aktion *Redirect* (Umleiten) ausgeführt wurde. Im Feld `details_matches_s` sehen Sie, dass die Details für beide Bedingungen übereinstimmen.

### <a name="disabling-rules"></a>Deaktivieren von Regeln

Eine weitere Möglichkeit zum Umgehen eines falsch positiven Ergebnisses ist das Deaktivieren der Regel, die eine Übereinstimmung mit der Eingabe ergab, die von der WAF als schädlich angesehen wird. Da Sie die WAF-Protokolle analysiert und die Regel auf 942110 eingeschränkt haben, können Sie sie im Azure-Portal deaktivieren. Weitere Informationen finden Sie unter [Anpassen von Web Application Firewall-Regeln mit dem Azure-Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Das Deaktivieren einer Regel ist von Vorteil, wenn Sie sicher sind, dass alle Anforderungen, die eine bestimmte Bedingung erfüllen, tatsächlich legitime Anforderungen sind, oder wenn Sie sicher sind, dass die Regel ganz einfach nicht für Ihre Umgebung gilt (z. B. das Deaktivieren einer SQL-Einschleusungsregel, da Sie keine SQL-Back-Ends verwenden). 
 
Die Deaktivierung einer Regel ist jedoch eine globale Einstellung, die für alle Front-End-Hosts gilt, die der WAF-Richtlinie zugeordnet sind. Wenn Sie eine Regel deaktivieren, werden Sicherheitsrisiken möglicherweise ohne Schutz oder Erkennung für andere Front-End-Hosts offengelegt, die der WAF-Richtlinie zugeordnet sind.
 
Wenn Sie eine verwaltete Regel mithilfe von Azure PowerShell deaktivieren möchten, finden Sie weitere Informationen in der Dokumentation zum [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0)-Objekt. Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, finden Sie weitere Informationen in der Dokumentation zu [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest).

![WAF-Regeln](../media/waf-front-door-tuning/waf-rules.png)

## <a name="finding-request-fields"></a>Suchen von Anforderungsfeldern

Mithilfe eines Browserproxys wie [Fiddler](https://www.telerik.com/fiddler) können Sie einzelne Anforderungen überprüfen und bestimmen, welche spezifischen Felder einer Webseite aufgerufen werden. Dies ist nützlich, um bestimmte Felder mithilfe von Ausschlusslisten in WAF von der Überprüfung auszuschließen.

### <a name="finding-request-attribute-names"></a>Suchen von Anforderungsattributnamen
 
In diesem Beispiel können Sie sehen, dass das Feld, in dem die Zeichenfolge `1=1` eingegeben wurde, den Namen `comment` hat. Diese Daten wurden im Text einer POST-Anforderung übergeben.

![Fiddler-Anforderung mit Text](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Dies ist ein Feld, das Sie ausschließen können. Weitere Informationen zu Ausschlusslisten finden Sie unter [Web Application Firewall-Ausschlusslisten](./waf-front-door-exclusion.md). Sie können die Auswertung in diesem Fall ausschließen, indem Sie den folgenden Ausschluss konfigurieren:

![Ausschlussregel](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Sie können auch die Firewallprotokolle überprüfen, um Informationen dazu zu erhalten, was Sie der Ausschlussliste hinzufügen müssen. Informationen zum Aktivieren der Protokollierung finden Sie unter [Überwachen von Metriken und Protokollen in Azure Front Door](./waf-front-door-monitor.md).

Überprüfen Sie das Firewallprotokoll, und sehen Sie sich die Datei `PT1H.json` für die Stunde an, in der die zu untersuchende Anforderung aufgetreten ist. `PT1H.json`-Dateien sind in den Speicherkontocontainern verfügbar, in denen die `FrontDoorWebApplicationFirewallLog`- und `FrontDoorAccessLog`-Diagnoseprotokolle gespeichert sind.

In diesem Beispiel können Sie die Regel sehen, die die Anforderung blockiert hat (mit dem gleichen Transaktionsverweis) und genau zur gleichen Zeit aufgetreten ist:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Mit Ihrem Wissen über die Funktionsweise der von Azure verwalteten Regelsätze (siehe [Web Application Firewall für Azure Front Door](afds-overview.md)) wissen Sie, dass die Regel mit der Eigenschaft *action: Block* basierend auf den Daten, die im Anforderungstext übereinstimmen, blockiert. Wie Sie in den Details sehen können, stimmt er mit einem Muster (`1=1`) überein, und das Feld hat den Namen `comment`. Führen Sie die gleichen Schritte wie zuvor aus, um „Request body post args name“ mit `comment` auszuschließen.

### <a name="finding-request-header-names"></a>Suchen von Anforderungsheadernamen

Fiddler erweist sich auch beim Suchen nach Anforderungsheadernamen als ein nützliches Tool. Im folgenden Screenshot sehen Sie die Header für diese GET-Anforderung, darunter Content-Type, User-Agent usw. Sie können auch mithilfe von Anforderungsheadern Ausschlüsse und benutzerdefinierte Regeln in WAF erstellen.

![Fiddler-Anforderung mit Header](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Eine weitere Möglichkeit zum Anzeigen von Anforderungs- und Antwortheadern bieten die Entwicklertools Ihres Browsers, z. B. Edge oder Chrome. Drücken Sie F12, oder klicken Sie mit der rechten Maustaste, und wählen Sie **Überprüfen** -> **Entwicklertools** und anschließend die Registerkarte **Netzwerk** aus. Laden Sie eine Webseite, und klicken Sie auf die Anforderung, die Sie untersuchen möchten.

![Anforderung zum Untersuchen des Netzwerks](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Suchen nach Anforderungscookienamen

Wenn die Anforderung Cookies enthält, können Sie die Registerkarte Cookies auswählen, um sie in Fiddler anzuzeigen. Auch Cookieinformationen können zum Erstellen von Ausschlüssen oder benutzerdefinierten Regeln in WAF verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Azure Web Application Firewall finden Sie [hier](../overview.md).
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](../../frontdoor/quickstart-create-front-door.md).