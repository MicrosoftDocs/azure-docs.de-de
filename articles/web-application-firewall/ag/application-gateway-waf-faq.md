---
title: Häufig gestellte Fragen zu Azure Web Application Firewall in Application Gateway
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Web Application Firewall in Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 1fbe930780ff026be748bc42104f821ee9e5c443
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785101"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Häufig gestellte Fragen zu Azure Web Application Firewall in Application Gateway

In diesem Artikel werden gängige Fragen zu den Features und Funktionen von Azure Web Application Firewall (WAF) in Application Gateway beantwortet. 

## <a name="what-is-azure-waf"></a>Was ist Azure WAF?

Azure WAF ist eine Web Application Firewall, die dazu beiträgt, Ihre Webanwendungen vor allgemeinen Bedrohungen wie Einschleusung von SQL-Befehlen, Cross-Site Scripting und anderen Web-Exploits zu schützen. Sie können eine WAF-Richtlinie mit einer Kombination aus benutzerdefinierten und verwalteten Regeln definieren, um den Zugriff auf Ihre Webanwendungen zu steuern.

Eine Azure WAF-Richtlinie kann auf Webanwendungen angewendet werden, die in Application Gateway oder Azure Front Door gehostet werden.

## <a name="what-features-does-the-waf-sku-support"></a>Welche Features unterstützt die WAF-SKU?

Die WAF-SKU unterstützt alle Features, die in der Standard-SKU verfügbar sind.

## <a name="how-do-i-monitor-waf"></a>Wie überwache ich WAF?

Überwachen Sie WAF über die Diagnoseprotokollierung. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokollierung und Metriken für Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Wird Datenverkehr durch den Erkennungsmodus blockiert?

Nein. Der Erkennungsmodus protokolliert nur Datenverkehr, der eine WAF-Regel auslöst.

## <a name="can-i-customize-waf-rules"></a>Kann ich WAF-Regeln anpassen?

Ja. Weitere Informationen finden Sie unter [Anpassen von Web Application Firewall-Regeln mit dem Azure-Portal](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Welche Regeln sind derzeit für WAF verfügbar?

WAF unterstützt derzeit CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) und [3.1](application-gateway-crs-rulegroups-rules.md#owasp31). Diese Regeln bieten grundlegende Sicherheit für die meisten der zehn wichtigsten, von Open Web Application Security Project (OWASP) identifizierten Sicherheitsrisiken. 

* Schutz vor Einschleusung von SQL-Befehlen
* Schutz vor websiteübergreifendem Skripting
* Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion.
* Schutz vor Verletzungen des HTTP-Protokolls
* Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts
* Verhindern von Bots, Crawlern und Scannern
* Erkennung allgemeiner Fehler bei der Anwendungskonfiguration (in Bezug auf Apache, IIS usw.)

Weitere Informationen finden Sie im Artikel zu den [zehn wichtigsten, von OWASP identifizierten Sicherheitsrisiken](https://owasp.org/www-project-top-ten/).

## <a name="does-waf-support-ddos-protection"></a>Unterstützt WAF DDoS-Schutz?

Ja. Sie können DDoS-Schutz im virtuellen Netzwerk aktivieren, in dem das Anwendungsgateway bereitgestellt wird. Mit dieser Einstellung wird sichergestellt, dass der Azure DDoS Protection-Dienst auch die VIP-Adresse des Anwendungsgateways schützt.

### <a name="does-waf-store-customer-data"></a>Speichert WAF Kundendaten?

Nein, WAF speichert keine Kundendaten.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [ Azure Web Application Firewall](../overview.md).
- Erfahren Sie mehr zu [Azure Front Door](../../frontdoor/front-door-overview.md).
