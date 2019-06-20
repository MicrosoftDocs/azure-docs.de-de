---
title: Funktionsweise von Azure Traffic Manager | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Traffic Manager Datenverkehr für eine hohe Leistung und Verfügbarkeit Ihrer Webanwendungen weiterleitet.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: a74af002dfdad5df9640be4b5fdd7f657b183bd4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071184"
---
# <a name="how-traffic-manager-works"></a>Funktionsweise von Traffic Manager

Mit Azure Traffic Manager können Sie die Verteilung von Datenverkehr auf Anwendungsendpunkte steuern. Ein Endpunkt ist ein Dienst mit Internetzugriff, der innerhalb oder außerhalb von Azure gehostet wird.

Traffic Manager bietet zwei wesentliche Vorteile:

- Verteilung von Datenverkehr gemäß einer von mehreren [Datenverkehrsrouting-Methoden](traffic-manager-routing-methods.md)
- [Kontinuierliche Überwachung der Endpunktintegrität](traffic-manager-monitoring.md) und automatisches Failover bei einem Ausfall von Endpunkten

Wenn ein Client eine Verbindung mit einem Dienst herstellen möchte, muss zunächst der DNS-Name des Diensts in eine IP-Adresse aufgelöst werden. Anschließend stellt der Client eine Verbindung mit dieser IP-Adresse her, um auf den Dienst zuzugreifen.

**Wichtig: Traffic Manager operiert auf der DNS-Ebene.**  Traffic Manager verwendet DNS, um Clients anhand der Regeln der Routingmethode für den Datenverkehr an bestimmte Dienstendpunkte weiterzuleiten. Clients stellen mit dem ausgewählten Endpunkt eine **direkte Verbindung** her. Traffic Manager ist weder ein Proxy noch ein Gateway. Traffic Manager hat keinen Zugriff auf den Datenverkehr zwischen Client und Dienst.

## <a name="traffic-manager-example"></a>Traffic Manager-Beispiel

Contoso Corp hat ein neues Partnerportal entwickelt. Die URL für dieses Portal lautet https://partners.contoso.com/login.aspx. Die Anwendung wird in drei Bereichen von Azure gehostet. Traffic Manager wird zur Verteilung des Clientdatenverkehrs auf den nächstgelegenen verfügbaren Endpunkt verwendet, um die Verfügbarkeit zu verbessern und die allgemeine Leistung zu maximieren.

Um diese Konfiguration zu erzielen, werden die folgenden Schritte ausgeführt:

1. Drei Instanzen des Diensts werden bereitgestellt. Die DNS-Namen dieser Bereitstellungen lauten „contoso-us.cloudapp.net“, „contoso-eu.cloudapp.net“ und „contoso-asia.cloudapp.net“.
1. Ein Traffic Manager-Profil namens „contoso.trafficmanager.net“ wird erstellt und für die Verwendung der leistungsorientierten Datenverkehrsrouting-Methode für die drei Endpunkte konfiguriert.
1. Der Vanity-Domänenname „partners.contoso.com“ mit einem Verweis auf „contoso.trafficmanager.net“ (unter Verwendung eines DNS CNAME-Eintrags) wird konfiguriert.

![Traffic Manager-DNS-Konfiguration][1]

> [!NOTE]
> Wenn Sie eine Vanity-Domäne mit Azure Traffic Manager verwenden, müssen Sie mithilfe eines CNAME-Eintrags von Ihrem Vanity-Domänennamen auf Ihren Traffic Manager-Domänennamen verweisen. DNS-Standards lassen nicht zu, dass ein CNAME auf der „Spitze“ (oder dem Stamm) einer Domäne erstellt wird. Daher kann für „contoso.com“ (gelegentlich auch als reiner Domänenname bezeichnet) kein CNAME erstellt werden. Ein CNAME kann nur für eine Domäne unter „contoso.com“ erstellt werden (beispielsweise für „www.contoso.com“). Um diese Einschränkung zu umgehen, wird empfohlen, Ihre DNS-Domäne in [Azure DNS](../dns/dns-overview.md) zu hosten und [Aliaseinträge](../dns/tutorial-alias-tm.md) zu verwenden, die auf Ihr Traffic Manager-Profil verweisen. Sie können auch eine einfache HTTP-Umleitung verwenden, um an „contoso.com“ gerichtete Anforderungen an einen alternativen Namen (etwa „www.contoso.com“) umzuleiten.

### <a name="how-clients-connect-using-traffic-manager"></a>Verbindungsherstellung von Clients über Traffic Manager

Wenn ein Client (das vorherige Beispiel fortgesetzt) die Seite https://partners.contoso.com/login.aspx anfordert, führt er die folgenden Schritte durch, um den DNS-Namen aufzulösen und eine Verbindung herzustellen:

