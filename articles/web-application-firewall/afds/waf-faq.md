---
title: Häufig gestellte Fragen zu Azure Web Application Firewall in Azure Front Door Service
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Web Application Firewall in Azure Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545668"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Häufig gestellte Fragen zu Azure Web Application Firewall in Azure Front Door Service

In diesem Artikel werden gängige Fragen zu den Features und Funktionen von Azure Web Application Firewall (WAF) in Azure Front Door Service beantwortet. 

## <a name="what-is-azure-waf"></a>Was ist Azure WAF?

Azure WAF ist eine Web Application Firewall, die dazu beiträgt, Ihre Webanwendungen vor allgemeinen Bedrohungen wie Einschleusung von SQL-Befehlen, Cross-Site Scripting und anderen Web-Exploits zu schützen. Sie können eine WAF-Richtlinie mit einer Kombination aus benutzerdefinierten und verwalteten Regeln definieren, um den Zugriff auf Ihre Webanwendungen zu steuern.

Eine Azure WAF-Richtlinie kann auf Webanwendungen angewendet werden, die in Application Gateway oder Azure Front Door gehostet werden.

## <a name="what-is-waf-on-azure-front-door"></a>Was ist WAF in Azure Front Door? 

Azure Front Door Service ist ein hochgradig skalierbares, global verteiltes APN (Application Delivery Network) und CDN (Content Delivery Network). Nach der Integration in Front Door unterbindet Azure WAF Denial-of-Service-Angriffe und gezielte Anwendungsangriffe im Edgebereich nahe der Angriffsquelle, bevor Angreifer in Ihr virtuelles Netzwerk eindringen, und bietet Schutz ohne Leistungseinbußen.

## <a name="does-azure-waf-support-https"></a>Wird HTTPS von Azure WAF unterstützt?

Frontdoor bietet TLS-Abladung. WAF ist nativ in Front Door integriert und kann Anforderungen nach ihrer Entschlüsselung untersuchen.

## <a name="does-azure-waf-support-ipv6"></a>Wird IPv6 von Azure WAF unterstützt?

Ja. Sie können IP-Einschränkungen für IPv4 und IPv6 konfigurieren.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Wie aktuell sind die verwalteten Regelsätze?

Wir tun unser Bestes, um mit der dynamischen Bedrohungslage Schritt zu halten. Nachdem eine neue Regel aktualisiert wurde, wird sie dem Standardregelsatz mit einer neuen Versionsnummer hinzugefügt.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Wie lange dauert es, bis eine Änderung, die ich an meiner WAF-Richtlinie vorgenommen habe, verteilt wurde?

Das globale Bereitstellen einer WAF-Richtlinie dauert in der Regel etwa fünf Minuten, geht häufig aber schneller.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Können für verschiedene Regionen unterschiedliche WAF-Richtlinien verwendet werden?

Wenn WAF in Front Door integriert wird, ist WAF eine globale Ressource. Die gleiche Konfiguration gilt also für alle Front Door-Standorte.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Wie kann ich den Zugriff auf mein Back-End allein auf Zugriffe über Front Door beschränken?

Sie können die IP-Zugriffssteuerungsliste Ihres Back-Ends so konfigurieren, dass Zugriffe nur über ausgehende IP-Adressbereiche von Front Door zugelassen werden und jeglicher Direktzugriff über das Internet abgelehnt wird. Diensttags werden unterstützt und können für Ihr virtuelles Netzwerk verwendet werden. Darüber hinaus können Sie überprüfen, ob das HTTP-Headerfeld „X-Forwarded-Host“ für Ihre Webanwendung gültig ist.

## <a name="which-azure-waf-options-should-i-choose"></a>Welche Azure WAF-Optionen soll ich auswählen?

