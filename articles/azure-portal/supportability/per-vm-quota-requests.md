---
title: Anfordern einer Erhöhung des vCPU-Kontingentgrenzwerts pro Azure-VM-Serie
description: Es wird beschrieben, wie Sie eine Erhöhung des vCPU-Kontingentgrenzwerts für eine VM-Serie im Azure-Portal anfordern, wobei der vCPU-Grenzwert für die gesamte Region um denselben Wert erhöht wird.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745433"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardkontingent: Erhöhen der Grenzwerte nach VM-Serie

Azure Resource Manager unterstützt zwei Arten von vCPU-Kontingenten für virtuelle Computer:

* *VMs mit nutzungsbasierter Bezahlung* und *reservierte VM-Instanzen* verwenden *vCPU-Standardkontingente*.
* *Spot-VMs* unterliegen einem *Spot-vCPU-Kontingent*.

Das vCPU-Standardkontingent für nutzungsbasierte Bezahlung und reservierte VM-Instanzen wird für jedes Abonnement in jeder Region auf zwei Ebenen erzwungen:

* Die erste Ebene ist der *Grenzwert regionaler vCPUs gesamt* (für alle VM-Serien).
* Die zweite Ebene ist der *vCPU-Grenzwert pro VM-Serie*, z. B. vCPUs der Dv3-Serie.

Wenn Sie eine neue Spot-VM bereitstellen, darf die gesamte neue und vorhandene vCPU-Nutzung für alle Spot-VM-Instanzen die genehmigte Spot-vCPU-Kontingentgrenze nicht überschreiten. Wenn das Spot-Kontingent überschritten wird, ist die Bereitstellung der Spot-VM unzulässig.

Über das Azure-Portal können Sie eine Erhöhung der vCPU-Kontingentgrenze für die VM-Serie anfordern. Bei einer Erhöhung des Kontingents für die VM-Serie erhöht sich automatisch auch der Grenzwert „Regionale vCPUs gesamt“ um den gleichen Wert.

Weitere Informationen zu vCPU-Standardkontingenten finden Sie unter [vCPU-Kontingente für virtuelle Computer](../../virtual-machines/windows/quotas.md) und [Einschränkungen für Azure-Abonnements und Dienste](./classic-deployment-model-quota-increase-requests.md).

Weitere Informationen zum Erhöhen des vCPU-Grenzwerts nach Region für das Standardkontingent finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach Region](regional-quota-requests.md).

Weitere Informationen zum Erhöhen der vCPU-Grenzwerte für Spot-VMs finden Sie unter [Spot-Kontingent: Erhöhen der Grenzwerte für alle VM-Serien](low-priority-quota.md).

Sie können auf zwei Arten eine Erhöhung der Grenzwerte des vCPU-Standardkontingents pro VM-Serie anfordern. Dies ist in den folgenden Abschnitten beschrieben.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Anfordern einer Erhöhung des Standardkontingents über „Hilfe und Support“

Gehen Sie wie folgt vor, um über **Hilfe und Support** eine Erhöhung des vCPU-Standardkontingents pro VM-Serie anzufordern:

