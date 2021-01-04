---
title: Häufig gestellte Fragen (FAQ) – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie häufig gestellte Fragen und Antworten zu Azure und Azure Active Directory sowie zur Kennwortverwaltung und zum Anwendungszugriff.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a8fcb14ac397791822c3a9065275dbfb2b3b7dd
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860421"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Häufig gestellte Fragen zu Azure Active Directory
Azure Active Directory (Azure AD) ist eine umfassende IDaaS-Lösung (Identity as a Service) für sämtliche Aspekte von Identität, Zugriffsverwaltung und Sicherheit.

Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Zugreifen auf Azure und Azure Active Directory
**F: Warum wird „Keine Abonnements gefunden“ angezeigt, wenn ich versuche, im Azure-Portal auf Azure AD zuzugreifen?**

**A:** Für den Zugriff auf das Azure-Portal benötigt jeder Benutzer Berechtigungen mit einem Azure-Abonnement. Wenn Sie nicht über ein kostenpflichtiges Microsoft 365- oder Azure AD-Abonnement verfügen, müssen Sie ein kostenloses [Azure-Konto](https://azure.microsoft.com/free/
) oder ein kostenpflichtiges Abonnement aktivieren.

Weitere Informationen finden Sie unter

* [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**F: Welche Beziehung besteht zwischen Azure AD, Microsoft 365 und Azure?**

**A:** Azure AD bietet Ihnen allgemeine Identitäts- und Zugriffsfunktionen für alle Webdienste. Wenn Sie Microsoft 365, Microsoft Azure, Intune oder andere Anwendungen nutzen, verwenden Sie bereits Azure AD, um die Anmeldung und Zugriffsverwaltung für diese Dienste zu ermöglichen.

Alle Benutzer, die für die Verwendung von Webdiensten eingerichtet sind, sind als Benutzerkonten in mindestens einer Azure AD-Instanz definiert. Für diese Konten können Sie kostenlose Azure AD-Funktionen wie etwa den Zugriff auf Cloudanwendungen einrichten.

Kostenpflichtige Azure AD-Dienste wie Enterprise Mobility + Security ergänzen andere Webdienste wie Microsoft 365 und Microsoft Azure durch umfassende Verwaltungs- und Sicherheitslösungen für Unternehmen.

---

**F:  Was sind die Unterschiede zwischen Besitzer und globalem Administrator?**

**A:** Der Person, die sich für ein Azure-Abonnement registriert, wird standardmäßig die Rolle „Besitzer“ für Azure-Ressourcen zugewiesen. Ein Besitzer kann entweder ein Microsoft-Konto oder ein Geschäfts-, Schul- oder Unikonto aus dem Verzeichnis verwenden, dem das Azure-Abonnement zugeordnet ist.  Diese Rolle ist zum Verwalten von Diensten im Azure-Portal berechtigt.

Anderen Benutzern, die sich mithilfe des gleichen Abonnements anmelden und auf die Dienste zugreifen müssen, können Sie die geeignete [integrierte Rolle](../../role-based-access-control/built-in-roles.md) zuweisen. Zusätzliche Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

Der Person, die sich für ein Azure-Abonnement registriert, wird standardmäßig die globale Administratorrolle für das Verzeichnis zugewiesen. Der globale Administrator hat Zugriff auf alle Azure AD-Verzeichnisfunktionen. Azure AD weist eine andere Gruppe von Administratorrollen zum Verwalten der Features für Verzeichnisse und Identitäten. Diese Administratoren haben Zugriff auf verschiedene Funktionen im Azure-Portal. Die Rolle des Administrators bestimmt, welche Aktionen er ausführen kann. Mögliche Aktionen sind beispielsweise das Erstellen oder Bearbeiten von Benutzern, das Zuweisen von Administratorrollen zu anderen Personen, das Zurücksetzen von Benutzerkennwörtern sowie das Verwalten von Benutzerlizenzen oder Domänen.  Weitere Informationen zu Azure AD-Verzeichnisadministratoren und deren Rollen finden Sie unter [Zuweisen eines Benutzers zu Administratorrollen in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) und [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md).

Kostenpflichtige Azure AD-Dienste wie Enterprise Mobility + Security ergänzen zudem andere Webdienste wie Microsoft 365 und Microsoft Azure durch umfassende Verwaltungs- und Sicherheitslösungen für Unternehmen.

---
**F: Gibt es einen Bericht, der zeigt, wann meine Azure AD-Benutzerlizenzen ablaufen?**

**A:** Nein.  Ein solches Feature ist derzeit nicht verfügbar.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Erste Schritte mit Azure AD als Hybridanwendung


**F: Wie verlasse ich einen Mandanten, wenn ich als Projektmitarbeiter hinzugefügt werde?**

**A:** Wenn Sie dem Mandanten einer anderen Organisation als Projektmitarbeiter hinzugefügt werden, können Sie rechts oben über den „Mandantenumschalter“ zwischen Mandanten wechseln.  Derzeit gibt es noch keine Möglichkeit, die einladende Organisation zu verlassen, Microsoft arbeitet aber bereits daran.  In der Zwischenzeit können Sie die einladende Organisation bitten, Sie aus dem Mandanten zu entfernen.

---
**F: Wie kann ich mein lokales Verzeichnis mit Azure AD verbinden?**

**A:** Sie können mithilfe von Azure AD Connect eine Verbindung zwischen Ihrem lokalen Verzeichnis und Azure AD herstellen.

Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**F: Wie richte ich SSO zwischen meinem lokalen Verzeichnis und meinen Cloudanwendungen ein?**

**A:** Einmaliges Anmelden (Single Sign-On, SSO) muss lediglich zwischen Ihrem lokalen Verzeichnis und Azure AD eingerichtet werden. Solange Sie auf Ihre Cloudanwendungen über Azure AD zugreifen, wird vom Dienst für die Benutzer automatisch die richtige Authentifizierung mit ihren lokalen Anmeldeinformationen verwendet.

SSO kann in einer lokalen Umgebung mühelos mithilfe von Verbundlösungen wie Active Directory Federation Services (AD FS) oder durch Konfigurieren der Kennworthashsynchronisierung implementiert werden. Mithilfe des Assistenten zum Konfigurieren von Azure AD Connect können Sie problemlos beide Optionen bereitstellen.

Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**F: Bietet Azure AD ein Self-Service-Portal für Benutzer in meiner Organisation?**

**A:** Ja. Für Self-Service-Aktionen und den Anwendungszugriff der Benutzer stellt Azure AD den [Azure AD-Zugriffsbereich](https://myapps.microsoft.com) bereit. Für Microsoft 365-Kunden stehen viele dieser Funktionen bereits im [Office 365-Portal](https://portal.office.com) zur Verfügung.

Weitere Informationen finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

---
**F: Unterstützt mich Azure AD bei der Verwaltung meiner lokalen Infrastruktur?**

**A:** Ja. In der Premium Edition von Azure AD ist Azure AD Connect Health enthalten. Azure AD Connect Health unterstützt Sie bei der Überwachung Ihrer lokalen Identitätsinfrastruktur und Synchronisierungsdienste und vermittelt Ihnen wichtige Einblicke.  

Ausführlichere Informationen finden Sie unter [Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Kennwortverwaltung
**F: Kann ich die Azure AD-Funktion „Kennwort zurückschreiben“ ohne Kennwortsynchronisierung verwenden? (Ist es in diesem Szenario möglich, die Self-Service-Kennwortzurücksetzung von Azure AD mit dem Kennwortrückschreiben-Feature zu verwenden und Kennwörter nicht in der Cloud zu speichern?)**

**A:** Sie müssen Ihre Active Directory-Kennwörter nicht mit Azure AD synchronisieren, um die Funktion „Kennwort zurückschreiben“ aktivieren zu können. Bei Verwendung einer Verbundumgebung nutzt das einmalige Anmelden von Azure AD die Benutzerauthentifizierung des lokalen Verzeichnisses. In diesem Szenario muss das lokale Kennwort nicht in Azure AD nachverfolgt werden.

---
**F: Wie lange dauert das Zurückschreiben eines Kennworts in das lokale Active Directory?**

**A:** Kennwörter werden in Echtzeit zurückgeschrieben.

Weitere Informationen finden Sie unter [Erste Schritte mit der Kennwortverwaltung](../authentication/tutorial-enable-sspr.md).

---
**F: Kann ich die Funktion „Kennwort zurückschreiben“ für Kennwörter verwenden, die von einem Administrator verwaltet werden?**

**A:** Ja. Wenn die Funktion „Kennwort zurückschreiben“ aktiviert ist, werden die von einem Administrator ausgeführten Kennwortvorgänge in Ihre lokale Umgebung zurückgeschrieben.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Weitere Antworten auf kennwortbezogene Fragen finden Sie unter [Häufig gestellte Fragen zur Kennwortverwaltung](../authentication/active-directory-passwords-faq.md).
---
**F:  Was kann ich tun, wenn ich mein vorhandenes Microsoft 365-/Azure AD-Kennwort ändern möchte, aber das Kennwort vergessen habe?**

**A:** Für diese Situation stehen mehrere Optionen zur Verfügung.  Verwenden Sie die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR), sofern verfügbar.  Ob die SSPR funktioniert, hängt davon ab, wie sie konfiguriert ist.  Weitere Informationen finden Sie unter [Wie funktioniert das Portal für die Kennwortzurücksetzung?](../authentication/howto-sspr-deployment.md).

Für Microsoft 365-Benutzer kann der Administrator das Kennwort mithilfe der Anleitung unter [Zurücksetzen von Benutzerkennwörtern](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) zurücksetzen.

Für Azure AD-Konten können Administratoren Kennwörter mit einer der folgenden Methoden zurücksetzen:

- [Zurücksetzen der Konten im Azure-Portal](active-directory-users-reset-password-azure-portal.md)
- [Mithilfe von PowerShell](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Sicherheit
**F: Werden Konten nach einer bestimmten Anzahl misslungener Anmeldeversuche gesperrt, oder wird eine ausgereiftere Strategie angewandt?**

Wir verwenden eine ausgereiftere Strategie zum Sperren von Konten.  Sie basiert auf der IP-Adresse der Anforderung und den eingegebenen Kennwörtern. Die Dauer der Sperre wird ebenfalls gemäß der Wahrscheinlichkeit erhöht, dass es sich um einen Angriff handelt.  

**F:  Bestimmte (häufige) Kennwörter werden mit der Meldung „Dieses Kennwort wurde zu oft verwendet“ zurückgewiesen. Bezieht sich dies auf Kennwörter, die in der aktuellen Active Directory-Instanz verwendet werden?**

Dies bezieht sich auf Kennwörter, die global sehr häufig verwendet werden, z. B. alle Varianten von „Password“ und „123456“.

**F: Wird eine Anmeldeanforderung aus fragwürdigen Quellen (Botnets, Tor-Endpunkt) in einem B2C-Mandanten blockiert, oder setzt dies einen Basic- oder Premium Edition-Mandanten voraus?**

Wir haben ein Gateway, das Anforderungen filtert, gewissen Schutz gegen Botnets bietet und für alle B2C-Mandanten gilt.

## <a name="application-access"></a>Anwendungszugriff

**F: Wo finde ich eine Liste mit bereits in Azure AD integrierten Anwendungen und deren Funktionen?**

**A:** Für Azure AD stehen über 2.600 vorab integrierte Anwendungen von Microsoft, Anwendungsdienstanbietern und Partnern zur Verfügung. Alle vorab integrierten Anwendungen unterstützen einmaliges Anmelden (Single Sign-On, SSO). Mit SSO können Sie unter Verwendung der Anmeldeinformationen Ihres Unternehmens auf Ihre Apps zugreifen. Einige Anwendungen unterstützen auch die automatisierte Bereitstellung und Aufhebung der Bereitstellung.

Eine vollständige Liste mit integrierten Anwendungen finden Sie unter [Active Directory-Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**F: Was kann ich tun, wenn die Anwendung, die ich benötige, nicht im Azure AD-Marketplace verfügbar ist?**

**A:** Mit Azure AD Premium können Sie jede beliebige Anwendung hinzufügen und konfigurieren. Abhängig von den Funktionen Ihrer Anwendung und Ihren Präferenzen können Sie SSO und die automatische Bereitstellung konfigurieren.  

Weitere Informationen finden Sie unter

* [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](../manage-apps/configure-saml-single-sign-on.md)
* [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**F: Wie melden sich Benutzer mit Azure AD bei Anwendungen an?**

**A:** Azure AD bietet Benutzern mehrere Möglichkeiten zum Anzeigen von und Zugreifen auf Anwendungen. Nachfolgend finden Sie einige Beispiele für die verfügbaren Möglichkeiten:

* Azure AD-Zugriffsbereich
* Microsoft 365-Anwendungsstartprogramm
* Direkte Anmeldung bei Verbund-Apps
* Deep-Links zu verbundenen, kennwortbasierten oder vorhandene Apps

Weitere Informationen finden Sie unter [End-user experiences for applications in Azure Active Directory](../manage-apps/end-user-experiences.md) (Benutzeroberflächen von Endbenutzern für Anwendungen in Azure Active Directory).

---
**F: Auf welche Arten ermöglicht Azure AD die Authentifizierung und das einmalige Anmelden bei Anwendungen?**

**A:** Azure AD unterstützt für die Authentifizierung und Autorisierung viele standardisierte Protokolle wie SAML 2.0, OpenID Connect, OAuth 2.0 und WS-Verbund. Außerdem unterstützt Azure AD Kennworttresore und Funktionen für die automatisierte Anmeldung für Apps, die nur die formularbasierte Authentifizierung unterstützen.  

Weitere Informationen finden Sie unter

* [Authentifizierungsszenarien für Azure AD](../develop/authentication-vs-authorization.md)
* [Active Directory-Authentifizierungsprotokolle](/previous-versions/azure/dn151124(v=azure.100))
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

---
**F: Kann ich lokal ausgeführte Anwendungen hinzufügen?**

**A:** Mit dem Azure AD-Anwendungsproxy haben Sie einfachen und sicheren Zugriff auf lokale Webanwendungen Ihrer Wahl. Sie können auf diese Anwendungen auf die gleiche Weise zugreifen wie auf Ihre SaaS-Apps (Software as a Service) in Azure AD. Ein VPN bzw. eine Änderung der Netzwerkinfrastruktur ist nicht erforderlich.  

Weitere Informationen finden Sie unter [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](../manage-apps/application-proxy.md).

---
**F: Wie kann ich für Benutzer, die auf eine bestimmte Anwendung zugreifen, die mehrstufige Authentifizierung erforderlich machen?**

**A:** Mit dem bedingten Azure AD-Zugriff können Sie jeder Anwendung eine individuelle Zugriffsrichtlinie zuweisen. In dieser Richtlinie können Sie festlegen, dass die mehrstufige Authentifizierung immer oder nur dann erforderlich sein soll, wenn Benutzer gerade nicht mit dem lokalen Netzwerk verbunden sind.  

Weitere Informationen finden Sie unter [Sicherer Zugriff auf Microsoft 365 und andere Apps, die mit Azure Active Directory verbunden sind](../conditional-access/overview.md).

---
**F: Was ist eine automatisierte Benutzerbereitstellung für SaaS-Apps?**

**A:** Azure AD ermöglicht die Automatisierung der Erstellung, Wartung und Entfernung von Benutzeridentitäten in vielen gängigen SaaS-Cloud-Apps.

Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

---
**F:  Kann ich mit Azure AD eine sichere LDAP-Verbindung einrichten?**

**A:**  Nein. Das LDAP-Protokoll (Lightweight Directory Access Protocol) oder Secure LDAP wird von Azure AD nicht direkt unterstützt. Allerdings können Sie eine Azure AD Domain Services (Azure AD DS)-Instanz in Ihrem Azure AD-Mandanten mit ordnungsgemäß konfigurierten Netzwerksicherheitsgruppen über das Azure-Netzwerk aktivieren, um LDAP-Konnektivität zu erreichen. Weitere Informationen finden Sie unter [Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](../../active-directory-domain-services/tutorial-configure-ldaps.md).
