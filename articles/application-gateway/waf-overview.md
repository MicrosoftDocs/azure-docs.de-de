---
title: Einführung in die Web Application Firewall für Azure Application Gateway
description: Dieser Artikel enthält eine Übersicht über die Web Application Firewall (WAF) für Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991400"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Web Application Firewall für Azure Application Gateway

Azure Application Gateway bietet eine Web Application Firewall (WAF), die zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Einschleusung von SQL-Befehlen und websiteübergreifendes Skripting gehören zu den häufigsten Angriffen.

Es ist schwierig, solche Angriffe im Anwendungscode zu verhindern. Hierzu können rigorose Wartungs-, Patch- und Überwachungsmaßnahmen auf mehreren Ebenen der Anwendungstopologie erforderlich sein. Eine zentrale Web Application Firewall vereinfacht die Sicherheitsverwaltung erheblich. Eine WAF bietet auch Anwendungsadministratoren eine bessere Gewähr des Schutzes vor Bedrohungen und Angriffen.

Mit einer WAF-Lösung können Sie schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways lassen sich problemlos in Anwendungsgateways konvertieren, für die eine Firewall aktiviert ist.

Die Application Gateway-WAF basiert auf dem [Core Rule Set (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) Version 3.0 oder 2.2.9 von Open Web Application Security Project (OWASP). Die WAF wird automatisch aktualisiert und bietet Schutz vor neuen Sicherheitsrisiken, ohne dass zusätzliche Konfiguration erforderlich ist.

![Application Gateway-WAF-Diagramm](./media/waf-overview/WAF1.png)

Application Gateway wird als Application Delivery Controller (ADC, Controller zur Anwendungsbereitstellung) betrieben. Die Anwendung bietet SSL-Beendigung (Secure Sockets Layer), cookiebasierte Sitzungsaffinität, Lastverteilung per Roundrobin, inhaltsbasiertes Routing, die Möglichkeit zum Hosten mehrerer Websites sowie Sicherheitsverbesserungen.

Zu den Sicherheitsverbesserungen von Application Gateway gehören die SSL-Gruppenrichtlinienverwaltung sowie umfassende End-to-End-Unterstützung. Die Anwendungssicherheit wird von der WAF-Integration in Application Gateway gestärkt. Die Kombination schützt Ihre Webanwendungen vor gängigen Sicherheitslücken. Außerdem bietet sie einen einfach konfigurierbaren zentralen Verwaltungsort.

## <a name="benefits"></a>Vorteile

Dieser Abschnitt beschreibt die wichtigsten Vorteile, die Application Gateway und seine WAF bieten.

### <a name="protection"></a>Schutz

* Schützen Sie Ihre Webanwendungen vor Sicherheitsrisiken und Angriffen im Web, ohne den Back-End-Code zu verändern.

* Schützen Sie mehrere Webanwendungen gleichzeitig. Eine Instanz von Application Gateway kann bis zu 100 Websites hosten, die von einer Web Application Firewall geschützt sind.

### <a name="monitoring"></a>Überwachung

* Überwachen Sie Angriffe auf Ihre Webanwendungen mithilfe eines WAF-Echtzeitprotokolls. Dieses Protokoll ist in [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) integriert und dient zum Nachverfolgen von WAF-Warnungen sowie zum mühelosen Überwachen von Trends.

* Die Application Gateway-WAF ist in Azure Security Center integriert. Security Center bietet eine zentrale Ansicht der Sicherheitsstatus sämtlicher Azure-Ressourcen.

### <a name="customization"></a>Anpassung

* Sie können WAF-Regeln und -Regelgruppen Ihren individuellen Anwendungsanforderungen anpassen, um falsch positive Ergebnisse zu vermeiden.

## <a name="features"></a>Features

- Schutz vor der Einschleusung von SQL-Befehlen.
- Schutz vor websiteübergreifendem Skripting.
- Schutz vor anderen allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion.
- Schutz vor Verletzungen des HTTP-Protokolls.
- Schutz vor HTTP-Protokollanomalien, z.B. fehlenden user-agent- und accept-Headern des Hosts.
- Schutz vor Bots, Crawlern und Scannern.
- Erkennung häufiger Fehler bei der Anwendungskonfiguration (z.B. Apache und IIS).
- Konfigurierbare Einschränkungen der Anforderungsgröße mit Unter- und Obergrenzen.
- Mit Ausschlusslisten können Sie bestimmte Anforderungsattribute in einer WAF-Auswertung weglassen. Ein gängiges Beispiel sind von Active Directory eingefügte Token, die für Authentifizierungs- oder Kennwortfelder verwendet werden.

### <a name="core-rule-sets"></a>Kernregelsätze

Application Gateway unterstützt zwei Regelsätze: CRS 3.0 und CRS 2.2.9. Diese Regeln schützen Ihre Webanwendungen vor schädlichen Aktivitäten.

Die Application Gateway-WAF wird standardmäßig mit CRS 3.0 vorkonfiguriert. Aber Sie können stattdessen auch CRS 2.2.9 verwenden. Verglichen mit 2.2.9 liefert CRS 3.0 weniger falsch positive Ergebnisse. Sie können auch [Regeln Ihren Anforderungen entsprechend anpassen](application-gateway-customize-waf-rules-portal.md).

Die WAF schützt vor folgenden Websicherheitslücken:

- Angriffe mit Einschleusung von SQL-Befehlen
- Angriffe durch websiteübergreifendes Skripting
- Andere allgemeine Angriffe wie z.B. Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion
- Verletzungen des HTTP-Protokolls
- HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts
- Bots, Crawler und Scanner
- Häufige Fehler bei der Anwendungskonfiguration (z.B. Apache und IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 umfasst 13 Regelgruppen, wie in der folgenden Tabelle gezeigt. Jede dieser Gruppen enthält mehrere Regeln, die deaktivierbar sind.

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Sperren von Methoden (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Schutz vor Port- und Umgebungsscannern|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Schutz vor Protokoll- und Codierungsproblemen|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Schutz vor Header Injection, Request Smuggling und Response Splitting|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Schutz vor Datei- und Pfadangriffen|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Schutz vor RFI-Angriffen (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Schutz vor der Remoteausführung von Code|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Schutz vor Angriffen mit Einschleusung von PHP-Befehlen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Schutz vor Angriffen durch websiteübergreifendes Skripting|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Schutz vor Session Fixation-Angriffen|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 umfasst 10 Regelgruppen, wie in der folgenden Tabelle gezeigt. Jede dieser Gruppen enthält mehrere Regeln, die deaktivierbar sind.

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Schutz vor Protokollverletzungen (etwa durch ungültige Zeichen oder GET mit einem Anforderungstext)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Schutz vor falschen Headerinformationen|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Schutz vor Argumenten oder Dateien mit Grenzwertüberschreitung|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Schutz vor eingeschränkten Methoden, Headern und Dateitypen|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Schutz vor Webcrawlern und -scannern|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Schutz vor generischen Angriffen (z.B. Session Fixation, Remote File Inclusion und PHP-Einschleusung)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Schutz vor Angriffen durch websiteübergreifendes Skripting|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Schutz vor Path Traversal-Angriffen|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Schutz vor Hintertürtrojanern|

### <a name="waf-modes"></a>WAF-Modi

Die Application Gateway-WAF kann für die Ausführung in den folgenden beiden Modi konfiguriert werden:

* **Erkennungsmodus**: Überwacht und protokolliert alle Bedrohungswarnungen. Sie aktivieren die Protokollierung von Diagnosedaten für Application Gateway im Abschnitt **Diagnose**. Zudem müssen Sie sicherstellen, dass das WAF-Protokoll ausgewählt und aktiviert ist. Im Erkennungsmodus werden eingehende Anforderungen von der Web Application Firewall nicht blockiert.
* **Schutzmodus**: Blockiert Eindringversuche und Angriffe, die die Regeln erkennen. Der Angreifer erhält eine „403 nicht autorisierter Zugriff“-Ausnahmemeldung, und die Verbindung wird beendet. Der Schutzmodus hält solche Angriffe weiterhin in den WAF-Protokollen fest.

### <a name="anomaly-scoring-mode"></a>Anomaliebewertungsmodus

OWASP kann in zwei Modi entscheiden, ob Datenverkehr blockiert wird: herkömmlicher Modus und Anomaliebewertungsmodus.

Im herkömmlichen Modus wird Datenverkehr, der einer Regel entspricht, unabhängig davon berücksichtigt, ob er mit einer anderen Regel übereinstimmt. Dieser Modus ist leicht verständlich. Doch der Mangel an Informationen darüber, wie viele Regeln mit einer bestimmten Anforderung übereinstimmen, ist eine Einschränkung. Darum wurde der Anomaliebewertungsmodus eingeführt. Er ist die Standardeinstellung für OWASP 3.*x*.

Im Anomaliebewertungsmodus wird Datenverkehr, der einer beliebigen Regel entspricht, nicht sofort blockiert, wenn die Firewall sich im Schutzmodus befindet. Regeln haben einen bestimmten Schweregrad: *Kritisch*, *Fehler*, *Warnung* oder *Hinweis*. Dieser Schweregrad wirkt sich auf einen numerischen Wert für die Anforderung aus, der als „Anomaliebewertung“ bezeichnet wird. So trägt eine Übereinstimmung mit einer *Warnungsregel* mit 3 zum Ergebnis bei. Eine Übereinstimmung mit einer *kritischen* Regel trägt mit 5 zum Ergebnis bei.

|Severity  |Wert  |
|---------|---------|
|Kritisch     |5|
|Error        |4|
|Warnung      |3|
|Hinweis       |2|

Ab einem Schwellenwert von 5 blockiert die Anomaliebewertung den Datenverkehr. Eine Übereinstimmung mit einer einzelnen *kritischen* Regel reicht also aus, damit die Application Gateway-WAF auch im Schutzmodus eine Anforderung blockiert. Aber eine Übereinstimmung mit einer *Warnungsregel* setzt die Anomaliebewertung nur um 3 herauf, was allein nicht ausreicht, um den Datenverkehr zu blockieren.

> [!NOTE]
> Die Meldung, die protokolliert wird, wenn eine WAF-Regel dem Datenverkehr entspricht, enthält den Aktionswert „Blockiert“. Aber der Datenverkehr ist tatsächlich nur bei einer Anomaliebewertung von 5 oder höher blockiert.  

### <a name="waf-monitoring"></a>WAF-Überwachung

Die Integrität Ihres Anwendungsgateways sollte unbedingt überwacht werden. Die Überwachung der Integrität Ihrer WAF und der durch sie geschützten Anwendungen wird mittels Integration in Azure Security Center, Azure Monitor und Azure Monitor-Protokolle erreicht.

![Diagramm der Application Gateway-WAF-Diagnose](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-Protokolle sind in [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) integriert. Dadurch können Sie Diagnoseinformationen einschließlich WAF-Warnungen und -Protokolle nachverfolgen. Sie können innerhalb der Application Gateway-Ressource im Portal auf der Registerkarte **Diagnose** oder direkt über den Azure Monitor-Dienst auf diese Funktion zugreifen. Weitere Informationen zum Aktivieren von Protokollen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

Mit [Security Center](../security-center/security-center-intro.md) können Sie Bedrohungen verhindern, erkennen und beheben. Security Center sorgt für eine größere Transparenz und bessere Kontrolle der Sicherheit Ihrer Azure-Ressourcen. Application Gateway ist [in Azure Security Center](application-gateway-integration-security-center.md) integriert. Security Center scannt die Umgebung, um ungeschützte Webanwendungen zu ermitteln. Security Center kann der Application Gateway-WAF die Empfehlung geben, diese ungeschützten Ressourcen zu schützen. Sie erstellen die Firewalls direkt von Security Center aus. Diese WAF-Instanzen sind in Security Center integriert. Sie senden Warnungen und Integritätsinformationen für die Berichterstellung an Security Center.

![Security Center-Übersichtsfenster](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Protokollierung

Die Application Gateway-WAF bietet detaillierte Berichte zu jeder erkannten Bedrohung. Die Protokollierung ist in Azure-Diagnoseprotokolle integriert. Warnungen werden im JSON-Format aufgezeichnet. Diese Protokolle können in [Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md) integriert werden.

![Diagnoseprotokollefenster für Application Gateway](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>SKU-Preise für die Application Gateway-WAF

Die Application Gateway-WAF ist unter einer neuen SKU erhältlich. Diese SKU steht nur im Rahmen des Azure Resource Manager-Bereitstellungsmodells zur Verfügung, nicht für das klassische Bereitstellungsmodell. Darüber hinaus ist die WAF-SKU nur in den Application Gateway-Instanzgrößen „Mittel“ und „Groß“ erhältlich. Alle Grenzwerte für Application Gateway gelten auch für die WAF-SKU.

Der Preis basiert auf einer Gatewayinstanzgebühr pro Stunde sowie auf einer Datenverarbeitungsgebühr. Der [Application Gateway-Preis](https://azure.microsoft.com/pricing/details/application-gateway/) für die WAF-SKU entspricht nicht den standardmäßigen SKU-Gebühren. Die Datenverarbeitungsgebühren bleiben unverändert. Es fallen keine regel- oder regelgruppenbasierten Kosten an. Sie können mehrere Webanwendungen hinter der gleichen Web Application Firewall schützen. Die Unterstützung von mehreren Anwendungen wird Ihnen nicht in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Konfigurieren der Web Application Firewall auf Application Gateway](tutorial-restrict-web-traffic-powershell.md).
