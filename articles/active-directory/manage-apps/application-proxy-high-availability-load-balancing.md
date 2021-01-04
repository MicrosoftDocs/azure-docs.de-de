---
title: Hochverfügbarkeit und Lastenausgleich – Azure AD-Anwendungsproxy
description: Funktionsweise der Verteilung des Datenverkehrs mit Ihrer Anwendungsproxy-Bereitstellung. Enthält Tipps zum Optimieren der Connectorleistung und zum Verwenden des Lastenausgleichs für Back-End-Server.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403fa4cab94ad6149e388b10acccd9d5e7a2b7a8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658161"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Hochverfügbarkeit und Lastenausgleich von Anwendungsproxy-Connectors und -Anwendungen

In diesem Artikel wird erläutert, wie die Verteilung des Datenverkehrs mit Ihrer Anwendungsproxy-Bereitstellung funktioniert. Folgendes wird behandelt:

- Verteilung des Datenverkehrs auf Benutzer und Connectors sowie Tipps zur Optimierung der Connectorleistung

- Fluss des Datenverkehrs zwischen Connectors und Back-End-App-Servern mit Empfehlungen für den Lastenausgleich zwischen mehreren Back-End-Servern

## <a name="traffic-distribution-across-connectors"></a>Verteilung des Datenverkehrs auf Connectors

Connectors richten Verbindungen basierend auf den Prinzipien für Hochverfügbarkeit ein. Es gibt keine Garantie dafür, dass der Datenverkehr immer gleichmäßig auf Connectors verteilt wird, und es besteht keine Sitzungsaffinität. Die Verwendung variiert jedoch, und Anforderungen werden nach dem Zufallsprinzip an die Dienstinstanzen von Anwendungsproxy gesendet. Daher wird der Datenverkehr in der Regel fast gleichmäßig auf die Connectors verteilt. Die folgende Abbildung und die nachfolgenden Schritte veranschaulichen, wie Verbindungen zwischen Benutzern und Connectors hergestellt werden.

