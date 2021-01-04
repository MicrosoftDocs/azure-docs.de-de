---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476676"
---
## <a name="azure-security-benchmark"></a>Vergleichstest für die Azure-Sicherheit

Der [Azure-Sicherheitsvergleichstest](../../../../articles/security/benchmarks/overview.md) enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Zum Anzeigen der vollständigen Zuordnung dieses Diensts zum Azure-Sicherheitsvergleichstest sehen Sie sich die [Zuordnungsdateien zum Azure-Sicherheitsvergleichstest](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: Azure Sicherheitsvergleichstest](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Datenschutz |4.8 |Verschlüsseln vertraulicher, ruhender Informationen |[Automation-Kontovariablen sollten verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Wenn Sie eine Automation-Kontovariable erstellen, können Sie deren Verschlüsselung und Speicherung durch Azure Automation als sichere Ressource angeben. Nachdem Sie die Variable erstellt haben, können Sie ihren Verschlüsselungsstatus nicht ändern, ohne die Variable neu erstellen zu müssen. Wenn Sie über Automation-Kontovariablen verfügen, die vertrauliche Daten speichern und noch nicht verschlüsselt sind, müssen Sie sie löschen und als verschlüsselte Variablen neu erstellen. Eine Azure Security Center-Empfehlung ist, alle Azure Automation-Variablen wie unter [Automation-Kontovariablen sollten verschlüsselt werden](../../../../articles/security-center/recommendations-reference.md#recs-computeapp) beschrieben zu verschlüsseln. Wenn Sie über unverschlüsselte Variablen verfügen, die aus dieser Sicherheitsempfehlung ausgeschlossen werden sollen, erfahren Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](../../../../articles/security-center/exempt-resource.md), wie Sie eine Ausnahmeregel erstellen.