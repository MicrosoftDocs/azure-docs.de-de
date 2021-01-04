---
title: Planen der Bereitstellung eines Azure Active Directory-Anwendungsproxys
description: Enthält eine umfassende Anleitung zur Planung der Bereitstellung eines Anwendungsproxys in Ihrer Organisation.
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 9815237617566eda4759ecc31718786b6cb2cac8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96176051"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planen der Bereitstellung eines Azure AD-Anwendungsproxys

Der Azure AD-Anwendungsproxy (Azure Active Directory) ist eine sichere und kostengünstige Lösung für den Remotezugriff auf lokale Anwendungen. „Cloud First“-Organisationen erhalten hiermit einen direkten Weg zur Verwaltung des Zugriffs auf lokale Legacyanwendungen, für die noch keine modernen Protokolle verwendet werden können. Zusätzliche einführende Informationen finden Sie unter [Was ist der Anwendungsproxy?](./application-proxy.md).

Der Anwendungsproxy wird empfohlen, um Remotebenutzern Zugriff auf interne Ressourcen zu gewähren. Durch den Anwendungsproxy entfällt die Notwendigkeit eines VPN oder Reverseproxys für solche Anwendungsfälle mit Remotezugriff. Er ist nicht für Benutzer im Unternehmensnetzwerk bestimmt. Wenn Benutzer den Anwendungsproxy für den Zugriff auf das Intranet verwenden, können Leistungsprobleme auftreten.

Dieser Artikel enthält die Ressourcen, die Sie zum Planen, Betreiben und Verwalten des Azure AD-Anwendungsproxys benötigen.

## <a name="plan-your-implementation"></a>Planen Ihrer Implementierung

Im folgenden Abschnitt erhalten Sie einen allgemeinen Überblick über die wichtigen Planungselemente, die Sie für die effiziente Durchführung einer Bereitstellung kennen sollten.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen die folgenden Voraussetzungen erfüllen, bevor Sie mit der Implementierung beginnen. Weitere Informationen zur Einrichtung Ihrer Umgebung, einschließlich dieser Voraussetzungen, finden Sie in [diesem Tutorial](application-proxy-add-on-premises-application.md).

* **Connectors**: Connectors sind einfache Agents, die Sie auf folgenden Komponenten bereitstellen können:
   * Lokale physische Hardware
   * In einer Hypervisor-Lösung gehostete VM
   * In Azure gehostete VM für die ausgehende Verbindung zum Anwendungsproxydienst

* Eine ausführlichere Übersicht finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md).

     * Auf den Connectorcomputern muss [TLS 1.2 aktiviert](application-proxy-add-on-premises-application.md) werden, bevor die Connectors installiert werden.

     * Stellen Sie die Connectors nach Möglichkeit in [demselben Netzwerk](application-proxy-network-topology.md) bereit, und führen Sie eine Segmentierung nach Back-End-Webanwendungsservern durch. Es empfiehlt sich, vor dem Bereitstellen der Connectors eine Anwendungsermittlung durchzuführen.
     * Um Hochverfügbarkeit und Skalierung zu gewährleisten, sollten jeder Connectorgruppe mindestens zwei Connectors zugewiesen werden. Mit drei Connectors sind Sie in dem Fall, dass Sie einen Computer warten müssen, optimal aufgestellt. Orientieren Sie sich bei der Auswahl des Computertyps, auf dem Sie Connectors installieren, an der [Tabelle zur Connectorkapazität](./application-proxy-connectors.md#capacity-planning). Je größer der Computer ist, desto mehr Pufferkapazität und Leistung bietet der Connector.

* **Einstellungen für Netzwerkzugriff**: Azure AD-Anwendungsproxyconnectors [stellen per HTTPS (TCP-Port 443) und HTTP (TCP-Port 80) eine Verbindung mit Azure her](application-proxy-add-on-premises-application.md).

   * Die Beendigung von TLS-Datenverkehr für Connectors wird nicht unterstützt. Hierdurch wird für Connectors verhindert, dass diese einen sicheren Kanal zu ihren entsprechenden Azure-Anwendungsproxy-Endpunkten einrichten.

   * Vermeiden Sie alle Arten von Inline-Untersuchungen der ausgehenden TLS-Kommunikation zwischen Connectors und Azure. Die interne Untersuchung zwischen einem Connector und Back-End-Anwendungen ist möglich, aber dies kann zu einer Beeinträchtigung der Benutzerfreundlichkeit führen. Aus diesem Grund wird diese Vorgehensweise nicht empfohlen.

   * Ein Lastenausgleich der Connectors selbst wird ebenfalls nicht unterstützt und ist auch nicht erforderlich.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Wichtige Aspekte vor der Konfiguration des Azure AD-Anwendungsproxys

Die folgenden grundlegenden Anforderungen müssen erfüllt sein, um den Azure AD-Anwendungsproxy konfigurieren und implementieren zu können.

*  **Azure-Onboarding**: Vor dem Bereitstellen des Anwendungsproxys müssen Benutzeridentitäten aus einem lokalen Verzeichnis synchronisiert oder direkt auf Ihren Azure AD-Mandanten erstellt werden. Mithilfe der Identitätssynchronisierung können Benutzer von Azure AD vorab authentifiziert werden, bevor ihnen Zugriff auf per Anwendungsproxy veröffentlichte Anwendungen gewährt wird. Außerdem können die benötigten Benutzer-ID-Informationen für einmaliges Anmelden (Single Sign-On, SSO) bereitgestellt werden.

* **Anforderungen für bedingten Zugriff**: Die Verwendung des Anwendungsproxys für den Zugriff auf das Intranet wird nicht empfohlen, da dies die Wartezeit erhöht und sich nachteilig für die Benutzer auswirkt. Wir empfehlen, den Anwendungsproxy mit Vorauthentifizierung und Richtlinien für bedingten Zugriff für den Remotezugriff über das Internet zu verwenden.  Wenn Sie bedingten Zugriff für das Intranet bereitstellen möchten, können Sie Ihre Anwendungen modernisieren, sodass sie die Authentifizierung direkt mit Azure Active Directory (AAD) durchführen können. Weitere Informationen finden Sie unter [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](./migration-resources.md).

* **Diensteinschränkungen**: Zum Schutz vor übermäßigem Ressourcenverbrauch durch einzelne Mandanten gelten bestimmte Drosselungsgrenzwerte pro Anwendung und Mandant. Die Grenzwerte finden Sie unter [Dienst- und andere Einschränkungen für Azure AD](../enterprise-users/directory-service-limits-restrictions.md). Die Drosselungsgrenzwerte basieren auf einem Richtwert, der weit über dem typischen Nutzungsvolumen liegt und für die meisten Bereitstellungen ausreichend Puffer bietet.

* **Öffentliches Zertifikat**: Wenn Sie benutzerdefinierte Domänennamen verwenden, müssen Sie ein TLS/SSL-Zertifikat beschaffen. Je nach den Anforderungen Ihrer Organisation kann die Beschaffung eines Zertifikats einige Zeit dauern. Wir empfehlen Ihnen daher, sich so früh wie möglich darum zu kümmern. Für den Azure-Anwendungsproxy werden Zertifikate vom Typ „Standard“, [„Platzhalter“](application-proxy-wildcard.md) oder „SAN-basiert“ unterstützt. Weitere Einzelheiten finden Sie unter [Konfigurieren von benutzerdefinierten Domänen per Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).

* **Domänenanforderungen**: Das einmalige Anmelden bei Ihren veröffentlichten Anwendungen mithilfe der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) erfordert, dass der Server, auf dem der Connector ausgeführt wird, und der Server, auf dem die App ausgeführt wird, in Domänen eingebunden sind und derselben Domäne oder vertrauenswürdigen Domänen angehören.
Ausführliche Informationen zu diesem Thema finden Sie unter [Eingeschränkte Delegierung von Kerberos für die einmalige Anmeldung zu Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md). Der Connectordienst wird im Kontext des lokalen Systems ausgeführt und sollte nicht für die Verwendung einer benutzerdefinierten Identität konfiguriert werden.