Beim Anwenden von WAF-Richtlinien in Azure gibt es zwei Optionen. WAF mit Azure Front Door ist eine global verteilte Edgesicherheitslösung. WAF mit Application Gateway ist eine regionale, dedizierte Lösung. Es empfiehlt sich, eine Lösung zu wählen, die zu Ihren individuellen Leistungs- und Sicherheitsanforderungen passt. Weitere Informationen finden Sie unter [Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Was ist die empfohlene Vorgehensweise, um WAF für Front Door zu aktivieren?

Wenn Sie WAF für eine vorhandene Anwendung aktivieren, treten häufig False Positive-Erkennungen auf, bei denen die WAF-Regeln legitimen Datenverkehr als Bedrohung erkennen. Um das Risiko einer Beeinträchtigung für die Benutzer zu minimieren, wird der folgende Prozess empfohlen:

* Aktivieren Sie WAF im [Modus **Erkennung**](./waf-front-door-create-portal.md#change-mode), um sicherzustellen, dass WAF keine Anforderungen blockiert, während Sie diesen Prozess durchlaufen.
  > [!IMPORTANT]
  > In diesem Prozess wird beschrieben, wie Sie WAF für eine neue oder vorhandene Lösung aktivieren, wenn die Priorität darin besteht, Störungen für die Benutzer Ihrer Anwendung zu minimieren. Bei einem Angriff oder einer unmittelbaren Bedrohung sollten Sie WAF stattdessen sofort im **Schutzmodus** bereitstellen und den Optimierungsprozess verwenden, um WAF über einen Zeitraum zu überwachen und zu optimieren. Dies führt wahrscheinlich dazu, dass ein Teil des legitimen Datenverkehrs blockiert wird. Aus diesem Grund wird dieses Vorgehen nur im Fall einer Bedrohung empfohlen.
* Befolgen Sie die [Anweisungen zum Optimieren von WAF](./waf-front-door-tuning.md). Für diesen Prozess ist es erforderlich, dass Sie die Diagnoseprotokollierung aktivieren, die Protokolle regelmäßig überprüfen und Regelausschlüsse und andere Risikominderungen hinzufügen.
* Wiederholen Sie diesen Prozess, und überprüfen Sie die Protokolle regelmäßig, bis Sie sicher sind, dass kein legitimer Datenverkehr blockiert wird. Der gesamte Prozess kann mehrere Wochen dauern. Im Idealfall sollten nach jeder vorgenommenen Optimierungsänderung weniger False Positive-Erkennungen auftreten.
* Aktivieren Sie schließlich WAF im **Schutzmodus**.
* Selbst wenn Sie WAF in der Produktionsumgebung ausführen, sollten Sie die Protokolle weiterhin überwachen, um andere False Positive-Erkennungen zu identifizieren. Durch regelmäßiges Überprüfen der Protokolle können Sie auch alle tatsächlichen Angriffsversuche ermitteln, die blockiert wurden.

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Werden auf allen integrierten Plattformen die gleichen WAF-Funktionen unterstützt?

ModSec CRS 2.2.9-, CRS 3.0- und CRS 3.1-Regeln werden derzeit nur bei Verwendung von WAF mit Application Gateway unterstützt. Ratenbegrenzung, Geofilterung und von Azure verwaltete Standardregelsatz-Regeln werden nur bei Verwendung von WAF in Azure Front Door unterstützt.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Ist DDoS-Schutz in Front Door integriert? 

Azure Front Door wird global im Edgebereich des Azure-Netzwerks verteilt. Dadurch kann der Dienst groß angelegte Angriffe absorbieren und geografisch isolieren. Sie können eine benutzerdefinierte WAF-Richtlinie erstellen, um HTTP(S)-Angriffe mit bekannten Signaturen automatisch zu blockieren sowie eine Ratenbegrenzung anzuwenden. Darüber hinaus können Sie DDoS Protection Standard für das VNET aktivieren, in dem Ihre Back-Ends bereitgestellt werden. Azure DDoS Protection Standard-Kunden profitieren von weiteren Vorteilen wie Kostenschutz, SLA-Garantie und Zugang zu Experten des DDoS Rapid Response-Teams für Soforthilfe im Angriffsfall. Weitere Informationen finden Sie unter [DDoS-Schutz für Front Door](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Warum werden zusätzliche Anforderungen oberhalb des Schwellenwerts, der für meine Ratenbegrenzungsregel konfiguriert ist, an meinen Back-End-Server übergeben?

Eine Ratenbegrenzungsregel kann anormal hohen Datenverkehr von einer beliebigen IP-Clientadresse einschränken. Sie können einen Schwellenwert für die Anzahl der Webanforderungen konfigurieren, die von einer IP-Clientadresse während einer Dauer von einer Minute oder fünf Minuten zulässig sind. Für eine noch präzisere Ratensteuerung kann die Ratenbegrenzung mit weiteren Übereinstimmungsbedingungen (z.B. HTTP(S)-Parametern) kombiniert werden. 

Anforderungen vom gleichen Client treffen häufig auf demselben Front-Door-Server ein. In diesem Fall erkennen Sie, dass zusätzliche Anforderungen oberhalb des Schwellenwerts sofort blockiert werden. 

Es ist jedoch möglich, dass Anforderungen desselben Clients auf einem anderen Front-Door-Server eingehen, der den Ratenbegrenzungszähler noch nicht aktualisiert hat. Beispielsweise kann der Client für jede Anforderung eine neue Verbindung öffnen, und der Schwellenwert ist niedrig. In diesem Fall wird die Überprüfung der Ratenbeschränkung durch die erste Anforderung an den neuen Front-Door-Server bestanden. Ein Schwellenwert für die Ratenbeschränkung wird normalerweise auf einen hohen Wert festgelegt, um sich gegen Denial-of-Service-Angriffe von beliebigen IP-Clientadressen zu verteidigen. Bei einem sehr niedrigen Schwellenwert werden möglicherweise zusätzliche Anforderungen oberhalb des Schwellenwerts durchgelassen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [ Azure Web Application Firewall](../overview.md).
- Erfahren Sie mehr zu [Azure Front Door](../../frontdoor/front-door-overview.md).