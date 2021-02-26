---
title: Regionsübergreifendes Verschieben einer Azure Automanage-VM
description: Hier erfahren Sie mehr über das regionsübergreifende Verschieben eines automatisch verwalteten virtuellen Computers.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e880992143bf79a5a99cc9830957f83167b6b46
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808018"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Verschieben einer Azure Automanage-VM in eine andere Region
In diesem Artikel wird beschrieben, wie Sie Automanage auf einem virtuellen Computer (VM) aktivieren, wenn Sie diesen in eine andere Region verschieben. Sie sollten Ihre VMs aus verschiedenen Gründen in eine andere Region verschieben. Beispielsweise, um eine neue Azure-Region zu nutzen, interne Richtlinien- und Governanceanforderungen zu erfüllen oder als Reaktion auf Anforderungen der Kapazitätsplanung. Die VMs, die Sie verschieben, werden derzeit möglicherweise automatisch verwaltet. Dies soll auch nach dem Verschieben noch der Fall sein.

## <a name="prerequisites"></a>Voraussetzungen
* Stellen Sie sicher, dass die Zielregion [von Automanage unterstützt wird](./automanage-virtual-machines.md#prerequisites).
* Vergewissern Sie sich, dass Ihre Log Analytics-Arbeitsbereichsregion, Ihre Automation-Kontoregion und die Zielregion alle Regionen sind, die von [diesen Regionszuordnungen](https://docs.microsoft.com/azure/automation/how-to/region-mappings) unterstützt werden.

## <a name="prepare-your-automanaged-vms-for-moving"></a>Vorbereiten der automatisch verwalteten VMs für das Verschieben
Deaktivieren Sie Automanage für Ihre automatisch verwalteten VMs. Wählen Sie hierzu Ihre VMs auf dem Blatt „Automanage“ aus, und klicken Sie dann auf **Disable automanagement** (Automatische Verwaltung deaktivieren).

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Verschieben der automatisch verwalteten VMs und erneutes Aktivieren von Automanage
Ausführliche Informationen zum Verschieben Ihrer VMs finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/resource-mover/tutorial-move-region-virtual-machines).

Nachdem Sie Ihre VMs regionsübergreifend verschoben haben, können Sie Automanage erneut für sie aktivieren. Weitere Informationen finden Sie [hier](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Automanage finden Sie [hier](./automanage-virtual-machines.md).
* [Häufig gestellte Fragen zur automatischen Azure-Verwaltung für VMs](./faq.md)