* **DNS-Einträge für URLs**

   * Vor der Verwendung von benutzerdefinierten Domänen im Anwendungsproxy müssen Sie im öffentlichen DNS einen CNAME-Eintrag erstellen, damit Clients die benutzerdefinierte externe URL in die vordefinierte Anwendungsproxyadresse auflösen können. Wenn für eine Anwendung, für die eine benutzerdefinierte Domäne genutzt wird, kein CNAME-Eintrag erstellt wird, können Remotebenutzer keine Verbindung mit der Anwendung herstellen. Die erforderlichen Schritte zum Hinzufügen von CNAME-Einträgen können von DNS-Anbieter zu DNS-Anbieter variieren. Es ist daher ratsam, dass Sie sich über das [Verwalten von DNS-Einträgen und -Ressourceneintragssätzen im Azure-Portal](../../dns/dns-operations-recordsets-portal.md) informieren.

   * Entsprechend müssen Connectorhosts die interne URL von veröffentlichten Anwendungen auflösen können.

* **Administratorrechte und -rollen**

   * Für die **Connectorinstallation** werden lokale Administratorrechte für den Windows-Server benötigt, auf dem die Installation durchgeführt wird. Darüber hinaus ist für die Authentifizierung und Registrierung der Connectorinstanz bei Ihrem Azure AD-Mandanten mindestens die Rolle *Anwendungsadministrator* erforderlich.

   * Für die **Anwendungsveröffentlichung und -verwaltung** wird die Rolle *Anwendungsadministrator* benötigt. Anwendungsadministratoren können alle Anwendungen im Verzeichnis verwalten, z. B. für Registrierungen, SSO-Einstellungen, Zuweisungen und Lizenzierung für Benutzer und Gruppen, Anwendungsproxyeinstellungen und die Einwilligung. Durch diese Rolle kann der bedingte Zugriff nicht verwaltet werden. Die Rolle *Cloudanwendungsadministrator* verfügt über alle Berechtigungen des Anwendungsadministrators, mit Ausnahme der Verwaltung von Anwendungsproxyeinstellungen.

