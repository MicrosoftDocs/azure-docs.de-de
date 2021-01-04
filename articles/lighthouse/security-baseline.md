---
title: Azure-Sicherheitsbaseline für Azure Lighthouse
description: Die Azure Lighthouse-Sicherheitsbaseline enthält Anleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen im Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a6a1b41dd4ede055ba81c069c2164bb36da99876
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602307"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azure-Sicherheitsbaseline für Azure Lighthouse

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 2.0](../security/benchmarks/overview.md) auf Azure Lighthouse an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure Lighthouse geltenden Empfehlungen definiert werden. Nicht auf Azure Lighthouse anwendbare **Kontrollen** wurden ausgeschlossen.

Um sich über die vollständige Zuordnung von Azure Lighthouse zum Azure-Sicherheitsvergleichstest zu informieren, sehen Sie sich die [vollständige Zuordnungsdatei der Azure Lighthouse-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

## <a name="identity-management"></a>Identitätsverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisieren von Azure Active Directory als zentrales Identitäts- und Authentifizierungssystem

**Leitfaden**: Azure Lighthouse verwendet Azure Active Directory (Azure AD) als Standarddienst für Identitäts- und Zugriffsverwaltung. Konfigurieren Sie Azure AD als Standardmethode für die Identitäts- und Zugriffsverwaltung Ihrer Organisation in folgenden Ressourcen:
- Microsoft-Cloudressourcen, wie z. B. Azure-Portal, Azure Storage, Azure-VMs (Linux und Windows), Azure Key Vault, PaaS- und SaaS-Anwendungen.
- Die Ressourcen Ihrer Organisation, z. B. Anwendungen in Azure oder Ihre Unternehmensnetzwerkressourcen.

Mit Azure Lighthouse erhalten designierte Benutzer in einem Verwaltungsmandanten eine integrierte Azure-Rolle, mit deren Hilfe sie auf delegierte Abonnements und/oder Ressourcengruppen im Mandanten eines Kunden zugreifen können. Alle integrierten Rollen werden derzeit unterstützt, ausgenommen „Besitzer“ sowie alle integrierten Rollen mit der Berechtigung „DataActions“. Die Rolle „Benutzerzugriffsadministrator“ wird nur für die eingeschränkte Verwendung beim Zuweisen von Rollen zu verwalteten Identitäten unterstützt. Benutzerdefinierte Rollen und klassische Abonnementadministratorrollen werden nicht unterstützt.

- [Mandanten in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Verwenden externer Identitätsanbieter für eine Anwendung](../active-directory/external-identities/identity-providers.md) 

- [Was ist der Identity Secure Score in Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Sicheres und automatisches Verwalten von Anwendungsidentitäten

**Leitfaden**: Verwaltete Identitäten in Azure können sich bei Azure-Diensten und -Ressourcen authentifizieren, die die Azure AD-Authentifizierung unterstützen. Die Authentifizierung wird durch vordefinierte Zugriffszuweisungsregeln aktiviert, sodass hart codierte Anmeldeinformationen im Quellcode oder in Konfigurationsdateien vermieden werden. Über Azure Lighthouse können Benutzer, die im Abonnement eines Kunden die Rolle „Benutzerzugriffsadministrator“ besitzen, eine verwaltete Identität im Mandanten dieses Kunden erstellen. Diese Rolle wird zwar in Azure Lighthouse nicht allgemein unterstützt, kann aber in diesem speziellen Szenario verwendet werden. So können Benutzer mit dieser Berechtigung verwalteten Identitäten bestimmte integrierte Rollen zuweisen.

Für Dienste, die keine verwalteten Identitäten unterstützen, können Sie stattdessen mit Azure AD Dienstprinzipale mit eingeschränkten Berechtigungen auf Ressourcenebene erstellen. Azure Lighthouse ermöglicht Dienstprinzipalen den Zugriff auf Kundenressourcen entsprechend den Rollen, die den Prinzipalen während des Onboardingprozesses zugewiesen wurden. Sie sollten Dienstprinzipale mit Zertifikatanmeldeinformationen und Fallback auf geheime Clientschlüssel konfigurieren. In beiden Fällen kann Azure Key Vault in Verbindung mit verwalteten Azure-Identitäten verwendet werden, damit die Laufzeitumgebung (z. B. eine Azure-Funktion) die Anmeldeinformationen aus dem Schlüsseltresor abrufen kann.

- [Von Azure verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure-Dienstprinzipal](/powershell/azure/create-azure-service-principal-azureps)

- [Verwenden von Azure Key Vault für die Registrierung von Sicherheitsprinzipalen](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Erstellen eines Benutzers, der einer verwalteten Identität in einem Kundenmandanten Rollen zuweisen kann](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Verwenden stärkerer Authentifizierungssteuerungen für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Legen Sie Multi-Factor Authentication (MFA) für alle Benutzer in Ihrem Verwaltungsmandanten als erforderlich fest. Dies schließt auch Benutzer ein, die Zugriff auf delegierte Kundenressourcen haben werden. Es empfiehlt sich, Ihre Kunden aufzufordern, MFA auch in ihren Mandanten zu implementieren.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Überwachen und Warnen bei Kontoanomalien

**Leitfaden**: Azure AD stellt die folgenden Datenquellen bereit: 

-   Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

-   Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Zu den in Überwachungsprotokollen protokollierten Änderungen zählen unter anderem das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

-   Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

-   Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Diese Datenquellen können in Azure Monitor, Azure Sentinel oder SIEM-Systeme von Drittanbietern integriert werden.

Dienstanbieter, die Azure Lighthouse verwenden, können Azure AD-Protokolle an Azure Sentinel weiterleiten und mandantenübergreifende Warnungen festlegen und anzeigen, um Kontoanomalien zu überwachen und zu melden.

- [Überwachungsaktivitätsberichte in Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)

- [Verwalten von Azure Sentinel-Arbeitsbereichen im großen Maßstab](how-to/manage-sentinel-workspaces.md)

- [Verbinden von Daten aus Azure AD mit Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Einschränken des Zugriffs auf Azure-Ressourcen basierend auf Bedingungen

**Leitfaden**: Azure Lighthouse unterstützt keine Funktion für bedingten Zugriff für delegierte Kundenressourcen. Verwenden Sie den bedingten Zugriff von Azure AD im Verwaltungsmandanten, um den Zugriff auf der Grundlage benutzerdefinierter Bedingungen präziser steuern zu können: Sie können beispielsweise festlegen, dass Benutzeranmeldungen aus bestimmten IP-Bereichen Multi-Factor Authentication (MFA) verwenden müssen. Eine differenzierte Authentifizierungssitzungsverwaltung kann auch über bedingte Azure AD-Zugriffsrichtlinien für verschiedene Anwendungsfälle erreicht werden. 

Sie sollten MFA für alle Benutzer in Ihrem Verwaltungsmandanten erzwingen, auch für Benutzer, die Zugriff auf delegierte Kundenressourcen haben werden. Es empfiehlt sich, Ihre Kunden aufzufordern, MFA auch in ihren Mandanten zu implementieren.

- [Übersicht über den bedingten Zugriff in Azure](../active-directory/conditional-access/overview.md)

- [Allgemeine Richtlinien für bedingten Zugriff](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="privileged-access"></a>Privilegierter Zugriff

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Schützen und Einschränken stark privilegierter Benutzer

**Leitfaden**: Schränken Sie die Anzahl der Benutzerkonten mit hohen Privilegien ein, und schützen Sie diese Konten auf höherer Ebene. Zum Aktivieren und Verwenden von Azure Lighthouse ist kein globales Administratorkonto erforderlich.

Für den Zugriff auf Aktivitätsprotokolldaten auf Mandantenebene muss einem Konto die integrierte Azure-Rolle Überwachungsleser im Stammbereich (/) zugewiesen werden. Da die Rolle „Benutzer mit Leseberechtigung für Überwachungsdaten“ auf Root-Ebene umfassenden Zugriff ermöglicht, wird empfohlen, diese Rolle einem Dienstprinzipalkonto und nicht einem einzelnen Benutzer oder einer Gruppe zuzuweisen. Diese Zuweisung muss von einem Benutzer durchgeführt werden, der über die globale Administratorrolle mit zusätzlichen erhöhten Zugriffsrechten verfügt. Diese erhöhten Zugriffsberechtigungen sollten direkt vor der Rollenzuweisung hinzugefügt und nach Abschluss der Zuweisung sofort wieder entfernt werden.

- [Administratorrollenberechtigungen in Azure AD](../active-directory/roles/permissions-reference.md)

- [Zuweisen des Zugriffs zur Überwachung von Aktivitätsprotokolldaten auf Mandantenebene](how-to/monitor-delegation-changes.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

**Leitfaden**: Azure Lighthouse verwendet die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um den Zugriff auf unternehmenskritische Systeme zu isolieren. Zu diesem Zweck werden die Konten begrenzt, denen privilegierter Zugriff auf die Abonnements und Verwaltungsgruppen gewährt wird, denen sie angehören.

Achten Sie darauf, das Prinzip der geringsten Rechte einzuhalten, damit Benutzer nur über die Berechtigungen verfügen, die zum Durchführen ihrer Aufgaben erforderlich sind.

Stellen Sie sicher, dass Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme einschränken, die über Administratorzugriff auf Ihre unternehmenskritischen Ressourcen verfügen, z. B. Active Directory-Domänencontroller (DCs), Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden.

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen.

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Zugriff auf die Verwaltungsgruppe](../governance/management-groups/overview.md#management-group-access)

- [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Best Practices für das Definieren von Benutzern und Rollen für Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Lighthouse verwendet Azure Active Directory-Konten (Azure AD) zum Verwalten von Ressourcen sowie zum regelmäßigen Überprüfen von Benutzerkonten und Zugriffszuweisungen, um die Gültigkeit der Konten und zugehörigen Zugriffsberechtigungen sicherzustellen. Sie können Azure AD-Zugriffsüberprüfungen verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu überprüfen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen. Sie können Azure AD Privileged Identity Management auch verwenden, um einen Workflow für den Zugriffsüberprüfungsbericht zu erstellen und so den Überprüfungsprozess zu vereinfachen.

Kunden können über Azure Lighthouse im Azure-Portal die Zugriffsebene überprüfen, die Benutzern im Verwaltungsmandanten zugewiesen wurde. Sie können diesen Zugriff jederzeit entfernen.

Darüber hinaus kann Azure Privileged Identity Management auch so konfiguriert werden, dass eine Warnung gesendet wird, wenn übermäßig viele Administratorkonten erstellt werden. Zudem kann Azure Privileged Identity Management veraltete oder falsch konfigurierte Administratorkonten ermitteln.

Beachten Sie, dass einige Azure-Dienste lokale Benutzer und Rollen unterstützen, die nicht über Azure AD verwaltet werden. Sie müssen diese Benutzer separat verwalten.

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Entfernen des Zugriffs auf eine Delegierung](how-to/remove-delegation.md)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Anzeigen der Dienstanbieterseite im Azure-Portal](how-to/view-manage-service-providers.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

**Leitfaden**: Azure Lighthouse ist zur Ressourcenverwaltung in Azure Active Directory integriert. Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.

Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](../active-directory/roles/security-emergency-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung 

**Leitfaden**: Azure Lighthouse ist zur Ressourcenverwaltung in Azure Active Directory (Azure AD) integriert. Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Die duale oder die mehrstufige Genehmigung wird ebenfalls unterstützt.

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md) 

- [Was ist die Azure AD-Berechtigungsverwaltung?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Verwenden von Privileged Access Workstations

**Leitfaden**: Gesicherte, isolierte Arbeitsstationen sind von entscheidender Bedeutung für die Sicherheit sensibler Rollen wie Administratoren, Entwickler und Operatoren kritischer Dienste. Je nach Anforderungen können Sie höchst sichere Benutzerarbeitsstationen und/oder Azure Bastion verwenden, um Verwaltungsaufgaben mit Azure Lighthouse in Produktionsumgebungen auszuführen. Verwenden Sie Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) und/oder Microsoft Intune, um eine sichere und verwaltete Benutzerworkstation für administrative Aufgaben einzurichten. Die gesicherten Workstations können zentral verwaltet werden, um eine gesicherte Konfiguration einschließlich starker Authentifizierung, Software- und Hardwarebaselines sowie eingeschränktem logischen und Netzwerkzugang durchzusetzen. 

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)

- [Bereitstellen einer sicheren, von Azure verwalteten Arbeitsstation](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte) 

**Leitfaden**: Azure Lighthouse ist zur Ressourcenverwaltung in die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) integriert. Mit Azure RBAC kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese integrierten Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Dies ergänzt den JIT-Ansatz (Just-in-Time) von Azure AD Privileged Identity Management (PIM), und diese Berechtigungen sollten regelmäßig überprüft werden. Verwenden Sie integrierte Rollen zur Zuweisung von Berechtigungen, und erstellen Sie benutzerdefinierte Rollen nur bei Bedarf.

Azure Lighthouse ermöglicht den Zugriff auf delegierte Kundenressourcen über integrierte Azure-Rollen. In den meisten Fällen sollten Sie diese Rollen einer Gruppe oder einem Dienstprinzipal zuweisen, nicht einer Vielzahl einzelner Benutzerkonten. Auf diese Weise können Sie den Zugriff für einzelne Benutzer hinzufügen oder entfernen, ohne den Plan aktualisieren und erneut veröffentlichen zu müssen, wenn sich ihre Zugriffsanforderungen ändern.

Um Kundenressourcen an einen Verwaltungsmandanten zu delegieren, muss eine Bereitstellung über ein Nicht-Gastkonto im Kundenmandanten durchgeführt werden. Dieses muss über die integrierte Rolle „Besitzer“ für das Abonnement verfügen, für das das Onboarding durchgeführt wird (oder das die Ressourcengruppen enthält, für die das Onboarding durchgeführt wird).

- [Grundlegendes zu Mandanten, Benutzern und Rollen in Azure Lighthouse](concepts/tenants-users-roles.md)

- [Anwenden des Prinzips der geringsten Rechte auf Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md) 

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="asset-management"></a>Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Ressourcenverwaltung](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

**Leitfaden**: Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Aus diesem Grund müssen Sicherheitserkenntnisse und -risiken immer innerhalb einer Organisation zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können weit gefasst auf einen gesamten Mandanten (Stammverwaltungsgruppe) angewandt oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Hinweis: Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

**Leitfaden**: Sicherheitsteams von Kunden können Aktivitätsprotokolle überprüfen, um Einblick in die Aktivitäten zu erhalten, die Dienstanbieter mit Azure Lighthouse ausführen. 

Wenn ein Dienstanbieter seinem Sicherheitsteam die Überprüfung von delegierten Kundenressourcen erlauben möchte, sollten die Autorisierungen dieses Teams die integrierte Rolle „Leser“ beinhalten.

- [Überprüfen von Dienstanbieteraktivitäten durch einen Kunden](how-to/view-service-provider-activity.md)

- [Angeben von Rollen beim Onboarding eines Kunden in Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln. Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

**Leitfaden**: Entfernen Sie den Zugriff auf über Azure Lighthouse delegierte Ressourcen, sobald diese nicht mehr benötigt werden, damit Dienstanbieter keinen Zugriff mehr auf diese Ressourcen haben. Der Zugriff kann entweder vom Kunden oder vom Dienstanbieter entfernt werden. 

- [Entfernen des Zugriffs auf eine Delegierung](how-to/remove-delegation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Azure Lighthouse ist zur Identitätsverwaltung und Authentifizierung in Azure Active Directory (Azure AD) integriert. Sie können den bedingten Azure-Zugriff verwenden, um die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="logging-and-threat-detection"></a>Protokollierung und Bedrohungserkennung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivieren der Bedrohungserkennung für Azure-Ressourcen

**Leitfaden**: Über Azure Lighthouse können Sie die Azure-Ressourcen Ihrer Kunden auf mögliche Bedrohungen und Anomalien überwachen. Das Ziel sollten möglichst hochwertige Warnungen sein. Damit verringern Sie die Anzahl falsch positiver Ergebnisse, die später von Analysten aussortiert werden müssen. Die Quellen von Warnungen können Protokolldaten, Agents oder andere Daten darstellen.

Verwenden Sie die integrierte Funktion zur Bedrohungserkennung in Azure Security Center, die auf der Überwachung der Telemetrie von Azure-Diensten und der Analyse von Dienstprotokollen basiert. Die Daten werden mit dem Log Analytics-Agent gesammelt. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle im System und kopiert die Daten zur Analyse in den Arbeitsbereich. 

Zusätzlich können Sie mithilfe von Azure Sentinel Analyseregeln erstellen, anhand derer in der gesamten Umgebung des Kunden nach Bedrohungen gesucht wird, die bestimmten Kriterien entsprechen. Bei Übereinstimmung mit den Kriterien werden durch die Regeln Incidents generiert, die Sie einzeln untersuchen können. Azure Sentinel kann auch Threat Intelligence von Drittanbietern importieren, um dessen Funktion zur Bedrohungserkennung zu verbessern. 

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md)

- [Sicherheitswarnungen von Azure Security Center (Referenzhandbuch)](../security-center/alerts-reference.md)

- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](../sentinel/tutorial-detect-threats-custom.md)

- [Verwalten von Azure Sentinel-Arbeitsbereichen im großen Maßstab](how-to/manage-sentinel-workspaces.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

**Leitfaden**: Über Azure Lighthouse können Sie Azure Security Center verwenden, um bei bestimmten verdächtigen Aktivitäten in den von Ihnen verwalteten Kundenmandanten zu warnen, z. B. bei einer übermäßigen Anzahl fehlerhafter Authentifizierungsversuche oder bei veralteten Konten im Abonnement.

Azure Active Directory (Azure AD) stellt die folgenden Benutzerprotokolle bereit, die Sie in der Azure AD-Berichterstellung anzeigen oder bei komplexeren Anwendungsfällen für Überwachung und Analyse in Azure Monitor, Azure Sentinel oder andere SIEM- oder Überwachungstools integrieren können:
- Anmeldungen: Der Bericht zu Anmeldungen enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
- Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.
- Riskante Anmeldungen: Eine riskante Anmeldung ist ein Hinweis auf einen Anmeldeversuch einer Person, die nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
- Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul von Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (VMs, Container, App Service), Datenressourcen (SQL-Datenbanken und -Speicher) und Azure-Dienstebenen sammeln. Diese Funktion bietet Einblick in Kontoanomalien innerhalb der einzelnen Ressourcen. 

- [Erweiterte Dienste und Szenarien mit Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Berichte zu Überwachungsaktivitäten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivieren von Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

**Leitfaden**: Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten alle Schreibvorgänge (PUT, POST, DELETE) für Ihre Azure Lighthouse-Ressourcen, aber keine Lesevorgänge (GET). Mit Aktivitätsprotokollen können Fehler ermittelt werden, und es kann nachverfolgt werden, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat.

Mit Azure Lighthouse können Sie Azure Monitor-Protokolle für alle von Ihnen verwalteten Kundenmandanten auf skalierbare Weise verwenden. Erstellen Sie Log Analytics-Arbeitsbereiche direkt in den Kundenmandanten, sodass die Kundendaten innerhalb der jeweiligen Mandanten verbleiben und nicht in Ihre Systeme exportiert werden. Dies ermöglicht auch eine zentrale Überwachung aller Ressourcen und Dienste, die von Log Analytics unterstützt werden, sodass Sie flexibler auswählen können, welche Arten von Daten Sie überwachen möchten.

Kunden, die delegierte Abonnements für Azure Lighthouse haben, können Daten des Azure-Aktivitätsprotokolls anzeigen, um sich alle ausgeführten Aktionen anzusehen. So erhalten Kunden umfassende Einblicke in Vorgänge, die von Dienstanbietern ausgeführt werden, sowie in Vorgänge, die von Benutzern innerhalb des eigenen Azure AD-Mandanten des Kunden ausgeführt werden.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Überwachen von delegierten Ressourcen in beliebigem Umfang](how-to/monitor-at-scale.md)

- [Anzeigen der Dienstanbieteraktivität](how-to/view-service-provider-activity.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

**Leitfaden**: Zentralisieren Sie die Speicherung und Analyse von Protokollen, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.

Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Zusätzlich können Sie auch Daten in Azure Sentinel oder der SIEM-Lösung eines Drittanbieters aktivieren und integrieren.

Mit Azure Lighthouse können Sie Azure Monitor-Protokolle für alle von Ihnen verwalteten Kundenmandanten auf skalierbare Weise verwenden. Erstellen Sie Log Analytics-Arbeitsbereiche direkt in den Kundenmandanten, sodass die Kundendaten innerhalb der jeweiligen Mandanten verbleiben und nicht in Ihre Systeme exportiert werden. Dies ermöglicht auch eine zentrale Überwachung aller Ressourcen und Dienste, die von Log Analytics unterstützt werden, sodass Sie flexibler auswählen können, welche Arten von Daten Sie überwachen möchten.

Kunden, die delegierte Abonnements für Azure Lighthouse haben, können Daten des Azure-Aktivitätsprotokolls anzeigen, um sich alle ausgeführten Aktionen anzusehen. So erhalten Kunden umfassende Einblicke in Vorgänge, die von Dienstanbietern ausgeführt werden, sowie in Vorgänge, die von Benutzern innerhalb des eigenen Azure AD-Mandanten des Kunden ausgeführt werden.

Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten.

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Überwachen von delegierten Ressourcen in beliebigem Umfang](how-to/monitor-at-scale.md)

- [Anzeigen von Dienstanbieteraktivitäten durch Kunden](how-to/view-service-provider-activity.md)

- [Verwalten von Azure Sentinel-Arbeitsbereichen im großen Maßstab](how-to/manage-sentinel-workspaces.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurieren der Aufbewahrungsdauer von Protokollen im Speicher

**Leitfaden**: Azure Lighthouse generiert derzeit keine sicherheitsbezogenen Protokolle. Kunden, die Aktivitäten ihres Dienstanbieters anzeigen möchten, können die Aufbewahrung von Protokollen gemäß ihren Compliance-, Regulierungs- und Geschäftsanforderungen konfigurieren. 

In Azure Monitor können Sie den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihres Unternehmens festlegen. Verwenden Sie für langfristige Speicherungen und Archivierungen Konten von Azure Storage, Data Lake oder des Log Analytics-Arbeitsbereichs.

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Exportieren von Azure Security Center-Warnungen und -Empfehlungen](../security-center/continuous-export.md): Konfigurieren Sie diese Einstellungen, und der Kunde kann keine Protokollaufbewahrung festlegen.

- [Überprüfen von Daten im Aktivitätsprotokoll für Dienstanbieter](how-to/view-service-provider-activity.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Vorbereitung – Aktualisieren des Prozesses zur Reaktion auf Vorfälle für Azure

**Leitfaden**: Stellen Sie sicher, dass Ihre Organisation über Prozesse verfügt, um auf Sicherheitsvorfälle zu reagieren, dass diese Prozesse für Azure aktualisiert wurden und dass sie regelmäßig trainiert werden, um die Bereitschaft zu gewährleisten.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Implementieren von Sicherheit in der gesamten Unternehmensumgebung)

- [Referenzleitfaden für die Reaktion auf Vorfälle](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Vorbereitung – Einrichten von Ereignisbenachrichtigungen

**Leitfaden**: Richten Sie Kontaktinformationen für Sicherheitsvorfälle im Azure Security Center ein. Microsoft wendet sich unter den angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Sie haben auch die Möglichkeit, die Warnungen und Benachrichtigungen bei Vorfällen in verschiedenen Azure-Diensten entsprechend Ihrer Anforderungen bezüglich der Reaktion auf Vorfälle anzupassen. 

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Erkennung und Analyse – Erstellen von Vorfällen basierend auf Warnungen mit hoher Qualität

**Leitfaden**: Stellen Sie sicher, dass Sie über einen Prozess zum Erstellen hochwertiger Warnungen und zum Messen der Qualität von Warnungen verfügen. Auf diese Weise können Sie Lehren aus vergangenen Vorfällen ziehen und Warnungen für Analysten priorisieren, damit diese keine Zeit mit falsch-positiven Ergebnissen verschwenden. 

Qualitativ hochwertige Warnungen können auf der Grundlage von Erfahrungen aus früheren Vorfällen, validierten Communityquellen und Tools zur Generierung und Bereinigung von Warnmeldungen durch Verschmelzung und Korrelation verschiedener Signalquellen erstellt werden. 

Azure Security Center bietet qualitativ hochwertige Warnungen für viele Azure-Ressourcen. Sie können den ASC-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. Mit Azure Sentinel können Sie erweiterte Warnregeln erstellen, um Vorfälle automatisch für eine Untersuchung zu generieren. 

Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über das Exportfeature, um Risiken für Azure-Ressourcen zu ermitteln. Exportieren Sie Warnungen und Empfehlungen entweder manuell oder fortlaufend, kontinuierlich.

- [Konfigurieren des Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

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

- [Momentaufnahme des Datenträgers eines Windows-Computers](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentaufnahme des Datenträgers eines Linux-Computers](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-Supportdiagnoseinformationen und Speicherabbilderfassung](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- Lesen Sie [Untersuchen von Incidents mit Azure Sentinel](../sentinel/tutorial-investigate-cases.md).

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Erkennung und Analyse – Priorisieren von Vorfällen

**Leitfaden**: Stellen Sie den Analysten Kontext bereit, auf welche Vorfälle sie sich zuerst konzentrieren sollten, je nach Schweregrad und Ressourcensensitivität. 

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Eindämmung, Ausmerzung und Wiederherstellung – Automatisieren der Behandlung von Vorfällen

**Leitfaden**: Automatisieren Sie manuelle, sich wiederholende Aufgaben, um die Antwortzeit zu beschleunigen und die Belastung der Analysten zu verringern. Manuelle Aufgaben dauern länger, verlangsamen jeden Vorfall und reduzieren die Anzahl der Vorfälle, die ein Analyst bewältigen kann. Manuelle Aufgaben erhöhen auch die Ermüdung der Analytiker. Dadurch erhöht sich das Risiko menschlicher Fehler, die zu Verzögerungen führen, und es verschlechtert sich die Fähigkeit der Analytiker, sich effektiv auf komplexe Aufgaben zu konzentrieren. Verwenden Sie die Workflowautomatisierungsfeatures in Azure Security Center und Azure Sentinel, um automatisch Aktionen auszulösen oder ein Playbook auszuführen, um auf eingehende Sicherheitswarnungen zu reagieren. Das Playbook führt Aktionen aus, wie das Versenden von Benachrichtigungen, das Deaktivieren von Konten und das Isolieren problematischer Netzwerke. 

- [Konfigurieren der Workflowautomatisierung in Security Center](../security-center/workflow-automation.md)

- [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md) vertraut.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="posture-and-vulnerability-management"></a>Status- und Sicherheitsrisikoverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Status- und Sicherheitsrisikoverwaltung](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Einrichten sicherer Konfigurationen für Azure-Dienste 

**Leitfaden**: Azure Lighthouse unterstützt die folgenden dienstspezifischen Richtlinien, die in Azure Security Center zur Überwachung und Durchsetzung von Konfigurationen Ihrer Azure-Ressourcen verfügbar sind. Die Konfiguration kann in Azure Security Center oder über Azure Policy-Initiativen erfolgen.

- Verwaltungsmandanten-IDs das Onboarding über Azure Lighthouse gestatten

- Delegierung von Bereichen an Verwaltungsmandanten überwachen

Sie können Azure Blueprints verwenden, um die Bereitstellung und Konfiguration von Diensten und Anwendungsumgebungen wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerelementen und Richtlinien in einer einzigen Blaupausendefinition zu automatisieren.

- [Azure Lighthouse-Richtlinien](samples/policy-reference.md)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Aufrechterhalten sicherer Konfigurationen für Azure-Dienste

**Leitfaden**: Azure Lighthouse unterstützt die folgenden dienstspezifischen Richtlinien, die in Azure Security Center zur Überwachung und Durchsetzung von Konfigurationen Ihrer Azure-Ressourcen verfügbar sind. Die Konfiguration kann in Azure Security Center oder über Azure Policy-Initiativen erfolgen.

- [Azure Lighthouse-Richtlinien](samples/policy-reference.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Einrichten sicherer Konfigurationen für Computeressourcen

**Leitfaden**: Verwenden Sie Azure Security Center und Azure Policy, um sichere Konfigurationen auf allen Computeressourcen wie beispielsweise VMs und Containern einzurichten.

- [Überwachen von Empfehlungen in Azure Security Center](../security-center/security-center-recommendations.md) 

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmäßiges Durchführen einer Angriffssimulation

**Leitfaden**: Führen Sie nach Bedarf Penetrationstests oder Red Team-Aktivitäten für Ihre Azure-Ressourcen durch, und stellen Sie sicher, dass alle kritischen Sicherheitsergebnisse behandelt werden.
Befolgen Sie die Einsatzregeln für Penetrationstests für die Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests in Azure](../security/fundamentals/pen-testing.md)

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="governance-and-strategy"></a>Governance und Strategie

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Governance und Strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Empfehlung für eine Azure-Sicherheitsarchitektur: Speicher, Daten, Verschlüsselung](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundlegende Azure-Sicherheitsinformationen: Sicherheit, Verschlüsselung und Speicherung von Azure-Daten](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure-Sicherheitsvergleichstest: Ressorcenverwaltung](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure-Sicherheitsvergleichstest: Datenschutz](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure-Sicherheitsvergleichstest: Verwaltung von Status und Sicherheitsrisiken](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

-   Strategie für Internet-Randpunkte, -Eingangspunkte und -Ausgangspunkte /azure/security/benchmarks/security-controls-v2-logging-threat-detection
-   Strategie für Konnektivität zwischen Hybrid Cloud und lokalen Systemen

-   Aktuelle Netzwerksicherheitsartefakte (z. B. Netzwerkdiagramme, Referenznetzwerkarchitektur)

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Die Netzwerksicherheit in Azure in der Übersicht](../security/fundamentals/network-overview.md)

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

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Übersicht über die Sicherheit der Azure-Identitätsverwaltung](../security/fundamentals/identity-management-overview.md)

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

- [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 – Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 4 – Prozess: Aktualisieren der Prozesse zur Reaktion auf Vorfälle für die Cloud)

- [Azure Adoption Framework, Entscheidungshilfe für Protokollierung und Berichterstattung](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure auf Unternehmensebene, Verwaltung und Überwachung](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](../security/benchmarks/overview.md) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).