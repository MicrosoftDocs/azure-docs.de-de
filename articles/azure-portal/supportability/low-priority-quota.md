---
title: Kontingent für virtuelle Spotcomputer
description: Erhöhen Sie die Kontingentgrenzwerte für Spot-VMs, die ein Modell der Azure-Nutzung bereitstellen, das zu niedrigeren Kosten führt, weil Azure bei Bedarf VMs entfernen kann.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745484"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Spot-Kontingent: Erhöhen der Grenzwerte für alle VM-Serien

Spot-VMs bieten ein anderes Modell der Azure-Nutzung. Sie können zu niedrigeren Kosten führen. Dafür entfernt Azure jedoch nach Bedarf virtuelle Computer für Bereitstellungen mit nutzungsbasierter Bezahlung oder reservierten VM-Instanzen. Weitere Informationen zu Spot-VMs finden Sie unter [Azure Spot-VMs für Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager unterstützt zwei Arten von vCPU-Kontingenten für virtuelle Computer:

* *VMs mit nutzungsbasierter Bezahlung* und *reservierte VM-Instanzen* verwenden *vCPU-Standardkontingente*.
* *Spot-VMs* unterliegen einem *Spot-vCPU-Kontingent*.

Beim Spot-Kontingenttyp werden vCPU-Kontingente von Resource Manager für alle verfügbaren VM-Serien als ein einzelner regionaler Grenzwert erzwungen.

Wenn Sie eine neue Spot-VM bereitstellen, darf die gesamte neue und vorhandene vCPU-Nutzung für alle Spot-VM-Instanzen die genehmigte Spot-vCPU-Kontingentgrenze nicht überschreiten. Wenn das Spot-Kontingent überschritten wird, ist die Bereitstellung der Spot-VM unzulässig.

In diesem Artikel wird erläutert, wie Sie mithilfe des Azure-Portals eine Erhöhung des vCPU-Kontingentgrenzwerts anfordern.

Weitere Informationen zu vCPU-Standardkontingenten finden Sie unter [vCPU-Kontingente für virtuelle Computer](../../virtual-machines/windows/quotas.md) und [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und Dienste](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Weitere Informationen zum Erhöhen des vCPU-Grenzwerts nach Region finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach Region](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Anfordern einer Erhöhung des Kontingentgrenzwerts über „Hilfe und Support“

So fordern Sie eine Erhöhung des Spotkontingentgrenzwerts für alle VM-Serien über **Hilfe und Support** an:

> [!NOTE]
> Sie können auch eine Erhöhung der Kontingentgrenzwerte für mehrere Regionen über eine einzige Supportanfrage anfordern. Einzelheiten dazu finden Sie in Schritt 8.

1. Wählen Sie auf dem Menü des [Azure-Portals](https://portal.azure.com) die Option **Hilfe und Support** aus.

   ![Der Link „Hilfe und Support“](./media/resource-manager-core-quotas-request/help-plus-support.png)

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

1. Führen Sie unter **Kontingentdetails** folgende Schritte aus:

   1. Wählen Sie für **Bereitstellungsmodell** das entsprechende Modell und für **Standorte** einen Standort aus.

      ![Zusätzliche Kontingentdetails angeben](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Wählen Sie für den ausgewählten Standort unter **Typen** im Feld **Typ auswählen** die Option **Spot** aus.

      ![Spottyp auswählen](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Sie können sowohl Standardkontingenttypen als auch Spot-Kontingenttypen über eine einzige Supportanfrage anfordern, indem Sie die Mehrfachauswahl unter **Typen** verwenden.

       Weitere Informationen finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach VM-Serie](per-vm-quota-requests.md) beschrieben.

   1. Geben Sie den neuen gewünschten Kontingentgrenzwert für dieses Abonnement ein.

      ![Ein neues Kontingent für Spot-VM auswählen](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Um eine Kontingenterhöhung für mehrere Standorte anzufordern, wählen Sie unter **Standorte** einen weiteren Standort und dann einen geeigneten VM-Typ aus. Sie können dann einen Grenzwert eingeben, der für den zusätzlichen Standort gilt.

   ![Angeben von zusätzlichen Standorten unter „Kontingentdetails“](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wählen Sie **Speichern und fortfahren** aus, um die Erstellung der Supportanfrage fortzusetzen.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Anfordern einer Erhöhung des Kontingentgrenzwerts über den Bereich „Abonnements“

Gehen Sie folgendermaßen vor, um eine Erhöhung des Spot-Kontingentgrenzwerts für alle VM-Serien über den Bereich **Abonnements** anzufordern:

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

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Wählen Sie **Problemdetails** aus, um zusätzliche Informationen einzugeben. Geben Sie in **Kontingentdetails** folgende Informationen ein:

   1. Wählen Sie für **Bereitstellungsmodell** das entsprechende Modell und für **Standorte** einen Standort aus.

      ![Kontingentdetails angeben](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Wählen Sie für den ausgewählten Standort unter **Typen** im Feld **Typ auswählen** die Option **Spot** aus.

      ![Spottyp auswählen](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Weitere Informationen finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach VM-Serie](per-vm-quota-requests.md) beschrieben.

   1. Geben Sie den neuen gewünschten Kontingentgrenzwert für dieses Abonnement ein.

      ![Einen neuen Wert für den vCPU-Grenzwert eingeben](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Um eine Kontingenterhöhung für mehrere Standorte anzufordern, wählen Sie unter **Standorte** einen weiteren Standort und dann einen geeigneten VM-Typ aus. Sie können dann einen Grenzwert eingeben, der für den zusätzlichen Standort gilt.

   ![Zusätzliche Standorte im Bereich „Kontingentdetails“ auswählen](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Wählen Sie **Speichern und fortfahren** aus, um die Erstellung der Supportanfrage fortzusetzen.