* **Lizenzierung**: Der Anwendungsproxy ist über ein Azure AD Premium-Abonnement verfügbar. Eine vollständige Liste mit Lizenzierungsoptionen und -funktionen finden Sie auf der [Seite mit den Azure Active Directory-Preisen](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="application-discovery"></a>Anwendungsermittlung

Erstellen Sie eine Bestandsliste mit allen relevanten Anwendungen, die per Anwendungsproxy veröffentlicht werden, indem Sie die folgenden Informationen erfassen:

| Informationstyp| Zu erfassende Information |
|---|---|
| Diensttyp| Beispiel: SharePoint, SAP, CRM, benutzerdefinierte Webanwendung, API |
| Anwendungsplattform | Beispiel: Windows IIS, Apache für Linux, Tomcat, NGINX |
| Domänenmitgliedschaft| Vollqualifizierter Domänenname (FQDN) des Webservers |
| Speicherort der Anwendung | Ort des Webservers bzw. der Farm in Ihrer Infrastruktur |
| Interner Zugriff | Die genaue URL für den internen Zugriff auf die Anwendung. <br> Bei einer Farm: Welche Art von Lastenausgleich wird verwendet? <br> Nutzt die Anwendung nicht nur eigene Inhalte, sondern auch aus anderen Quellen?<br> Ermitteln Sie, ob die Anwendung über WebSockets betrieben wird. |
| Externer Zugriff | Die Anbieterlösung, für die die Anwendung extern bereits verfügbar gemacht wurde. <br> Die URL, die Sie für den externen Zugriff verwenden möchten. Bei Verwendung von SharePoint: Stellen Sie sicher, dass alternative Zugriffszuordnungen gemäß [dieser Anleitung](/SharePoint/administration/configure-alternate-access-mappings) konfiguriert werden. Wenn dies nicht erfolgt, müssen Sie externe URLs definieren. |
| Öffentliches Zertifikat | Beschaffen Sie bei Verwendung einer benutzerdefinierten Domäne ein Zertifikat mit einem entsprechenden Antragstellernamen. Bei Vorhandensein eines Zertifikats: Notieren Sie sich die Seriennummer und den Ort, von dem es beschafft werden kann. |
| Authentifizierungsart| Die Authentifizierungsart, die von der Anwendung unterstützt wird, z. B. Einfach, Integrierte Windows-Authentifizierung, Formularbasiert, Headerbasiert und Ansprüche. <br>Wenn die Anwendung für die Ausführung unter einem bestimmten Domänenkonto konfiguriert ist, sollten Sie sich den vollqualifizierten Domänennamen (FQDN) des Dienstkontos notieren.<br> Notieren Sie sich bei „SAML-basiert“ den Bezeichner und die Antwort-URLs. <br> Bei „Headerbasiert“: die Anbieterlösung und die spezifische Anforderung für den Umgang mit der Authentifizierungsart. |
| Name der Connectorgruppe | Der logische Name für die Gruppe mit den Connectors, die angegeben werden, um die Kommunikation und das einmalige Anmelden für diese Back-End-Anwendung bereitzustellen. |
| Benutzer-/Gruppenzugriff | Die Benutzer oder Benutzergruppen, denen externer Zugriff auf die Anwendung gewährt wird. |
| Zusätzliche Anforderungen | Notieren Sie sich alle zusätzlichen Anforderungen in Bezug auf den Remotezugriff oder die Sicherheit, die bei der Veröffentlichung der Anwendung berücksichtigt werden sollten. |

Sie können dieses [Arbeitsblatt für den Anwendungsbestand](https://aka.ms/appdiscovery) herunterladen, um eine Bestandsübersicht für Ihre Apps zu erstellen.

### <a name="define-organizational-requirements"></a>Definieren der Anforderungen der Organisation

Für die folgenden Bereiche sollten Sie die geschäftlichen Anforderungen Ihrer Organisation definieren. Jeder Bereich enthält Beispiele für Anforderungen.

 **zugreifen**

* Remotebenutzer mit in Domänen oder in Azure AD eingebundenen Geräten können sicher per nahtlosem einmaligen Anmelden auf veröffentlichte Anwendungen zugreifen.

* Remotebenutzer mit genehmigten persönlichen Geräten können sicher auf veröffentlichte Anwendungen zugreifen, sofern sie für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) registriert sind und die Microsoft Authenticator-App auf ihrem Mobiltelefon als Authentifizierungsmethode registriert haben.

**Governance**

* Administratoren können den Lebenszyklus von Benutzerzuweisungen zu Anwendungen, die per Anwendungsproxy veröffentlicht werden, definieren und überwachen.

**Security**

* Nur Benutzer, die Anwendungen über die Gruppenmitgliedschaft oder individuell zugewiesen sind, können auf diese Anwendungen zugreifen.

**Leistung**

* Im Vergleich mit dem Zugriff auf die Anwendung über das interne Netzwerk kommt es nicht zu einer Verschlechterung der Anwendungsleistung.

**Benutzerfreundlichkeit**

* Benutzer wissen, wie sie auf ihre Anwendungen zugreifen können, weil sie auf allen Geräteplattformen vertraute Unternehmens-URLs verwenden können.

**Überwachung**
* Administratoren können die Zugriffsaktivitäten von Benutzern überwachen.


### <a name="best-practices-for-a-pilot"></a>Bewährte Methoden für einen Pilotversuch

Ermitteln Sie die erforderliche Zeit und den Aufwand für die vollständige Einrichtung einer einzelnen Anwendung für den Remotezugriff mit einmaligem Anmelden (SSO). Führen Sie hierzu einen Pilotversuch durch, bei dem die anfängliche Ermittlung, die Veröffentlichung und ein allgemeiner Test berücksichtigt werden. Die Verwendung einer einfachen IIS-basierten Webanwendung, die für die integrierte Windows-Authentifizierung (Integrated Windows Authentication, IWA) vorkonfiguriert ist, ermöglicht die Erstellung einer Baseline. Für einen erfolgreichen Pilotversuch in Bezug auf den Remotezugriff und einmaliges Anmelden ist der Aufwand hierbei nämlich nur gering.

Mit den folgenden Entwurfselementen erhöhen sich die Erfolgschancen Ihrer direkten Pilotimplementierung in einem Produktionsmandanten.

**Connectorverwaltung**:

* Connectors spielen eine wichtige Rolle, wenn es um die Bereitstellung des lokalen Kommunikationskanals für Ihre Anwendungen geht. Die Verwendung der Connectorgruppe **Standard** ist für anfängliche Pilottests für veröffentlichte Anwendungen geeignet, bevor diese in der Produktion in Betrieb genommen werden. Anwendungen, die erfolgreich getestet wurden, können dann in Connectorgruppen in der Produktion verlagert werden.

**Anwendungsverwaltung**:

* Ihre Mitarbeiter können sich am besten an eine externe URL erinnern, wenn diese vertraut und relevant ist. Vermeiden Sie das Veröffentlichen Ihrer Anwendung über unsere vordefinierten Suffixe „msappproxy.net“ oder „onmicrosoft.com“. Geben Sie stattdessen eine vertraute verifizierte Domäne der obersten Ebene an, die mit einem logischen Hostnamen als Präfix versehen ist, z. B. *intranet.<kundendomäne>.com*.

* Beschränken Sie die Sichtbarkeit des Symbols für die Pilotanwendung auf eine Pilotgruppe, indem Sie das Startsymbol im MyApps-Portal von Azure ausblenden. Wenn alles für die Produktion bereit ist, können Sie die App für die gewünschte Zielgruppe freigeben: entweder auf demselben Mandanten für die Präproduktion, oder indem Sie die Anwendung auch auf Ihrem Produktionsmandanten veröffentlichen.

**Einstellungen für einmaliges Anmelden**: Einige Einstellungen für einmaliges Anmelden verfügen über spezifische Abhängigkeiten, deren Einrichtung zeitaufwendig sein kann. Vermeiden Sie daher Verzögerungen bei der Änderungssteuerung, indem Sie sicherstellen, dass die Einrichtung der Abhängigkeiten rechtzeitig geregelt wird. Dies umfasst auch das Einbinden von Connectorhosts in die Domäne, um einmaliges Anmelden per eingeschränkter Kerberos-Delegierung (KCD) durchzuführen, sowie das Durchführen anderer zeitaufwendiger Aktivitäten. Ein Beispiel hierfür ist das Einrichten einer PingAccess-Instanz, falls headerbasiertes einmaliges Anmelden benötigt wird.

**TLS zwischen Connectorhost und Zielanwendung**: Da die Sicherheit an oberster Stelle steht, sollte zwischen dem Connectorhost und den Zielanwendungen immer TLS verwendet werden. Dies gilt vor allem, wenn die Webanwendung für die formularbasierte Authentifizierung (FBA) konfiguriert wird, da die Benutzeranmeldeinformationen dann auf effektive Weise als Klartext übertragen werden.

**Inkrementelles Implementieren und Testen jedes Schritts**:
Führen Sie nach dem Veröffentlichen einer Anwendung einen grundlegenden Funktionstest durch, um sicherzustellen, dass alle Benutzer- und Geschäftsanforderungen erfüllt sind, indem Sie die folgenden Schritte ausführen:

1. Testen und überprüfen Sie den allgemeinen Zugriff auf die Webanwendung bei deaktivierter Vorauthentifizierung.
2. Wenn dies erfolgreich verläuft, können Sie die Vorauthentifizierung aktivieren und Benutzer und Gruppen zuweisen. Testen und überprüfen Sie den Zugriff.
3. Fügen Sie anschließend die SSO-Methode für Ihre Anwendung hinzu, und testen Sie erneut, um den Zugriff zu überprüfen.
4. Wenden Sie je nach Bedarf Richtlinien für den bedingten Zugriff und MFA an. Testen und überprüfen Sie den Zugriff.

**Problembehandlungstools**: Beginnen Sie bei der Problembehandlung immer mit der Überprüfung des Zugriffs auf die veröffentlichte Anwendung mit dem Browser auf dem Connectorhost, und vergewissern Sie sich, dass die Anwendung wie erwartet funktioniert. Je einfacher Ihr Setup ist, desto leichter kann die Grundursache ermittelt werden. Erwägen Sie daher, Probleme bei einer Minimalkonfiguration zu reproduzieren, z. B. indem Sie nur einen Connector und kein SSO verwenden. In einigen Fällen können Webdebugtools, z. B. Fiddler von Telerik, unerlässlich sein, um Probleme mit dem Zugriff oder dem Inhalt in Anwendungen zu behandeln, auf die über einen Proxy zugegriffen wird. Fiddler kann auch als Proxy fungieren und zum Verfolgen und Debuggen von Datenverkehr für mobile Plattformen, z. B. iOS und Android, sowie für praktisch alle anderen Komponenten verwendet werden, für die das Routing über einen Proxy konfiguriert werden kann. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](application-proxy-troubleshoot.md).

