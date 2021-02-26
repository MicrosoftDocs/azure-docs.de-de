---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6dd60d30daa80b8d7b522c0695ada782e4bdb38b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105782"
---
## <a name="cmmc-level-3"></a>CMMC Level 3

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Integrationen für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Zugriffssteuerung |AC.1.001 |Beschränken Sie den Zugriff auf das Informationssystem auf autorisierte Benutzer, Prozesse, die im Namen von autorisierten Benutzern durchgeführt werden, und Geräte (einschließlich anderer Informationssysteme). |[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Zugriffssteuerung |AC.1.002 |Beschränken Sie den Zugriff auf das Informationssystem auf die Arten von Transaktionen und Funktionen, die autorisierte Benutzer ausführen dürfen. |[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Zugriffssteuerung |AC.2.016 |Steuern Sie den Fluss von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI) gemäß den genehmigten Autorisierungen. |[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Konfigurationsverwaltung |CM.3.068 |Sorgen Sie dafür, dass die Nutzung von nicht unbedingt erforderlichen Programmen, Funktionen, Ports, Protokollen und Diensten eingeschränkt, deaktiviert oder verhindert wird. |[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|System- und Kommunikationsschutz |SC.3.177 |Nutzen Sie FIPS-konforme Kryptografie zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI). |[Die Azure-API für fhir sollte einen vom Kunden verwalteten Schlüssel zum Verschlüsseln ruhender Daten verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|System- und Kommunikationsschutz |SC.3.183 |Verweigern Sie standardmäßig Netzwerkkommunikationsdatenverkehr, und erlauben Sie diesen nur basierend auf Ausnahmen (d. h. basierend auf einer Richtlinie zur Verweigerung aller Programme und ausnahmebasierten Zulassung). |[Zugriff auf API for FHIR über CORS nicht allen Domänen gestatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

