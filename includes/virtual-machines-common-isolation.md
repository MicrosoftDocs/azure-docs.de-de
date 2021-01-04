---
title: include file
description: include file
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: bd31152c6742271658cf4fd7ac2a5e2957651c84
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478403"
---
Azure Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind. Die Größen isolierter VMs gelten für eine bestimmte Hardwaregeneration und sind veraltet, sobald die Hardwaregeneration außer Betrieb gesetzt wird.

Die Größen isolierter VMs eignen sich am besten für Workloads, die ein hohes Maß an Isolation von den Workloads anderer Kunden erfordern, beispielsweise um Compliance zu erzielen und gesetzliche Anforderungen zu erfüllen.  Durch die Verwendung isolierter Größen wird sichergestellt, dass Ihr virtueller Computer als einziger in der jeweiligen Serverinstanz ausgeführt wird. 


Da isolierte VMs groß sind, können Kunden die Ressourcen dieser VMs zudem unterteilen, indem sie die [Azure-Support für geschachtelte VMs](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden.

Zu den aktuellen Angebote isolierter virtueller Computer zählen Folgende:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2


> [!NOTE]
> Die Größen von isolierten VMs haben eine durch die Hardware begrenzte Lebensdauer. Ausführliche Informationen hierzu finden Sie weiter unten.

## <a name="deprecation-of-isolated-vm-sizes"></a>Eingestellte Unterstützung von Größen isolierter VMs

Da es sich bei der Größe isolierter VMs um eine hardwaregebundene Größe handelt, werden über Azure 12 Monate vor der offiziellen Einstellung der Unterstützung der Größe Erinnerungen bereitgestellt.  Zudem wird über Azure eine aktualisierte Größe isolierter VMs für die nächste Hardwareversion bereitgestellt, auf die der Kunde seine Workload verschieben kann.

| Size | Datum für die Außerbetriebnahme der Isolation | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15. Mai 2020 |
| Standard_D15_v2<sup>1</sup>  | 15. Mai 2020 |

<sup>1</sup> Ausführliche Informationen zum Programm für die Außerbetriebnahme von Isolation Standard_DS15_v2 und Standard_D15_v2 finden Sie in den häufig gestellten Fragen.


## <a name="faq"></a>Häufig gestellte Fragen
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>F: Wird die Unterstützung der Größe eingestellt oder nur das Feature „Isolation“?
**A:** Wenn die VM-Größe keinen „i“-Index aufweist, wird nur die Unterstützung des Features „Isolation“ eingestellt. Wenn keine Isolation erforderlich ist, muss nichts unternommen werden und die VM funktioniert weiterhin erwartungsgemäß. Beispiele hierfür sind Standard_DS15_v2, Standard_D15_v2, Standard_M128ms usw. Wenn die VM-Größe einen „i“-Index aufweist, wird die Unterstützung der Größe eingestellt.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>F: Gibt es eine Ausfallzeit, wenn meine VM auf nicht isolierte Hardware verschoben wird?
**A:** Wenn keine Isolation erforderlich ist, muss nichts unternommen werden und es treten keine Ausfallzeiten auf.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>F: Gibt es Kostenänderungen beim Umstieg auf eine nicht isolierte VM?
**A:** Nein

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>F: Wann werden die anderen isolierten Größen außer Betrieb genommen?
**A:** Wir werden 12 Monate vor der offiziellen Einstellung der Unterstützung der Größen von isolierten VMs Erinnerungen bereitstellen.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>F: Ich bin ein Azure Service Fabric-Kunde, der die Dauerhaftigkeitsstufen Silver oder Gold verwendet. Wirkt sich diese Änderung auf mich aus?
**A:** Nein. Die von den [Dauerhaftigkeitsstufen](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) von Service Fabric bereitgestellten Garantien werden auch nach dieser Änderung weiterhin funktionieren. Wenn Sie aus anderen Gründen physische Hardwareisolierung benötigen, müssen Sie möglicherweise dennoch eine der oben beschriebenen Aktionen ausführen. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>F: Welche Meilensteine gibt es bei der Einstellung der Unterstützung der Isolation von D15_v2 bzw. DS15_v2? 
**A:** 
 
| Date | Aktion |
|---|---| 
| 18. November 2019 | Verfügbarkeit von D/DS15i_v2 (PAYG, 1 Jahr RI) | 
| 14. Mai 2020 | Letzter Tag zum Erwerb von D/DS15i_v2 RI für 1 Jahr | 
| 15. Mai 2020 | D/DS15_v2-Isolationsgarantie entfernt | 
| 15. Mai 2021 | Außerbetriebnahme von D/DS15i_v2 (alle Kunden mit Ausnahme derjenigen, die vor dem 18. November 2019 RIs von D/DS15_v2 für 3 Jahre erworben haben)| 
| 17. November 2022 | Außerbetriebnahme von D/DS15i_v2 nach Ablauf von RIs für 3 Jahre (für Kunden, die vor dem 18. November 2019 RIs von D/DS15_v2 für 3 Jahre erworben haben) |

## <a name="next-steps"></a>Nächste Schritte

Kunden können auch die Ressourcen dieser isolierten virtuellen Computer weiter unterteilen, indem sie die [Azure-Unterstützung für geschachtelte virtuelle Computer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden.
