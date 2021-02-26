---
title: Azure-Sicherheitsbaseline für Azure Managed Applications
description: Die Azure Managed Applications-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c1bcde3f2b1f61fd241143e308a508d5f19e7b0c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090569"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azure-Sicherheitsbaseline für Azure Managed Applications

Bei dieser Sicherheitsbaseline werden Empfehlungen der [Version 2.0 des Vergleichstests für die Azure-Sicherheit](../../security/benchmarks/overview.md) auf Azure Managed Applications angewendet. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und durch die für Azure Managed Applications geltenden Empfehlungen definiert werden. **Kontrollen**, die nicht für Azure Managed Applications gelten, wurden ausgeschlossen.

Sehen Sie sich die [vollständige Zuordnungsdatei der Azure Managed Applications-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an, um zu ermitteln, welche Gesamtzuordnung zwischen Azure Managed Applications und dem Vergleichstest für die Azure-Sicherheit besteht.

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Vereinfachen von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Azure Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewall zu definieren, die für Ihre Azure Managed Applications-Ressourcen konfiguriert sind. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Durch Angabe des Diensttagnamens (Beispiel: AzureResourceManager) im entsprechenden Quell- oder Zielfeld einer Regel können Sie den Datenverkehr für den jeweiligen Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Verwenden von Azure Resource Manager-Diensttags](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Für Azure Managed Applications wird Azure Active Directory (Azure AD) als Standarddienst für die Identitäts- und Zugriffsverwaltung verwendet. Konfigurieren Sie Azure AD als Standardmethode für die Identitäts- und Zugriffsverwaltung Ihrer Organisation.

Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf Managed Applications mit Azure AD und OAuth bereit:

- Rolle „Mitwirkender für verwaltete Anwendungen“: Ermöglicht das Erstellen von Ressourcen für verwaltete Anwendungen.

- Rolle „Bediener für verwaltete Anwendung“: Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung.

- Leser für verwaltete Anwendungen: Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern.

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Rolle „Mitwirkender für verwaltete Anwendungen“](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rolle „Bediener für verwaltete Anwendung“](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leser für verwaltete Anwendungen](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Mandanten in Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

**Leitfaden**: Verwenden Sie von Azure verwaltete Identitäten zum Gewähren von Berechtigungen für Ihre verwalteten Anwendungen (Managed Applications). Es wird empfohlen, das Feature für verwaltete Azure-Identitäten zu verwenden, anstatt ein leistungsfähigeres Benutzerkonto für den Zugriff auf Ihre Ressourcen oder deren Ausführung zu erstellen, um die Notwendigkeit der Verwaltung zusätzlicher Anmeldeinformationen einzuschränken. Dem Dienst „Azure Managed Applications“ kann auch selbst eine verwaltete Identität zugewiesen werden, um sich bei anderen Azure-Diensten/-Ressourcen, die Azure AD-Authentifizierung unterstützen, nativ zu authentifizieren. Dies kann hilfreich sein, um beim Abrufen von Geheimnissen den einfachen Zugriff von Azure Managed Applications auf Azure Key Vault zu ermöglichen. Wenn Sie verwaltete Identitäten verwenden, wird die Identität von der Azure-Plattform verwaltet, und Sie müssen keine Geheimnisse bereitstellen oder rotieren.

Azure Managed Applications unterstützt die Zuweisung von zwei Identitätsarten für Ihre Anwendung:

- Eine systemseitig zugewiesene Identität ist an Ihre Konfigurationsressource gebunden. Sie wird gelöscht, wenn Ihre Konfigurationsressource gelöscht wird. Eine Konfigurationsressource kann nur eine systemseitig zugewiesene Identität aufweisen.

- Eine benutzerseitig zugewiesene Identität ist eine eigenständige Azure-Ressource, die Ihrer Konfigurationsressource zugewiesen werden kann. Eine Konfigurationsressource kann mehrere benutzerseitig zugewiesene Identitäten aufweisen.

Wenn verwaltete Identitäten nicht genutzt werden können, erstellen Sie einen Dienstprinzipal mit eingeschränkten Berechtigungen auf der Azure Managed Applications-Ressourcenebene. Konfigurieren Sie diese Dienstprinzipale mit Zertifikatanmeldeinformationen und Fallback nur auf geheime Clientschlüssel. In beiden Fällen kann Azure Key Vault in Verbindung mit verwalteten Azure-Identitäten verwendet werden, damit die Laufzeitumgebung (z. B. eine Azure-Funktion) die Anmeldeinformationen aus dem Schlüsseltresor abrufen kann.

- [Verwaltete Azure-Anwendung mit verwalteter Identität](publish-managed-identity.md)

- [Von Azure verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure-Dienstprinzipal](/powershell/azure/create-azure-service-principal-azureps) 

- [Verwenden von Azure Key Vault für die Registrierung von Sicherheitsprinzipalen](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Verwenden von Azure AD Single Sign-on (SSO) für den Anwendungszugriff

**Leitfaden**: Für Azure Managed Applications wird Azure Active Directory verwendet, um die Identitäts- und Zugriffsverwaltung für Azure-Ressourcen, Cloudanwendungen und lokale Anwendungen bereitzustellen. Dies umfasst sowohl Unternehmensidentitäten wie Mitarbeiter als auch externe Identitäten wie Partner, Anbieter und Zulieferer. Dies ermöglicht SSO (Single Sign-On, einmaliges Anmelden) die Verwaltung und den Schutz des Zugriffs auf die Daten und Ressourcen Ihrer Organisation, die lokal und in der Cloud gespeichert sind. Verbinden Sie all Ihre Benutzer, Anwendungen und Geräte mit Azure AD, um einen nahtlosen, sicheren Zugriff sowie mehr Transparenz und Kontrolle zu ermöglichen.

- [Grundlegendes zu Anwendungs-SSO mit Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Azure Managed Applications verwendet den Azure Active Directory-Dienst, der starke Authentifizierungssteuerungen über mehrstufige Authentifizierung und starke kennwortlose Methoden unterstützt.
- Mehrstufige Authentifizierung: Aktivieren Sie die mehrstufige Azure AD-Authentifizierung, und befolgen Sie die Empfehlungen der Identitäts- und Zugriffsverwaltung von Azure Security Center in Bezug auf einige bewährte Methoden für Ihr Setup der mehrstufigen Authentifizierung. Die mehrstufige Authentifizierung kann für alle Benutzer, für ausgewählte Benutzer oder auf Benutzerebene auf der Grundlage von Anmeldebedingungen und Risikofaktoren durchgesetzt werden.
- Kennwortlose Authentifizierung: Es stehen drei Optionen für die kennwortlose Authentifizierung zur Verfügung: Windows Hello for Business, die Microsoft Authenticator-App und lokale Authentifizierungsmethoden wie Smartcards.

Stellen Sie für Administratoren und privilegierte Benutzer sicher, dass die höchste Ebene für die sichere Authentifizierung verwendet wird.

- [Planen einer Bereitstellung von Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Einführung in Optionen für die kennwortlose Authentifizierung für Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

**Leitfaden**: Azure Managed Applications ist in den Azure Active Directory-Dienst integriert, der folgende Datenquellen bereitstellt:
- Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Benutzeranmeldeaktivitäten.
- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können mit Azure Monitor, Azure Sentinel oder SIEM-Systemen von Drittanbietern integriert werden.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement.

Azure Advanced Threat Protection (ATP) ist eine Sicherheitslösung, die Active Directory-Signale nutzen kann, um komplexe Bedrohungen, potenziell gefährdete Identitäten und schädliche Aktionen von Insidern zu identifizieren, zu erkennen und zu untersuchen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../../security-center/security-center-identity-access.md)

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

**Leitfaden**: Azure Managed Applications unterstützt den bedingten Zugriff von Azure AD für eine präzisere Zugriffssteuerung basierend auf benutzerdefinierten Bedingungen, z. B. dass bei Benutzeranmeldungen aus bestimmten IP-Bereichen die mehrstufige Authentifizierung für die Anmeldung verwendet werden muss. Die Sitzungsverwaltungsrichtlinie für die differenzierte Authentifizierung kann ebenfalls für verschiedene Anwendungsfälle verwendet werden.

- [Übersicht über den bedingten Zugriff in Azure](../../active-directory/conditional-access/overview.md) 

- [Allgemeine Richtlinien für bedingten Zugriff](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Schützen und Einschränken stark privilegierter Benutzer

**Leitfaden**: Für Azure Managed Applications wird Azure Active Directory (Azure AD) für Identitäts- und Zugriffszwecke verwendet. Die wichtigsten integrierten Rollen in Azure AD sind „Globaler Administrator“ und „Administrator für privilegierte Rollen“, da Benutzer, die diesen beiden Rollen zugeordnet sind, Administratorrollen delegieren können:
- Globaler Administrator: Benutzer mit dieser Rolle haben Zugriff auf alle administrativen Funktionen in Azure AD sowie auf Dienste, die Azure AD-Identitäten verwenden.
- Administrator für privilegierte Rollen: Benutzer mit dieser Rolle können Rollenzuweisungen in Azure AD und Azure AD Privileged Identity Management (PIM) verwalten. Überdies ermöglicht diese Rolle Verwaltung aller Aspekte von PIM und administrativer Einheiten.

Hinweis: Möglicherweise verfügen Sie über weitere kritische Rollen, die geregelt werden müssen, wenn Sie benutzerdefinierte Rollen mit bestimmten zugewiesenen privilegierten Berechtigungen verwenden. Es empfiehlt sich, ähnliche Kontrollelemente auch auf das Administratorkonto für kritische Unternehmensressourcen anzuwenden.

Mit Azure AD Privileged Identity Management (PIM) können Sie privilegierten Just-in-Time-Zugriff (JIT) auf Azure-Ressourcen und Azure AD ermöglichen. JIT erteilt nur dann temporäre Berechtigungen zur Ausführung privilegierter Aufgaben, wenn die Benutzer sie benötigen. PIM kann auch Sicherheitswarnungen erzeugen, wenn es in Ihrer Azure AD-Organisation verdächtige oder unsichere Aktivitäten gibt.

- [Administratorrollenberechtigungen in Azure AD](../../active-directory/roles/permissions-reference.md)

- [Verwenden von Sicherheitswarnungen von Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../../active-directory/roles/security-planning.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: Azure Managed Applications verwendet die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf unternehmenskritische Systeme zu isolieren. Zu diesem Zweck werden die Konten begrenzt, denen privilegierter Zugriff auf die Abonnements und Verwaltungsgruppen gewährt wird, denen diese angehören.

Stellen Sie sicher, dass Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme einschränken, die über Administratorzugriff auf Ihre unternehmenskritischen Ressourcen verfügen, z. B. Active Directory-Domänencontroller (DCs), Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Zugriff auf die Verwaltungsgruppe](../../governance/management-groups/overview.md#management-group-access)

- [Azure-Abonnementadministratoren](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Überprüfen Sie Benutzerkonten und die Zugriffszuweisung regelmäßig, um sicherzustellen, dass die Benutzerkonten und ihre Zugriffsebene geeignet sind.

Azure Managed Applications verwendet Azure Active Directory-Konten (AAD) zum Verwalten von Ressourcen sowie zum regelmäßigen Überprüfen von Benutzerkonten und Zugriffszuweisungen, um die Gültigkeit der Konten und zugehörigen Zugriffsberechtigungen sicherzustellen. Sie können Azure AD-Zugriffsüberprüfungen verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu überprüfen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen. Sie können Azure AD Privileged Identity Management auch verwenden, um einen Workflow für den Zugriffsüberprüfungsbericht zu erstellen und so den Überprüfungsprozess zu vereinfachen.

Darüber hinaus können Sie Azure Privileged Identity Management auch so konfigurieren, dass Sie eine Warnung erhalten, wenn übermäßig viele Administratorkonten erstellt werden, und um veraltete oder falsch konfigurierte Administratorkonten zu ermitteln.

Hinweis: Einige Azure-Dienste unterstützen lokale Benutzer und Rollen, die nicht über Azure AD verwaltet werden. Sie müssen diese Benutzer separat verwalten.

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

**Leitfaden**: Für Azure Managed Applications wird Azure Active Directory verwendet, um die Ressourcen zu verwalten. Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.

Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung 

**Leitfaden**: Azure Managed Applications ist zur Ressourcenverwaltung mit Azure Active Directory integriert. Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Die duale oder die mehrstufige Genehmigung wird ebenfalls unterstützt.

- [Was sind Azure AD-Zugriffsüberprüfungen?](../../active-directory/governance/access-reviews-overview.md)

- [Was ist die Azure AD-Berechtigungsverwaltung?](../../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Verwenden von Privileged Access Workstations

**Leitfaden**: Gesicherte, isolierte Arbeitsstationen sind von entscheidender Bedeutung für die Sicherheit sensibler Rollen wie Administratoren, Entwickler und Operatoren kritischer Dienste. Verwenden Sie stark gesicherte Benutzerworkstations und/oder Azure Bastion für administrative Aufgaben im Zusammenhang mit Ihren verwalteten Anwendungen (Managed Applications). Verwenden Sie Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) und/oder Microsoft Intune, um eine sichere und verwaltete Benutzerworkstation für administrative Aufgaben einzurichten. Die gesicherten Workstations können zentral verwaltet werden, um eine gesicherte Konfiguration einschließlich starker Authentifizierung, Software- und Hardwarebaselines, eingeschränktem logischen und Netzwerkzugang durchzusetzen.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Bereitstellen einer sicheren, von Azure verwalteten Arbeitsstation](/security/compass/privileged-access-deployment)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte) 

**Leitfaden**: Azure Managed Applications ist zur Ressourcenverwaltung mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) integriert. Mit Azure RBAC kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Dies ergänzt den JIT-Ansatz (Just-in-Time) von Azure AD Privileged Identity Management (PIM), und diese Berechtigungen sollten regelmäßig überprüft werden.

Verwenden Sie nach Möglichkeit integrierte Rollen, um Berechtigungen zuzuweisen, und erstellen Sie benutzerdefinierte Rollen nur bei Bedarf.

Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf Managed Applications mit Azure AD und OAuth bereit:

- Rolle „Mitwirkender für verwaltete Anwendungen“: Ermöglicht das Erstellen von Ressourcen für verwaltete Anwendungen.

- Rolle „Bediener für verwaltete Anwendung“: Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung.

- Leser für verwaltete Anwendungen: Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern.

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Rolle „Mitwirkender für verwaltete Anwendungen“](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rolle „Bediener für verwaltete Anwendung“](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leser für verwaltete Anwendungen](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Auswählen des Genehmigungsprozesses für Microsoft-Support  

**Leitfaden**: Implementieren Sie einen organisationsbezogenen Genehmigungsprozess für Supportszenarien, in denen Microsoft unter Umständen Zugriff auf Ihre Azure Managed Applications-Daten benötigt. Die Kunden-Lockbox ist für Managed Applications-Szenarien derzeit nicht verfügbar.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Schützen von vertraulichen Daten

**Leitfaden**: Für die Verschlüsselung mit Ihren eigenen Schlüsseln können Sie Ihr eigenes Speicherkonto zum Speichern der Konfigurationsdateien für Managed Applications verwenden.

- [Bereitstellen eines eigenen Speichers (Bring Your Own Storage) für die Definition der verwalteten Anwendung](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Verschlüsseln vertraulicher ruhender Daten

**Leitfaden**: Definitionen für verwaltete Anwendungen, mit denen Ihre Anwendung und die zugehörigen Ressourcen definiert werden, können in Ihren eigenen Speicherkonten gespeichert werden. Für diese Speicherkonten können kundenseitig verwaltete Verschlüsselungsschlüssel konfiguriert werden.

Falls Sie in bestimmten Fällen nicht Ihren eigenen Speicher für Managed Applications-Definitionen nutzen möchten, wird von Azure standardmäßig die Verschlüsselung von ruhenden Daten bereitgestellt.

- [Bereitstellen eines eigenen Speichers (Bring Your Own Storage) für die Definition der verwalteten Anwendung](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault über das Azure-Portal](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Aus diesem Grund müssen Sicherheitserkenntnisse und -risiken immer innerhalb einer Organisation zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können weit gefasst auf einen gesamten Mandanten (Stammverwaltungsgruppe) angewandt oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Hinweis: Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../../governance/management-groups/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

**Leitfaden**: Wenden Sie Tags auf Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure an, um sie logisch in einer Taxonomie zu strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

Tags, die bei der Erstellung der verwalteten App angegeben werden, werden auch auf die verwaltete Ressourcengruppe angewendet. Der Herausgeber Ihrer Anwendung kann nach der Bereitstellung sein eigenes zusätzliches Tagging für die verwalteten Ressourcen angeben.

- [Benutzeroberflächenelement „Microsoft.Common.TagsByResource“](microsoft-common-tagsbyresource.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md) 

- [Weitere Informationen zum Taggen von Ressourcen finden Sie im „Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung“.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Azure Managed Applications unterstützt auf Azure Resource Manager basierende Bereitstellungen und die Erzwingung von Konfigurationen mithilfe von Azure Policy. Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

**Leitfaden**: Managed Applications-Ressourcen und die zugehörige verbundene verwaltete Ressourcengruppe kann gelöscht werden, indem die Managed Applications-Ressource gelöscht wird. Wenn eine Managed Applications-Ressource gelöscht wird, werden auch die verwaltete Ressourcengruppe und ihr Inhalt gelöscht. Weitere Lebenszyklusfunktionen werden vom Herausgeber der Anwendung angegeben. Hierdurch kann der Consumer über zulässige Aktionen zusätzliche Rechte für den Lebenszyklus der zugrunde liegenden verwalteten Ressourcen erhalten. Wenden Sie sich an den Herausgeber Ihrer verwalteten Anwendung, um zu ermitteln, welche Ressourcen vom Consumer verwaltet werden.

- [Bereinigen von Ressourcen](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: Azure AD stellt die folgenden Benutzerprotokolle bereit, die Sie in der Azure AD-Berichterstellung anzeigen oder mit Azure Monitor, Azure Sentinel oder anderen SIEM- und Überwachungstools integrieren können, um komplexere Anwendungsfälle für Überwachung und Analyse zu erhalten. Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Benutzeranmeldeaktivitäten.
Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul von Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (VMs, Container, App Service), Datenressourcen (SQL-Datenbanken und -Speicher) und Azure-Dienstebenen sammeln. Diese Funktion bietet Ihnen Einblick in Kontoanomalien innerhalb der einzelnen Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivieren von Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Bedrohungsschutz in Azure Security Center](../../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Managed Applications-Ressourcen (mit Ausnahme von Lesevorgängen (GET)). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Zentralisieren Sie die Protokollierung, Speicherung und Analyse, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.
Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Zusätzlich können Sie auch Daten in Azure Sentinel oder der SIEM-Lösung eines Drittanbieters aktivieren und integrieren.

Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Durchführen des Onboardings für Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurieren der Aufbewahrungsdauer von Protokollen im Speicher

**Leitfaden**: Stellen Sie sicher, dass für die Speicherkonten oder Log Analytics-Arbeitsbereiche zum Speichern der Protokolle, die von Ihren Managed Applications-Ressourcen erstellt wurden, der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.
In Azure Monitor können Sie den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihres Unternehmens festlegen. Verwenden Sie für langfristige Speicherungen und Archivierungen Konten von Azure Storage, Data Lake oder des Log Analytics-Arbeitsbereichs.

- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../../azure-monitor/platform/manage-cost-storage.md)

- [Azure-Ressourcenprotokolle](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Vorbereitung – Aktualisieren des Prozesses zur Reaktion auf Vorfälle für Azure

**Leitfaden**: Stellen Sie sicher, dass Ihre Organisation über Prozesse verfügt, um auf Sicherheitsvorfälle zu reagieren, dass diese Prozesse für Azure aktualisiert wurden und dass sie regelmäßig trainiert werden, um die Bereitschaft zu gewährleisten.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Implementieren von Sicherheit in der gesamten Unternehmensumgebung)

- [Referenzleitfaden für die Reaktion auf Vorfälle](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Vorbereitung – Einrichten von Ereignisbenachrichtigungen

**Leitfaden**: Richten Sie Kontaktinformationen für Sicherheitsvorfälle im Azure Security Center ein. Microsoft wendet sich unter den angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Sie haben auch die Möglichkeit, die Warnungen und Benachrichtigungen bei Vorfällen in verschiedenen Azure-Diensten entsprechend Ihrer Anforderungen bezüglich der Reaktion auf Vorfälle anzupassen. 

- [Festlegen der Kontaktinformationen in Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Erkennung und Analyse – Erstellen von Vorfällen basierend auf Warnungen mit hoher Qualität

**Leitfaden**: Stellen Sie sicher, dass Sie über einen Prozess zum Erstellen hochwertiger Warnungen und zum Messen der Qualität von Warnungen verfügen. Auf diese Weise können Sie Lehren aus vergangenen Vorfällen ziehen und Warnungen für Analysten priorisieren, damit diese keine Zeit mit falsch-positiven Ergebnissen verschwenden. 

Qualitativ hochwertige Warnungen können auf der Grundlage von Erfahrungen aus früheren Vorfällen, validierten Communityquellen und Tools zur Generierung und Bereinigung von Warnmeldungen durch Verschmelzung und Korrelation verschiedener Signalquellen erstellt werden. 

Azure Security Center bietet qualitativ hochwertige Warnungen für viele Azure-Ressourcen. Sie können den ASC-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. Mit Azure Sentinel können Sie erweiterte Warnregeln erstellen, um Vorfälle automatisch für eine Untersuchung zu generieren. 

Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über das Exportfeature, um Risiken für Azure-Ressourcen zu ermitteln. Exportieren Sie Warnungen und Empfehlungen entweder manuell oder fortlaufend, kontinuierlich.

- [Konfigurieren des Exports](../../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Erkennung und Analyse – Untersuchen eines Vorfalls

**Leitfaden**: Stellen Sie sicher, dass Analysten bei der Untersuchung potenzieller Vorfälle verschiedene Datenquellen abfragen und verwenden können, um eine vollständige Übersicht über das Geschehnis zu erhalten. Es empfiehlt sich, verschiedene Protokolle zu sammeln, um die Aktivitäten eines potenziellen Angreifers über das gesamte Kill Chain-Spektrum hinweg nachzuverfolgen und Schwachpunkte zu vermeiden.  Stellen Sie außerdem sicher, dass Erkenntnisse und Erfahrungen für andere Analysten und als zukünftige Referenzen erfasst werden.  

Zu den zu untersuchenden Datenquellen gehören die zentralisierten Protokollierungsquellen, die bereits von den zum Umfang gehörige Diensten und ausgeführten Systemen gesammelt werden, aber auch andere:

- Netzwerkdaten: Verwenden Sie die Datenflussprotokolle von Netzwerksicherheitsgruppen sowie Azure Network Watcher und Azure Monitor, um Netzwerkflussprotokolle und andere Analyseinformationen zu erfassen. 

- Momentaufnahmen von laufenden Systemen: 

    - Verwenden Sie die Momentaufnahmenfunktion des virtuellen Azure-Computers, um eine Momentaufnahme der Festplatte des laufenden Systems zu erstellen. 

    - Verwenden Sie die native Speicherabbildfunktion des Betriebssystems, um eine Momentaufnahme des Arbeitsspeichers des laufenden Systems zu erstellen.

    - Verwenden Sie das Momentaufnahmenfeature der Azure-Dienste oder die Funktion Ihrer Software, um Momentaufnahmen der laufenden Systeme zu erstellen.

Azure Sentinel bietet umfangreiche Datenanalysen über praktisch jede Protokollquelle sowie ein Fallverwaltungsportal zum Verwalten des gesamten Lebenszyklus von Vorfällen. Intelligenceinformationen während einer Untersuchung können zu Verfolgungs- und Berichtszwecken mit einem Vorfall verknüpft werden. 

- [Momentaufnahme des Datenträgers eines Windows-Computers](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentaufnahme des Datenträgers eines Linux-Computers](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-Supportdiagnoseinformationen und Speicherabbilderfassung](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- Lesen Sie [Untersuchen von Incidents mit Azure Sentinel](../../sentinel/tutorial-investigate-cases.md).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Erkennung und Analyse – Priorisieren von Vorfällen

**Leitfaden**: Stellen Sie den Analysten Kontext bereit, auf welche Vorfälle sie sich zuerst konzentrieren sollten, je nach Schweregrad und Ressourcensensitivität. 

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, der oder die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Eindämmung, Ausmerzung und Wiederherstellung – Automatisieren der Behandlung von Vorfällen

**Leitfaden**: Automatisieren Sie manuelle, sich wiederholende Aufgaben, um die Antwortzeit zu beschleunigen und die Belastung der Analysten zu verringern. Manuelle Aufgaben dauern länger, verlangsamen jeden Vorfall und reduzieren die Anzahl der Vorfälle, die ein Analyst bewältigen kann. Manuelle Aufgaben erhöhen auch die Ermüdung der Analytiker. Dadurch erhöht sich das Risiko menschlicher Fehler, die zu Verzögerungen führen, und es verschlechtert sich die Fähigkeit der Analytiker, sich effektiv auf komplexe Aufgaben zu konzentrieren. 

Verwenden Sie die Workflowautomatisierungsfeatures in Azure Security Center und Azure Sentinel, um automatisch Aktionen auszulösen oder ein Playbook auszuführen, um auf eingehende Sicherheitswarnungen zu reagieren. Das Playbook führt Aktionen aus, wie das Versenden von Benachrichtigungen, das Deaktivieren von Konten und das Isolieren problematischer Netzwerke. 

- [Konfigurieren der Workflowautomatisierung in Security Center](../../security-center/workflow-automation.md)

- [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md) vertraut.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="posture-and-vulnerability-management"></a>Status- und Sicherheitsrisikoverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Status- und Sicherheitsrisikoverwaltung](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Einrichten sicherer Konfigurationen für Azure-Dienste 

**Leitfaden**: Definieren Sie Sicherheitsleitlinien für Infrastruktur- und DevOps-Teams, indem Sie die sichere Konfiguration der verwendeten Azure-Dienste erleichtern.

Konfigurieren Sie Azure Policy so, dass die Konfigurationen Ihrer Ressourcen für Ihre Managed Applications-Bereitstellungen überwacht und erzwungen werden.

Sie können Azure Blueprints verwenden, um die Bereitstellung und Konfiguration von Diensten und Anwendungsumgebungen, z. B. Azure Resource Manager-Vorlagen, Azure RBAC-Zuweisungen und Azure Policy-Zuweisungen, in nur einer Blaupausendefinition zu automatisieren.

- [CAF-Zielzone (Cloud Adoption Framework) auf Unternehmensebene](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Aufrechterhalten sicherer Konfigurationen für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Security Center, um Ihre Ressourcen für Azure Managed Applications zu überwachen, und verwenden Sie die Azure Policy-Effekte [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Konfigurationen zu erhalten.
- [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmäßiges Durchführen einer Angriffssimulation

**Leitfaden**: Führen Sie nach Bedarf Penetrationstests oder Red Team-Aktivitäten für Ihre Azure-Ressourcen durch, und stellen Sie sicher, dass alle kritischen Sicherheitsergebnisse behandelt werden.
Befolgen Sie die Einsatzregeln für Penetrationstests für die Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests in Azure](../../security/fundamentals/pen-testing.md)

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sicherung und Wiederherstellung](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Stellen Sie beim Speichern Ihrer Managed Applications-Definitionen in Ihrem eigenen Speicherkonto sicher, dass Sie alle zugeordneten kundenseitig verwalteten Schlüssel wiederherstellen können, die zur Verschlüsselung des Kontos genutzt werden und in Azure Key Vault gespeichert sind.

- [Bereitstellen eines eigenen Speichers (Bring Your Own Storage) für die Definition der verwalteten Anwendung](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Wiederherstellen von Key Vault-Schlüsseln in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Minimieren der Auswirkungen verlorener Schlüssel

**Leitfaden**: Wenn Sie Ihren eigenen Speicher für Ihre Managed Applications-Definitionen nutzen möchten, sollten Sie dafür sorgen, dass der Verlust von Schlüsseln für die Verschlüsselung Ihrer Definitionen verhindert wird und deren Wiederherstellung möglich ist. Aktivieren Sie das vorläufige Löschen und den Bereinigungsschutz für die Azure Key Vault-Instanz, unter der Ihre kundenseitig verwalteten Schlüssel gespeichert werden, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.  

- [Bereitstellen eines eigenen Speichers (Bring Your Own Storage) für die Definition der verwalteten Anwendung](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="governance-and-strategy"></a>Governance und Strategie

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Governance und Strategie](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definieren der Strategie für Ressourcenverwaltung und Datenschutz 

**Leitfaden**: Stellen Sie sicher, dass Sie eine klare Strategie für die kontinuierliche Überwachung und den Schutz von Systemen und Daten dokumentieren und kommunizieren. Priorisieren Sie die Ermittlung, die Bewertung, den Schutz und die Überwachung unternehmenskritischer Daten und Systeme. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Datenklassifizierungsstandard in Übereinstimmung mit den Geschäftsrisiken

-   Sicherheitsorganisationseinblick in Risiken und Ressourcenbestand 

-   Genehmigung durch die Sicherheitsorganisation für die Nutzung der Azure-Dienste 

-   Sicherheit von Ressourcen durch ihren gesamten Lebenszyklus

-   Erforderliche Zugriffssteuerungsstrategie in Übereinstimmung mit der Organisationsdatenklassifizierung

-   Verwendung von nativen Azure- und Drittanbieterfunktionen für den Datenschutz

-   Datenverschlüsselungsanforderungen für Anwendungsfälle während der Übertragung und für ruhende Anwendungsfälle

-   Geeignete Verschlüsselungsstandards

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Empfehlung für eine Azure-Sicherheitsarchitektur: Speicher, Daten, Verschlüsselung](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundlegende Azure-Sicherheitsinformationen: Sicherheit, Verschlüsselung und Speicherung von Azure-Daten](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure-Sicherheitsvergleichstest: Ressorcenverwaltung](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure-Sicherheitsvergleichstest: Datenschutz](../../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definieren der Strategie für Unternehmenssegmentierung 

**Leitfaden**: Erstellen Sie eine unternehmensweite Strategie zum Segmentieren des Zugriffs auf Ressourcen durch eine Kombination aus Identität, Netzwerk, Anwendung, Abonnement, Verwaltungsgruppe und anderen Steuerelementen.

Wägen Sie die Notwendigkeit einer Sicherheitstrennung sorgfältig gegen die Notwendigkeit ab, den täglichen Betrieb der Systeme zu ermöglichen, die miteinander kommunizieren und auf Daten zugreifen müssen.

Stellen Sie sicher, dass die Segmentierungsstrategie einheitlich für alle Steuerelementtypen implementiert wird, einschließlich Netzwerksicherheit, Identitäts- und Zugriffsmodelle, Berechtigungs-/Zugriffsmodelle für Anwendungen sowie Steuerelemente für Benutzerprozesse.

- [Leitfaden für die Segmentierungsstrategie in Azure (Video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Leitfaden für die Segmentierungsstrategie in Azure (Dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ausrichten der Netzwerksegmentierung an der Segmentierungsstrategie des Unternehmens](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definieren der Strategie für die Sicherheitsstatusverwaltung

**Leitfaden**: Führen Sie kontinuierliche Messungen durch, und Mindern Sie die Risiken für Ihre individuellen Ressourcen und die Umgebung, in der diese gehostet werden. Priorisieren Sie Ressourcen mit hohem Wert und hoch exponierte Angriffsflächen, wie z. B. veröffentlichte Anwendungen, Eingangs- und Ausgangspunkte für Netzwerke, Benutzer- und Administratorendpunkte usw.

- [Azure-Sicherheitsvergleichstest: Verwaltung von Status und Sicherheitsrisiken](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Ausrichten von Organisationsrollen, Zuständigkeiten und Verantwortlichkeiten

**Leitfaden**: Stellen Sie sicher, dass Sie eine klare Strategie für Rollen und Zuständigkeiten in ihrer Sicherheitsorganisation dokumentieren und kommunizieren. Priorisieren Sie durch Zuweisung klarer Verantwortlichkeiten für Sicherheitsentscheidungen, Schulung aller Beteiligten für das Modell der gemeinsamen Verantwortung und Schulung von technischen Teams hinsichtlich der Technologie zum Sichern der Cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen von Teams auf dem Weg zur Cloudsicherheit)

- [Azure Security Best Practice 2 – People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen der Teams in Cloudsicherheitstechnologie)

- [Azure Security Best Practice 3 – Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 1 – Prozess: Zuweisen von Verantwortlichkeit für Cloudsicherheitsentscheidungen)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definieren einer Netzwerksicherheitsstrategie

**Leitfaden**: Richten Sie einen Ansatz für die Azure-Netzwerksicherheit im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Zentralisieren der Verantwortlichkeit für Netzwerkverwaltung und -sicherheit

-   Segmentierungsmodell für virtuelle Netzwerke, das auf die Segmentierungsstrategie des Unternehmens abgestimmt ist

-   Wiederherstellungsstrategie in verschiedenen Bedrohungs- und Angriffsszenarien

-   Strategie für Internet-Randpunkte, -Eingangspunkte und -Ausgangspunkte

-   Strategie für Konnektivität zwischen Hybrid Cloud und lokalen Systemen

-   Aktuelle Netzwerksicherheitsartefakte (z. B. Netzwerkdiagramme, Referenznetzwerkarchitektur)

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../../security/benchmarks/security-controls-v2-network-security.md)

- [Die Netzwerksicherheit in Azure in der Übersicht](../../security/fundamentals/network-overview.md)

- [Strategie für die Unternehmensnetzwerkarchitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definieren der Strategie für Identität und privilegierten Zugriff

**Leitfaden**: Richten Sie einen Ansatz für Azure-Identität und privilegierten Zugriff im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Zentralisiertes Identitäts- und Authentifizierungssystem mit Interkonnektivität zu anderen internen und externen Identitätssystemen

-   Starke Authentifizierungsmethoden in verschiedenen Anwendungsfällen und Bedingungen

-   Schutz stark privilegierter Benutzer

-   Überwachung und Behandlung von Anomalien bei Benutzeraktivitäten  

-   Benutzeridentität und Zugriffsüberprüfung und Abstimmungsprozess

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Übersicht über die Sicherheit der Azure-Identitätsverwaltung](../../security/fundamentals/identity-management-overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definieren einer Strategie für Protokollierung und Reaktion auf Bedrohungen

**Leitfaden**: Richten Sie eine Strategie für Protokollierung und Reaktion auf Bedrohungen zur schnellen Erkennung und Behebung von Bedrohungen bei gleichzeitiger Erfüllung von Complianceanforderungen ein. Priorisieren Sie die Bereitstellung wertvoller Warnungen und nahtloser Funktionen für Analysten, damit sie sich auf Bedrohungen konzentrieren können, anstatt mit der Integration und der Ausführung manueller Schritte. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

-   Rolle und Verantwortlichkeiten der Organisation für Sicherheitsvorgänge (SecOps) 

-   Ein klar definierter Prozess zur Reaktion auf Vorfälle gemäß NIST oder einem anderen Branchenframework 

-   Erfassung und Aufbewahrung von Protokollen zur Unterstützung von Bedrohungserkennung, Reaktion auf Vorfälle und Complianceanforderungen

-   Zentralisierte Sichtbarkeit von und Korrelationsinformationen zu Bedrohungen unter Verwendung von SIEM, nativen Azure-Funktionen und anderen Quellen 

-   Kommunikations- und Benachrichtigungsplan mit Ihren Kunden, Lieferanten und öffentlichen Interessengruppen

-   Verwendung von nativen Azure-Plattformen und Plattformen von Drittanbietern für die Behandlung von Vorfällen, wie z. B. Protokollierung und Bedrohungserkennung, Forensik sowie Reduzierung und Bekämpfung von Angriffen

-   Prozesse für den Umgang mit Vorfällen und Aktivitäten nach Vorfällen, wie z. B. Erkenntnisse und Beweissicherung

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4 – Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 4 – Prozess: Aktualisieren der Prozesse zur Reaktion auf Vorfälle für die Cloud)

- [Azure Adoption Framework, Entscheidungshilfe für Protokollierung und Berichterstattung](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure auf Unternehmensebene, Verwaltung und Überwachung](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](../../security/benchmarks/overview.md) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../../security/benchmarks/security-baselines-overview.md).
