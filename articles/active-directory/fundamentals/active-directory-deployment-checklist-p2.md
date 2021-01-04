---
title: Prüfliste für die Azure AD-Bereitstellung
description: Bereitstellungsprüfliste für Azure Active Directory-Features
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836919"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Bereitstellungshandbuch für Azure Active Directory-Features

Es mag für Ihre Organisation als unlösbare Aufgabe erscheinen, Azure Active Directory (Azure AD) bereitzustellen und sicher zu halten. In diesem Artikel werden allgemeine Aufgaben vorgestellt, deren Ausführung in Phasen im Verlauf von 30, 60, 90 oder mehr Tagen sich für Kunden als nützlich bei der Verbesserung ihres Sicherheitsstatus erweist. Selbst Unternehmen, die Azure AD bereits bereitgestellt haben, können mit diesem Leitfaden sicherstellen, dass sie ihre Investitionen optimal nutzen.

Eine gut geplante und ausgeführte Identitätsinfrastruktur ebnet den Weg zum sicheren Zugriff ausschließlich durch bekannte Benutzer und Geräte auf Ihre Produktivitätsworkloads und Daten.

Darüber hinaus können Kunden anhand ihres [Identity Secure Score](identity-secure-score.md) überprüfen, welchem Maße sie die bewährten Methoden von Microsoft umsetzen. Überprüfen Sie Ihren Secure Score vor und nach der Implementierung dieser Empfehlungen, um festzustellen, wie gut Ihre Leistung verglichen mit anderen Unternehmen in Ihrer Branche und Organisationen Ihrer Größe ist.

## <a name="prerequisites"></a>Voraussetzungen

Viele der Empfehlungen in diesem Leitfaden können mit Azure AD Free oder ganz ohne Lizenz implementiert werden. Wenn Lizenzen erforderlich sind, ist angegeben, welche Lizenz zum Ausführen der jeweiligen Aufgabe mindestens erforderlich ist.

Weitere Informationen zur Lizenzierung finden Sie auf den folgenden Seiten:

