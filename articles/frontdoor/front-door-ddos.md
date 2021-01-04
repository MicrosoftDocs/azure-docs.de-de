---
title: Azure Front Door – DDoS-Schutz
description: Diese Seite enthält Informationen darüber, wie Azure Front Door vor DDoS-Angriffen schützt.
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350782"
---
# <a name="ddos-protection-on-front-door"></a>DDoS-Schutz unter Front Door

Azure Front Door bietet mehrere Features und Merkmale, die helfen können, DDoS-Angriffe (Denial of Service) zu verhindern. Diese Features können verhindern, dass Angreifer auf Ihre Anwendung zugreifen und deren Verfügbarkeit und Leistung beeinträchtigen.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integration in Azure DDoS Protection Basic

Front Door wird durch Azure DDoS Protection Basic geschützt. Dies ist standardmäßig und ohne zusätzliche Kosten in die Front Door-Plattform integriert. Die vollständige Skalierung und Kapazität des global bereitgestellten Front Door-Netzwerks bietet über AlwaysOn-Datenverkehrsüberwachung und Risikominderung in Echtzeit Schutz vor häufigen Vermittlungsschichtangriffen. Basic DDos-Schutz schützt auch vor den gängigsten, häufig auftretenden DNS-Abfrage-Floods (Schicht 7) und volumetrischen Angriffen (Schicht 3 und 4), die auf öffentliche Endpunkte abzielen. Dieser Dienst hat sich außerdem in der Praxis beim Schutz von Unternehmens- und Endkundendiensten von Microsoft vor großangelegten Angriffen bewährt. Weitere Informationen finden Sie unter [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Protokollblockierung

Front Door akzeptiert nur Datenverkehr über die HTTP- und HTTPS-Protokolle und verarbeitet nur gültige Anforderungen mit einem bekannten `Host`-Header. Dieses Verhalten trägt dazu bei, einige gängige DDoS-Angriffstypen abzuschwächen, darunter volumetrische Angriffe, die auf verschiedene Protokolle und Ports verteilt sind, DNS-Verstärkungsangriffe und TCP-Poisoningangriffe.

## <a name="capacity-absorption"></a>Kapazitätsabsorption

Front Door ist ein hochgradig skalierter, global verteilter Dienst. Wir haben viele Kunden, einschließlich der großen Cloudprodukte von Microsoft, die pro Sekunde Hunderttausende von Anforderungen erhalten. Front Door befindet sich am Rand des Azure-Netzwerks und absorbiert großvolumige Angriffe und isoliert sie geografisch. Dadurch kann verhindert werden, dass schädlicher Datenverkehr über den Rand des Azure-Netzwerks hinausgeht.

## <a name="caching"></a>Zwischenspeicherung

Die [Zwischenspeicherungsfunktionen von Front Door](./front-door-caching.md) können verwendet werden, um Back-Ends vor durch einen Angriff generierten großen Datenverkehrsvolumen zu schützen. Zwischengespeicherte Ressourcen werden von den Front Door-Edgeknoten zurückgegeben, sodass sie nicht an Ihr Back-End weitergeleitet werden. Sogar kurze Cacheablaufzeiten (Sekunden oder Minuten) bei dynamischen Antworten können die Auslastung von Back-End-Diensten erheblich verringern. Weitere Informationen zum Zwischenspeichern von Konzepten und Mustern finden Sie unter [Caching](/azure/architecture/best-practices/caching) und [Cachefremdes Muster](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Web Application Firewall (WAF)

Die [Azure Web Application Firewall für Azure Front Door](../web-application-firewall/afds/afds-overview.md) kann verwendet werden, um eine Reihe von unterschiedlichen Arten von Angriffen abzuschwächen:

* Die Verwendung des verwalteten Regelsatzes bietet Schutz gegen eine Reihe häufiger Angriffe.
* Datenverkehr von außerhalb einer definierten geografischen Region oder innerhalb einer definierten Region kann blockiert oder an eine statische Webseite umgeleitet werden. Weitere Informationen finden Sie unter [Was ist die Geofilterung in einer Domäne für Azure Front Door Service?](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* IP-Adressen und -Bereiche, die Sie als bösartig identifizieren, können blockiert werden.
* Die Ratenbegrenzung kann angewendet werden, um zu verhindern, dass IP-Adressen ihren Dienst zu häufig aufrufen.
* Sie können [benutzerdefinierte WAF-Regeln](../web-application-firewall/afds/waf-front-door-custom-rules.md) erstellen, um HTTP- oder HTTPS-Angriffe mit bekannten Signaturen automatisch zu blockieren sowie eine Ratenbegrenzung anzuwenden.

## <a name="for-further-protection"></a>Für weiteren Schutz

Wenn Sie weiteren Schutz benötigen, können Sie [Azure DDoS Protection Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) für das VNET aktivieren, in dem Ihre Back-Ends bereitgestellt werden. DDoS Protection Standard-Kunden profitieren von weiteren Vorteilen wie Kostenschutz, SLA-Garantie und Zugang zu Experten des DDoS Rapid Response-Teams für Soforthilfe im Angriffsfall.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie ein [WAF-Profil auf Front Door](front-door-waf.md) konfigurieren. 
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).