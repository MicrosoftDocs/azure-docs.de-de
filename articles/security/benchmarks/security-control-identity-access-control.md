---
title: 'Azure-Sicherheitskontrolle: Identität und Zugriffssteuerung'
description: Azure-Sicherheitskontrolle – Identität und Zugriffssteuerung
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8d2901e71a3c638e25899803ff9b24e20fdf1969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412662"
---
# <a name="security-control-identity-and-access-control"></a>Sicherheitskontrolle: Identität und Zugriffssteuerung

Die Empfehlungen für die Identitäts- und Zugriffsverwaltung konzentrieren sich auf die Behebung von Problemen, die bei der identitätsbasierten Zugriffssteuerung, beim Sperren des Administratorzugriffs, bei Warnungen zu identitätsbezogenen Ereignissen, bei anomalem Verhalten und bei der rollenbasierten Zugriffssteuerung auftreten.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.1 | 4,1 | Kunde |

Azure AD umfasst integrierte Rollen, die explizit zugewiesen werden müssen und abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.2 | 4,2 | Kunde |

Azure AD verfügt nicht über das Konzept von Standardkennwörtern. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.3 | 4.3 | Kunde |

Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Sie können außerdem einen Just-in-Time- oder Just-Enough-Zugriff aktivieren, indem Sie mit Azure AD Privileged Identity Management verwaltete privilegierte Rollen für Microsoft-Dienste und Azure Resource Manager verwenden. 

- [Weitere Informationen zu Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.4 | 4.4 | Kunde |

Verwenden Sie nach Möglichkeit Azure Active Directory SSO, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

- [Grundlegendes zu SSO mit Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Kunde |

Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Kunde |

Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) mit MFA, die für die Anmeldung bei und die Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivieren von MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3,7 | 4.8, 4.9 | Kunde |

Verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.8 | 11.7 | Kunde |

Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Kunde |

Verwenden Sie Azure Active Directory als zentrales Authentifizierungs- und Autorisierungssystem. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.10 | 16.9, 16.10 | Kunde |

Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff haben. 

- [Grundlegendes zur Azure AD-Berichterstellung](../../active-directory/reports-monitoring/index.yml)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3,11 | 16.12 | Kunde |

Sie haben Zugriff auf die Protokollquellen von Azure AD-Anmeldeaktivitäten und von Überwachungs- und Risikoereignissen, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3,12 | 16.13 | Kunde |

Mit Azure AD-Funktionen zum Risiko- und Identitätsschutz können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Anzeigen riskanter Azure AD-Anmeldungen](../../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

| Azure-ID | CIS-IDs | Verantwortlichkeit |
|--|--|--|
| 3.13 | 16 | Kunde |

In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, bietet Kunden-Lockbox eine Oberfläche, auf der Sie Anforderungen nach Zugriff auf Kundendaten prüfen und dann genehmigen oder ablehnen können.

- [Grundlegendes zu Kunden-Lockbox](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der nächsten Sicherheitskontrolle: [Datenschutz](security-control-data-protection.md)