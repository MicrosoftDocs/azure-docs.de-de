---
title: Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen im Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Erläutert das Erstellen und Verwalten von Connectorgruppen im Azure AD-Anwendungsproxy.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22d44b02b3cc25c855361cab17132c46fa04794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783696"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen

Kunden verwenden den Azure AD-Anwendungsproxy für immer mehr Szenarien und Anwendungen. Daher haben wir den Anwendungsproxy durch Aktivieren weiterer Topologien noch flexibler gestaltet. Sie können Anwendungsproxy-Connectorgruppen erstellen, um bestimmte Connectors bestimmten Anwendungen zuzuweisen. Diese Funktion bietet Ihnen mehr Kontrolle sowie Möglichkeiten zur Optimierung der Anwendungsproxybereitstellung. 

Jeder Anwendungsproxyconnector wird einer Connectorgruppe zugewiesen. Alle Connectors, die zur gleichen Connectorgruppe gehören, fungieren als separate Einheit für Hochverfügbarkeit und Lastenausgleich. Alle Connectors gehören zu einer Connectorgruppe. Wenn Sie keine Gruppen erstellen, befinden sich alle Ihre Connectors in einer Standardgruppe. Ihr Administrator kann im Azure-Portal neue Gruppen erstellen und ihnen Connectors zuweisen. 

Alle Anwendungen werden einer Connectorgruppe zugewiesen. Wenn Sie keine Gruppen erstellen, befinden sich alle Ihre Anwendungen in einer Standardgruppe. Wenn Sie jedoch Ihre Connectors gruppieren, können Sie festlegen, dass jede Anwendung mit einer bestimmten Connectorgruppe arbeitet. In diesem Fall bedienen nur die Connectors in dieser Gruppe die Anforderungen der Anwendung. Diese Funktion ist nützlich, wenn Ihre Anwendungen an verschiedenen Standorten gehostet werden. Sie können Connectorgruppen standortabhängig erstellen, sodass Anwendungen immer von Connectors bedient werden, die physisch nahe bei ihnen liegen.

>[!TIP] 
>Wenn Sie über eine umfangreiche Anwendungsproxy-Bereitstellung verfügen, weisen Sie der Standardconnectorgruppe keine Anwendungen zu. Auf diese Weise erhalten neue Connectors keinen Livedatenverkehr, bis Sie sie einer aktiven Connectorgruppe zuordnen. Mit dieser Konfiguration können Sie Connectors auch in einen Ruhezustand versetzen, indem Sie sie wieder in die Standardgruppe verschieben, damit Sie die Wartung ohne Auswirkungen auf Ihre Benutzer ausführen können.

## <a name="prerequisites"></a>Voraussetzungen
Um Connectors zu gruppieren, müssen Sie sicherstellen, dass [mehrere Connectors installiert sind](application-proxy-add-on-premises-application.md). Wenn Sie einen neuen Connector installieren, wird diese automatisch der Connectorgruppe **Standard** hinzugefügt.

## <a name="create-connector-groups"></a>Erstellen von Connectorgruppen
Erstellen Sie mit diesen Schritten beliebig viele Connectorgruppen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Anwendungsproxy** aus.
2. Wählen Sie **Neue Connectorgruppe**. Das Blatt „Neue Connectorgruppe“ wird geöffnet.

   ![Neue Connectorgruppe auswählen](./media/application-proxy-connector-groups/new-group.png)

3. Bennen Sie Ihre neue Connectorgruppe, und verwenden Sie das Dropdownmenü, um auszuwählen, welche Connectoren dieser Gruppe angehören sollen.
4. Wählen Sie **Speichern** aus.

## <a name="assign-applications-to-your-connector-groups"></a>Zuweisen von Anwendungen zu Ihren Connectorgruppen
Verwenden Sie diese Schritte für jede Anwendung, die Sie mit dem Anwendungsproxy veröffentlicht haben. Sie können eine Anwendung einer Connectorgruppe zuweisen, wenn Sie sie zuerst veröffentlichen, oder Sie können mit diesen Schritten jederzeit die Zuweisung ändern.   

1. Klicken Sie im Verwaltungsdashboard Ihres Verzeichnisses auf **Unternehmensanwendungen** > **Alle Anwendungen** > Anwendung, die Sie einer Connectorgruppe zuweisen möchten > **Anwendungsproxy**.
2. Wählen Sie im Dropdownmenü **Connectorgruppe** die Gruppe aus, die die Anwendung verwenden soll.
3. Klicken Sie auf **Speichern** , um die Änderungen zu übernehmen.