![Abbildung zu Verbindungen zwischen Benutzern und Connectors](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Ein Benutzer auf einem Clientgerät versucht, auf eine lokale Anwendung zuzugreifen, die über den Anwendungsproxy veröffentlicht wurde.
2. Die Anforderung durchläuft eine Azure Load Balancer-Instanz, die bestimmt, welche Dienstinstanzen von Anwendungsproxy die Anforderung übernehmen sollen. Pro Region sind Dutzende Instanzen verfügbar, die die Anforderung akzeptieren können. Mit dieser Methode kann der Datenverkehr gleichmäßig auf die Dienstinstanzen verteilt werden.
3. Die Anforderung wird an [Service Bus](../../service-bus-messaging/index.yml) gesendet.
4. Service Bus sendet ein Signal an einen verfügbaren Connector. Der Connector übernimmt die Anforderung dann von Service Bus.
   - In Schritt 2 werden Anforderungen an verschiedene Anwendungsproxy-Dienstinstanzen gesendet, sodass Verbindungen eher mit unterschiedlichen Connectors hergestellt werden. Folglich werden Connectors fast gleichmäßig innerhalb der Gruppe verwendet.
5. Der Connector übergibt die Anforderung an den Back-End-Server der Anwendung. Die Anwendung sendet dann die Antwort an den Connector zurück.
6. Der Connector schließt die Antwort ab, indem eine ausgehende Verbindung mit der Dienstinstanz geöffnet wird, von der die Anforderung stammt. Diese Verbindung wird dann sofort geschlossen. Standardmäßig ist jeder Connector auf 200 gleichzeitige ausgehende Verbindungen beschränkt.
7. Die Antwort wird dann von der Dienstinstanz an den Client zurückgegeben.
8. Bei nachfolgenden Anforderungen von derselben Verbindung werden die oben angegebenen Schritte wiederholt.

Eine Anwendung umfasst häufig viele Ressourcen und öffnet beim Laden mehrere Verbindungen. Jede Verbindung durchläuft die oben beschriebenen Schritte, damit sie einer Dienstinstanz zugeordnet wird. Wählen Sie einen neuen verfügbaren Connector aus, wenn die Verbindung noch nicht mit einem Connector gekoppelt wurde.


## <a name="best-practices-for-high-availability-of-connectors"></a>Bewährte Methoden für die Hochverfügbarkeit von Connectors

- Aufgrund der Art und Weise, wie Datenverkehr für die Hochverfügbarkeit auf Connectors verteilt wird, ist es unerlässlich, dass in einer Connectorgruppe immer mindestens zwei Connectors enthalten sind. Drei Connectors werden bevorzugt, um einen zusätzlichen Puffer zwischen Connectors bereitzustellen. Informationen zum Ermitteln der richtigen Anzahl der benötigten Connectors finden Sie in der Dokumentation zur Kapazitätsplanung.

- Platzieren Sie Connectors für verschiedene ausgehende Verbindungen, um einen Single Point of Failure zu vermeiden. Wenn Connectors dieselbe ausgehende Verbindung verwenden, kann sich ein Netzwerkproblem bei der Verbindung auf alle Connectors auswirken, die die Verbindung verwenden.

- Vermeiden Sie die Erzwingung des Neustarts von Connectors, wenn sie mit Produktionsanwendungen verbunden sind. Dies kann sich negativ auf die Verteilung des Datenverkehrs auf Connectors auswirken. Das Neustarten von Connectors führt dazu, dass mehr Connectors nicht mehr verfügbar sind und dass Verbindungen mit dem verbleibenden verfügbaren Connector erzwungen werden. Dies führt zu einer anfänglich ungleichmäßigen Verwendung der Connectors.

- Vermeiden Sie alle Arten von Inline-Untersuchungen der ausgehenden TLS-Kommunikation zwischen Connectors und Azure. Diese Art der Inline-Untersuchung führt zu einer Verschlechterung des Kommunikationsflusses.

- Achten Sie darauf, dass automatische Updates für die Connectors ausgeführt werden. Wenn Application Proxy Connector Updater ausgeführt wird, werden die Connectors automatisch aktualisiert und erhalten die neuesten Upgrades. Wenn der Connector Updater-Dienst auf Ihrem Server nicht angezeigt wird, müssen Sie den Connector neu installieren, um Updates zu erhalten.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Datenverkehrsfluss zwischen Connectors und Back-End-Anwendungsservern

Ein weiterer wichtiger Bereich, in dem die Hochverfügbarkeit eine wichtige Rolle spielt, ist die Verbindung zwischen Connectors und den Back-End-Servern. Wenn eine Anwendung über Azure AD-Anwendungsproxy veröffentlicht wird, fließt der gesamte Datenverkehr von den Benutzern zu den Anwendungen über drei Hops:

1. Der Benutzer stellt eine Verbindung mit dem öffentlichen Endpunkt des Azure AD-Anwendungsproxy-Diensts in Azure her. Die Verbindung wird zwischen der ursprünglichen Client-IP-Adresse (öffentlich) des Clients und der IP-Adresse des Anwendungsproxy-Endpunkts hergestellt.
2. Der Anwendungsproxy-Connector ruft die HTTP-Anforderung des Clients vom Anwendungsproxy-Dienst ab.
3. Der Anwendungsproxy-Connector stellt eine Verbindung mit der Zielanwendung her. Der Connector verwendet zum Herstellen der Verbindung seine eigene IP-Adresse.

![Abbildung: Benutzer stellt über Anwendungsproxy eine Verbindung mit einer Anwendung her](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Überlegungen zum Headerfeld „X-Forwarded-For“
In einigen Fällen (z. B bei der Überwachung oder beim Lastenausgleich) muss die ursprüngliche IP-Adresse des externen Clients in der lokalen Umgebung freigegeben werden. Um diese Anforderung zu erfüllen, fügt der Azure AD-Anwendungsproxy-Connector der HTTP-Anforderung das Headerfeld „X-Forwarded-For“ mit der ursprünglichen Client-IP-Adresse (öffentlich) hinzu. Die Informationen können dann vom entsprechenden Netzwerkgerät (Load Balancer, Firewall) oder von dem Webserver oder der Back-End-Anwendung gelesen und verwendet werden.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Bewährte Methoden für den Lastenausgleich zwischen mehreren App-Servern
Wenn die Connectorgruppe, die der Anwendungsproxy-Anwendung zugewiesen ist, zwei oder mehr Connectors umfasst und Sie die Back-End-Webanwendung auf mehreren Servern (Serverfarm) ausführen, ist eine gute Strategie für den Lastenausgleich erforderlich. Mit einer guten Strategie wird sichergestellt, dass Server Clientanforderungen gleichmäßig übernehmen, und eine Über- oder Unterauslastung von Servern in der Serverfarm vermieden.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Szenario 1: Back-End-Anwendung erfordert keine Sitzungspersistenz
Im einfachsten Szenario erfordert die Back-End-Webanwendung keine Sitzungsbindung (Sitzungspersistenz). Alle Anforderungen des Benutzers können von jeder Back-End-Anwendungsinstanz in der Serverfarm verarbeitet werden. Sie können einen Lastenausgleich der Ebene 4 verwenden und ohne Affinität konfigurieren. Mögliche Optionen sind Microsoft-Netzwerklastenausgleich und Azure Load Balancer oder ein Lastenausgleich von einem anderen Anbieter. Alternativ kann Roundrobin-DNS konfiguriert werden.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Szenario 2: Back-End-Anwendung erfordert Sitzungspersistenz
In diesem Szenario erfordert die Back-End-Webanwendung die Sitzungsbindung (Sitzungspersistenz) während der authentifizierten Sitzung. Alle Anforderungen vom Benutzer müssen über die Back-End-Anwendungsinstanz verarbeitet werden, die auf dem gleichen Server in der Serverfarm ausgeführt wird.
Dieses Szenario kann komplizierter sein, da der Client in der Regel mehrere Verbindungen mit dem Anwendungsproxydienst herstellt. Anforderungen über verschiedene Verbindungen werden möglicherweise in unterschiedlichen Connectors und Servern in der Farm empfangen. Da jeder Connector seine eigene IP-Adresse für diese Kommunikation verwendet, kann der Load Balancer die Sitzungsbindung nicht basierend auf der IP-Adresse der Connectors sicherstellen. Auch die Quell-IP-Affinität kann nicht verwendet werden.
Es folgen einige Optionen für Szenario 2:

- Option 1: Basieren Sie die Sitzungspersistenz auf einem Sitzungscookie, das vom Lastenausgleich festgelegt wird. Diese Option wird empfohlen, da die Last gleichmäßiger auf die Back-End-Server verteilt werden kann. Hierfür ist ein Lastenausgleich auf Schicht 7 mit dieser Funktion erforderlich, der den HTTP-Datenverkehr verarbeiten und die TLS-Verbindung beenden kann. Sie können Azure Application Gateway (Sitzungsaffinität) oder einen Lastenausgleich von einem anderen Anbieter verwenden.

- Option 2: Basieren Sie die Sitzungspersistenz auf dem Headerfeld „X-Forwarded-For“. Für diese Option ist ein Lastenausgleich auf Schicht 7 mit dieser Funktion erforderlich, der den HTTP-Datenverkehr verarbeiten und die TLS-Verbindung beenden kann.  

- Option 3: Konfigurieren Sie die Back-End-Anwendung so, dass keine Sitzungspersistenz erforderlich ist.

Informationen zu den Anforderungen für den Lastenausgleich der Back-End-Anwendung finden Sie in der Dokumentation des Softwareherstellers.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des Anwendungsproxys](application-proxy-add-on-premises-application.md)
- [Aktivieren der einmaligen Anmeldung](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivieren des bedingten Zugriffs](application-proxy-integrate-with-sharepoint-server.md)
- [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)
- [Erfahren Sie, wie die Azure AD-Architektur Hochverfügbarkeit unterstützt](../fundamentals/active-directory-architecture.md).