> [!NOTE]
> Sie können auch eine Erhöhung der Kontingentgrenzwerte für mehrere Regionen über eine einzige Supportanfrage anfordern. Einzelheiten dazu finden Sie in Schritt 8.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü die Option **Hilfe und Support** aus.

   ![Link „Hilfe und Support“](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Wählen Sie unter **Hilfe und Support** die Option **Neue Supportanfrage** aus.

    ![Erstellen einer neuen Supportanfrage](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.

   ![Auswählen eines Problemtyps](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Wählen Sie unter **Abonnement** das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Auswählen eines Abonnements für eine Kontingenterhöhung](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Wählen Sie unter **Kontingenttyp** die Option **Abonnementlimit für Compute/VM (Kerne/vCPUs) erhöhen** aus.

   ![Auswählen eines Kontingenttyps](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Wählen Sie **Details angeben** aus, um zusätzliche Informationen einzugeben.

   ![Der Link „Details angeben“](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Führen Sie unter **Kontingentdetails** die folgenden Schritte aus:

   ![Angeben von zusätzlichen Kontingentdetails](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Wählen Sie unter **Bereitstellungsmodell** das gewünschte Modell aus.

   1. Wählen Sie unter **Standorte** einen Standort aus. Wählen Sie für den ausgewählten Standort unter **Typen** im Feld **Typ auswählen** die Option **Standard** aus.

      ![Kontingentdetails: Kontingenttypen](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Sie können sowohl Standardkontingenttypen als auch Spot-Kontingenttypen über eine einzige Supportanfrage anfordern, indem Sie die Mehrfachauswahl unter **Typen** verwenden.

      Weitere Informationen zum Erhöhen des Spot-Kontingentgrenzwerts finden Sie unter [Azure Spot-VMs für Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

   1. Wählen Sie unter **Standard** die SKU-Serie für erhöhte Kontingente aus.

      ![Kontingentdetails: SKU-Serie](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Geben Sie die neuen gewünschten Kontingentgrenzwerte für dieses Abonnement ein. Um eine SKU aus der Liste zu entfernen, deaktivieren Sie das Kontrollkästchen neben der SKU, oder wählen Sie das Löschsymbol „X“ aus.

      ![Auswählen eines neuen vCPU-Grenzwerts](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Um eine Kontingenterhöhung für mehrere Standorte anzufordern, wählen Sie unter **Standorte** einen weiteren Standort und dann einen geeigneten VM-Typ aus. Sie können dann einen Grenzwert eingeben, der für den zusätzlichen Standort gilt.

   ![Angeben von zusätzlichen Standorten unter „Kontingentdetails“](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wählen Sie **Speichern und fortfahren** aus, um die Erstellung der Supportanfrage fortzusetzen.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Anfordern einer Erhöhung des Standardkontingents über „Abonnements“

Gehen Sie wie folgt vor, um über **Abonnements** eine Erhöhung des vCPU-Standardkontingents pro VM-Serie anzufordern:

> [!NOTE]
> Sie können auch eine Erhöhung der Kontingentgrenzwerte für mehrere Regionen über eine einzige Supportanfrage anfordern. Einzelheiten dazu finden Sie in Schritt 7.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   ![Suchen nach Abonnements im Azure-Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wählen Sie das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Abonnements zur Auswahl für Änderungen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Wählen Sie im linken Bereich **Nutzung + Kontingente** aus.

   ![Der Link „Nutzung und Kontingente“](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Wählen Sie oben rechts **Erhöhung anfordern** aus.

   ![Auswählen, um das Kontingent zu erhöhen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Wählen Sie unter **Kontingenttyp** die Option **Abonnementlimit für Compute/VM (Kerne/vCPUs) erhöhen** aus.

   ![Auswählen eines Kontingenttyps](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Führen Sie unter **Kontingentdetails** die folgenden Schritte aus:

   1. Wählen Sie unter **Bereitstellungsmodell** das entsprechende Modell und unter **Standorte** einen Standort aus.

      ![Angeben von Kontingentdetails](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Wählen Sie für den ausgewählten Standort unter **Typen** die Option **Typ auswählen** und dann **Standard** aus.

      ![Auswählen des Typs „Standard“](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Sie können sowohl Standardkontingenttypen als auch Spot-Kontingenttypen über eine einzige Supportanfrage anfordern, indem Sie die Mehrfachauswahl unter **Typen** verwenden.

      Weitere Informationen zum Erhöhen des Spot-Kontingentgrenzwerts finden Sie unter [Azure Spot-VMs für Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

   1. Wählen Sie unter **Standard** die SKU-Serie aus, deren Kontingente erhöht werden sollen.

      ![Kontingentdetails: SKU-Serie](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Geben Sie die neuen gewünschten Kontingentgrenzwerte für dieses Abonnement ein. Um eine SKU aus der Liste zu entfernen, deaktivieren Sie das Kontrollkästchen neben der SKU, oder wählen Sie das Löschsymbol „X“ aus.

      ![Auswählen eines neuen vCPU-Grenzwerts](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Um eine Kontingenterhöhung für mehrere Standorte anzufordern, wählen Sie unter **Standorte** einen weiteren Standort und dann einen geeigneten VM-Typ aus.

   In diesem Schritt wird die SKU-Serie vorab geladen, die Sie für frühere Standorte ausgewählt haben. Geben Sie die Kontingentgrenzwerte ein, die Sie auf die zusätzliche Serie anwenden möchten.

   ![Auswählen von zusätzlichen Standorten unter „Kontingentdetails“](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wählen Sie **Speichern und fortfahren** aus, um die Erstellung der Supportanfrage fortzusetzen.