## <a name="use-cases-for-connector-groups"></a>Anwendungsfälle für Connectorgruppen 

Connectorgruppen sind unter anderem in folgenden Szenarien nützlich:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Standorte mit mehreren verbundenen Rechenzentren

Viele Organisationen verfügen über mehrere miteinander verbundene Rechenzentren. In diesem Fall soll jeweils möglichst viel Datenverkehr innerhalb des Rechenzentrums verlaufen, da Verbindungen zwischen Rechenzentren für gewöhnlich teuer und langsam sind. Sie können in jedem Rechenzentrum Connectors bereitstellen, die jeweils nur die lokalen Anwendungen bedienen. Mit diesem Vorgehen werden bei völliger Transparenz für den Benutzer die Verbindungen zwischen den Rechenzentren minimiert.

### <a name="applications-installed-on-isolated-networks"></a>In isolierten Netzwerken installierte Anwendungen

Anwendungen können in Netzwerken gehostet werden, die nicht Teil des Hauptnetzwerks des Unternehmens sind. Connectorgruppen können verwendet werden, um dedizierte Connectors für isolierte Netzwerke zu installieren und außerdem die Anwendungen vom Netzwerk zu isolieren. Dies ist üblicherweise der Fall, wenn ein Drittanbieter eine bestimmte Anwendung für Ihre Organisation verwaltet. 

Mithilfe von Connectorgruppen können Sie dedizierte Connectors für diese Netzwerke installieren, die nur bestimmte Anwendungen veröffentlichen. So lässt sich die Anwendungsverwaltung einfacher und sicherer an Drittanbieter auslagern.

### <a name="applications-installed-on-iaas"></a>In IaaS installierte Anwendungen 

Für IaaS-Anwendungen mit Cloudzugriff bieten Connectorgruppen einen gemeinsamen Dienst zum Absichern des Zugriffs auf alle Apps. Connectorgruppen erzeugen keine zusätzliche Abhängigkeit vom Unternehmensnetzwerk und beeinträchtigen nicht das App-Erlebnis. Connectors können in allen Cloudrechenzentren installiert werden und bedienen nur Anwendungen, die sich in diesem Netzwerk befinden. Es können mehrere Connectors installiert werden, um Hochverfügbarkeit zu gewährleisten.

Betrachten Sie z.B. eine Organisation, die über eine Reihe virtueller Computer verfügt, die über ein eigenes, per IaaS gehostetes virtuelles Netzwerk verbunden sind. Damit Mitarbeiter die Anwendungen verwenden können, sind diese privaten Netzwerke über Site-to-Site-VPN mit dem Unternehmensnetzwerk verbunden. Für Mitarbeiter, die vor Ort im lokalen Netzwerk arbeiten, funktioniert dieses Setup gut. Für Remotemitarbeiter ist die Konfiguration allerdings möglicherweise nicht ganz ideal, da sie weitere lokale Infrastruktur zum Weiterleiten des Zugriffs erfordert, wie in der folgenden Abbildung veranschaulicht:

