---
title: Anfordern einer Erhöhung der regionalen Azure vCPU-Kontingentgrenzwerte
description: Erfahren Sie, wie Sie im Azure-Portal eine Erhöhung des vCPU-Kontingentgrenzwerts für eine Region anfordern.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745416"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standardkontingent: Erhöhen der Grenzwerte nach Region

Azure Resource Manager unterstützt zwei Arten von vCPU-Kontingenten für virtuelle Computer:

* *VMs mit nutzungsbasierter Bezahlung* und *reservierte VM-Instanzen* verwenden *vCPU-Standardkontingente*.
* *Spot-VMs* unterliegen einem *Spot-vCPU-Kontingent*.

Das vCPU-Standardkontingent für nutzungsbasierte Bezahlung und reservierte VM-Instanzen wird für jedes Abonnement in jeder Region auf zwei Ebenen erzwungen:

* Die erste Ebene ist der *Grenzwert regionaler vCPUs gesamt* (für alle VM-Serien).
* Die zweite Ebene ist der *vCPU-Grenzwert pro VM-Serie* (z. B. vCPUs der D-Serie).

Wenn Sie eine neue Spot-VM bereitstellen, darf die gesamte neue und vorhandene vCPU-Nutzung für diese VM-Serie das genehmigte vCPU-Kontingent für diese bestimmte VM-Serie nicht überschreiten. Darüber hinaus sollte die Gesamtzahl der neuen und vorhandenen vCPUs, die über alle VM-Serien hinweg bereitgestellt werden, das genehmigte regionale vCPU-Gesamtkontingent für das Abonnement nicht überschreiten. Wenn eines dieser Kontingente überschritten wird, ist die VM-Bereitstellung unzulässig.

Über das Azure-Portal können Sie eine Erhöhung der vCPU-Kontingentgrenze für die VM-Serie anfordern. Bei einer Erhöhung des Kontingents für die VM-Serie erhöht sich automatisch auch der Grenzwert „Regionale vCPUs gesamt“ um den gleichen Wert.

Wenn Sie ein neues Abonnement erstellen, entspricht der Standardwert für „Regionale vCPUs gesamt“ möglicherweise nicht der Gesamtsumme der vCPU-Standardkontingente für alle einzelnen VM-Serien. Diese Abweichung kann zu einem Abonnement mit ausreichendem Kontingent für jede einzelne VM-Serie führen, die Sie bereitstellen möchten. Möglicherweise ist jedoch nicht genügend Kontingent vorhanden, um die Gesamtzahl der regionalen vCPUs für alle Bereitstellungen zu unterstützen. In diesem Fall müssen Sie eine Anforderung zur expliziten Erhöhung des Grenzwerts für die Gesamtzahl der regionalen vCPUs übermitteln. Der Grenzwert „Regionale vCPUs gesamt“ darf die Gesamtsumme des genehmigten Kontingents für alle VM-Serien der Region nicht überschreiten.

Weitere Informationen zu vCPU-Standardkontingenten finden Sie unter [vCPU-Kontingente für virtuelle Computer](../../virtual-machines/windows/quotas.md) und [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und Dienste](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Weitere Informationen zum Erhöhen der vCPU-Grenzwerte für Spot-VMs finden Sie unter [Spot-Kontingent: Erhöhen der Grenzwerte für alle VM-Serien](low-priority-quota.md).

Sie können auf zwei Arten eine Erhöhung der Grenzwerte des vCPU-Standardkontingents nach Region anfordern.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Anfordern einer Kontingenterhöhung nach Region über „Hilfe und Support“

So fordern Sie eine vCPU-Kontingenterhöhung nach Region über **Hilfe und Support** an

1. Wählen Sie auf dem Menü des [Azure-Portals](https://portal.azure.com) die Option **Hilfe und Support** aus.

   ![Der Link „Hilfe und Support“](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Wählen Sie unter **Hilfe und Support** die Option **Neue Supportanfrage** aus.

    ![Neue Supportanfrage](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.

   ![Auswählen eines Problemtyps](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Wählen Sie für **Abonnement** das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Auswählen eines Abonnements](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Wählen Sie für **Kontingenttyp** die Option **Andere Anforderungen** aus.

   ![Kontingenttyp auswählen](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Geben Sie unter **Beschreibung** die folgenden Informationen ein:

    1. **Bereitstellungsmodell**: Geben Sie **Resource Manager** an.  
    1. **Angeforderte Region**: Geben Sie die gewünschte Region an (z. B. **USA, Osten 2**).  
    1. **Neuer Grenzwert**: Geben Sie einen neuen vCPU-Grenzwert für die Region an. Dieser Wert sollte die Summe der genehmigten Kontingente für die einzelnen SKU-Serien für dieses Abonnement nicht überschreiten.

    ![Details für die Kontingentanforderung eingeben](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wählen Sie **Bewerten + erstellen** aus, um die Erstellung der Supportanfrage fortzusetzen.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Anfordern einer Kontingenterhöhung nach Region über „Abonnements“

So fordern Sie eine vCPU-Kontingenterhöhung nach Region über **Abonnements** an

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   ![Im Azure-Portal zu Abonnements navigieren](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wählen Sie das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Abonnement auswählen, das Sie ändern möchten](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Wählen Sie im linken Bereich **Nutzung + Kontingente** aus.

   ![Link „Nutzung + Kontingente“ folgen](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Wählen Sie oben rechts **Erhöhung anfordern** aus.

   ![Auswählen, um das Kontingent zu erhöhen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Wählen Sie für **Kontingenttyp** die Option **Andere Anforderungen** aus.

   ![Kontingenttyp auswählen](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Geben Sie im Feld **Beschreibung** die folgenden zusätzlichen Informationen ein:

    1. **Bereitstellungsmodell**: Geben Sie **Resource Manager** an.  
    1. **Angeforderte Region**: Geben Sie die gewünschte Region an (z. B. **USA, Osten 2**).  
    1. **Neuer Grenzwert**: Geben Sie einen neuen vCPU-Grenzwert für die Region an. Dieser Wert sollte die Summe der genehmigten Kontingente für die einzelnen SKU-Serien für dieses Abonnement nicht überschreiten.

    ![Details eingeben](./media/resource-manager-core-quotas-request/regional-details.png)

1. Wählen Sie **Bewerten + erstellen** aus, um die Erstellung der Supportanfrage fortzusetzen.
