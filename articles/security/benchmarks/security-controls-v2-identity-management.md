---
title: 'Azure-Sicherheitsvergleichstest V2: Identitätsverwaltung'
description: 'Azure-Sicherheitsvergleichstest V2: Identitätsverwaltung'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2b21bea924ee8eaed37246ce55feaf8243e3e7d4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408736"
---
# <a name="security-control-v2-identity-management"></a>Sicherheitskontrolle V2: Identitätsverwaltung

Identitätsverwaltung umfasst Kontrollelemente zur Einrichtung einer sicheren Identität sowie Zugriffssteuerungen mit Azure Active Directory. Dazu gehören die Verwendung von Single Sign-On, starken Authentifizierungen, verwalteten Identitäten (und Dienstprinzipalen) für Anwendungen, bedingter Zugriff sowie Überwachung von Kontoanomalien.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-1 | 16.1, 16.2, 16.4, 16.5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) ist der Standarddienst für Identitäts- und Zugriffsverwaltung in Azure. Sie sollten Azure AD als Standard für die Identitäts- und Zugriffsverwaltung Ihrer Organisation verwenden:
- Microsoft-Cloudressourcen, wie z. B. das Azure-Portal, Azure Storage, Azure Virtual Machines (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen.

- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen.

Die Sicherung von Azure AD sollte bei den Methoden Ihrer Organisation im Zusammenhang mit Cloudsicherheit eine hohe Priorität haben. Azure AD bietet eine Identitätssicherheitsbewertung, die Ihnen dabei hilft, Ihren Identitätssicherheitsstatus in Bezug auf die Empfehlungen zu bewährten Methoden von Microsoft zu bewerten. Verwenden Sie die Bewertung, um zu beurteilen, wie gut Ihre Konfiguration den Empfehlungen zu bewährten Methoden entspricht, und um Ihren Sicherheitsstatus zu verbessern.

Hinweis: Azure AD unterstützt externe Identitätsanbieter, die es Benutzern ohne Microsoft-Konto ermöglichen, sich mit ihrer externen Identität bei ihren Anwendungen und Ressourcen anzumelden.

- [Mandant in Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definieren von Azure AD-Mandanten](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Verwenden externer Identitätsanbieter für eine Anwendung](../../active-directory/external-identities/identity-providers.md)

- [Was ist die Identitätssicherheitsbewertung in Azure AD?](../../active-directory/fundamentals/identity-secure-score.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-2 | – | AC-2, AC-3, IA-2, IA-4, IA-9 |

Verwenden Sie für nicht zu einer Person gehörige Konten (z. B. Dienste oder Automatisierung) von Azure verwaltete Identitäten, anstatt ein leistungsfähigeres zu einer Person gehöriges Konto für den Zugriff auf Ressourcen oder die Ausführung von Code zu erstellen. Verwaltete Identitäten in Azure können sich bei Azure-Diensten und -Ressourcen authentifizieren, die die Azure AD-Authentifizierung unterstützen. Die Authentifizierung wird durch vordefinierte Zugriffszuweisungsregeln aktiviert, sodass hart codierte Anmeldeinformationen im Quellcode oder in Konfigurationsdateien vermieden werden. 

Für Dienste, die keine verwalteten Identitäten unterstützen, können Sie stattdessen mit Azure AD Dienstprinzipale mit eingeschränkten Berechtigungen auf Ressourcenebene erstellen.  Sie sollten Dienstprinzipale mit Zertifikatanmeldeinformationen und Fallback auf geheime Clientschlüssel konfigurieren. In beiden Fällen kann Azure Key Vault in Verbindung mit verwalteten Azure-Identitäten verwendet werden, damit die Laufzeitumgebung (z. B. eine Azure-Funktion) die Anmeldeinformationen aus dem Schlüsseltresor abrufen kann.

- [Von Azure verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md)

- [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-Dienstprinzipal](/powershell/azure/create-azure-service-principal-azureps)

- [Erstellen eines Dienstprinzipals mit Zertifikaten](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Verwenden Sie Azure Key Vault für die Registrierung von Sicherheitsprinzipalen: authentication#authorize-a-security-principal-to-access-key-vault

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Verwenden von Azure AD Single Sign-on (SSO) für den Anwendungszugriff

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-3 | 4.4 | IA-2, IA-4 |

Azure AD bietet Identitäts- und Zugriffsverwaltung für Azure-Ressourcen, Cloudanwendungen und lokale Anwendungen. Die Identitäts- und Zugriffsverwaltung gilt sowohl für Unternehmensidentitäten wie Mitarbeiter als auch für externe Identitäten wie Partner, Anbieter und Zulieferer.

Verwenden Sie Azure AD Single Sign-On (SSO), um den Zugriff auf die Daten und Ressourcen Ihres Unternehmens lokal und in der Cloud zu verwalten und zu sichern. Verbinden Sie alle Ihre Benutzer, Anwendungen und Geräte mit Azure AD für einen nahtlosen, sicheren Zugriff sowie mehr Transparenz und Kontrolle. 

- [Grundlegendes zu Anwendungs-SSO mit Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-4 | 4.2, 4.4 4.5, 11.5, 12.11, 16.3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD unterstützt starke Authentifizierungskontrollelemente durch Multi-Factor Authentication (MFA) und starke kennwortlose Methoden.  
- Multi-Factor Authentication: Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center für Ihr MFA-Setup. MFA kann für alle Benutzer, für ausgewählte Benutzer oder auf Benutzerebene auf der Grundlage von Anmeldebedingungen und Risikofaktoren durchgesetzt werden. 

- Kennwortlose Authentifizierung: Es sind drei kennwortlose Authentifizierungsoptionen verfügbar: Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie Smartcards. 

Stellen Sie für Administratoren und privilegierte Benutzer sicher, dass die höchste Stufe der starken Authentifizierungsmethode verwendet wird, und führen Sie für andere Benutzer eine geeignete starke Authentifizierungsrichtlinie ein.

Wenn für die Azure AD-Authentifizierung noch die veraltete kennwortbasierte Authentifizierung verwendet wird, beachten Sie, dass für reine Cloudkonten (Benutzerkonten, die direkt in Azure erstellt wurden) eine Standardbaseline-Kennwortrichtlinie gilt. Und Hybridkonten (Benutzerkonten, die aus dem lokalen Active Directory stammen) folgen den lokalen Kennwortrichtlinien. Bei der Verwendung der kennwortbasierten Authentifizierung bietet Azure AD eine Kennwortschutzfunktion, die Benutzer daran hindert, leicht zu erratende Kennwörter festzulegen. Microsoft stellt eine globale Liste verbotener Kennwörter zur Verfügung, die auf der Grundlage von Telemetrie aktualisiert wird, und Kunden können die Liste entsprechend ihren Bedürfnissen erweitern (z. B. um Markennamen, kulturelle Verweise usw.). Dieser Kennwortschutz kann für reine Cloud- und Hybridkonten verwendet werden. 

Hinweis: Die Authentifizierung allein auf der Grundlage von Kennwortanmeldeinformationen ist anfällig für beliebte Angriffsmethoden. Für höhere Sicherheit verwenden Sie starke Authentifizierungsmethoden wie MFA und eine starke Kennwortrichtlinie. Bei Anwendungen von Drittanbietern und Marketplace-Dienste, die möglicherweise über Standardkennwörter verfügen, sollten Sie diese während der ersten Einrichtung des Dienstes ändern. 

- [Aktivieren von MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Einführung in Optionen für die kennwortlose Authentifizierung für Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD-Standardkennwortrichtlinie](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ausschließen von ungeeigneten Kennwörtern mit dem Azure AD-Kennwortschutz](../../active-directory/authentication/concept-password-ban-bad.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-5 | 4.8, 4.9, 16.12, 16.13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD stellt die folgenden Datenquellen bereit: 
-   Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

-   Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Zu den in Überwachungsprotokollen protokollierten Änderungen zählen unter anderem das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

-   Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

-   Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können mit Azure Monitor, Azure Sentinel oder SIEM-Systemen von Drittanbietern integriert werden.

Azure Security Center kann auch bei bestimmten verdächtigen Aktivitäten warnen, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche und veraltete Konten im Abonnement. 

Azure Advanced Threat Protection (ATP) ist eine Sicherheitslösung, die lokale Active Directory-Signale nutzen kann, um erweiterte Bedrohungen, kompromittierte Identitäten und schädliche Aktionen von Insidern zu identifizieren, zu erkennen und zu untersuchen.

- [Überwachungsaktivitätsberichte in Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../../security-center/security-center-identity-access.md)

- [Warnungen im Threat Intelligence-Schutzmodul von Azure Security Center](../../security-center/alerts-reference.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Verknüpfen von Daten aus Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-6 | – | AC-2, AC-3 |

Verwenden Sie die Azure AD-Zugriffsberechtigung für eine präzisere Zugriffssteuerung auf der Grundlage benutzerdefinierter Bedingungen, wie z. B. dass Benutzer, die sich aus bestimmten IP-Bereichen anmelden, MFA verwenden müssen. Eine differenzierte Authentifizierungssitzungsverwaltung kann auch über bedingte Azure AD-Zugriffsrichtlinien für verschiedene Anwendungsfälle erreicht werden. 

- [Übersicht über den bedingten Zugriff in Azure](../../active-directory/conditional-access/overview.md)

- [Allgemeine Richtlinien für bedingten Zugriff](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-7 | 18.1, 18.7 | IA-5 |

Implementieren Sie Credential Scanner von Azure DevOps, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen.

Für GitHub können Sie das native Feature zur geheimen Überprüfung verwenden, um Anmeldeinformationen oder eine andere Form von Geheimnissen im Code zu identifizieren.

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Geheime Überprüfung in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: Sicheren Benutzerzugriff auf ältere Anwendungen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-8 | 14.6 | AC-2, AC-3, SC-11 |

Stellen Sie sicher, dass Sie über moderne Zugriffssteuerungen und Sitzungsüberwachung für Legacyanwendungen und die darin gespeicherten und verarbeiteten Daten verfügen. Zwar werden für den Zugriff auf Legacyanwendungen häufig VPNs verwendet, diese verfügen aber oft nur über eine grundlegende Zugriffssteuerung und eine begrenzte Sitzungsüberwachung.

Der Azure AD-Anwendungsproxy ermöglicht es Ihnen, lokale Legacyanwendungen für Remotebenutzer mit Single Sign-On (SSO) zu veröffentlichen und gleichzeitig die Vertrauenswürdigkeit der Remotebenutzer und der Geräte mit bedingtem Zugriff auf Azure AD explizit zu überprüfen. 

Alternativ dazu ist Microsoft Cloud App Security ein CASB-Service (Cloud Access Security Broker), der Kontrollelemente zur Überwachung der Anwendungssitzungen eines Benutzers und zum Blockieren von Aktionen (sowohl für lokale Legacyanwendungen als auch für Software as a Service (SaaS)-Cloudanwendungen) bereitstellen kann. 

- [Azure AD-Anwendungsproxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Bewährte Methoden für Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)