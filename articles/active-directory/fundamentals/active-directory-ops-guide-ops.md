---
title: Allgemeiner Betriebsleitfaden für Azure Active Directory – Referenz
description: In diesem Referenzleitfaden werden die Überprüfungen und Aktionen beschrieben, die Sie zum Sichern der allgemeinen Vorgänge vornehmen sollten.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836834"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Allgemeiner Betriebsleitfaden für Azure Active Directory – Referenz

In diesem Abschnitt des [Azure AD-Referenzleitfaden](active-directory-ops-guide-intro.md) werden die Überprüfungen und Aktionen beschrieben, die Sie durchführen sollten, um die allgemeinen Vorgänge von Azure Active Directory (Azure AD) zu optimieren.

> [!NOTE]
> Diese Empfehlungen sind auf dem Stand des Veröffentlichungsdatums, können sich aber im Laufe der Zeit ändern. Organisationen sollten ihre Betriebsverfahren fortlaufend überprüfen, da die Produkte und Dienste von Microsoft mit der Zeit weiterentwickelt werden.

## <a name="key-operational-processes"></a>Wichtige Betriebsabläufe

### <a name="assign-owners-to-key-tasks"></a>Zuweisen von Besitzern zu wichtigen Aufgaben

Für die Verwaltung von Azure Active Directory ist die kontinuierliche Ausführung wichtiger betrieblicher Aufgaben und Prozesse erforderlich, die möglicherweise nicht Teil eines Rolloutprojekts sind. Es ist außerdem wichtig, dass Sie diese Aufgaben einrichten, um Ihre Umgebung zu optimieren. Im Folgenden werden die wichtigen Aufgaben und empfohlene Besitzer für diese aufgeführt:

| Aufgabe | Besitzer |
| :- | :- |
| Vorantreiben von Verbesserungen bei der Identitätssicherheitsbewertung | Team für Informationssicherheitvorgänge |
| Verwalten von Azure AD Connect-Servern | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Regelmäßiges Ausführen und Sichten von IdFix-Berichten | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Sichten von Azure AD Connect Health-Warnungen für die Synchronisierung und AD FS | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Wenn Sie Azure AD Connect Health nicht verwenden, verfügt der Kunde über gleichwertige Prozesse und Tools zum Überwachen der benutzerdefinierten Infrastruktur | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Wenn Sie AD FS nicht verwenden, verfügt der Kunde über gleichwertige Prozesse und Tools zum Überwachen der benutzerdefinierten Infrastruktur | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Überwachen von Hybrid-Protokollen: Azure AD-App-Proxy-Connectors | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Überwachen von Hybrid-Protokollen: Passthrough-Authentifizierungs-Agents | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Überwachen von Hybrid-Protokollen: Dienst für das Kennwortrückschreiben | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Überwachen von Hybrid-Protokollen: Lokales Kennwortschutzgateway | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Überwachen von Hybrid-Protokollen: Azure AD MFA NPS-Erweiterung (falls zutreffend) | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |

Beim Überprüfen Ihrer Liste stellen Sie ggf. fest, dass Sie entweder einen Besitzer für Aufgaben zuweisen müssen, denen kein Besitzer zugeteilt ist, oder Aufgaben anpassen müssen, deren Besitzer nicht den obigen Empfehlungen entspricht.

#### <a name="owners-recommended-reading"></a>Empfohlene Artikel für Besitzer

