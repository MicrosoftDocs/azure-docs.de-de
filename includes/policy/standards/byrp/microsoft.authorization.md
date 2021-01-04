---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 13169635a235816b79306f73a826dea944549d07
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988668"
---
## <a name="azure-security-benchmark"></a>Vergleichstest für die Azure-Sicherheit

Der [Azure-Sicherheitsvergleichstest](../../../../articles/security/benchmarks/overview.md) enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Zum Anzeigen der vollständigen Zuordnung dieses Diensts zum Azure-Sicherheitsvergleichstest sehen Sie sich die [Zuordnungsdateien zum Azure-Sicherheitsvergleichstest](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: Azure Sicherheitsvergleichstest](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Datenschutz |4.6 |Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen |[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitäts- und Zugriffsverwaltung |1.23 |Sicherstellen, dass keine benutzerdefinierten Abonnementbesitzerrollen erstellt werden |[Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Integrationen für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Berechtigungsverwaltung |1148.01c2System.78 - 01.c |Die Organisation schränkt den Zugriff auf privilegierte Funktionen und alle sicherheitsrelevanten Informationen ein. |[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Aufgabentrennung |1230.09c2Organizational.1 - 09.c |Einzelpersonen können ohne Autorisierung oder Erkennung nicht auf Informationssysteme zugreifen und diese nicht ändern oder nutzen. |[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Aufgabentrennung |1276.09c2Organizational.2 - 09.c |Mit der Sicherheitsüberwachung verbundene Aktivitäten werden unabhängig behandelt. |[Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|Aufgabentrennung |1278.09c2Organizational.56 - 09.c |Die Organisation identifiziert Aufgaben, für die eine Trennung erforderlich ist, und definiert Zugriffsautorisierungen für Informationssysteme, um die Trennung von Aufgaben zu unterstützen. Inkompatible Aufgaben sind auf mehrere Benutzer verteilt, um die Wahrscheinlichkeit für Missbrauchs- oder Betrugsfälle zu verringern. |[Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Zugriffssteuerung |AC-2 (7) |Kontoverwaltung \| Rollenbasierte Schemas |[Verwendung benutzerdefinierter RBAC-Regeln überwachen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

