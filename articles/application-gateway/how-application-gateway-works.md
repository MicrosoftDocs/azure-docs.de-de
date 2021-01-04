---
title: Funktionsweise von Anwendungsgateways
description: In diesem Artikel erfahren Sie, wie Anwendungsgateways die eingehenden Anforderungen akzeptieren und sie an das Back-End routen.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 9166125fac28f43a93cbee2875b91bee986b1400
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397466"
---
# <a name="how-an-application-gateway-works"></a>Funktionsweise von Anwendungsgateways

In diesem Artikel wird erläutert, wie Anwendungsgateways die eingehenden Anforderungen akzeptieren und sie an das Back-End routen.

![Akzeptieren einer Anforderung durch ein Anwendungsgateway](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Akzeptieren einer Anforderung durch ein Anwendungsgateway

1. Bevor ein Client eine Anforderung an ein Anwendungsgateway sendet, löst er den Domänennamen des Anwendungsgateways mithilfe eines DNS-Servers (Domain Name System) auf. Der DNS-Eintrag wird von Azure gesteuert, da sich alle Anwendungsgateways in der Domäne azure.com befinden.

2. Der Azure-DNS gibt die IP-Adresse an den Client zurück. Dabei handelt es sich um die Front-End-IP-Adresse des Anwendungsgateways.

3. Das Anwendungsgateway akzeptiert eingehenden Datenverkehr an einem oder mehreren Listenern. Ein Listener ist eine logische Entität, die auf eingehende Verbindungsanforderungen prüft. Er wird mit einer Front-End-IP-Adresse, einem Protokoll und einer Portnummer für Verbindungen vom Client mit dem Anwendungsgateway konfiguriert.

4. Wenn eine Web Application Firewall (WAF) verwendet wird, überprüft das Anwendungsgateway die Anforderungsheader und ggf. den -text anhand von WAF-Regeln. Durch diese Aktion wird ermittelt, ob es sich bei der Anforderung um eine gültige Anforderung oder ein Sicherheitsrisiko handelt. Ist es eine gültige Anforderung, wird sie an das Back-End weitergeleitet. Ist es keine gültige Anforderung, und WAF befindet sich im Präventionsmodus, wird sie als Sicherheitsrisiko blockiert. Wenn sie sich im Erkennungsmodus befindet, wird die Anforderung ausgewertet und protokolliert, aber trotzdem an den Back-End-Server weitergeleitet.

Azure Application Gateway kann als interner Lastenausgleich für Anwendungen oder als ein vom Internet zugänglicher Lastenausgleich für Anwendungen verwendet werden. Ein internetseitiges Anwendungsgateway nutzt eine öffentliche IP-Adresse. Der DNS-Name eines internetseitigen Anwendungsgateways kann öffentlich in seine öffentliche IP-Adresse aufgelöst werden. Daher können internetseitige Anwendungsgateways Clientanforderungen über das Internet routen.

Interne Anwendungsgateways nutzen nur private IP-Adressen. Wenn Sie eine benutzerdefiniertes oder [Private DNS-Zone](../dns/private-dns-overview.md) verwenden, sollte sich der Domänenname intern in die private IP-Adresse des Application Gateway auflösen lassen. Daher können interne Lastenausgleichsmodule nur Anforderungen von Clients mit Zugriff auf ein virtuelles Netzwerk für das Anwendungsgateway routen.

## <a name="how-an-application-gateway-routes-a-request"></a>Routen einer Anforderung durch ein Anwendungsgateway

Wenn eine Anforderung gültig ist und nicht von WAF blockiert wird, wertet das Anwendungsgateway die Anforderungsroutingregel aus, die mit dem Listener verknüpft ist. Durch diese Aktion wird ermittelt, an welchen Back-End-Pool die Anforderung weitergeleitet werden soll.

Auf der Grundlage der Routingregel für Anforderungen entscheidet das Anwendungsgateway, ob alle Anforderungen am Listener an einen bestimmten Back-End-Pool, basierend auf dem URL-Pfad an verschiedene Back-End-Pools oder an Umleitungsanforderungen zu einem anderen Port oder einer externen Website weitergeleitet werden sollen.
>[!NOTE]
>Regeln werden in der Reihenfolge verarbeitet, in der sie im Portal für v1-SKU aufgeführt sind. 

Nachdem das Anwendungsgateway einen Back-End-Pool ausgewählt hat, sendet es die Anforderung an einen der fehlerfreien Back-End-Server im Pool (y.y.y.y). Die Integrität des Servers wird mithilfe eines Integritätstests überprüft. Wenn der Back-End-Pool mehrere Server enthält, verwendet das Anwendungsgateway einen Roundrobinalgorithmus zum Routen der Anforderungen an die einzelnen fehlerfreien Server. Dadurch wird ein Lastenausgleich für die Anforderungen auf den Servern gebildet.

Nachdem das Anwendungsgateway einen Back-End-Server festgelegt hat, öffnet es eine neue TCP-Sitzung mit dem Back-End-Server anhand der HTTP-Einstellungen. Die HTTP-Einstellungen geben das Protokoll, den Port und andere routingbezogene Einstellungen an, die zum Herstellen einer neuen Sitzung mit dem Back-End-Server erforderlich sind.

Die Angaben für Port und Protokoll in den HTTP-Einstellungen legen fest, ob der Datenverkehr zwischen dem Anwendungsgateway und den Back-End-Servern verschlüsselt wird (sodass End-to-End-TLS erzielt wird) oder unverschlüsselt bleibt.

Wenn ein Anwendungsgateway die ursprüngliche Anforderung an den Back-End-Server sendet, berücksichtigt es etwaige benutzerdefinierte Konfigurationen in den HTTP-Einstellungen, durch die Hostname, Pfad oder Protokoll überschrieben werden. Durch diese Aktion werden die cookiebasierte Sitzungsaffinität, der Verbindungsausgleich, die Auswahl des Hostnamens über das Back-End usw. beibehalten.

 >[!NOTE]
>Für den Back-End-Pool gilt Folgendes:
> - Wenn er **ein öffentlicher Endpunkt ist** , verwendet das Anwendungsgateway die öffentliche Front-End-IP-Adresse, um den Server zu erreichen. Wenn keine öffentliche Front-End-IP-Adresse vorliegt, wird für die ausgehende externe Konnektivität eine zugewiesen.
> - Wenn er **einen intern auflösbaren FQDN oder eine private IP-Adresse enthält** , routet das Anwendungsgateway die Anforderung über die privaten Instanz-IP-Adressen des Back-End-Servers an diesen.
> - Wenn er **einen externen Endpunkt oder einen extern auflösbaren FQDN enthält** , routet das Anwendungsgateway die Anforderung über die öffentliche Front-End-IP-Adresse des Back-End-Servers an diesen. Die DNS-Auflösung basiert auf einer privaten DNS-Zone oder einem benutzerdefinierten DNS-Server (sofern konfiguriert), oder es wird das von Azure bereitgestellte Standard-DNS verwendet. Wenn keine öffentliche Front-End-IP-Adresse vorliegt, wird für die ausgehende externe Konnektivität eine zugewiesen.

### <a name="modifications-to-the-request"></a>Änderungen an der Anforderung

Anwendungsgateways fügen vier zusätzliche Header in alle Anforderungen ein, bevor sie die Anforderungen an das Back-End weiterleiten. Diese Header sind „x-forwarded-for“, „x-forwarded-proto“, „x-forwarded-port“ und „x-original-host“. Das Format für den x-forwarded-for-Header ist eine durch Trennzeichen getrennte Liste von IP:Port.

Die gültigen Werte für x-forwarded-proto sind HTTP und HTTPS. „x-forwarded-port“ gibt den Port an, an dem die Anforderung das Anwendungsgateway erreicht hat. Der X-original-host-Header enthält den ursprünglichen Hostheader, mit dem die Anforderung eingetroffen ist. Dieser Header ist nützlich bei der Azure-Websiteintegration, bei der der eingehende Hostheader vor dem Routen des Datenverkehrs zum Back-End geändert wird. Wenn Sitzungsaffinität als Option aktiviert ist, wird ein vom Gateway verwaltetes Affinitätscookie eingefügt.

Sie können das Anwendungsgateway so konfigurieren, dass Anforderungs- und Antwortheader sowie die URL durch das [erneute Generieren von HTTP-Headern und URLs](rewrite-http-headers-url.md) geändert werden oder der URI-Pfad mithilfe der Einstellung für die Pfadüberschreibung geändert wird. Sofern dies nicht konfiguriert ist, werden jedoch alle eingehenden Anforderungen per Proxy an das Back-End weitergeleitet.

## <a name="next-steps"></a>Nächste Schritte

[Informationen zu den Komponenten von Anwendungsgateways](application-gateway-components.md)