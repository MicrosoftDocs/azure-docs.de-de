---
title: Schnelles Reagieren auf Sicherheitsvorfälle mit Azure Active Directory
description: Schnelles Reagieren auf Bedrohungen mit cloudbasierten Azure AD-Identitäten
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836681"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Schnelles Reagieren auf Sicherheitsvorfälle mit Azure AD

Es kann sehr aufwendig erscheinen, Ihre Worker in der aktuellen Umgebung zu schützen, insbesondere wenn Sie schnell reagieren und Zugriff auf viele Dienste bereitstellen müssen. In diesem Artikel finden Sie eine umfassende Liste aller auszuführenden Aktionen, die Ihnen dabei helfen soll, die Azure AD Features basierend auf Ihrem Lizenztyp zu identifizieren und zu priorisieren. Azure AD bietet viele Features und zahlreiche Sicherheitsebenen für Ihre Identitäten, sodass die Auswahl der relevanten Features manchmal überwältigend sein kann. Viele Unternehmen verwenden bereits die Cloud oder arbeiten mit Nachdruck am Umstieg. Dieses Dokument soll Ihnen ermöglichen, Dienste schnell bereitzustellen und dabei den Schutz Ihrer Identitäten im Blick zu behalten. 

Jede Tabelle enthält eine konsistente Sicherheitsempfehlung zum Schutz von Administrator- und Benutzeridentitäten vor den wichtigsten Sicherheitsangriffen (Breach Replay, Phishing und Kennwortspray), während gleichzeitig die Auswirkungen auf die Benutzer minimiert und die Benutzerfreundlichkeit verbessert werden sollen. 

Die Anleitungen ermöglichen Administratoren auch den Zugriff auf SaaS- und lokale Anwendungen auf sichere und geschützte Weise. Sie gelten für Cloud- und Hybrididentitäten (synchronisiert) und für Benutzer, die remote oder vor Ort arbeiten.

Diese Checkliste erläutert folgende Aspekte und hilft Ihnen dabei, empfohlene wichtige Aktionen zum Schutz Ihrer Organisation sofort auszuführen:

- Verstärken Sie Ihre Anmeldeinformationen.
- Verringern Sie die Angriffsfläche.
- Automatisieren Sie die Reaktion auf Bedrohungen.
- Nutzen Sie Cloud Intelligence.
- Aktivieren Sie den Self-Service für Endbenutzer.

## <a name="prerequisites"></a>Voraussetzungen

In dieser Anleitung wird davon ausgegangen, dass Ihre cloudbasierten oder Hybrididentitäten bereits in Azure AD eingerichtet wurden. Hilfe bei der Auswahl des Identitätstyps finden Sie im Artikel [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../hybrid/choose-ad-authn.md). 

## <a name="summary"></a>Zusammenfassung

Bei sicheren Identitätsinfrastrukturen gilt es eine Vielzahl von Aspekten zu berücksichtigen. Bei dieser Prüfliste liegt das Hauptaugenmerk jedoch auf einer sichereren Identitätsinfrastruktur, die es Benutzern ermöglicht, remote zu arbeiten. Das Schützen Ihrer Identität ist nur ein Teil eines Sicherheitskonzepts – auch der Schutz von Daten, Anwendungen und Geräten sollte berücksichtigt werden.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Leitfaden für Azure AD Free-, Office 365- oder Microsoft 365-Kunden.

Es gibt eine Reihe von Empfehlungen, die Kunden mit Azure AD Free, Office 365- oder Microsoft 365-Apps zum Schutz ihrer Benutzeridentitäten anwenden sollten. In der folgenden Tabelle sind die wichtigsten Aktionen für die folgenden Lizenzabonnements aufgeführt:

- Office 365 (Office 365 E1, E3, E5, F1, A1, A3, A5)
- Microsoft 365 (Business Basic, Apps for Business, Business Standard, Business Premium, A1)
- Azure AD Free (enthalten in Azure, Dynamics 365, Intune und Power Platform)

