---
title: Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search
description: Hier sind Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy aufgelistet, die für Azure Cognitive Search verfügbar sind. Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung der Compliance Ihrer Azure-Ressourcen.
ms.date: 11/20/2020
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 9d271445d472739c304cbf8fa43fe7feb1c6b1bb
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985626"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search

Wenn Sie [Azure Policy](../governance/policy/overview.md) zum Erzwingen der Empfehlungen im [Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md) verwenden, wissen Sie wahrscheinlich bereits, dass Sie Richtlinien zum Ermitteln und Korrigieren nicht konformer Dienste erstellen können. Diese Richtlinien können benutzerdefiniert sein oder auf integrierten Definitionen basieren, die Konformitätskriterien und geeignete Lösungen für leicht nachvollziehbare Best Practices bereitstellen.

Für Azure Cognitive Search gibt es derzeit eine integrierte Definition (siehe unten), die Sie in einer Richtlinienzuweisung verwenden können. Diese Definition dient der Protokollierung und Überwachung. Wenn Sie diese integrierte Definition in einer [von Ihnen erstellten Richtlinie](../governance/policy/assign-policy-portal.md) verwenden, sucht das System nach Diensten, für die keine [Diagnoseprotokollierung](search-monitor-logs.md) aktiviert ist, und aktiviert die Protokollierung dann entsprechend.

Die [Einhaltung gesetzlicher Bestimmungen in Azure Policy](../governance/policy/concepts/regulatory-compliance.md) umfasst von Microsoft erstellte und verwaltete Initiativendefinitionen (als _Integrationen_ bezeichnet) für die **Compliancedomänen** und **Sicherheitskontrollen**, die sich auf unterschiedliche Compliancestandards beziehen. Auf dieser Seite werden die **Compliancedomänen** und **Sicherheitskontrollen** für Azure Cognitive Search aufgeführt. Sie können die integrierten Elemente für eine **Sicherheitskontrolle** einzeln zuweisen, um Ihre Azure-Ressourcen mit dem jeweiligen Standard kompatibel zu machen.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Einhaltung gesetzlicher Bestimmungen in Azure Policy](../governance/policy/concepts/regulatory-compliance.md)
- Sehen Sie sich die Integrationen im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) an.
