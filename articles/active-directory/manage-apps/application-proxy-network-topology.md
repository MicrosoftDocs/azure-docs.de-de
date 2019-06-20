---
title: Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys | Microsoft-Dokumentation
description: Es werden die Aspekte der Netzwerktopologie beschrieben, die bei Verwendung des Azure AD-Anwendungsproxys wichtig sind.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf8e7fed30a9b25b2960e3321eca5c4398911f35
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236246"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys

In diesem Artikel geht es darum, welche Aspekte der Netzwerktopologie wichtig sind, wenn der Azure Active Directory-Anwendungsproxy (Azure AD) für die Veröffentlichung und den Zugriff auf Ihre Anwendungen per Remotezugriff verwendet wird.

## <a name="traffic-flow"></a>Datenverkehrsfluss

Wenn eine Anwendung über den Azure AD-Anwendungsproxy veröffentlicht wird, fließt der gesamte Datenverkehr von den Benutzern zu den Anwendungen über drei Verbindungen:

1. Vom Benutzer zum öffentlichen Endpunkt des Azure AD-Anwendungsproxydiensts in Azure
2. Vom Anwendungsproxydienst zum Anwendungsproxyconnector
3. Vom Anwendungsproxyconnector zur Zielanwendung

![Diagramm mit dem Datenverkehrsfluss vom Benutzer zur Zielanwendung](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Mandantenstandort und Anwendungsproxydienst

Wenn Sie sich für einen Azure AD-Mandanten registrieren, wird die Region Ihres Mandanten durch Ihre Angabe von Land/Region bestimmt. Bei Aktivierung des Anwendungsproxys werden die Instanzen des Anwendungsproxydiensts für Ihren Mandanten in derselben Region wie Ihr Azure AD-Mandant bzw. in der nächstgelegenen Region ausgewählt oder erstellt.

Wenn das Land oder die Region Ihres Azure AD-Mandanten beispielsweise das Vereinigte Königreich ist, werden für alle Anwendungsproxyconnectors Dienstinstanzen in EU-Rechenzentren verwendet. Dies bedeutet auch, der Datenverkehr für Ihre Benutzer über die Instanzen des Anwendungsproxydiensts an diesem Standort geleitet wird, wenn sie versuchen, auf veröffentlichte Anwendungen zuzugreifen.

## <a name="considerations-for-reducing-latency"></a>Reduzieren der Wartezeit

Für alle Proxylösungen kommt es in Bezug auf Ihre Netzwerkverbindungen zu Wartezeit. Unabhängig davon, welche Proxy- oder VPN-Lösung Sie als Lösung für den Remotezugriff wählen, ist immer eine Gruppe von Servern vorhanden, mit denen die Verbindung mit Ihrem Unternehmensnetzwerk ermöglicht wird.

Organisationen integrieren ihre Serverendpunkte normalerweise in ihr Umkreisnetzwerk. Beim Azure AD-Anwendungsproxy fließt der Datenverkehr jedoch über den Proxydienst in der Cloud, während sich die Connectors in Ihrem Unternehmensnetzwerk befinden. Es ist kein Umkreisnetzwerk erforderlich.

Die nächsten Abschnitte enthalten weitere Vorschläge dazu, wie Sie die Wartezeit noch weiter reduzieren können. 

### <a name="connector-placement"></a>Platzierung von Connectors

Der Anwendungsproxy wählt den Standort der Instanzen basierend auf Ihrem Mandantenstandort für Sie aus. Sie können aber entscheiden, wo der Connector installiert werden soll, und die Merkmale der Wartezeit für Ihren Netzwerkdatenverkehr definieren.

Beim Einrichten des Anwendungsproxydiensts sollten Sie die folgenden Fragen stellen:

* Wo befindet sich die App?
* Wo befinden sich die meisten Benutzer, die auf die App zugreifen?
* Wo befindet sich die Anwendungsproxyinstanz?
* Verfügen Sie bereits über eine dedizierte Netzwerkverbindung mit Azure-Rechenzentren, z.B. Azure ExpressRoute oder ein ähnliches VPN?

Der Connector muss sowohl mit Azure als auch mit Ihren Anwendungen kommunizieren (Schritte 2 und 3 im Diagramm zum Datenverkehrsfluss). Daher wirkt sich die Platzierung des Connectors auf die Latenz dieser beiden Verbindungen aus. Beachten Sie beim Auswerten der Anordnung des Connectors die folgenden Punkte:

* Wenn Sie die eingeschränkte Kerberos-Delegierung (KCD) für einmaliges Anmelden verwenden möchten, benötigt der Connector eine „Sichtverbindung“ mit einem Rechenzentrum. Darüber hinaus muss der Connectorserver in die Domäne eingebunden sein.  
* Installieren Sie im Zweifelsfall den Connector näher an der Anwendung.

### <a name="general-approach-to-minimize-latency"></a>Allgemeiner Ansatz zum Verringern der Wartezeit

Sie können die Wartezeit des End-to-End-Datenverkehrs verringern, indem Sie die einzelnen Netzwerkverbindungen optimieren. Jede Verbindung kann wie folgt optimiert werden:

* Reduzieren Sie den Abstand zwischen den beiden Enden des Hops.
* Wählen Sie das richtige zu durchlaufende Netzwerk. Wenn anstelle des öffentlichen Internet beispielsweise ein privates Netzwerk durchlaufen wird, kann dies aufgrund von dedizierten Links schneller gehen.

Wenn Sie für die Verbindung zwischen Azure und Ihrem Unternehmensnetzwerk über eine dedizierte VPN- oder ExpressRoute-Verbindung verfügen, können Sie diese hierfür nutzen.

## <a name="focus-your-optimization-strategy"></a>Präzises Ausrichten Ihrer Optimierungsstrategie

Sie können nicht viel tun, um die Verbindung zwischen Ihren Benutzern und dem Anwendungsproxydienst zu steuern. Benutzer können von einem Heimnetzwerk, einem Lokal oder anderen Ländern/Regionen aus auf Ihre Apps zugreifen. Stattdessen können Sie die Verbindungen vom Anwendungsproxydienst zu den Anwendungsproxyconnectors und den Apps optimieren. Halten Sie sich bei der Einrichtung Ihrer Umgebung an folgende Muster.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Muster 1: Platzieren des Connectors in der Nähe der Anwendung

Platzieren Sie den Connector so nah wie möglich an der Zielanwendung im Kundennetzwerk. Mit dieser Konfiguration wird der Aufwand für Schritt 3 im Topografiediagramm verringert, da der Connector und die Anwendung nicht weit voneinander entfernt sind. 

Wenn Ihr Connector über eine „Sichtlinie“ zum Domänencontroller verfügen muss, ist dieses Muster vorteilhaft. Sehr viele unserer Kunden verwenden dieses Muster, weil es für die meisten Szenarien gut funktioniert. Das Muster kann auch mit Muster 2 kombiniert werden, um den Datenverkehr zwischen dem Dienst und dem Connector zu optimieren.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Muster 2: Nutzen von ExpressRoute mit Microsoft-Peering

Wenn Sie ExpressRoute mit Microsoft-Peering eingerichtet haben, können Sie die schnellere ExpressRoute-Verbindung für den Datenverkehr zwischen Anwendungsproxy und Connector verwenden. Der Connector befindet sich weiterhin in Ihrem Netzwerk, in der Nähe der App.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Muster 3: Nutzen von ExpressRoute mit privatem Peering

Wenn Sie über ein dediziertes VPN- oder ExpressRoute-Setup mit privatem Peering zwischen Azure und Ihrem Unternehmensnetzwerk verfügen, haben Sie eine weitere Option. Bei dieser Konfiguration wird das virtuelle Netzwerk in Azure normalerweise als Erweiterung des Unternehmensnetzwerks angesehen. Sie können den Connector also im Azure-Rechenzentrum installieren und für die Verbindung vom Connector zur App trotzdem die Anforderungen an eine geringe Latenz erfüllen.

Die Wartezeit wird nicht negativ beeinträchtigt, weil der Datenverkehr über eine dedizierte Verbindung fließt. Auch die Latenz zwischen Anwendungsproxydienst und Connector verbessert sich, da der Connector in einem Azure-Rechenzentrum in der Nähe Ihres Azure AD-Mandantenstandorts installiert ist.

![Diagramm zur Installation des Connectors in einem Azure-Datencenter](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andere Ansätze

Der Schwerpunkt dieses Artikels ist zwar die Anordnung des Connectors, aber Sie können die Anordnung der Anwendung auch ändern, um bessere Wartezeiteigenschaften zu erhalten.

Immer mehr Organisationen verschieben ihre Netzwerke in gehostete Umgebungen. Auf diese Weise können sie ihre Apps in einer gehosteten Umgebung anordnen, die gleichzeitig Teil ihres Unternehmensnetzwerks ist und sich noch innerhalb der Domäne befindet. In diesem Fall können die in den vorherigen Abschnitten beschriebenen Muster auf den neuen Anwendungsspeicherort angewendet werden. Informationen zu diesem Ansatz finden Sie unter [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Erwägen Sie außerdem, Ihre Connectors mithilfe von [Connectorgruppen](application-proxy-connector-groups.md) für Apps zu organisieren, die sich an verschiedenen Standorten bzw. in verschiedenen Netzwerken befinden. 

## <a name="common-use-cases"></a>Gängige Anwendungsfälle

In diesem Abschnitt werden einige häufige Szenarien beschrieben. Angenommen, der Azure AD-Mandant (und somit auch der Proxy-Dienstendpunkt) befindet sich in den USA. Die in diesen Anwendungsfällen beschriebenen Aspekte gelten normalerweise auch für andere Regionen weltweit.

Für diese Szenarien werden die einzelnen Verbindungen als „Hop“ bezeichnet und zur Vereinfachung der Erklärungen durchnummeriert:

- **Hop 1**: Vom Benutzer zum Anwendungsproxydienst
- **Hop 2**: Vom Anwendungsproxydienst zum Anwendungsproxyconnector
- **Hop 3**: Vom Anwendungsproxyconnector zur Zielanwendung 

### <a name="use-case-1"></a>Anwendungsfall 1

**Szenario:** Die App befindet sich in den USA im Netzwerk einer Organisation mit Benutzern in derselben Region. Zwischen dem Azure-Datencenter und dem Unternehmensnetzwerk besteht keine ExpressRoute- oder VPN-Verbindung.

**Empfehlung:** Verwenden Sie Muster 1, das im vorherigen Abschnitt beschrieben wird. Erwägen Sie ggf. die Verwendung von ExpressRoute, um die Wartezeit zu verbessern.

Dies ist ein einfaches Muster. Sie optimieren Hop 3, indem Sie den Connector in der Nähe der App anordnen. Dies ist eine natürliche Wahl, da der Connector normalerweise mit einer „Sichtverbindung“ zur App und zum Datencenter installiert wird, um KCD-Vorgänge durchführen zu können.

![Benutzer, Proxy, Connector und App befinden sich in den USA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Anwendungsfall 2

**Szenario:** Die App befindet sich in den USA im Netzwerk einer Organisation, und die Benutzer sind weltweit verteilt. Zwischen dem Azure-Datencenter und dem Unternehmensnetzwerk besteht keine ExpressRoute- oder VPN-Verbindung.

**Empfehlung:** Verwenden Sie Muster 1, das im vorherigen Abschnitt beschrieben wird. 

Das am häufigsten verwendete Muster ist wieder die Optimierung von Hop 3, bei dem Sie den Connector in der Nähe der App anordnen. Hop 3 ist normalerweise nicht mit hohen Kosten verbunden, wenn sich alles in derselben Region abspielt. Die Kosten für Hop 1 können je nach Standort eines Benutzers dagegen höher sein, da Benutzer aus der ganzen Welt auf die Anwendungsproxyinstanz in den USA zugreifen. Hinweis: Alle Proxylösungen verfügen über ähnliche Merkmale, was die weltweite Verteilung von Benutzern betrifft.

![Benutzer weltweit, Proxy, Connector und App jedoch in den USA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Anwendungsfall 3

**Szenario:** Die App befindet sich im Netzwerk einer Organisation in den USA. ExpressRoute mit Microsoft-Peering ist zwischen Azure und dem Unternehmensnetzwerk vorhanden.

**Empfehlung:** Verwenden Sie die Muster 1 und 2, die im vorherigen Abschnitt beschrieben werden.

Platzieren Sie den Connector zuerst so nah wie möglich an der App. Für Hop 2 wird automatisch ExpressRoute verwendet. 

Wenn für die ExpressRoute-Verbindung das Microsoft-Peering verwendet wird, fließt der Datenverkehr zwischen dem Proxy und dem Connector über diese Verbindung. Hop 2 verfügt über eine optimierte Wartezeit.

![ExpressRoute zwischen Proxy und Connector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Anwendungsfall 4

**Szenario:** Die App befindet sich im Netzwerk einer Organisation in den USA. ExpressRoute mit privatem Peering ist zwischen Azure und dem Unternehmensnetzwerk vorhanden.

**Empfehlung:** Verwenden Sie Muster 3, das im vorherigen Abschnitt beschrieben wird.

Platzieren Sie den Connector in dem Azure-Datencenter, das über das private ExpressRoute-Peering mit dem Unternehmensnetzwerk verbunden ist. 

Der Connector kann im Azure-Datencenter angeordnet werden. Da der Connector weiterhin über das private Netzwerk über eine Sichtverbindung mit der Anwendung und dem Datencenter verfügt, bleibt Hop 3 optimiert. Darüber hinaus wird Hop 2 weiter optimiert.

![Connector in einem Azure-Rechenzentrum mit ExpressRoute zwischen Connector und App](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Anwendungsfall 5

**Szenario:** Die App wird im Netzwerk einer Organisation in der EU ausgeführt, während sich die Anwendungsproxyinstanz und die meisten Benutzer in den USA befinden.

**Empfehlung:** Platzieren Sie den Connector in der Nähe der App. Da Benutzer in den USA auf eine Anwendungsproxyinstanz zugreifen, die sich in derselben Region befindet, sind die Kosten für Hop 1 nicht übermäßig hoch. Hop 3 ist optimiert. Erwägen Sie die Verwendung von ExpressRoute zur Optimierung von Hop 2. 

![Benutzer und Proxy in den USA, Connector und App in der EU](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

In dieser Situation können Sie auch eine andere Variante verwenden. Wenn sich die meisten Benutzer der Organisation in den USA befinden, ist die Wahrscheinlichkeit hoch, dass auch Ihr Netzwerk bis in die USA reicht. Platzieren Sie den Connector in den USA, und verwenden Sie die dedizierte interne Unternehmensnetzwerkleitung zur Anwendung in der EU. Auf diese Weise werden die Hops 2 und 3 optimiert.

![Benutzer, Proxy und Connector in den USA, App in der EU](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des Anwendungsproxys](application-proxy-add-on-premises-application.md)
- [Aktivieren der einmaligen Anmeldung](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivieren des bedingten Zugriffs](application-proxy-integrate-with-sharepoint-server.md)
- [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)