![Verbindungsherstellung mit Traffic Manager][2]

1. Der Client sendet eine DNS-Abfrage zur Auflösung des Namens „partners.contoso.com“ an den konfigurierten rekursiven DNS-Dienst. Ein rekursiver DNS-Dienst wird gelegentlich auch als lokaler DNS-Dienst bezeichnet und fungiert nicht als direkter Host für DNS-Domänen. Stattdessen wird er vom Client zum Abladen der Arbeit für die Kontaktaufnahme mit den verschiedenen autoritativen DNS-Diensten im Internet verwendet, die zum Auflösen eines DNS-Namens benötigt werden.
2. Zum Auflösen des DNS-Namens ermittelt der rekursive DNS-Dienst die Namenserver für die Domäne „contoso.com“. Anschließend fordert er von diesen Namenservern den DNS-Eintrag „partners.contoso.com“ an. Die „contoso.com“-DNS-Server geben den CNAME-Eintrag zurück, der auf „contoso.trafficmanager.net“ verweist.
3. Als Nächstes ermittelt der rekursive DNS-Dienst die Namenserver für die Domäne „trafficmanager net“, die vom Azure Traffic Manager-Dienst bereitgestellt werden. Anschließend sendet er eine Anforderung für den DNS-Eintrag „contoso.trafficmanager.net“ an diese DNS-Server.
4. Die Anforderung geht bei den Traffic Manager-Namenservern ein. Diese wählen einen Endpunkt. Dabei ist Folgendes ausschlaggebend:

    - Konfigurierter Zustand der einzelnen Endpunkte. (Deaktivierte Endpunkte werden nicht zurückgegeben.)
    - Aktuelle Integrität der einzelnen Endpunkte gemäß Traffic Manager-Integritätsüberprüfungen. Weitere Informationen finden Sie unter [Traffic Manager-Endpunktüberwachung](traffic-manager-monitoring.md).
    - Gewählte Datenverkehrsrouting-Methode. Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting](traffic-manager-routing-methods.md).

5. Der ausgewählte Endpunkt wird als anderer DNS-CNAME-Eintrag zurückgegeben. Nehmen wir beispielsweise an, dass „contoso-us.cloudapp.net“ zurückgegeben wird.
6. Als Nächstes ermittelt nun der rekursive DNS-Dienst die Namenserver für die Domäne „cloudapp.net“. Von diesen Namenservern fordert er den DNS-Eintrag „contoso-us.cloudapp.net“ an. Ein DNS-A-Eintrag mit der IP-Adresse des Dienstendpunkts in den USA wird zurückgegeben.
7. Der rekursive DNS-Dienst konsolidiert die Ergebnisse und gibt an den Client eine DNS-Antwort zurück.
8. Der Client erhält die DNS-Ergebnisse und stellt eine Verbindung mit der angegebenen IP-Adresse her. Der Client stellt die Verbindung mit dem Dienstendpunkt der Anwendung direkt und nicht über Traffic Manager her. Da es sich um einen HTTPS-Endpunkt handelt, führt der Client erst den erforderlichen SSL/TLS-Handshake durch und fordert anschließend per HTTP GET-Anforderung die Seite „/login.aspx“ an.

Der rekursive DNS-Dienst speichert die erhaltenen DNS-Antworten im Cache. Von der DNS-Auflösung auf dem Clientgerät wird das Ergebnis ebenfalls im Cache gespeichert. Durch das Speichern im Cache können nachfolgende DNS-Abfragen schneller abgewickelt werden, da keine anderen Namenserver abgefragt werden müssen, sondern die Daten aus dem Cache verwendet werden können. Wie lange die Daten zwischengespeichert werden, hängt von der TTL-Eigenschaft der einzelnen DNS-Einträge ab. Kleinere Werte bedeuten, dass der Cache schneller abläuft und somit mehr Roundtrips zu den Traffic Manager-Nameservern erforderlich sind. Größere Werte bedeuten, dass es möglicherweise länger dauert, bis der Datenverkehr von einem fehlerhaften Endpunkt weggeleitet wird. Mit Traffic Manager können Sie die TTL für Traffic Manager-DNS-Antworten von 0 bis 2.147.483.647 Sekunden (maximale Länge, die [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt) entspricht)konfigurieren und so einen Wert wählen, der für die Anforderungen Ihrer Anwendung am besten geeignet ist.


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Endpunktüberwachung und automatisches Failover](traffic-manager-monitoring.md)von Traffic Manager.

Informieren Sie sich ausführlicher über [Traffic Manager-Routingmethoden](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