## <a name="implement-your-solution"></a>Implementieren Ihrer Lösung

### <a name="deploy-application-proxy"></a>Bereitstellen des Anwendungsproxys

Die Schritte zum Bereitstellen Ihres Anwendungsproxys sind in [diesem Tutorial zum Hinzufügen einer lokalen Anwendung für den Remotezugriff](application-proxy-add-on-premises-application.md) beschrieben. Falls die Installation nicht erfolgreich ist, wählen Sie im Portal die Option für die **Problembehandlung für den Anwendungsproxy** aus, oder verwenden Sie den Leitfaden für [Probleme beim Installieren des Anwendungsproxy-Agent-Connectors](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Veröffentlichen von Anwendungen mit dem Anwendungsproxy

Beim Veröffentlichen von Anwendungen wird vorausgesetzt, dass Sie alle Voraussetzungen erfüllt haben und über mehrere Connectors verfügen, die auf der Seite für den Anwendungsproxy als registriert und aktiv angezeigt werden.

Sie können Anwendungen auch mit [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview) veröffentlichen.

Unten sind einige bewährte Methoden angegeben, an die Sie sich beim Veröffentlichen einer Anwendung halten sollten:

* **Verwenden von Connectorgruppen**: Weisen Sie eine Connectorgruppe zu, die für die Veröffentlichung der einzelnen Anwendungen angegeben wurde. Um Hochverfügbarkeit und Skalierung zu gewährleisten, sollten jeder Connectorgruppe mindestens zwei Connectors zugewiesen werden. Mit drei Connectors sind Sie in dem Fall, dass Sie einen Computer warten müssen, optimal aufgestellt. Lesen Sie auch den Artikel [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md). Dort wird beschrieben, wie Sie Ihre Connectors mithilfe von Connectorgruppen nach Netzwerk oder Standort segmentieren.

* **Festlegen des Timeouts für die Back-End-Anwendung**: Diese Einstellung ist in Szenarien hilfreich, in denen die Anwendung für die Verarbeitung einer Clienttransaktion ggf. mehr als 75 Sekunden benötigt. Ein Beispiel hierfür ist der Fall, in dem ein Client eine Abfrage an eine Webanwendung sendet, die als Front-End für eine Datenbank fungiert. Das Front-End sendet diese Abfrage an seinen Back-End-Datenbankserver und wartet auf eine Antwort. Wenn die Antwort eingeht, ist für die Konversation auf Clientseite bereits ein Timeout aufgetreten. Wenn Sie die Timeouteinstellung auf „Lang“ festlegen, beträgt der Zeitraum 180 Sekunden, damit auch längere Transaktionen abgeschlossen werden können.

* **Verwenden von geeigneten Cookietypen**

   * **Nur-HTTP-Cookie**: Sorgt für zusätzliche Sicherheit, indem der Anwendungsproxy das HTTPOnly-Flag in den set-cookie-HTTP-Antwortheader einfügt. Diese Einstellung trägt dazu bei, Exploits abzuwehren, z. B. Cross-Site Scripting (XSS). Behalten Sie bei dieser Einstellung für Clients bzw. Benutzer-Agents, die keinen Zugriff auf das Sitzungscookie benötigen, die Option „Nein“ bei. Beispiel: Verbindungsherstellung eines RDP/MTSC-Clients mit einem Remotedesktopgateway, das per Anwendungsproxy veröffentlicht wird.

   * **Sicheres Cookie**: Wenn ein Cookie mit dem Attribut „Secure“ festgelegt wird, fügt der Benutzer-Agent (clientseitige App) das Cookie nur dann in HTTP-Anforderungen ein, wenn die Anforderung über einen per TLS geschützten Kanal übertragen wird. Auf diese Weise wird das Risiko verringert, dass ein Cookie über Klartextkanäle kompromittiert werden kann.

   * **Beständiges Cookie**: Ermöglicht es, dass das Sitzungscookie des Anwendungsproxys zwischen Browserschließungen beibehalten wird, indem es gültig bleibt, bis es entweder abläuft oder gelöscht wird. Dies wird für Szenarien genutzt, in denen eine umfangreiche Anwendung, z. B. Office, auf ein Dokument in einer veröffentlichten Webanwendung zugreift, ohne dass der Benutzer erneut zum Authentifizieren aufgefordert wird. Gehen Sie beim Aktivieren aber mit Bedacht vor, da es bei beständigen Cookies passieren kann, dass für einen Dienst der unberechtigte Zugriff ermöglicht wird, falls nicht zusätzlich weitere Kontrollen eingebaut werden. Diese Einstellung sollte nur für ältere Anwendungen verwendet werden, bei denen keine Cookies zwischen Prozessen freigegeben werden können. Es ist besser, Ihre Anwendung zur gemeinsamen Nutzung von Cookies zwischen Prozessen zu aktualisieren, als diese Einstellung zu verwenden.

* **Übersetzen von URLs in Headern**: Sie aktivieren dies für Szenarien, in denen das interne DNS nicht so konfiguriert werden kann, dass es mit dem öffentlichen Namespace der Organisation übereinstimmt (auch als „geteiltes DNS“ bezeichnet). Behalten Sie für diesen Wert die Einstellung „Ja“ bei, sofern für Ihre Anwendung nicht der ursprüngliche Hostheader in der Clientanforderung benötigt wird. Die Alternative besteht darin, für den Connector den FQDN in der internen URL zu verwenden, um den eigentlichen Datenverkehr weiterzuleiten, und den FQDN in der externen URL als Hostheader. In den meisten Fällen sollte diese Alternative die gewohnte Funktion der Anwendung ermöglichen, wenn von einem Remotestandort darauf zugegriffen wird. Für Ihre Benutzer geht aber der Vorteil einer übereinstimmenden internen und externen URL verloren.

* **URLs übersetzen in Anwendungstext:** Aktivieren Sie für eine App die Option zum Übersetzen von Links im Anwendungstext, wenn Sie möchten, dass Links dieser App in Antworten an den Client übersetzt werden. Wenn diese Funktion aktiviert ist, werden alle internen Links, die vom Anwendungsproxy in HTML- und CSS-Antworten an Clients gefunden werden, bestmöglich übersetzt. Dies ist hilfreich bei der Veröffentlichung von Apps, die im Inhalt entweder hartcodierte absolute Links oder NetBIOS-Kurznamenlinks aufweisen, oder von Apps mit Inhalten, die Links zu anderen lokalen Anwendungen enthalten.

Aktivieren Sie in Szenarien, in denen eine veröffentlichte App Links zu anderen veröffentlichten Apps enthält, die Linkübersetzung für jede Anwendung, damit Sie die Benutzeroberfläche auf App-Ebene steuern können.

Beispiel: Angenommen, drei Ihrer Anwendungen werden über Anwendungsproxy veröffentlicht und verweisen mit Links aufeinander: Vorteile, Ausgaben und Reisen und eine vierte App für Feedback, die nicht über den Anwendungsproxy veröffentlicht wird.

![Abbildung 1](media/App-proxy-deployment-plan/link-translation.png)

Wenn Sie die Linkübersetzung für die App „Vorteile“ aktivieren, werden die Links zu „Ausgaben“ und „Reisen“ an die externen URLs für diese Apps umgeleitet, damit auch Benutzer von außerhalb des Unternehmensnetzwerks auf die Apps zugreifen können. Links von „Ausgaben“ und „Reisen“ zurück zu „Vorteile“ funktionieren nicht, da die Linkübersetzung für diese beiden Apps nicht aktiviert wurde. Für den Link zu „Feedback“ erfolgt keine Umleitung, weil keine externe URL vorhanden ist. Benutzer, die die App „Vorteile“ nutzen, können also nicht von außerhalb des Unternehmensnetzwerks auf die App „Feedback“ zugreifen. Ausführliche Informationen finden Sie unter [Umleiten von hartcodierten Links für Apps, die mit Azure AD-Anwendungsproxy veröffentlicht wurden](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Zugreifen auf Ihre Anwendung

Es gibt mehrere Optionen zum Verwalten des Zugriffs auf Ressourcen, die über den Anwendungsproxy veröffentlicht wurden. Sie sollten also die am besten geeignete Option für Ihr jeweiliges Szenario und Ihre Anforderungen an die Skalierbarkeit wählen. Häufige Ansätze sind: Verwendung von lokalen Gruppen, die per Azure AD Connect synchronisiert werden, Erstellung von dynamischen Gruppen in Azure AD basierend auf Benutzerattributen, Verwendung von Self-Service-Gruppen, die von einem Ressourcenbesitzer verwaltet werden, oder eine Kombination all dieser Ansätze. Unter den verlinkten Ressourcen sind die jeweiligen Vorteile beschrieben.

Die einfachste Vorgehensweise beim Zuweisen des Zugriffs auf eine Anwendung für Benutzer ist die Verwendung der Optionen unter **Benutzer und Gruppen** im linken Bereich Ihrer veröffentlichten Anwendung und die direkte Zuweisung von Gruppen oder Einzelpersonen.

![Bild 24](media/App-proxy-deployment-plan/add-user.png)

Sie können Benutzern auch den Self-Service-Zugriff auf Ihre Anwendung ermöglichen, indem Sie eine Gruppe zuweisen, in der die Benutzer derzeit nicht Mitglied sind, und die Self-Service-Optionen konfigurieren.

![Abbildung 25](media/App-proxy-deployment-plan/allow-access.png)

Wenn die Option aktiviert ist, können sich Benutzer dann am MyApps-Portal anmelden und den Zugriff anfordern. Dies wird entweder automatisch genehmigt, und sie werden der bereits zugelassenen Self-Service-Gruppe hinzugefügt, oder es ist eine Genehmigung einer angegebenen zuständigen Person erforderlich.

Gastbenutzer können auch [eingeladen werden, damit sie auf interne Anwendungen zugreifen können, die per Anwendungsproxy über Azure AD B2B veröffentlicht wurden](../external-identities/add-users-information-worker.md).

Für lokale Anwendungen, auf die normalerweise anonym zugegriffen werden kann und die keine Authentifizierung erfordern, kann es ratsam sein, die Option in den **Eigenschaften** der Anwendung zu deaktivieren.

![Abbildung 26](media/App-proxy-deployment-plan/assignment-required.png)


Wenn Sie für diese Option die Einstellung „Nein“ beibehalten, können die Benutzer auf die lokale Anwendung über den Azure AD-Anwendungsproxy zugreifen, ohne dass Berechtigungen erforderlich sind. Gehen Sie hierbei also mit Bedacht vor.

Nachdem Ihre Anwendung veröffentlicht wurde, sollte der Zugriff darauf möglich sein, indem die entsprechende externe URL in einem Browser eingegeben oder das Symbol unter [https://myapps.microsoft.com](https://myapps.microsoft.com/) verwendet wird.

### <a name="enable-pre-authentication"></a>Aktivieren der Vorauthentifizierung

Vergewissern Sie sich, dass anhand der externen URL über den Anwendungsproxy auf Ihre Anwendung zugegriffen werden kann.

1. Navigieren Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen**, und wählen Sie die App aus, die Sie verwalten möchten.

2. Wählen Sie **Anwendungsproxy**.

3. Wählen Sie im Feld **Vorauthentifizierung** in der Dropdownliste die Option **Azure Active Directory** und dann **Speichern**.

Wenn die Vorauthentifizierung aktiviert ist, fordert Azure AD Benutzer zuerst zur Authentifizierung auf. Ist einmaliges Anmelden konfiguriert, wird der Benutzer anschließend auch von der Back-End-Anwendung überprüft, bevor der Zugriff auf die Anwendung gewährt wird. Wenn Sie den Vorauthentifizierungsmodus von „Passthrough“ in „Azure AD“ ändern, wird auch die externe URL mit HTTPS konfiguriert. Alle Anwendungen, für die ursprünglich HTTP konfiguriert wurde, sind dann also per HTTPS geschützt.

### <a name="enable-single-sign-on"></a>Aktivieren der einmaligen Anmeldung

Mit einmaligem Anmelden (SSO) erzielen Sie die bestmögliche Benutzerfreundlichkeit und Sicherheit, weil sich Benutzer nur einmal beim Zugreifen auf Azure AD anmelden müssen. Nachdem für einen Benutzer die Vorauthentifizierung erfolgt ist, wird einmaliges Anmelden vom Connector des Anwendungsproxys durchgeführt, um für die lokale Anwendung die Authentifizierung im Namen des Benutzers zu erreichen. Die Anmeldung wird von der Back-End-Anwendung so verarbeitet, als ob es sich um den Benutzer selbst handeln würde.

Bei Auswahl der Option **Passthrough** können Benutzer auf die veröffentlichte Anwendung zugreifen, ohne sich gegenüber Azure AD authentifizieren zu müssen.

Einmaliges Anmelden ist nur möglich, wenn der Benutzer, der den Zugriff auf eine Ressource anfordert, von Azure AD identifiziert werden kann. Ihre Anwendung muss also zur Vorauthentifizierung von Benutzern mit Azure AD konfiguriert sein, damit einmaliges Anmelden funktioniert. Andernfalls sind die SSO-Optionen deaktiviert.

Lesen Sie [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](what-is-single-sign-on.md), damit Sie beim Konfigurieren Ihrer Anwendungen die am besten geeignete SSO-Methode wählen können.

###  <a name="working-with-other-types-of-applications"></a>Arbeiten mit anderen Arten von Anwendungen

Der Azure AD-Anwendungsproxy kann auch Anwendungen unterstützen, die für die Nutzung der [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md) entwickelt wurden. Hierbei werden native Client-Apps unterstützt, indem von Azure AD ausgestellte Token genutzt werden, die in den Headerinformationen von Clientanforderungen empfangen wurden, um die Vorauthentifizierung im Namen von Benutzern durchzuführen.

Lesen Sie [Aktivieren von nativen Client-Apps für die Interaktion mit Proxyanwendungen](./application-proxy-configure-native-client-application.md) und [Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy](./application-proxy-configure-for-claims-aware-applications.md), um sich über die verfügbaren Konfigurationen des Anwendungsproxys zu informieren.

### <a name="use-conditional-access-to-strengthen-security"></a>Verwenden des bedingten Zugriffs zur Erhöhung der Sicherheit

Für die Anwendungssicherheit sind erweiterte Sicherheitsfunktionen erforderlich, mit denen der Schutz vor und die Reaktion auf komplexe Bedrohungen in der lokalen Umgebung und in der Cloud sichergestellt werden kann. Am häufigsten erlangen Angreifer mit unsicheren, standardmäßigen oder gestohlenen Benutzeranmeldeinformationen Zugriff auf Unternehmensnetzwerke.  Mit den identitätsgesteuerten Sicherheitsfunktionen von Microsoft wird die Nutzung von gestohlenen Anmeldeinformationen eingedämmt, indem sowohl Identitäten mit als auch ohne Berechtigungen verwaltet und geschützt werden.

Die folgenden Funktionen können verwendet werden, um den Azure AD-Anwendungsproxy zu unterstützen:

* Benutzer- und standortbasierter bedingter Zugriff: Schützen Sie vertrauliche Daten, indem Sie den Benutzerzugriff per geografischem Standort oder über eine IP-Adresse mit [Richtlinien für den standortbasierten bedingten Zugriff](../conditional-access/location-condition.md) einschränken.

* Gerätebasierter bedingter Zugriff: Stellen Sie per [gerätebasiertem bedingtem Zugriff](../conditional-access/require-managed-devices.md) sicher, dass nur registrierte, genehmigte und konforme Geräte auf Unternehmensdaten zugreifen können.

* Anwendungsbasierter bedingter Zugriff: Wenn sich ein Benutzer außerhalb des Unternehmensnetzwerks befindet, muss ihn das nicht von der Arbeit abhalten. [Schützen Sie den Zugriff auf die Cloud- und lokalen Apps des Unternehmens](../conditional-access/app-based-conditional-access.md), und behalten Sie mit bedingtem Zugriff die Kontrolle.

* Risikobasierter bedingter Zugriff: Schützen Sie Ihre Daten vor böswilligen Hackern, indem Sie eine [risikobasierte Richtlinie für bedingten Zugriff](https://www.microsoft.com/cloud-platform/conditional-access) verwenden, die auf alle Apps und Benutzer angewendet werden kann – lokal und in der Cloud.

* „Meine Apps“ in Azure AD: Nachdem Sie Ihren Anwendungsproxydienst bereitgestellt und die Anwendungen sicher veröffentlicht haben, können Sie für Ihre Benutzer einen einfachen Hub einrichten, über den sie alle Anwendungen ermitteln und darauf zugreifen können. Steigern Sie über [Meine Apps](https://aka.ms/AccessPanelDPDownload) die Produktivität mit Self-Service-Funktionen, z. B. der Möglichkeit zum Anfordern des Zugriffs auf neue Apps und Gruppen oder zum Verwalten des Zugriffs auf diese Ressourcen im Namen von anderen Personen.

## <a name="manage-your-implementation"></a>Verwalten Ihrer Implementierung

### <a name="required-roles"></a>Erforderliche Rollen

Microsoft empfiehlt die Vorgehensweise, bei der jeweils die geringstmöglichen Rechte gewährt werden, die für die Erledigung der erforderlichen Aufgaben mit Azure AD benötigt werden. Informieren Sie sich über die [unterschiedlichen verfügbaren Azure-Rollen](../roles/permissions-reference.md), und wählen Sie die passende Rolle aus, um die jeweiligen Anforderungen der einzelnen Personen zu erfüllen. Einige Rollen müssen unter Umständen nur vorübergehend angewendet und können nach Abschluss der Bereitstellung wieder entfernt werden.

| Geschäftliche Rolle| Geschäftsaufgaben| Azure AD-Rollen |
|---|---|---|
| Helpdesk-Administrator | Normalerweise auf die Qualifizierung der von Endbenutzern gemeldeten Probleme und die Durchführung von begrenzten Aufgaben beschränkt, z. B. Änderung der Kennwörter von Benutzern, Annullierung von Aktualisierungstoken und Überwachung der Dienstintegrität. | Helpdeskadministrator |
| Identitätsadministrator| Lesen von Azure AD-Anmeldeberichten und -Überwachungsprotokollen zum Debuggen von Problemen, die sich auf den Anwendungsproxy beziehen.| Sicherheitsleseberechtigter |
| Anwendungsbesitzer| Erstellen und Verwalten aller Aspekte von Unternehmensanwendungen, Anwendungsregistrierungen und Anwendungsproxyeinstellungen.| Anwendungsadministrator |
| Infrastrukturadministrator | Zertifikatrollover-Besitzer | Anwendungsadministrator |

Durch das Geringhalten der Anzahl von Personen mit Zugriff auf sichere Informationen oder Ressourcen wird das Risiko verringert, dass ein böswilliger Akteur Zugriff darauf erhält oder ein autorisierter Benutzer versehentlich eine sensible Ressource kompromittiert.

Benutzer müssen aber weiterhin die täglichen Vorgänge durchführen, für die Berechtigungen benötigt werden. Das Erzwingen von Richtlinien auf Just-In-Time-Basis für das [Privileged Identity Management](../privileged-identity-management/pim-configure.md), um den bedarfsgesteuerten privilegierten Zugriff auf Azure-Ressourcen und Azure AD zu ermöglichen, ist deshalb unser empfohlener Ansatz zur effektiven Verwaltung des Administratorzugriffs und der damit verbundenen Überwachung.

### <a name="reporting-and-monitoring"></a>Berichterstellung und Überwachung

Azure AD liefert Ihrer Organisation in [Überwachungsprotokollen und Berichten](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zusätzliche Erkenntnisse zur Anwendungsnutzung und betrieblichen Integrität. Der Anwendungsproxy erleichtert auch das Überwachen von Connectors im Azure AD-Portal und in Windows-Ereignisprotokollen.

#### <a name="application-audit-logs"></a>Überwachungsprotokolle für Anwendungen

Diese Protokolle enthalten ausführliche Informationen zu Anmeldungen bei Anwendungen, die mit einem Anwendungsproxy konfiguriert sind, sowie zum Gerät und Benutzer, das bzw. der auf die Anwendung zugreift. [Überwachungsprotokolle](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) stehen im Azure-Portal und in der [Überwachungs-API](/graph/api/resources/directoryaudit?view=graph-rest-beta) zum Export bereit. Außerdem sind [Nutzungs- und Insights-Berichte](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) für Ihre Anwendung verfügbar.

#### <a name="application-proxy-connector-monitoring"></a>Überwachung des Anwendungsproxyconnectors

Die Connectors und der Dienst führen alle Aufgaben in Bezug auf Hochverfügbarkeit aus. Sie können den Status Ihrer Connectors auf der Seite „Anwendungsproxy“ im Azure AD-Portal überwachen. Informationen zu Connectors finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](./application-proxy-connectors.md#maintenance).

![Beispiel: Azure AD-Anwendungsproxy-Connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-Ereignisprotokolle und -Leistungsindikatoren

Connectors verfügen über Administrator- und Sitzungsprotokolle. Die Administratorprotokolle enthalten wichtige Ereignisse und die dazugehörigen Fehler. Die Sitzungsprotokolle enthalten alle Transaktionen und die dazugehörigen Verarbeitungsdetails. Protokolle und Indikatoren befinden sich in Windows-Ereignisprotokollen. Weitere Informationen finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](./application-proxy-connectors.md#under-the-hood). Befolgen Sie dieses [Tutorial für die Konfiguration von Datenquellen für das Ereignisprotokoll in Azure Monitor](../../azure-monitor/platform/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Leitfaden zur Problembehandlung und zu den Schritten

Erfahren Sie mehr zu häufigen Problemen und ihrer Lösung, indem Sie unseren Leitfaden zur [Problembehandlung](application-proxy-troubleshoot.md) für Fehlermeldungen verwenden.

In den folgenden Artikeln werden allgemeine Szenarien behandelt, die Sie auch zum Erstellen eigener Problembehandlungsleitfäden für Ihren Support verwenden können.

* [Problem beim Anzeigen der App-Seite](application-proxy-page-appearance-broken-problem.md)
* [Laden der Anwendung dauert zu lange.](application-proxy-page-load-speed-problem.md)
* [Links auf der Anwendungsseite funktionieren nicht.](application-proxy-page-links-broken-problem.md)
* [Für die App zu öffnende Ports](application-proxy-add-on-premises-application.md)
* [Kein funktionierender Connector in einer Connectorguppe für meine App](application-proxy-connectivity-no-working-connector.md)
* [Konfigurieren im Verwaltungsportal](application-proxy-config-how-to.md)
* [Konfigurieren des einmaligen Anmeldens für meine App](application-proxy-config-sso-how-to.md)
* [Problem beim Erstellen einer App im Verwaltungsportal](application-proxy-config-problem.md)
* [Konfigurieren der eingeschränkten Kerberos-Delegierung](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurieren mit PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Fehler „Zugriff auf diese Unternehmensanwendung nicht möglich“](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem beim Installieren des Anwendungsproxy-Agent-Connectors](application-proxy-connector-installation-problem.md)
* [Probleme beim Anmelden](application-sign-in-problem-on-premises-application-proxy.md)