- [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Hybridverwaltung

### <a name="recent-versions-of-on-premises-components"></a>Aktuelle Versionen von lokalen Komponenten

Die Verwendung der neuesten Versionen der lokalen Komponenten bietet dem Kunden die neuesten Sicherheitsupdates, Leistungsverbesserungen sowie Funktionen, die zur weiteren Vereinfachung der Umgebung beitragen können. Die meisten Komponenten verfügen über eine Einstellung für automatische Upgrades, die den Upgradeprozess automatisiert.

Zu diesen Komponenten gehören:

- Azure AD Connect
- Azure AD-Anwendungsproxyconnectors
- Agent für die Azure AD-Passthrough-Authentifizierung
- Azure AD Connect Health-Agents

Sofern dies nicht bereits geschehen ist, sollten Sie einen Prozess für das Upgrade dieser Komponenten definieren und sich nach Möglichkeit auf die Funktion für automatische Upgrades verlassen. Wenn Sie Komponenten finden, die sechs oder mehr Monate im Rückstand sind, sollten Sie so schnell wie möglich ein Upgrade durchführen.

#### <a name="hybrid-management-recommended-reading"></a>Empfohlene Artikel zur Hybridverwaltung

- [Azure AD Connect: Automatisches Upgrade](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Grundlegendes zu Azure AD-Anwendungsproxyconnectors | Automatische Updates](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health: Baseline zu Warnungen

Organisationen sollten [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) bereitstellen, um Azure AD Connect und AD FS zu überwachen und zugehörige Berichte zu erstellen. Azure AD Connect und AD FS sind wichtige Komponenten, die zu schwerwiegenden Fehlern bei der Lebenszyklusverwaltung und Authentifizierung und damit zu Ausfällen führen können. Azure AD Connect Health unterstützt Sie bei der Überwachung Ihrer lokalen Identitätsinfrastruktur, liefert Ihnen wichtige Erkenntnisse und stellt so die Zuverlässigkeit Ihrer Umgebung sicher.

![Architektur von Azure AD Connect Health](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Wenn Sie den Zustand Ihrer Umgebung überwachen, müssen Sie sofort alle Warnungen mit hoher Priorität behandeln, gefolgt von Warnungen mit niedrigerer Priorität.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health: Empfohlene Artikel

- [Installieren des Azure AD Connect Health-Agents](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Lokale Agent-Protokolle

Einige Identitäts- und Zugriffsverwaltungsdienste erfordern lokale Agents, um Hybridszenarien zu ermöglichen. Beispiele sind Kennwortzurücksetzung, Passthrough-Authentifizierung (PTA), Azure AD-Anwendungsproxy und die Azure AD MFA NPS-Erweiterung. Es ist von entscheidender Bedeutung, dass die Baseline des Betriebsteams die Integrität dieser Komponenten überwacht, indem sie die Protokolle der Komponenten-Agents mit Lösungen wie System Center Operations Manager oder SIEM archiviert und analysiert. Es ist ebenso wichtig, dass Ihr Team für Vorgänge im Bereich Informationssicherheit oder Ihr Helpdesk versteht, wie Sie Fehlermuster beheben können.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Empfohlene Artikel für lokale Agent-Protokolle

- [Problembehandlung von Anwendungsproxys](../manage-apps/application-proxy-troubleshoot.md)
- [Problembehandlung bei der Self-Service-Kennwortzurücksetzung – Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: Behandeln von Passthrough-Authentifizierungsproblemen](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Fehlercodes zur Problembehandlung für die Azure AD MFA NPS-Erweiterung](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Verwaltung lokaler Agents

Die Einführung von bewährten Methoden kann den optimalen Einsatz von lokalen Agents unterstützen. Berücksichtigen Sie die folgenden bewährten Methoden:

- Es werden mehrere Azure AD-Anwendungsproxyconnectors pro Connectorgruppe empfohlen, um einen reibungslosen Lastenausgleich und eine hohe Verfügbarkeit sicherzustellen, indem ein Single Point of Failure (SPOF) beim Zugriff auf die Proxyanwendungen vermieden wird. Wenn Sie derzeit nur über einen Connector in einer Connectorgruppe verfügen, der Anwendungen in der Produktion verwaltet, sollten Sie aus Redundanzgründen mindestens zwei Connectors bereitstellen.
- Das Erstellen und Verwenden einer App-Proxyconnectorgruppe zu Debuggingzwecken kann in Problembehandlungsszenarien und beim Onboarding neuer lokaler Anwendungen nützlich sein. Wir empfehlen zudem die Installation von Netzwerktools wie Message Analyzer und Fiddler auf den Connectorcomputern.
- Es werden mehrere Passthrough-Authentifizierungs-Agents empfohlen, um einen nahtlosen Lastenausgleich und die Hochverfügbarkeit sicherzustellen, indem ein Single Point of Failure (SPOF) während des Authentifizierungsablaufs vermieden wird. Stellen Sie sicher, dass Sie mindestens zwei Passthrough-Authentifizierungs-Agents zwecks Redundanz bereitstellen.

#### <a name="on-premises-agents-management-recommended-reading"></a>Empfohlene Artikel für die Verwaltung lokaler Agents

- [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](../manage-apps/application-proxy-connectors.md)
- [Agent für die Azure AD-Passthrough-Authentifizierung: Schnellstart](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Bedarfsorientierte Verwaltung

### <a name="identity-secure-score"></a>Identity Secure Score

Die [Identitätssicherheitsbewertung](./identity-secure-score.md) liefert ein quantifizierbares Maß für den Sicherheitsstatus Ihres Unternehmens. Es ist wichtig, die gemeldeten Ergebnisse ständig zu überprüfen und zu behandeln und sich um die bestmögliche Bewertung zu bemühen. Anhand der Bewertung können Sie:

- Den Status Ihrer Identitätssicherheit objektiv messen
- Verbesserungen der Identitätssicherheit planen
- Den Erfolg Ihrer Verbesserungen überprüfen

![Sicherheitsbewertung](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Wenn Ihr Unternehmen derzeit über kein Programm zur Überwachung von Änderungen bei der Identitätssicherheitsbewertung verfügt, wird empfohlen, einen Plan zu implementieren und Besitzer zuzuweisen, die Verbesserungsaktionen überwachen und steuern. Unternehmen sollten Verbesserungsaktionen mit einer Auswirkung der Bewertung von mehr als 30 so schnell wie möglich beheben.

### <a name="notifications"></a>Benachrichtigungen

Microsoft sendet E-Mail-Nachrichten an Administratoren, um verschiedene Änderungen im Dienst, erforderliche Konfigurationsupdates und Fehler, die ein Eingreifen des Administrators erfordern, zu melden. Es ist wichtig, dass Kunden die E-Mail-Adressen für die Benachrichtigung so einstellen, dass Benachrichtigungen an die richtigen Teammitglieder gesendet werden, die alle Benachrichtigungen bestätigen und darauf reagieren können. Es wird empfohlen, mehrere Empfänger zum [Nachrichtencenter](/office365/admin/manage/message-center) hinzuzufügen und anzufordern, dass Benachrichtigungen (einschließlich Azure AD Connect Health-Benachrichtigungen) an eine Verteilerliste oder ein freigegebenes Postfach gesendet werden. Wenn Sie nur über ein globales Administratorkonto mit einer E-Mail-Adresse verfügen, stellen Sie sicher, dass Sie mindestens zwei E-Mail-fähige Konten konfigurieren.

Es werden zwei Absenderadressen („Von“) von Azure AD verwendet: <o365mc@email2.microsoft.com>, von der Benachrichtigungen des Nachrichtencenters gesendet werden, und <azure-noreply@microsoft.com>, von der Benachrichtigungen zu Folgendem gesendet werden:

- [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Benachrichtigungen zu ablaufenden Zertifikaten von Unternehmen-Apps](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Benachrichtigungen zum Bereitstellungsdienst von Unternehmen-Apps

In der folgenden Tabelle erfahren Sie, welche Art von Benachrichtigungen gesendet werden und wo Sie diese finden können:

| Benachrichtigungsquelle | Was wird gesendet? | Wo suchen? |
|:-|:-|:-|
| Technischer Kontakt | Synchronisierungsfehler | Azure-Portal – Blatt „Eigenschaften“ |
| Nachrichtencenter | Hinweise zu Incidents und Leistungsminderung bei Identitätsdiensten und Microsoft 365-Back-End-Diensten | Office-Portal |
| Wöchentliche Übersicht für Identity Protection | Übersicht für Identity Protection | Blatt „Azure AD Identity Protection“ |
| Azure AD Connect Health | Warnungsbenachrichtigungen | Azure-Portal – Blatt „Azure AD Connect Health“ |
| Benachrichtigungen von Unternehmensanwendungen | Benachrichtigungen beim Ablauf von Zertifikaten und bei Bereitstellungsfehlern | Azure-Portal – Blatt „Unternehmensanwendung“ (jede App verfügt über eigene Einstellung für E-Mail-Adresse) |

#### <a name="notifications-recommended-reading"></a>Empfohlene Artikel zu Benachrichtigungen

- [Ändern von Adresse, technischem Kontakt und weiteren Informationen für Ihr Unternehmen](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Betriebsflächenbereich

### <a name="ad-fs-lockdown"></a>AD FS-Sperre

Organisationen, die Anwendungen für die direkte Authentifizierung in Azure AD konfigurieren, profitieren von der [intelligenten Kennwortsperrung von Azure AD](../authentication/concept-sspr-howitworks.md). Wenn Sie AD FS unter Windows Server 2012 R2 verwenden, implementieren Sie den [Extranetsperrschutz](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) von AD FS. Wenn Sie AD FS unter Windows Server 2016 oder höher verwenden, implementieren Sie [Extranet Smart Lockout](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Wir empfehlen Ihnen, mindestens die Extranetsperre zu aktivieren, um das Risiko von Brute-Force-Angriffen für das lokale Active Directory einzugrenzen. Wenn Sie jedoch AD FS unter Windows 2016 oder höher verwenden, sollten Sie auch die intelligente Extranetsperre aktivieren, die dazu beiträgt, das Risiko von [Kennwortspray](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)-Angriffen zu verringern.

Wenn AD FS nur für den Azure AD-Verbund verwendet wird, können einige Endpunkte deaktiviert werden, um die Angriffsfläche zu minimieren. Wenn AD FS z. B. nur für Azure AD verwendet wird, sollten Sie andere WS-Trust-Endpunkte als die für **usernamemixed** und **windowstransport** aktivierten Endpunkte deaktivieren.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Zugriff auf Computer mit lokalen Identitätskomponenten

Unternehmen sollten den Zugriff auf Computer mit lokalen Hybridkomponenten genauso sperren wie für Ihre lokale Domäne. So sollte sich z. B. ein Sicherungsoperator oder Hyper-V-Administrator nicht am Azure AD Connect-Server anmelden können, um Regeln zu ändern.

Das Active Directory-Verwaltungsebenenmodell wurde entwickelt, um Identitätssysteme mithilfe einer Reihe von Pufferzonen zwischen dem Vollzugriff auf die Umgebung (Ebene 0) und den Arbeitsstationsressourcen mit hohem Risiko zu schützen, die Angreifer häufig kompromittieren. ![Diagramm mit den drei Ebenen des Ebenenmodells](./media/active-directory-ops-guide/active-directory-ops-img18.png)

Das [Ebenenmodell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) besteht aus drei Ebenen und umfasst nur Verwaltungskonten, nicht Standardbenutzerkonten.

- **Ebene 0**: Direkte Kontrolle von Unternehmensidentitäten innerhalb der Umgebung. Ebene 0 umfasst Konten, Gruppen und andere Ressourcen, die direkt oder indirekt über die administrative Steuerung der Active Directory-Gesamtstruktur, Domänen oder Domänencontroller und aller darin enthaltenen Ressourcen verfügen. Die Sicherheitsempfindlichkeit aller Ressourcen der Ebene 0 ist gleichwertig, da sie sich effektiv alle gegenseitig kontrollieren.
- **Ebene 1**: Kontrolle von Unternehmensservern und -anwendungen. Zu den Ressourcen der Ebene 1 gehören Serverbetriebssysteme, Clouddienste und Unternehmensanwendungen. Administratorkonten der Ebene 1 haben die administrative Kontrolle über einen erheblichen Teil des Geschäftswerts, der auf diesen Ressourcen gehostet wird. Eine allgemeine Beispielrolle sind Serveradministratoren, die diese Betriebssysteme mit der Möglichkeit pflegen, auf alle Unternehmensdienste einwirken zu können.
- **Ebene 2**: Kontrolle von Benutzerarbeitsstationen und -geräten. Administratorkonten der Ebene 2 haben die administrative Kontrolle über einen erheblichen Teil des Geschäftswerts, der auf Benutzerarbeitsstationen und -geräten gehostet wird. Beispiele sind Helpdesk- und Computersupport-Administratoren, da sie auf die Integrität fast aller Benutzerdaten Einfluss nehmen können.

Sperren Sie den Zugriff auf lokale Identitätskomponenten wie Azure AD Connect, AD FS und SQL-Dienste auf dieselbe Weise wie bei Domänencontrollern.

## <a name="summary"></a>Zusammenfassung

Sichere Identitätsinfrastrukturen weisen sieben Aspekte auf. Diese Liste hilft Ihnen, die zu ergreifenden Aktionen zu finden, um die Vorgänge für Azure Active Directory (Azure AD) zu optimieren.

- Zuweisen von Besitzern zu wichtigen Aufgaben
- Automatisieren des Upgradeprozesses für lokale Hybridkomponenten
- Bereitstellen von Azure AD Connect Health zur Überwachung und Berichterstellung von Azure AD Connect und AD FS
- Überwachen des Zustands von lokalen Hybridkomponenten durch Archivierung und Analyse der Komponenten-Agent-Protokolle mit System Center Operations Manager oder einer SIEM-Lösung
- Implementieren von Sicherheitsverbesserungen durch Messung Ihres Sicherheitsstatus mit Identitätssicherheitsbewertung
- Sperren von AD FS
- Sperren des Zugriffs auf Computer mit lokalen Identitätskomponenten

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Implementierungsdetails für alle Funktionen, die Sie nicht bereitgestellt haben, finden Sie in den [Azure AD-Bereitstellungsplänen](active-directory-deployment-plans.md).