![AzureAD-IaaS-Netzwerk](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Mit Azure AD-Anwendungsproxy-Connectorgruppen können Sie einen gemeinsamen Dienst zum Sichern des Zugriffs auf alle Anwendungen verwenden, ohne weitere Abhängigkeiten in Ihrem Unternehmensnetzwerk einzuführen:

![AzureAD-IaaS – mehrere Cloudanbieter](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Mehrere Gesamtstrukturen – verschiedene Connectorgruppen für jede Gesamtstruktur

Die meisten Kunden, die den Anwendungsproxy bereitgestellt haben, verwenden dessen Funktionen zum einmaligen Anmelden (Single Sign-On, SSO) mithilfe der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD). Zu diesem Zweck müssen die Computer des Connectors einer Domäne beigetreten sein, die die Benutzer an die Anwendung delegieren kann. Die eingeschränkte Kerberos-Delegierung unterstützt gesamtstrukturübergreifende Funktionen. In Unternehmen jedoch, die über Umgebungen mit mehreren Gesamtstrukturen ohne gegenseitige Vertrauensstellung verfügen, kann kein einzelner Connector für alle Gesamtstrukturen verwendet werden. 

In diesem Fall kann für jede Gesamtstruktur ein spezifischer Connector bereitgestellt und für Anwendungen eingerichtet werden, die nur für die Benutzer dieser bestimmten Gesamtstruktur veröffentlicht wurden. Jede Connectorgruppe repräsentiert eine andere Gesamtstruktur. Während der Mandant und der größte Teil der Funktionalität für alle Gesamtstrukturen gleich sind, können Benutzer mithilfe von Azure AD-Gruppen ihren spezifischen Gesamtstrukturanwendungen zugewiesen werden.
 
### <a name="disaster-recovery-sites"></a>Standorte für die Notfallwiederherstellung

Bei Standorten für die Notfallwiederherstellung gibt es zwei verschiedene Herangehensweisen, je nach Implementierung Ihrer Standorte:

* Wenn Ihr Standort für die Notfallwiederherstellung im Aktiv-Aktiv-Modus erstellt wurde und exakt die gleichen Netzwerk- und AD-Einstellungen wie Ihr Hauptstandort aufweist, können Sie die Connectors im Standort für die Notfallwiederherstellung in der gleichen Connectorgruppe wie für den Hauptstandort erstellen. So kann Azure AD Failover selbstständig erkennen.
* Wenn Ihr Standort für die Notfallwiederherstellung von Ihrem Hauptstandort getrennt ist, können Sie im Standort für die Notfallwiederherstellung eine andere Connectorgruppe erstellen. Hierbei gibt es zwei Möglichkeiten: Sie erstellen Sicherungsanwendungen, oder Sie leiten die vorhandene Anwendung nach Bedarf an die Connectorgruppe für die Notfallwiederherstellung weiter.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Bereitstellen von Diensten für mehrere Unternehmen über einen einzigen Mandanten

Es gibt viele verschiedene Möglichkeiten, ein Modell zu implementieren, in dem ein einzelner Dienstanbieter Dienste im Zusammenhang mit Azure AD für mehrere Unternehmen bereitstellt und verwaltet. Connectorgruppen helfen den Administratoren dabei, die Connectors und Anwendungen auf verschiedene Gruppen aufzuteilen. Eine Möglichkeit – die sich für kleinere Unternehmen eignet – besteht darin, einen einzelnen Azure AD-Mandanten einzurichten, während die verschiedenen Unternehmen über einen eigenen Domänennamen und eigene Netzwerke verfügen. Dies gilt auch für M&A-Szenarien (Mergers & Acquisitions) sowie Situationen, in denen eine einzelne IT-Abteilung aus regulatorischen oder geschäftlichen Gründen Dienste für mehrere Unternehmen bereitstellt. 

## <a name="sample-configurations"></a>Beispielkonfigurationen

Im Folgenden finden Sie einige Beispiele, die Sie implementieren können, wie etwa die folgenden Connectorgruppen.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Standardkonfiguration – nicht für Connectorgruppen

Wenn Sie keine Connectorgruppen verwenden, sieht Ihre Konfiguration in etwa folgendermaßen aus:

![Azure AD – keine Connectorgruppen](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Diese Konfiguration ist für kleine Bereitstellungen und Tests ausreichend. Sie funktioniert auch dann gut, wenn Ihre Organisation über eine flache Netzwerktopologie verfügt.
 
### <a name="default-configuration-and-an-isolated-network"></a>Standardkonfiguration und ein isoliertes Netzwerk

Diese Konfiguration ist eine Weiterentwicklung der Standardkonfiguration und umfasst eine spezifische App, die in einem isolierten Netzwerk – z.B. einem virtuellen IaaS-Netzwerk – ausgeführt wird: 

![Azure AD – keine Connectorgruppen](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Empfohlene Konfiguration – mehrere spezifische Gruppen und eine Standardgruppe für den Ruhezustand

Bei der empfohlenen Konfiguration für große und komplexe Organisationen ist die Standardconnectorgruppe als Gruppe eingerichtet, die keine Dienste für Anwendungen bereitstellt und für neu installierte Connectors oder Connectors im Ruhezustand verwendet wird. Die Dienste für alle Anwendungen werden über benutzerdefinierte Connectorgruppen bereitgestellt. Diese Konfiguration unterstützt die oben beschriebenen Szenarien in ihrer ganzen Komplexität.

Im folgenden Beispiel verfügt das Unternehmen über zwei Rechenzentren: A und B. Jeder Standort verfügt über zwei Connectors. In jedem Standort werden unterschiedliche Anwendungen ausgeführt. 

![Azure AD – keine Connectorgruppen](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)
* [Aktivieren der einmaligen Anmeldung](what-is-single-sign-on.md)


