---
title: Über die Azure AD Cloud gesteuerte Verwaltung für lokale Workloads – Azure
description: In diesem Thema wird die über die Cloud gesteuerte Verwaltung für lokale Workloads beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340848"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>So stellt Azure AD die über die Cloud gesteuerte Verwaltung für lokale Workloads bereit

Azure Active Directory (Azure AD) ist eine umfassende IDaaS-Lösung (Identity as a Service) für sämtliche Aspekte von Identität, Zugriffsverwaltung und Sicherheit, die von Millionen von Organisationen verwendet wird. Azure AD nimmt mehr als eine Milliarde Benutzeridentitäten auf und hilft Benutzern bei der Anmeldung und dem sicheren Zugriff auf beides:

* Externe Ressourcen wie Microsoft 365, das Azure-Portal und Tausende andere SaaS-Anwendungen (Software-as-a-Service).
* Interne Ressourcen wie Anwendungen im Firmennetzwerk und Intranet eines Unternehmens zusammen mit Cloudanwendungen, die von dieser Organisation selbst entwickelt wurden.

Organisationen können Azure AD verwenden, wenn sie „nur die Cloud“ verwenden, oder als eine „Hybrid“bereitstellung, wenn sie auch lokale Workloads haben. Eine Hybridbereitstellung von Azure AD kann Teil einer Strategie für eine Organisation sein, um ihre IT-Ressourcen zur Cloud zu migrieren, oder um die Integration vorhandener lokaler Infrastruktur zusammen mit neuen Clouddiensten fortzusetzen.

In der Vergangenheit haben „Hybrid“organisationen Azure AD als Erweiterung der vorhandenen lokalen Infrastruktur betrachtet. In diesen Bereitstellungen sind die lokale Verwaltung der Identitäts-Governance, Windows Server Active Directory oder andere interne Verzeichnissysteme die Kontrollpunkte, und Benutzer und Gruppen werden aus diesen Systemen in ein Cloudverzeichnis wie Azure AD synchronisiert. Sobald sich diese Identitäten in der Cloud befinden, können sie für Microsoft 365, Azure und andere Anwendungen verfügbar gemacht werden.

