---
title: 'Azure-Sicherheitsvergleichstest V2: Privilegierter Zugriff'
description: 'Azure-Sicherheitsvergleichstest V2: Privilegierter Zugriff'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd39f5e0af34c702cddc8e08b6a94e428c7f0167
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092049"
---
# <a name="security-control-v2-privileged-access"></a>Sicherheitskontrolle V2: Privilegierter Zugriff

Privilegierter Zugriff gilt für Kontrollelemente, um den privilegierten Zugriff auf Ihren Azure-Mandanten und Ihre Azure-Ressourcen zu schützen. Dies umfasst eine Reihe von Kontrollelementen, um Ihr Verwaltungsmodell, Ihre Verwaltungskonten und Workstations mit privilegiertem Zugriff vor absichtlichen und unbeabsichtigten Risiken zu schützen.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Schützen und Einschränken stark privilegierter Benutzer

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

Schränken Sie die Anzahl der Benutzerkonten mit hohen Privilegien ein, und schützen Sie diese Konten auf höherer Ebene. Die wichtigsten integrierten Rollen in Azure AD sind „Globaler Administrator“ und „Administrator für privilegierte Rollen“, da Benutzer, die diesen beiden Rollen zugeordnet sind, Administratorrollen delegieren können. Mit diesen Berechtigungen können Benutzer jede Ressource in Ihrer Azure-Umgebung direkt oder indirekt lesen und ändern:

- Globaler Administrator: Benutzer mit dieser Rolle haben Zugriff auf alle administrativen Funktionen in Azure AD sowie auf Dienste, die Azure AD-Identitäten verwenden.

- Administrator für privilegierte Rollen: Benutzer mit dieser Rolle können Rollenzuweisungen in Azure AD und Azure AD Privileged Identity Management (PIM) verwalten. Überdies ermöglicht diese Rolle Verwaltung aller Aspekte von PIM und administrativer Einheiten.

Hinweis: Möglicherweise verfügen Sie über weitere kritische Rollen, die geregelt werden müssen, wenn Sie benutzerdefinierte Rollen mit bestimmten zugewiesenen privilegierten Berechtigungen verwenden. Es empfiehlt sich, ähnliche Kontrollelemente auch auf das Administratorkonto für kritische Unternehmensressourcen anzuwenden.  

Mit Azure AD Privileged Identity Management (PIM) können Sie privilegierten Just-in-Time-Zugriff (JIT) auf Azure-Ressourcen und Azure AD ermöglichen. JIT erteilt nur dann temporäre Berechtigungen zur Ausführung privilegierter Aufgaben, wenn die Benutzer sie benötigen. PIM kann auch Sicherheitswarnungen erzeugen, wenn es in Ihrer Azure AD-Organisation verdächtige oder unsichere Aktivitäten gibt.

- [Administratorrollenberechtigungen in Azure AD](../../active-directory/roles/permissions-reference.md)