* [Azure AD-Lizenzierung](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Abrechnungsmodell für externe Identitäten in Azure AD](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Phase 1: Erstellen eines Fundaments aus Sicherheit

In dieser Phase aktivieren Administratoren die grundlegenden Sicherheitsfunktionen, um in Azure AD eine sichere und benutzerfreundliche Basis zu schaffen, bevor wir normale Benutzerkonten importieren oder erstellen. In dieser grundlegende Phase wird sichergestellt, dass Sie sich von Anfang an in einem sichereren Zustand befinden und Ihre Endbenutzer nur ein Mal in neue Konzepte eingeführt werden müssen.

| Aufgabe | Detail | Erforderliche Lizenz |
| ---- | ------ | ---------------- |
| [Festlegen mehrerer globaler Administratoren](../roles/security-emergency-access.md) | Weisen Sie mindestens zwei permanente, globale Nur-Cloud-Administratorkonten für die Verwendung bei einem Notfall zu. Diese Konten sind nicht für die tägliche Verwendung vorgesehen und sollten lange und komplexe Kennwörter haben. | Azure AD Free |
| [Nach Möglichkeit nicht globale Administratorrollen verwenden](../roles/permissions-reference.md) | Gewähren Sie Ihren Administratoren nur den Zugriff, den sie benötigen, und nur für Bereiche, auf die sie zugreifen müssen. Nicht alle Administratoren müssen globale Administratoren sein. | Azure AD Free |
| [Aktivieren von Privileged Identity Management für die Nachverfolgung der Nutzung der Administratorrolle](../privileged-identity-management/pim-getting-started.md) | Aktivieren Sie Privileged Identity Management, um mit der Nachverfolgung der Nutzung der Administratorrolle zu beginnen. | Azure AD Premium P2 |
| [Erfolgreicher Rollout der Self-Service-Kennwortzurücksetzung](../authentication/howto-sspr-deployment.md) | Reduzieren Sie Anrufe beim Helpdesk für Kennwortzurücksetzungen, indem Sie Mitarbeitern mithilfe von Richtlinien, die Sie als Administrator steuern, das Zurücksetzen ihrer eigenen Kennwörter ermöglichen. | |
| [Erstellen einer benutzerdefinierten organisationsspezifischen Liste gesperrter Kennwörter](../authentication/tutorial-configure-custom-password-protection.md) | Verhindern Sie, dass Benutzer Kennwörter erstellen, die Wörter oder Ausdrücke enthalten, die in Ihrer Organisation bzw. Ihrem Bereich häufig vorkommen. | |
| [Aktivieren von Azure AD-Kennwortschutz für die lokale Integration](../authentication/concept-password-ban-bad-on-premises.md) | Erweitern Sie die Liste der gesperrten Kennwörtern auf Ihr lokales Verzeichnis, um sicherzustellen, dass auch lokal festgelegte Kennwörter den globalen und mandantenspezifischen Listen gesperrter Kennwörter entsprechen. | Azure AD Premium P1 |
| [Kennwortleitfaden von Microsoft aktivieren](https://www.microsoft.com/research/publication/password-guidance/) | Wenn Ihre Benutzer ihre Kennwörter nicht mehr nach einem festgelegten Zeitplan ändern müssen, und Sie die Anforderungen an die Kennwortkomplexität abschaffen, fällt es Ihren Benutzern leichter, sich Kennwörter zu merken und sichere Kennwörter festzulegen. | Azure AD Free |
| [Deaktivieren von regelmäßigen Kennwortzurücksetzungen für cloudbasierte Benutzerkonten](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Regelmäßige Kennwortzurücksetzungen regen Ihre Benutzer dazu an, ihre vorhandenen Kennwörter lediglich mit geänderten Zahlen wiederzuverwenden. Verwenden Sie die Richtlinien aus dem Kennwortleitfaden von Microsoft, und spiegeln Sie Ihre lokalen Richtlinie für reine Cloudbenutzer. | Azure AD Free |
| [Anpassen von Azure Active Directory Smart Lockout](../authentication/howto-password-smart-lockout.md) | Verhindern Sie, dass Sperrungen von cloudbasierten Benutzern für lokale Active Directory-Benutzer repliziert werden. | |
| [Aktivieren der intelligenten Extranetsperre für AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Die intelligente Extranetsperre schützt vor Brute-Force-Angriffen, bei denen das Kennwort erraten wird, und lässt gültige AD FS-Benutzer ihre Konten weiterhin verwenden. | |
| [Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff](../conditional-access/block-legacy-authentication.md) | Blockieren von Legacyauthentifizierungsprotokolle wie POP, SMTP, IMAP und MAPI, die keine Multi-Factor Authentication erzwingen können und dadurch bevorzugte Einstiegspunkte für Angreifer sind. | Azure AD Premium P1 |
| [Bereitstellen von Azure AD Multi-Factor Authentication mit Richtlinien für bedingten Zugriff](../authentication/howto-mfa-getstarted.md) | Legen Sie mithilfe von Richtlinien für bedingten Zugriff fest, dass Benutzer beim Zugriff auf sensible Anwendungen eine zweistufige Überprüfung durchführen müssen. | Azure AD Premium P1 |
| [Aktivieren der Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) | Aktivieren Sie die Nachverfolgung von riskanten Anmeldungen und kompromittierten Anmeldeinformationen für Benutzer in Ihrer Organisation. | Azure AD Premium P2 |
| [Verwenden von Risikoerkennungen zum Auslösen von mehrstufiger Authentifizierung und Kennwortänderungen](../authentication/tutorial-risk-based-sspr-mfa.md) | Aktivieren Sie eine Automatisierung, die Ereignisse risikobasiert auslösen kann, z.B. mehrstufige Authentifizierung, Kennwortzurücksetzung und das Blockieren von Anmeldungen. | Azure AD Premium P2 |
| [Aktivieren der kombinierten Registrierung für die Self-Service-Kennwortzurücksetzung und Azure AD Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) | Ermöglichen Sie Ihren Benutzern die Registrierung über eine gemeinsame Umgebung sowohl für Azure AD Multi-Factor Authentication als auch Self-Service-Kennwortzurücksetzung. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Phase 2: Importieren von Benutzern, Aktivieren der Synchronisierung und Verwalten von Geräten

Als Nächstes ergänzen wir die in Phase 1 geschaffene Grundlage durch das Importieren unserer Benutzer und das Aktivieren der Synchronisierung, das Planen des Gastzugriffs und das Vorbereiten der Unterstützung zusätzlicher Funktionen.

| Aufgabe | Detail | Erforderliche Lizenz |
| ---- | ------ | ---------------- |
| [Installieren von Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Bereiten Sie die Synchronisierung von Benutzern aus Ihrem vorhandenen lokalen Verzeichnis mit der Cloud vor. | Azure AD Free |
| [Implementieren der Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md) | Synchronisieren Sie Kennworthashes, um die Replikation von Kennwortänderungen, die Erkennung und Behandlung falscher Kennwörter und das Melden kompromittierter Anmeldeinformationen zu ermöglichen. | Azure AD Free |
| [Implementieren der Kennwortrückschreibung](../authentication/tutorial-enable-sspr-writeback.md) | Lassen Sie zu, dass Kennwortänderungen in der Cloud in eine lokale Windows Server Active Directory-Umgebung zurückgeschrieben werden. | Azure AD Premium P1 |
| [Implementieren von Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Aktivieren Sie die Überwachung wichtiger Integritätsstatistiken für Ihre Azure AD Connect-Server, AD FS-Server und Domänencontroller. | Azure AD Premium P1 |
| [Zuweisen von Lizenzen zu Benutzer nach Gruppenmitgliedschaft in Azure Active Directory](../enterprise-users/licensing-groups-assign.md) | Sparen Sie Zeit und Aufwand durch das Erstellen von Lizenzierungsgruppen, die Funktionen anhand von Gruppen anstatt einer Einstellung pro Benutzer aktivieren oder deaktivieren. | |
| [Erstellen eines Plans für den Zugriff durch Gastbenutzer](../external-identities/what-is-b2b.md) | Arbeiten Sie mit Gastbenutzern zusammen, indem Sie ihnen ermöglichen, sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei Ihren Anwendungen und Diensten anzumelden. | [Abrechnungsmodell für externe Identitäten in Azure AD](../external-identities/external-identities-pricing.md) |
| [Entscheidungen zur Geräteverwaltungsstrategie](../devices/overview.md) | Entscheiden Sie, was Ihre Organisation in Bezug auf Geräte zulässt, z.B. Registrierung oder Beitritt, Bring Your Own Device oder unternehmenseigene Geräte. | |
| [Bereitstellen von Windows Hello for Business in Ihrer Organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Bereiten Sie die kennwortlose Authentifizierung unter Verwendung von Windows Hello vor. | |
| [Bereitstellen von Methoden zur kennwortlosen Authentifizierung für Ihre Benutzer](../authentication/concept-authentication-passwordless.md) | Stellen Sie für Ihre Benutzer Methoden für die Authentifizierung ohne Kennwort bereit. | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Phase 3: Verwalten von Anwendungen

Während wir weiter auf den vorherigen Phasen aufbauen, identifizieren wir Kandidatenanwendungen für die Migration und Integration in Azure AD und schließen die Einrichtung dieser Anwendungen ab.

| Aufgabe | Detail | Erforderliche Lizenz |
| ---- | ------ | ---------------- |
| Identifizieren Ihrer Anwendungen | Identifizieren Sie in Ihrer Organisation verwendete Anwendungen: lokale Anwendungen, SaaS-Anwendungen in der Cloud und andere Branchenanwendungen. Legen Sie fest, ob diese Anwendungen mit Azure AD verwaltet werden können und sollten. | Keine Lizenz erforderlich |
| [Integrieren von unterstützten SaaS-Anwendungen in den Katalog](../manage-apps/add-application-portal.md) | Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen. Einige der von Ihrer Organisation verwendeten Anwendungen sind wahrscheinlich im Katalog enthalten, der direkt über das Azure-Portal zugänglich ist. | Azure AD Free |
| [Verwenden eines Anwendungsproxys für die Integration lokaler Anwendungen](../manage-apps/application-proxy-add-on-premises-application.md) | Mit einem Anwendungsproxy können Benutzer auf lokale Anwendungen zugreifen, indem sie sich mit ihrem Azure AD-Konto anmelden. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Phase 4: Überwachen berechtigter Identitäten, Durchführen einer Zugriffsüberprüfung und Verwalten des Benutzerlebenszyklus

In Phase 4 erzwingen Administratoren die Prinzipien der geringsten Rechte für die Verwaltung, führen ihre ersten Zugriffsüberprüfungen durch und aktivieren die Automatisierung von gängigen Benutzerlebenszyklus-Aufgaben.

| Aufgabe | Detail | Erforderliche Lizenz |
| ---- | ------ | ---------------- |
| [Erzwingen der Verwendung von Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Entfernen Sie Administratorrollen aus normalen, alltäglichen Benutzerkonten. Erteilen Sie Administratoren die Berechtigung zur Nutzung ihrer Rolle, nachdem folgende Voraussetzungen erfüllt sind: eine erfolgreiche Multi-Factor Authentication-Überprüfung, die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen. | Azure AD Premium P2 |
| [Abschließen einer Zugriffsüberprüfung für Azure AD-Verzeichnisrollen in PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Arbeiten Sie mit Ihren Sicherheits- und Führungsteams zusammen, um eine Richtlinie für die Zugriffsüberprüfung zu erstellen, anhand derer der Administratorzugriff basierend auf den Richtlinien Ihrer Organisation überprüft wird. | Azure AD Premium P2 |
| [Implementieren von Richtlinien für dynamische Gruppenmitgliedschaft](../enterprise-users/groups-dynamic-membership.md) | Weisen Sie mit dynamischen Gruppen automatisch Benutzer basierend auf deren Attributen von HR (oder Ihrer zuverlässigen Datenquelle) wie z.B. Abteilung, Titel, Region und anderen Attributen Gruppen zu. |  |
| [Implementieren von gruppenbasierter Anwendungsbereitstellung](../manage-apps/what-is-access-management.md) | Verwenden Sie die gruppenbasierte Bereitstellung der Zugriffsverwaltung zum automatischen Bereitstellen von Benutzern für SaaS-Anwendungen. |  |
| [Automatisieren der Bereitstellung und Bereitstellungsaufhebung](../app-provisioning/user-provisioning.md) | Entfernen Sie manuelle Schritte aus dem Kontolebenszyklus Ihrer Mitarbeiter, um nicht autorisierte Zugriffe zu verhindern. Synchronisieren Sie Identitäten über Ihre zuverlässige Datenquelle (HR-System) mit Azure AD. |  |

## <a name="next-steps"></a>Nächste Schritte

[Details zu Azure AD-Lizenzierung und Preisen](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Allgemeine empfohlene Identitäts- und Gerätezugriffsrichtlinien](/microsoft-365/enterprise/identity-access-policies)