![Identitätslebenszyklus](media/cloud-governed-management-for-on-premises//image1.png)

Wenn Organisationen mehr von ihrer IT-Infrastruktur zusammen mit ihren Anwendungen in die Cloud verschieben, suchen viele die verbesserte Sicherheit und die vereinfachten Verwaltungsfunktionen der Identitätsverwaltung als Dienst (IDaaS). Die in der Cloud bereitgestellten IDaaS-Funktionen in Azure AD beschleunigen den Übergang zur cloudgesteuerten Verwaltung durch Bereitstellung der Lösungen und Funktionen, die es Organisationen erlauben, sich schnell anzupassen und zunehmend mehr von ihrer Identitätsverwaltung aus konventionellen, lokalen Systemen in Azure AD zu verschieben, und gleichzeitig vorhandene und neue Anwendungen zu unterstützen.

In diesem Artikel wird Microsofts Strategie für Hybrid-IDaaS erläutert und wie Organisationen Azure AD für ihre vorhandenen Anwendungen verwenden können.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Der Azure AD-Ansatz für cloudgesteuerte Identitätsverwaltung

Beim Übergang zur Cloud benötigen Organisationen Zusicherungen, dass sie die Kontrolle über ihre vollständige Umgebung behalten – mehr Sicherheit und mehr Einblick in Aktivitäten, unterstützt durch Automatisierung, sowie proaktive Erkenntnisse erhalten. „**Cloudgesteuerte Verwaltung**“ beschreibt, wie Organisationen ihre Benutzer, Anwendungen, Gruppen und Geräte aus der Cloud heraus verwalten und steuern.

In dieser modernen Welt müssen Unternehmen aufgrund der zunehmenden Verbreitung von SaaS-Anwendungen und der zunehmenden Rolle von Zusammenarbeit und externen Identitäten in der Lage sein, effektiv und maßstabsgetreu zu verwalten. Die neue Risikolandschaft der Cloud bedeutet, dass eine Organisation reaktionsschneller sein muss – ein bösartiger Akteur, der einen Cloudbenutzer kompromittiert, könnte Cloud- und lokale Anwendungen beeinträchtigen.

Insbesondere Hybridorganisationen müssen in der Lage sein, Aufgaben zu delegieren und zu automatisieren, die die IT bisher manuell durchgeführt hat. Um Aufgaben zu automatisieren, benötigen sie APIs und Prozesse, die den Lebenszyklus der verschiedenen identitätsbezogenen Ressourcen (Benutzer, Gruppen, Anwendungen, Geräte) orchestrieren, damit sie die tägliche Verwaltung dieser Ressourcen an mehr Personen außerhalb des IT-Kernpersonals delegieren können. Azure AD erfüllt diese Anforderungen durch Benutzerkontenverwaltung und native Authentifizierung für Benutzer, ohne dass eine lokale Identitätsinfrastruktur erforderlich ist. Der Verzicht auf den Aufbau einer lokalen Infrastruktur kann Organisationen zugute kommen, die über neue Benutzercommunitys verfügen, wie z. B. Geschäftspartner, die nicht aus ihrem lokalen Verzeichnis stammen, deren Zugriffsverwaltung aber für die Erreichung von Geschäftsergebnissen entscheidend ist.

Darüber hinaus ist Verwaltung ohne Governance nicht vollständig – und Governance in dieser neuen Welt ist eher ein integrierter Teil des Identitätssystems als eine Ergänzung. Mit Identity Governance können Organisationen sowohl für Mitarbeiter, Geschäftspartner und Anbieter als auch für Dienste und Anwendungen die Identität verwalten und auf den Lebenszyklus zugreifen.

Die Integration der Identity Governance erleichtert den Übergang des Unternehmens zur cloudgesteuerten Verwaltung, ermöglicht es der IT-Abteilung, zu skalieren, geht neue Herausforderungen mit Gästen an und bietet tiefere Erkenntnisse und stärkere Automatisierung als das, was Kunden bisher mit lokalen Infrastrukturen hatten. Governance in dieser neuen Welt bedeutet die Fähigkeit einer Organisation, Transparenz, Sichtbarkeit und angemessene Kontrolle des Zugangs zu Ressourcen innerhalb der Organisation zu haben. Mit Azure AD haben Sicherheitsbetriebs- und Auditteams einen Überblick darüber, wer Zugriff auf welche Ressourcen (auf welchen Geräten) in der Organisation hat – und wer diesen haben sollte, was diese Benutzer mit diesem Zugriff machen, und ob die Organisation über geeignete Kontrollen verfügt und diese einsetzt, um den Zugriff gemäß den Richtlinien der Organisation oder der Aufsichtsbehörden zu entfernen oder einzuschränken.

Das neue Verwaltungsmodell kommt Organisationen mit sowohl SaaS- als auch Branchenanwendungen (LOB, Line-of-Business) zugute, da sie leichter in der Lage sind, den Zugriff auf diese Anwendungen zu verwalten und abzusichern. Durch die Integration von Anwendungen in Azure AD schaffen Organisationen es, den Zugriff auf sowohl aus der Cloud stammende als auch lokal erstellte Identitäten konsistent zu nutzen und zu verwalten. Die Verwaltung des Anwendungslebenszyklus wird immer stärker automatisiert, und Azure AD bietet umfassende Erkenntnisse in die Anwendungsnutzung, die mit der lokalen Identitätsverwaltung nicht so einfach zu erreichen waren. Durch die Self-Service-Funktionen von Azure AD, Microsoft 365-Gruppen und Teams können Organisationen einfach Gruppen für die Zugriffsverwaltung und die Zusammenarbeit erstellen und Benutzer in der Cloud hinzufügen oder entfernen, um Anforderungen an die Zusammenarbeit und Zugriffsverwaltung einzurichten.

Die Auswahl der richtigen Azure AD-Funktionen für die cloudgesteuerte Verwaltung hängt von den zu verwendenden Anwendungen ab und wie diese Anwendungen in Azure AD integriert werden. Die folgenden Abschnitte beschreiben die zu verfolgenden Ansätze für AD-integrierte Anwendungen sowie Anwendungen, die Verbundprotokolle verwenden (z. B. SAML, OAuth oder OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Cloudgesteuerte Verwaltung für AD-integrierte Anwendungen

Azure AD verbessert die Verwaltung der in Active Directory integrierten Anwendungen eines Unternehmens vor Ort durch sicheren Remotezugriff und bedingten Zugriff auf diese Anwendungen. Darüber hinaus bietet Azure AD auch Verwaltung des Kontolebenszyklus und die Verwaltung von Anmeldeinformationen für die vorhandenen AD-Konten des Benutzers, einschließlich:

* **Sicherer Remotezugriff und bedingter Zugriff für lokale Anwendungen**

Für viele Unternehmen ist der erste Schritt bei der Verwaltung des Zugriffs aus der Cloud für lokale AD-integrierte Web- und remotedesktopbasierte Anwendungen die Bereitstellung des [Anwendungsproxys](../manage-apps/application-proxy.md) vor diesen Anwendungen, um einen sicheren Remotezugriff zu ermöglichen.

Nach dem einmaligen Anmelden in Azure AD können Benutzer über eine externe URL oder ein internes Anwendungsportal auf Cloudanwendungen und lokale Anwendungen zugreifen. Der Anwendungsproxy bietet z. B. Remotezugriff und einmaliges Anmelden beim Remotedesktop, SharePoint sowie Apps wie Tableau und Qlik als auch Branchenanwendungen (LOB). Darüber hinaus können Richtlinien für bedingten Zugriff beinhalten, dass die [Nutzungsbedingungen](../conditional-access/terms-of-use.md) angezeigt werden, und dass [sichergestellt wird, dass der Benutzer diesen zugestimmt hat](../conditional-access/require-tou.md), bevor er auf eine Anwendung zugreifen kann.

![App-Proxyarchitektur](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatische Lebenszyklusverwaltung für Active Directory-Konten**

Identity Governance hilft Organisationen, ein Gleichgewicht herzustellen zwischen der *Produktivität* – wie schnell eine Person auf die benötigten Ressourcen zugreifen kann (beispielsweise, wenn sie der Organisation beitritt) – und der *Sicherheit* – wie sich der Zugriff im Laufe der Zeit ändern sollte (beispielsweise aufgrund von Änderungen des Beschäftigungsstatus einer Person). Identity Lifecycle Management ist die Grundlage für Identity Governance, und eine effektive Governance in großem Maßstab erfordert eine Modernisierung der Identity Lifecycle Management-Infrastruktur für Anwendungen.

Bei vielen Organisationen ist der Identitätslebenszyklus für Mitarbeiter mit der Darstellung des Benutzers in einem Personalverwaltungssystem (Human Capital Management, HCM) verknüpft. Für Organisationen, die Workday als ihr Personalverwaltungssystemen verwenden, kann Azure AD sicherstellen, dass Benutzerkonten in AD [für Mitarbeiter in Workday automatisch bereitgestellt bzw. deren Bereitstellung aufgehoben wird](../saas-apps/workday-inbound-tutorial.md). Dies führt zu einer verbesserten Benutzerproduktivität durch die Automatisierung von Birthright-Konten und verwaltet Risiken, indem sichergestellt wird, dass der Anwendungszugriff automatisch aktualisiert wird, wenn ein Benutzer die Rolle wechselt oder die Organisation verlässt. Der Workday-basierte [Benutzerbereitstellungsplan](https://aka.ms/WorkdayDeploymentPlan) ist eine schrittweise Anleitung, die Organisationen in einem fünfstufigen Prozess durch die Implementierung von Workday in eine Active Directory-Benutzerbereitstellungslösung anhand bewährter Methoden führt.

Azure AD Premium umfasst außerdem Microsoft Identity Manager, eine Lösung, mit der Datensätze aus anderen lokalen Personalverwaltungssystemen, einschließlich SAP, Oracle eBusiness und Oracle PeopleSoft, importiert werden können.

Die geschäftliche Zusammenarbeit zwischen Unternehmen (B2B) erfordert zunehmend, Personen außerhalb Ihrer Organisation Zugriff zu gewähren. Mithilfe der [Azure AD B2B](/azure/active-directory/b2b/)-Zusammenarbeit (Business-to-Business) können Organisationen ihre Anwendungen und Dienste für Gastbenutzer und externe Partner sicher freigeben und dabei die Kontrolle über Ihre eigenen Unternehmensdaten behalten.

Azure AD kann nach Bedarf [automatisch Konten für Gastbenutzer in AD erstellen](../external-identities/hybrid-cloud-to-on-premises.md), und so Gästen des Unternehmens den Zugriff auf lokale, AD-integrierte Anwendungen gewähren, ohne dass ein weiteres Kennwort erforderlich wäre. Organisationen können [Richtlinien für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Gastbenutzer](../external-identities/conditional-access.md) einrichten, damit während der Anwendungsproxyauthentifizierung MFA-Prüfungen durchgeführt werden. Außerdem gelten alle [Zugriffsüberprüfungen](../governance/manage-guest-access-with-access-reviews.md), die für Cloud-B2B-Benutzer durchgeführt werden, auch für lokale Benutzer. Wenn der Cloudbenutzer beispielsweise durch Richtlinien für die Lebenszyklusverwaltung gelöscht wird, wird der lokale Benutzer ebenfalls gelöscht.

**Verwaltung von Anmeldeinformationen für Active Directory-Konten** Die Self-Service-Kennwortzurücksetzung von Azure AD erlaubt Benutzern, die ihre Kennwörter vergessen haben, eine erneute Authentifizierung sowie das Zurücksetzen ihrer Kennwörter, wobei die geänderten Kennwörter [in das lokale Active Directory geschrieben werden](../authentication/concept-sspr-writeback.md). Der Prozess zum Zurücksetzen des Kennworts kann auch die lokalen Active Directory-Kennwortrichtlinien verwenden: Wenn ein Benutzer sein Kennwort zurücksetzt, wird es überprüft, um sicherzustellen, dass es die lokalen Active Directory-Richtlinien erfüllt, bevor es für dieses Verzeichnis übernommen wird. Der [Bereitstellungsplan](../authentication/howto-sspr-deployment.md) für die Self-Service-Kennwortzurücksetzung legt bewährte Methoden für die Einführung der Self-Service-Kennwortzurücksetzung für Benutzer über Web- und Windows-integrierte Erfahrungen dar.

![Azure AD-SSPR-Architektur](media/cloud-governed-management-for-on-premises/image3.png)

Schließlich kann AD für Organisationen, die Benutzern gestatten, ihre Kennwörter in AD zu ändern, mithilfe der [Azure AD-Kennwortschutzfunktion](../authentication/concept-password-ban-bad-on-premises.md) (zurzeit in der Vorschauphase) so konfiguriert werden, dass es dieselbe Kennwortrichtlinie verwendet, wie die Organisation sie in AD verwendet.

Wenn eine Organisation bereit ist, eine AD-integrierte Anwendung in die Cloud zu verschieben, indem sie das Betriebssystem, das die Anwendung hostet, in Azure verschiebt, stellen die [Azure AD Domain Services](../../active-directory-domain-services/overview.md) AD-kompatible Domänendienste bereit (z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos/NTLM-Authentifizierung). Die Azure AD-Domänendienste können in den vorhandenen Azure AD-Mandanten der Organisation integriert werden, wodurch es Benutzern ermöglicht wird, sich mit ihren Unternehmensanmeldeinformationen anzumelden. Außerdem können vorhandene Gruppen und Benutzerkonten verwendet werden, um den Zugriff auf Ressourcen zu schützen. So stellen Sie sicher, dass die Verlagerung von lokalen Ressourcen auf Azure-Infrastrukturdienste reibungsloser verläuft.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Cloudgesteuerte Verwaltung für lokale, verbundbasierte Anwendungen

Für eine Organisationen, die bereits einen lokalen Identitätsanbieter verwendet, ermöglicht das Verschieben von Anwendungen in Azure AD einen sichereren Zugriff und eine einfachere Verwaltungserfahrung für die Verbundverwaltung. Azure AD ermöglicht das Konfigurieren präziser Zugriffssteuerungen pro Anwendung, einschließlich Azure AD Multi-Factor Authentication, indem der bedingte Zugriff in Azure AD verwendet wird. Azure AD unterstützt weitere Funktionen, einschließlich anwendungsspezifischer Tokensignaturzertifikate und konfigurierbarer Zertifikatablaufdaten. Diese Funktionen, Tools und Anleitungen ermöglichen es Unternehmen, ihre lokalen Identitätsanbieter auszumustern. Die eigene IT von Microsoft IT hat beispielsweise 17.987 Anwendungen aus den internen Active Directory-Verbunddiensten (AD FS) von Microsoft in Azure AD verschoben.

![Azure AD-Entwicklung](media/cloud-governed-management-for-on-premises/image5.png)

Informationen, wie Sie mit der Migration von Verbundanwendungen zu Azure AD als Identitätsanbieter beginnen können, erhalten Sie unter https://aka.ms/migrateapps. Dort finden Sie Links zu folgenden Ressourcen:

* Das Whitepaper [Migrieren Ihrer Anwendungen zu Azure Active Directory](https://aka.ms/migrateapps/whitepaper), in dem die Vorteile der Migration vorgestellt und beschrieben wird, wie sich die Migration in vier klar dargestellten Phasen planen lässt: Ermittlung, Klassifizierung, Migration und laufende Verwaltung. Sie werden durch den Prozess geführt und erfahren, wie Sie das Projekt in einfach umzusetzende Teilschritte aufteilen. Im gesamten Dokument finden Sie Links zu wichtigen Ressourcen, die Ihnen im Verlauf des Prozesses helfen.

* Das Lösungshandbuch [Migrieren der Anwendungsauthentifizierung von den Active Directory-Verbunddiensten (AD FS) zu Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) untersucht dieselben vier Phasen der Planung und Ausführung eines Anwendungsmigrationsprojekts in größerem Detail. In dieser Anleitung erfahren Sie, wie Sie diese Phasen auf das spezifisches Ziel des Verschiebens einer Anwendung aus den Azure Directory-Verbunddiensten (AD FS) in Azure AD anwenden.

* Das [Migrationsbereitschaftsskript für Active Directory-Verbunddienste (AD FS)](https://aka.ms/migrateapps/adfstools) kann auf Ihrem lokalen AD FS-Server (Active Directory-Verbunddienste) ausgeführt werden, um die Bereitschaft von Anwendungen für die Migration zu Azure AD zu bestimmen.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Laufende Zugriffsverwaltung bei Cloud- und lokalen Anwendungen

Organisationen benötigen einen Prozess zum Verwalten des Zugriffs, der skalierbar ist. Benutzer akkumulieren Zugriffsrechte und verfügen am Ende über mehr Rechte, als ihnen anfänglich bereitgestellt wurden. Zudem müssen Unternehmensorganisationen in der Lage sein, ihre Systeme effizient zu skalieren, damit sie Zugriffsrichtlinien und -kontrollen kontinuierlich entwickeln und erzwingen können.

In der Regel delegiert die IT-Abteilung Entscheidungen bezüglich der Zugriffsgenehmigung an Entscheidungsträger im Unternehmen. Darüber hinaus kann die IT die Benutzer selbst in den Prozess einbeziehen. Beispielsweise müssen Benutzer, die in Europa auf vertrauliche Kundendaten in der Marketinganwendung eines Unternehmens zugreifen, die Richtlinien des Unternehmens kennen. Außerdem kennen Gastbenutzer die Anforderungen für die Behandlung von Daten in einer Organisation, zu der sie eingeladen wurden, möglicherweise nicht.

Organisationen können den Zugriffslebenszyklusprozess mit Technologien wie [dynamischen Gruppen](../enterprise-users/groups-dynamic-membership.md) in Verbindung mit der Benutzerbereitstellung in [SaaS-Anwendungen](../saas-apps/tutorial-list.md) oder [mithilfe des SCIM-Standards (System for Cross-Domain Identity Management) integrierten Apps](../app-provisioning/use-scim-to-provision-users-and-groups.md) automatisieren. Außerdem können Organisationen steuern, welche [Gastbenutzer Zugriff auf lokale Anwendungen haben](../external-identities/hybrid-cloud-to-on-premises.md). Diese Zugriffsrechte können dann regelmäßig mithilfe von wiederkehrenden [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md) überprüft werden.

## <a name="future-directions"></a>Künftige Ausrichtung

Bei Hybridumgebungen verfolgt Microsoft die Strategie, Bereitstellungen zu ermöglichen, bei denen die **Cloud die Steuerungsebene für Identitäten ist**, und lokale Verzeichnisse und andere Identitätssysteme, z. B. Active Directory und andere lokale Anwendungen, das Ziel für die Bereitstellung von Benutzern mit Zugriff sind. Diese Strategie wird fortgesetzt, um die Rechte, Identitäten und den Zugriff in und auf diese Anwendungen und Workloads zu gewährleisten, die auf diesen basieren. In diesem Endzustand können Organisationen die Produktivität der Endbenutzer vollständig aus der Cloud heraus steuern.

![Azure AD-Architektur](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den ersten Schritten auf diesem Weg finden Sie in den Azure AD-Bereitstellungsplänen unter <https://aka.ms/deploymentplans>. Diese stellen umfassende Anleitungen für die Bereitstellung von Azure Active Directory-Funktionen (Azure AD) bereit. In jedem Plan werden der Geschäftswert, Planungsüberlegungen, der Entwurf und Betriebsverfahren erläutert, die Sie zur erfolgreichen Bereitstellung gängiger Azure AD-Funktionen benötigen. Microsoft aktualisiert die Bereitstellungspläne kontinuierlich mit bewährten Methoden, die aus Kundenimplementierungen und anderem Feedback gewonnen wurden, wenn wir Azure AD neue Funktionen für die Verwaltung aus der Cloud hinzufügen.