| Empfohlene Maßnahme | Detail |
| --- | --- |
| [Sicherheitsstandards aktivieren](concept-fundamentals-security-defaults.md) | Schützt alle Benutzeridentitäten und Anwendungen durch Aktivieren von MFA und Blockieren der Legacy-Authentifizierung |
| [Kennworthashsynchronisierung aktivieren](../hybrid/how-to-connect-password-hash-synchronization.md) (bei Verwendung von Hybrididentitäten) | Sorgt für Redundanz bei der Authentifizierung und erhöht die Sicherheit (einschließlich Smart Lockout, IP-Sperre und der Möglichkeit, kompromittierte Anmeldeinformationen zu ermitteln). |
| [AD FS Smart Lockout aktivieren](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (sofern zutreffend) | Schützt Ihre Benutzer vor der Sperrung von Extranetkonten durch böswillige Aktivitäten. |
| [Azure Active Directory Smart Lockout aktivieren](../authentication/howto-password-smart-lockout.md) (bei Verwendung verwalteter Identitäten) | Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. |
| [Benutzereinwilligung für Anwendungen deaktivieren](../manage-apps/configure-user-consent.md) | Der Workflow für die Administratoreinwilligung bietet Administratoren eine sichere Möglichkeit zum Gewähren von Zugriff auf Anwendungen, die eine Administratorgenehmigung erfordern. Auf diese Weise legen Endbenutzer keine Unternehmensdaten offen. Microsoft empfiehlt die Deaktivierung zukünftiger Vorgänge für die Benutzereinwilligung, um die Angriffsfläche und dieses Risiko zu verringern. |
| [Unterstützte SaaS-Anwendungen aus dem Katalog in Azure AD integrieren und einmaliges Anmelden aktivieren](../manage-apps/add-application-portal.md) | Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen. Einige der von Ihrer Organisation verwendeten Anwendungen sind wahrscheinlich im Katalog enthalten, der direkt über das Azure-Portal zugänglich ist. Ermöglicht den sicheren Remotezugriff auf SaaS-Unternehmensanwendungen mit höherer Benutzerfreundlichkeit (SSO). |
| [Bereitstellen und Aufheben der Bereitstellung von Benutzern für SaaS-Anwendungen automatisieren](../app-provisioning/user-provisioning.md) (sofern zutreffend) | Erstellt automatisch Benutzeridentitäten und -rollen in den Cloudanwendungen (SaaS), auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Dadurch wird die Sicherheit in Ihrer Organisation noch weiter gesteigert. |
| [Sicheren Hybridzugriff aktivieren: Schützen von Legacy-Apps mit vorhandenen App-Bereitstellungscontrollern und -netzwerken](../manage-apps/secure-hybrid-access.md) (sofern zutreffend) | Veröffentlichen und Schützen Sie Ihre lokalen und cloudbasierten Legacy-Authentifizierungsanwendungen, indem Sie sie über vorhandene Anwendungsbereitstellungscontroller oder Netzwerke mit Azure AD verbinden. |
| [Self-Service-Kennwortzurücksetzung aktivieren](../authentication/tutorial-enable-sspr.md) (gilt nur für ausschließliche Cloudkonten) | Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann. |
| [Nach Möglichkeit nicht globale Administratorrollen verwenden](../roles/permissions-reference.md) | Gewähren Sie Ihren Administratoren nur den Zugriff, den sie benötigen, und nur für Bereiche, auf die sie zugreifen müssen. Nicht alle Administratoren müssen globale Administratoren sein. |
| [Kennwortleitfaden von Microsoft aktivieren](https://www.microsoft.com/research/publication/password-guidance/) | Wenn Ihre Benutzer ihre Kennwörter nicht mehr nach einem festgelegten Zeitplan ändern müssen, und Sie die Anforderungen an die Kennwortkomplexität abschaffen, fällt es Ihren Benutzern leichter, sich Kennwörter zu merken und sichere Kennwörter festzulegen. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Leitfaden für Azure AD Premium Plan 1-Kunden

In der folgenden Tabelle sind die wichtigsten Aktionen für die folgenden Lizenzabonnements aufgeführt:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| Empfohlene Maßnahme | Detail |
| --- | --- |
| [Kombinierte Registrierung für Azure AD MFA und SSPR zum Vereinfachen der Benutzerregistrierung aktivieren](../authentication/howto-registration-mfa-sspr-combined.md) | Ermöglichen Sie Ihren Benutzern die Registrierung über eine gemeinsame Umgebung sowohl für Azure AD Multi-Factor Authentication als auch Self-Service-Kennwortzurücksetzung. |
| [MFA-Einstellungen für Ihre Organisation konfigurieren](../authentication/howto-mfa-getstarted.md) | Stellt sicher, dass Konten durch Multi-Factor Authentication vor Änderungen geschützt sind |
| [Aktivieren der Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr.md) | Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann. |
| [Kennwortrückschreiben implementieren](../authentication/tutorial-enable-sspr-writeback.md) (bei Verwendung von Hybrididentitäten) | Lassen Sie zu, dass Kennwortänderungen in der Cloud in eine lokale Windows Server Active Directory-Umgebung zurückgeschrieben werden. |
| Richtlinien für bedingten Zugriff erstellen und aktivieren | [MFA für Administratoren, um Konten zu schützen, denen Administratorrechte zugewiesen sind](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blockieren älterer Authentifizierungsprotokolle wegen des höheren Risikos im Zusammenhang mit Legacy-Authentifizierungsprotokollen](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA für alle Benutzer und Anwendungen, um eine ausgeglichene MFA-Richtlinie für Ihre Umgebung zu schaffen und Ihre Benutzer und Anwendungen zu schützen](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Erzwingen von MFA für die Azure-Verwaltung, um durch Erzwingung der mehrstufigen Authentifizierung für jeden Benutzer, der auf Azure-Ressourcen zugreift, Ressourcen mit erhöhten Rechten zu schützen](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Kennworthashsynchronisierung aktivieren](../hybrid/how-to-connect-password-hash-synchronization.md) (bei Verwendung von Hybrididentitäten) | Sorgt für Redundanz bei der Authentifizierung und erhöht die Sicherheit (einschließlich Smart Lockout, IP-Sperre und der Möglichkeit, kompromittierte Anmeldeinformationen zu ermitteln). |
| [AD FS Smart Lockout aktivieren](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (sofern zutreffend) | Schützt Ihre Benutzer vor der Sperrung von Extranetkonten durch böswillige Aktivitäten. |
| [Azure Active Directory Smart Lockout aktivieren](../authentication/howto-password-smart-lockout.md) (bei Verwendung verwalteter Identitäten) | Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. |
| [Benutzereinwilligung für Anwendungen deaktivieren](../manage-apps/configure-user-consent.md) | Der Workflow für die Administratoreinwilligung bietet Administratoren eine sichere Möglichkeit zum Gewähren von Zugriff auf Anwendungen, die eine Administratorgenehmigung erfordern. Auf diese Weise legen Endbenutzer keine Unternehmensdaten offen. Microsoft empfiehlt die Deaktivierung zukünftiger Vorgänge für die Benutzereinwilligung, um die Angriffsfläche und dieses Risiko zu verringern. |
| [Remotezugriff auf lokale Legacy-Anwendungen mit Anwendungsproxy aktivieren](../manage-apps/application-proxy-add-on-premises-application.md) | Aktivieren Sie den Azure AD-Anwendungsproxy, und integrieren Sie ihn mit Legacy-Apps, damit Benutzer sicher auf lokale Anwendungen zugreifen können, indem sie sich mit ihrem Azure AD-Konto anmelden. |
| [Sicheren Hybridzugriff aktivieren: Schützen von Legacy-Apps mit vorhandenen App-Bereitstellungscontrollern und -netzwerken](../manage-apps/secure-hybrid-access.md) (sofern zutreffend) | Veröffentlichen und Schützen Sie Ihre lokalen und cloudbasierten Legacy-Authentifizierungsanwendungen, indem Sie sie über vorhandene Anwendungsbereitstellungscontroller oder Netzwerke mit Azure AD verbinden. |
| [Unterstützte SaaS-Anwendungen aus dem Katalog in Azure AD integrieren und einmaliges Anmelden aktivieren](../manage-apps/add-application-portal.md) | Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen. Einige der von Ihrer Organisation verwendeten Anwendungen sind wahrscheinlich im Katalog enthalten, der direkt über das Azure-Portal zugänglich ist. Ermöglicht den sicheren Remotezugriff auf SaaS-Unternehmensanwendungen mit höherer Benutzerfreundlichkeit (SSO). |
| [Bereitstellen und Aufheben der Bereitstellung von Benutzern für SaaS-Anwendungen automatisieren](../app-provisioning/user-provisioning.md) (sofern zutreffend) | Erstellt automatisch Benutzeridentitäten und -rollen in den Cloudanwendungen (SaaS), auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Dadurch wird die Sicherheit in Ihrer Organisation noch weiter gesteigert. |
| [Bedingten Zugriff aktivieren – gerätebasiert](../conditional-access/require-managed-devices.md) | Verbessert die Sicherheit und die Benutzerfreundlichkeit durch gerätebasierten bedingten Zugriff. Mit diesem Schritt wird sichergestellt, dass Benutzer nur von Geräten aus Zugriff erhalten, die Ihren Sicherheits- und Compliancestandards entsprechen. Diese Geräte werden auch als verwaltete Geräte bezeichnet. Verwaltete Geräte können mit Intune konforme oder in Azure AD Hybrid eingebundene Geräte sein. |
| [Kennwortschutz aktivieren](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Schützt Benutzer vor der Verwendung schwacher und einfach zu erratender Kennwörter. |
| [Festlegen mehrerer globaler Administratoren](../roles/security-emergency-access.md) | Weisen Sie mindestens zwei permanente, globale Nur-Cloud-Administratorkonten für die Verwendung bei einem Notfall zu. Diese Konten sind nicht für die tägliche Verwendung vorgesehen und sollten lange und komplexe Kennwörter haben. Notfallkonten stellen sicher, dass Sie im Notfall auf den Dienst zugreifen können. |
| [Nach Möglichkeit nicht globale Administratorrollen verwenden](../roles/permissions-reference.md) | Gewähren Sie Ihren Administratoren nur den Zugriff, den sie benötigen, und nur für Bereiche, auf die sie zugreifen müssen. Nicht alle Administratoren müssen globale Administratoren sein. |
| [Kennwortleitfaden von Microsoft aktivieren](https://www.microsoft.com/research/publication/password-guidance/) | Wenn Ihre Benutzer ihre Kennwörter nicht mehr nach einem festgelegten Zeitplan ändern müssen, und Sie die Anforderungen an die Kennwortkomplexität abschaffen, fällt es Ihren Benutzern leichter, sich Kennwörter zu merken und sichere Kennwörter festzulegen. |
| [Erstellen eines Plans für den Zugriff durch Gastbenutzer](../external-identities/what-is-b2b.md) | Arbeiten Sie mit Gastbenutzern zusammen, indem Sie ihnen ermöglichen, sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei Ihren Anwendungen und Diensten anzumelden. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Leitfaden für Azure AD Premium Plan 2-Kunden

In der folgenden Tabelle sind die wichtigsten Aktionen für die folgenden Lizenzabonnements aufgeführt:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Empfohlene Maßnahme | Detail |
| --- | --- |
| [Kombinierte Registrierung für Azure AD MFA und SSPR zum Vereinfachen der Benutzerregistrierung aktivieren](../authentication/howto-registration-mfa-sspr-combined.md) | Ermöglichen Sie Ihren Benutzern die Registrierung über eine gemeinsame Umgebung sowohl für Azure AD Multi-Factor Authentication als auch Self-Service-Kennwortzurücksetzung. |
| [MFA-Einstellungen für Ihre Organisation konfigurieren](../authentication/howto-mfa-getstarted.md) | Stellt sicher, dass Konten durch Multi-Factor Authentication vor Änderungen geschützt sind |
| [Aktivieren der Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr.md) | Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann. |
| [Kennwortrückschreiben implementieren](../authentication/tutorial-enable-sspr-writeback.md) (bei Verwendung von Hybrididentitäten) | Lassen Sie zu, dass Kennwortänderungen in der Cloud in eine lokale Windows Server Active Directory-Umgebung zurückgeschrieben werden. |
| [Identity Protection-Richtlinien zum Erzwingen der MFA-Registrierung aktivieren](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Verwalten Sie die Umsetzung von Azure AD Multi-Factor Authentication (MFA). |
| [Identity Protection-Richtlinien für Benutzer und Anmelderisiken aktivieren](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Aktivieren Sie Identity Protection-Richtlinien für Benutzer und Anmelderichtlinien. Die empfohlene Anmelderichtlinie zielt auf Anmeldungen mit mittlerem Risiko ab und erfordert MFA. Benutzerrichtlinien sollten auf Benutzer mit hohem Risiko ausgerichtet sein und eine Kennwortänderung erfordern. |
| Richtlinien für bedingten Zugriff erstellen und aktivieren | [MFA für Administratoren, um Konten zu schützen, denen Administratorrechte zugewiesen sind](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blockieren älterer Authentifizierungsprotokolle wegen des höheren Risikos im Zusammenhang mit Legacy-Authentifizierungsprotokollen](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Erzwingen von MFA für die Azure-Verwaltung, um durch Erzwingung der mehrstufigen Authentifizierung für jeden Benutzer, der auf Azure-Ressourcen zugreift, Ressourcen mit erhöhten Rechten zu schützen](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Kennworthashsynchronisierung aktivieren](../hybrid/how-to-connect-password-hash-synchronization.md) (bei Verwendung von Hybrididentitäten) | Sorgt für Redundanz bei der Authentifizierung und erhöht die Sicherheit (einschließlich Smart Lockout, IP-Sperre und der Möglichkeit, kompromittierte Anmeldeinformationen zu ermitteln). |
| [AD FS Smart Lockout aktivieren](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (sofern zutreffend) | Schützt Ihre Benutzer vor der Sperrung von Extranetkonten durch böswillige Aktivitäten. |
| [Azure Active Directory Smart Lockout aktivieren](../authentication/howto-password-smart-lockout.md) (bei Verwendung verwalteter Identitäten) | Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. |
| [Benutzereinwilligung für Anwendungen deaktivieren](../manage-apps/configure-user-consent.md) | Der Workflow für die Administratoreinwilligung bietet Administratoren eine sichere Möglichkeit zum Gewähren von Zugriff auf Anwendungen, die eine Administratorgenehmigung erfordern. Auf diese Weise legen Endbenutzer keine Unternehmensdaten offen. Microsoft empfiehlt die Deaktivierung zukünftiger Vorgänge für die Benutzereinwilligung, um die Angriffsfläche und dieses Risiko zu verringern. |
| [Remotezugriff auf lokale Legacy-Anwendungen mit Anwendungsproxy aktivieren](../manage-apps/application-proxy-add-on-premises-application.md) | Aktivieren Sie den Azure AD-Anwendungsproxy, und integrieren Sie ihn mit Legacy-Apps, damit Benutzer sicher auf lokale Anwendungen zugreifen können, indem sie sich mit ihrem Azure AD-Konto anmelden. |
| [Sicheren Hybridzugriff aktivieren: Schützen von Legacy-Apps mit vorhandenen App-Bereitstellungscontrollern und -netzwerken](../manage-apps/secure-hybrid-access.md) (sofern zutreffend) | Veröffentlichen und Schützen Sie Ihre lokalen und cloudbasierten Legacy-Authentifizierungsanwendungen, indem Sie sie über vorhandene Anwendungsbereitstellungscontroller oder Netzwerke mit Azure AD verbinden. |
| [Unterstützte SaaS-Anwendungen aus dem Katalog in Azure AD integrieren und einmaliges Anmelden aktivieren](../manage-apps/add-application-portal.md) | Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen. Einige der von Ihrer Organisation verwendeten Anwendungen sind wahrscheinlich im Katalog enthalten, der direkt über das Azure-Portal zugänglich ist. Ermöglicht den sicheren Remotezugriff auf SaaS-Unternehmensanwendungen mit höherer Benutzerfreundlichkeit (SSO). |
| [Bereitstellen und Aufheben der Bereitstellung von Benutzern für SaaS-Anwendungen automatisieren](../app-provisioning/user-provisioning.md) (sofern zutreffend) | Erstellt automatisch Benutzeridentitäten und -rollen in den Cloudanwendungen (SaaS), auf die Benutzer Zugriff benötigen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Dadurch wird die Sicherheit in Ihrer Organisation noch weiter gesteigert. |
| [Bedingten Zugriff aktivieren – gerätebasiert](../conditional-access/require-managed-devices.md) | Verbessert die Sicherheit und die Benutzerfreundlichkeit durch gerätebasierten bedingten Zugriff. Mit diesem Schritt wird sichergestellt, dass Benutzer nur von Geräten aus Zugriff erhalten, die Ihren Sicherheits- und Compliancestandards entsprechen. Diese Geräte werden auch als verwaltete Geräte bezeichnet. Verwaltete Geräte können mit Intune konforme oder in Azure AD Hybrid eingebundene Geräte sein. |
| [Kennwortschutz aktivieren](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Schützt Benutzer vor der Verwendung schwacher und einfach zu erratender Kennwörter. |
| [Festlegen mehrerer globaler Administratoren](../roles/security-emergency-access.md) | Weisen Sie mindestens zwei permanente, globale Nur-Cloud-Administratorkonten für die Verwendung bei einem Notfall zu. Diese Konten sind nicht für die tägliche Verwendung vorgesehen und sollten lange und komplexe Kennwörter haben. Notfallkonten stellen sicher, dass Sie im Notfall auf den Dienst zugreifen können. |
| [Nach Möglichkeit nicht globale Administratorrollen verwenden](../roles/permissions-reference.md) | Gewähren Sie Ihren Administratoren nur den Zugriff, den sie benötigen, und nur für Bereiche, auf die sie zugreifen müssen. Nicht alle Administratoren müssen globale Administratoren sein. |
| [Kennwortleitfaden von Microsoft aktivieren](https://www.microsoft.com/research/publication/password-guidance/) | Wenn Ihre Benutzer ihre Kennwörter nicht mehr nach einem festgelegten Zeitplan ändern müssen, und Sie die Anforderungen an die Kennwortkomplexität abschaffen, fällt es Ihren Benutzern leichter, sich Kennwörter zu merken und sichere Kennwörter festzulegen. |
| [Erstellen eines Plans für den Zugriff durch Gastbenutzer](../external-identities/what-is-b2b.md) | Arbeiten Sie mit Gastbenutzern zusammen, indem Sie ihnen ermöglichen, sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei Ihren Anwendungen und Diensten anzumelden. |
| [Aktivieren von Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Ermöglicht Ihnen, den Zugriff auf wichtige Ressourcen in Ihrer Organisation zu verwalten, zu steuern und zu überwachen, um sicherzustellen, dass Administratoren nur bei Bedarf und mit Genehmigung Zugriff erhalten. |

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zur Bereitstellung der einzelnen Features von Azure AD finden Sie unter [Azure Active Directory-Bereitstellungspläne](active-directory-deployment-plans.md).

- Eine umfassende Azure AD-Bereitstellungsprüfliste finden Sie im Artikel [Bereitstellungshandbuch für Azure Active Directory-Features](active-directory-deployment-checklist-p2.md).