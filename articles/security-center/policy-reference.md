---
title: Integrierte Richtliniendefinitionen für Azure Security Center
description: Hier finden Sie eine Liste der integrierten Azure Policy-Richtliniendefinitionen für Azure Security Center. Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung von Azure-Ressourcen.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: ab6556bc391eb3136fa8c4ba40f5a5d71da956e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093041"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Integrierte Azure Policy-Definitionen für Azure Security Center

Diese Seite enthält einen Index der integrierten [Azure Policy](../governance/policy/overview.md)-Richtliniendefinitionen im Zusammenhang mit Azure Security Center. Die folgenden Gruppierungen von Richtliniendefinitionen sind verfügbar:

- Die Gruppe [Initiativen](#azure-security-center-initiatives) listet die Azure Policy-Initiativdefinitionen in der Kategorie „Security Center“ auf.
- In der Gruppe [Standardinitiative](#azure-security-center-initiatives) werden alle Azure Policy-Definitionen aufgeführt, die Teil der Security Center-Standardinitiative [Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md) sind. Diese von Microsoft erstellte, weit verbreitete Benchmark basiert auf Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.
- Die Gruppe [Kategorie](#azure-security-center-category) listet alle Azure Policy-Definitionen in der Kategorie „Security Center“ auf.

Weitere Informationen zu Sicherheitsrichtlinien finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](./tutorial-security-policy.md). Weitere Azure Policy-Integrationen für andere Dienste finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](../governance/policy/samples/built-in-policies.md).

Die Namen der einzelnen integrierten Richtliniendefinitionen sind Links zur entsprechenden Richtliniendefinition im Azure-Portal. Verwenden Sie den Link in der Spalte **Version**, um die Quelle im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) anzuzeigen.

## <a name="azure-security-center-initiatives"></a>Azure Security Center-Initiativen

Informationen zu den integrierten Initiativen, die von Security Center überwacht werden, finden Sie in der folgenden Tabelle:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure Security Center-Standardinitiative

Informationen zu den integrierten Richtlinien, die von Security Center überwacht werden, finden Sie in der folgenden Tabelle:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center-Kategorie

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Policy-Sicherheitsrichtliniendefinitionen in Security Center informiert. Weitere Informationen finden Sie in den folgenden Artikeln:

- Sehen Sie sich die Integrationen im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../governance/policy/concepts/effects.md).
- [Planungs- und Betriebshandbuch für Azure Security Center](./security-center-planning-and-operations-guide.md): Hier erfahren Sie, wie Sie Entwurfsaspekte in Azure Security Center planen und verstehen.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](./security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](./security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Überwachen von Partnerlösungen mit Azure Security Center](./security-center-partner-integration.md): Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
- [Azure Policy](../governance/policy/overview.md): Hier erfahren Sie, wie Sie Ihre Azure-Ressourcen überwachen und steuern.
