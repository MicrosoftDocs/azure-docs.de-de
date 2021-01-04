---
title: Empfohlene Richtlinien für Azure-Dienste.
description: Hier wird beschrieben, wie Sie empfohlene Richtlinien für Azure-Dienste wie Azure Virtual Machines finden und anwenden können.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350278"
---
# <a name="recommended-policies-for-azure-services"></a>Empfohlene Richtlinien für Azure-Dienste.

Neue Kunden von Azure Policy suchen oft nach gemeinsamen Richtliniendefinitionen zur Verwaltung und Steuerung ihrer Ressourcen. **Empfohlene Richtlinien** von Azure Policy bietet eine entsprechende Liste von gemeinsamen Richtliniendefinitionen für die ersten Schritte. Die **empfohlenen Richtlinien** für unterstützte Ressourcen sind in die Portalumgebung für diese Ressource eingebettet.

Weitere Azure Policy-Integrationen finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Die **empfohlenen Richtlinien** für [Azure Virtual Machines](../../../virtual-machines/index.yml) befinden sich auf der Seite **Übersicht** für virtuelle Computer und auf der Registerkarte **Funktionen**. Wählen Sie auf der Karte _Azure Policy_ den Text „Not configured“ (Nicht konfiguriert) oder „# assigned“ (zugewiesene Anzahl) aus, um einen Seitenbereich mit den empfohlenen Richtlinien zu öffnen. Alle Richtliniendefinitionen, die bereits einem Bereich zugewiesen sind, zu dem der virtuelle Computer gehört, werden abgeblendet dargestellt. Wählen Sie die empfohlenen Richtlinien, die auf diesen virtuellen Computer angewendet werden sollen, und dann **Richtlinien zuweisen** aus, um für jede eine Zuweisung zu erstellen.

Wenn eine Organisation mit der [Anordnung ihrer Ressourcen und der Ressourcenhierarchie](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions) die Einsatzreife erlangt, empfiehlt es sich, bei diesen Richtlinienzuweisungen von „Eine pro Ressource“ auf die Abonnement- oder [Verwaltungsgruppenebene](../../management-groups/index.yml) überzugehen.

### <a name="azure-virtual-machines-recommended-policies"></a>Empfohlene Richtlinien für Azure Virtual Machines

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VMs überwachen, für die keine Notfallwiederherstellung konfiguriert ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Hiermit werden VMs überwacht, für die keine Notfallwiederherstellung konfiguriert ist. Weitere Informationen zur Notfallwiederherstellung finden Sie unter [https://aka.ms/asr-doc](../../../site-recovery/index.yml). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Diese Richtlinie überwacht virtuelle Computer, die keine verwalteten Datenträger verwenden. |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup muss für Virtual Machines aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Mit dieser Richtlinie können Sie überwachen, ob der Azure Backup-Dienst für alle virtuellen Computer aktiviert ist. Azure Backup ist eine kostengünstige Sicherungslösung per Mausklick, die die Datenwiederherstellung vereinfacht und leichter zu aktivieren ist als andere Cloudsicherungsdienste. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](./effects.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.