- [Verwenden von Sicherheitswarnungen von Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../../active-directory/roles/security-planning.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Einschränken des administrativen Zugriffs auf unternehmenskritische Systeme

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-2 | 13.2, 2.10 | AC-2, SC-3, SC-7 |

Isolieren Sie den Zugriff auf unternehmenskritische Systeme, indem Sie die Konten begrenzen, denen privilegierter Zugriff auf die Abonnements und Verwaltungsgruppen gewährt wird, in denen sie sich befinden. Stellen Sie sicher, dass Sie auch den Zugriff auf die Verwaltungs-, Identitäts- und Sicherheitssysteme einschränken, die über Administratorzugriff auf Ihre unternehmenskritischen Ressourcen verfügen, z. B. Active Directory-Domänencontroller (DCs), Sicherheitstools und Systemverwaltungstools mit Agents, die auf unternehmenskritischen Systemen installiert sind. Angreifer, die diese Verwaltungs- und Sicherheitssysteme kompromittieren, können diese sofort ausnutzen, um geschäftskritische Ressourcen zu gefährden. 

Alle Zugriffssteuerungstypen sollten an der Segmentierungsstrategie des Unternehmens ausgerichtet werden, um eine konsistente Zugriffssteuerung sicherzustellen. 

Vergewissern Sie sich, dass Sie separate privilegierte Konten zuweisen, die sich von den Standardbenutzerkonten unterscheiden, die für E-Mail-, Browser- und Produktivitätsaufgaben verwendet werden.

- [Azure-Komponenten und -Referenzmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Zugriff auf die Verwaltungsgruppe](../../governance/management-groups/overview.md#management-group-access)

- [Azure-Abonnementadministratoren](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-3 | 4.1, 16.9, 16.10 | AC-2 |

Überprüfen Sie Benutzerkonten und die Zugriffszuweisung regelmäßig, um sicherzustellen, dass die Benutzerkonten und ihre Zugriffsebene geeignet sind. Sie können Azure AD-Zugriffsüberprüfungen verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu überprüfen. Azure AD-Berichte können Protokolle zum Ermitteln von veralteten Konten bereitstellen. Sie können auch Azure AD Privileged Identity Management verwenden, um einen Workflow für den Zugriffsüberprüfungsbericht zu erstellen und so den Überprüfungsprozess zu vereinfachen.
Darüber hinaus können Sie Azure Privileged Identity Management konfigurieren, sodass Sie eine Warnung erhalten, wenn übermäßig viele Administratorkonten erstellt werden, und um veraltete oder falsch konfigurierte Administratorkonten zu ermitteln. 

Hinweis: Einige Azure-Dienste unterstützen lokale Benutzer und Rollen, die nicht über Azure AD verwaltet werden. Diese Benutzer müssen separat verwaltet werden.

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../../active-directory/governance/access-reviews-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Einrichten des Notfallzugriffs in Azure AD

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Um zu verhindern, dass Sie versehentlich aus Ihrer Azure AD-Organisation ausgesperrt werden, richten Sie ein Konto für den Notfallzugriff ein, für den Fall, dass normale Verwaltungskonten nicht verwendet werden können. Konten für den Notfallzugriff verfügen normalerweise über umfangreiche Berechtigungen und sollten keinen Einzelpersonen zugewiesen werden. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können.
Sie sollten sicherstellen, dass die Anmeldeinformationen (z. B. Kennwort, Zertifikat oder Smartcard) für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung im Notfall autorisiert sind.

- [Verwalten von Konten für den Notfallzugriff in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Security Operations (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisieren der Berechtigungsverwaltung

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Verwenden Sie die Berechtigungsverwaltungsfeatures von Azure AD, um die Zugriffsanforderungsworkflows zu automatisieren, einschließlich Zugriffszuweisungen, Überprüfungen und Ablauf. Die duale oder die mehrstufige Genehmigung wird ebenfalls unterstützt.
- [Was sind Azure AD-Zugriffsüberprüfungen?](../../active-directory/governance/access-reviews-overview.md) 

- [Was ist die Azure AD-Berechtigungsverwaltung?](../../active-directory/governance/entitlement-management-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Verwenden von Privileged Access Workstations

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-6 | 4.6, 11.6, 12.12 | AC-2, SC-3, SC-7 |

Gesicherte, isolierte Arbeitsstationen sind von entscheidender Bedeutung für die Sicherheit sensibler Rollen wie Administratoren, Entwickler und Operatoren kritischer Dienste. Verwenden Sie stark gesicherte Benutzerworkstations und/oder Azure Bastion für administrative Aufgaben. Verwenden Sie Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) und/oder Microsoft Intune, um eine sichere und verwaltete Benutzerworkstation für administrative Aufgaben einzurichten. Die gesicherten Workstations können zentral verwaltet werden, um eine gesicherte Konfiguration einschließlich starker Authentifizierung, Software- und Hardwarebaselines sowie eingeschränktem logischen und Netzwerkzugang durchzusetzen. 

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Bereitstellen einer sicheren, von Azure verwalteten Arbeitsstation](/security/compass/privileged-access-deployment)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Security Operations (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Befolgen Sie die Prinzipien der Just Enough Administration (Prinzip der geringsten Rechte)

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-7 | 14.6 | AC-2, AC-3, SC-3 |

Mit der rollenbasierten Zugriffssteuerung (Azure RBAC) kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppendienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell und das Azure-Portal inventarisiert oder abgefragt werden. Die Berechtigungen, die Sie Ressourcen über Azure RBAC zuweisen, sollten immer auf die nötigsten Anforderungen der Rollen beschränkt sein. Eingeschränkte Berechtigungen ergänzen den JIT-Ansatz (Just-in-Time) von Azure AD Privileged Identity Management (PIM), und diese Berechtigungen sollten regelmäßig überprüft werden.
Verwenden Sie integrierte Rollen, um Berechtigungen zuzuweisen und benutzerdefinierte Rollen nur bei Bedarf zu erstellen. 

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)

- [Konfigurieren von Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../../active-directory/governance/access-reviews-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Auswählen des Genehmigungsprozesses für Microsoft-Support 

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP 800-53 r4 |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, bietet Kunden-Lockbox Ihnen die Möglichkeit, einzelne Anforderungen des Zugriffs auf Kundendaten explizit zu prüfen und dann zu genehmigen oder abzulehnen.

- [Grundlegendes zu Kunden-Lockbox](../fundamentals/customer-lockbox-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Funktion von Identitäts- und Schlüsselverwaltung in der Cloud](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)