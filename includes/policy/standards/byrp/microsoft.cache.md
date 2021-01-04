---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8aba76832117650c0833958362732e446755756f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992289"
---
## <a name="azure-security-benchmark"></a>Vergleichstest für die Azure-Sicherheit

Der [Azure-Sicherheitsvergleichstest](../../../../articles/security/benchmarks/overview.md) enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Zum Anzeigen der vollständigen Zuordnung dieses Diensts zum Azure-Sicherheitsvergleichstest sehen Sie sich die [Zuordnungsdateien zum Azure-Sicherheitsvergleichstest](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: Azure Sicherheitsvergleichstest](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Datenschutz |4.4 |Verschlüsseln aller vertraulichen Informationen während der Übertragung |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Integrationen für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Steuerung von Netzwerkverbindungen |0809.01n2Organizational.1234 - 01.n |Netzwerkdatenverkehr wird entsprechend der Zugriffssteuerungsrichtlinie der Organisation per Firewall und andere netzwerkbezogene Einschränkungen für jeden Netzwerkzugriffspunkt oder die verwaltete Schnittstelle des externen Telekommunikationsdiensts gesteuert. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Steuerung von Netzwerkverbindungen |0810.01n2Organizational.5 - 01.n |Die übertragenen Informationen sind geschützt und werden mindestens über geöffnete öffentliche Netzwerke verschlüsselt. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Steuerung von Netzwerkverbindungen |0811.01n2Organizational.6 - 01.n |Ausnahmen von der Datenverkehrsflussrichtlinie werden mit einer unterstützenden Unternehmens-/Geschäftsanforderung und einer Dauer der Ausnahme dokumentiert sowie mindestens jährlich geprüft. Ausnahmen bei der Datenverkehrsflussrichtlinie werden entfernt, wenn sie von einer expliziten Unternehmens-/Geschäftsanforderung nicht mehr unterstützt werden. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Steuerung von Netzwerkverbindungen |0812.01n2Organizational.8 - 01.n |Remotegeräte, die eine andere Verbindung als eine Remoteverbindung herstellen, dürfen mit externen Ressourcen (Remoteressourcen) nicht kommunizieren. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Steuerung von Netzwerkverbindungen |0814.01n1Organizational.12 - 01.n |Die Möglichkeit von Benutzern, eine Verbindung mit dem internen Netzwerk herzustellen, wird entsprechend der Zugriffssteuerungsrichtlinie und der Anforderungen von klinischen Anwendungen und Geschäftsanwendungen mit einer Richtlinie zur standardmäßigen Verweigerung von Programmen und ausnahmebasierten Zulassung auf verwalteten Schnittstellen eingeschränkt. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identifizierung von Risiken in Bezug auf externe Parteien |1451.05iCSPOrganizational.2 - 05.i |Clouddienstanbieter entwerfen und implementieren Steuerungen, um Risiken in Bezug auf die Datensicherheit zu entschärfen und zu minimieren, indem eine entsprechende Aufgabentrennung, rollenbasierte Zugriffssteuerung und Zugriff mit den geringsten Rechten für das gesamte Personal der Lieferkette genutzt werden. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Onlinetransaktionen |0946.09y2Organizational.14 - 09.y |Die Organisation erzwingt die Nutzung der Verschlüsselung zwischen den an einer Transaktion beteiligten Parteien und die Verwendung von elektronischen Signaturen. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- und Kommunikationsschutz |3.13.1 |Sorgen Sie für die Überwachung, die Kontrolle und den Schutz der Kommunikation (Informationen, die für Unternehmenssysteme übertragen oder empfangen werden) an den äußeren Grenzen und wichtigen internen Grenzen der Unternehmenssysteme. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System- und Kommunikationsschutz |3.13.8 |Implementieren Sie kryptografische Mechanismen zur Verhinderung einer unbefugten Offenlegung von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI) während der Übertragung, sofern diese nicht durch andere physische Sicherheitsmaßnahmen geschützt sind. |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- und Kommunikationsschutz |SC-8 (1) |Vertraulichkeit und Integrität von übertragenen Informationen \| Kryptografischer oder alternativer physischer Schutz |[Für Azure Cache for Redis dürfen nur sichere Verbindungen aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

