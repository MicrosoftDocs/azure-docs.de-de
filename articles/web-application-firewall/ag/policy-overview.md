---
title: Übersicht über die Azure Web Application Firewall (WAF)-Richtlinien
description: Dieser Artikel stellt eine Übersicht über die globalen Richtlinien für Web Application Firewall (WAF) sowie die Richtlinien pro Website und pro URI bereit.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518735"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Übersicht über die Azure Web Application Firewall (WAF)-Richtlinien

Web Application Firewall-Richtlinien enthalten alle WAF-Einstellungen und -Konfigurationen. Dies umfasst Ausschlüsse, benutzerdefinierte Regeln, verwaltete Regeln usw. Diese Richtlinien werden dann einer Anwendungsgateway- (global), einer Listener- (pro Website) oder einer pfadbasierten Regel (pro URI) zugeordnet, damit Sie wirksam werden.

Die Anzahl der Richtlinien, die Sie erstellen können, ist nach oben hin nicht begrenzt. Wenn Sie eine Richtlinie erstellen, muss sie einem Anwendungsgateway zugeordnet werden, um wirksam zu werden. Sie kann einer beliebigen Kombination von Anwendungsgateways, Listenern und pfadbasierten Regeln zugeordnet werden.

## <a name="global-waf-policy"></a>Globale WAF-Richtlinie

Wenn Sie eine WAF-Richtlinie global zuordnen, wird jede Website hinter ihrer Application Gateway-WAF durch dieselben verwalteten Regeln, benutzerdefinierte Regeln, Ausschlüsse und alle anderen konfigurierten Einstellungen geschützt.

Wenn Sie möchten, dass eine einzelne Richtlinie auf alle Standorte angewendet wird, können Sie die Richtlinie dem Anwendungsgateway zuordnen. Weitere Informationen zum Erstellen und Anwenden einer WAF-Richtlinie über das Azure-Portal finden Sie unter [Erstellen von Web Application Firewall-Richtlinien für Application Gateway](create-waf-policy-ag.md). 

## <a name="per-site-waf-policy"></a>Pro Website-WAF-Richtlinie

Mit den Pro Website-WAF-Richtlinien können Sie mehrere Websites mit unterschiedlichen Sicherheitsanforderungen hinter einer einzigen WAF mithilfe von Pro Website-Richtlinien schützen. Wenn sich beispielsweise hinter Ihrer WAF fünf Websites befinden, können Sie fünf einzelne WAF-Richtlinien (eine für jeden Listener) verwenden, um Ausschlüsse, benutzerdefinierte Regeln und verwaltete Regelsätze und alle anderen WAF-Einstellungen für die einzelnen Websites anzupassen.

Angenommen, Sie haben eine globale Richtlinie auf Ihr Anwendungsgateway angewendet. Anschließend wenden Sie eine andere Richtlinie auf einen Listener auf diesem Anwendungsgateway an. Die Richtlinie des Listener wird jetzt nur für diesen Listener wirksam. Die globale Richtlinie des Anwendungsgateways gilt weiterhin für alle anderen Listener und pfadbasierten Regeln, denen keine spezielle Richtlinie zugewiesen ist.

## <a name="per-uri-policy"></a>Pro-URI-Richtlinie

Für eine noch bessere Anpassung bis auf URI-Ebene können Sie eine WAF-Richtlinie mit einer pfadbasierten Regel verknüpfen. Wenn bestimmte Seiten innerhalb einer einzelnen Website verschiedene Richtlinien erfordern, können Sie Änderungen an der WAF-Richtlinie vornehmen, die nur für einen bestimmten URI gelten. Dies gilt möglicherweise für eine Zahlungs- oder Anmeldeseite oder für andere URIs, die eine noch spezifischere WAF-Richtlinie benötigen, als die anderen Websites hinter der WAF.

Wie bei den Pro-Website-WAF-Richtlinien setzen spezifischere Richtlinien weniger spezifische Richtlinien außer Kraft. Das bedeutet, dass eine Pro-URI-Richtlinie auf einer URL-Pfadkarte jede Pro-Website- oder globale WAF-Richtlinie darüber außer Kraft setzt.

### <a name="example"></a>Beispiel

Angenommen, Sie verfügen über drei Websites („contoso.com“, „fabrikam.com“ und „adatum.com“) hinter demselben Anwendungsgateway. Sie möchten eine WAF auf alle drei Websites anwenden, aber Sie benötigen zusätzliche Sicherheit für „adatum.com“, da Kunden hier Produkte anzeigen, suchen und kaufen.

Sie können auf die WAF eine globale Richtlinie mit einigen grundlegenden Einstellungen, Ausschlüssen oder benutzerdefinierten Regeln, falls erforderlich, anwenden, um zu verhindern, dass falsch positive Ergebnisse den Datenverkehr blockieren. In diesem Fall müssen keine globalen SQL-Einschleusungsregeln ausgeführt werden, da „fabrikam.com“ und „contoso.com“ statische Seiten ohne SQL-Back-End sind. Sie können diese Regeln daher in der globalen Richtlinie deaktivieren.

Diese globale Richtlinie eignet sich für „contoso.com“ und „fabrikam.com“, aber Sie müssen bei „adatum.com“ vorsichtiger sein, da hier Anmeldeinformationen und Zahlungen verarbeitet werden. Sie können eine Pro-Website-Richtlinie auf den adatum-Listener anwenden und die SQL-Regeln weiter ausführen lassen. Nehmen wir des Weiteren an, dass ein Cookie einen Teil des Datenverkehrs blockiert, sodass Sie einen Ausschluss für dieses Cookie erstellen können, um falsch positive Ergebnisse zu beenden. 

Für den URI „adatum.com/payments“ müssen Sie größere Vorsicht walten lassen. Wenden Sie daher eine andere Richtlinie auf diesen URI an, lassen Sie alle Regeln aktiviert, und entfernen Sie alle Ausschlüsse.

In diesem Beispiel sehen Sie eine globale Richtlinie, die für zwei Websites gilt. Sie verfügen über eine Pro-Website-Richtlinie, die auf einen Standort angewendet wird, und eine Pro-URI-Richtlinie, die für eine bestimmte pfadbasierte Regel gilt. Weitere Informationen zur PowerShell-Konfiguration für dieses Beispiel finden Sie unter [Konfigurieren von websitespezifischen WAF-Richtlinien mithilfe von Azure PowerShell](per-site-policies.md).

## <a name="existing-waf-configurations"></a>Vorhandene WAF-Konfigurationen

Alle neuen WAF-Einstellungen (benutzerdefinierte Regeln, verwaltete Regelsatzkonfigurationen, Ausschlüsse usw.) sind in einer WAF-Richtlinie enthalten. Wenn Sie bereits über eine WAF verfügen, sind diese Einstellungen möglicherweise weiterhin in Ihrer WAF-Konfiguration vorhanden. Weitere Informationen zum Umstieg auf die neue WAF-Richtlinie finden Sie unter [Migrieren der WAF-Konfiguration zu einer WAF-Richtlinie](./migrate-policy.md). 


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Pro-Website- und Pro-URI-Richtlinien mit Azure PowerShell](per-site-policies.md).
