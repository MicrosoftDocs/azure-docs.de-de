---
title: Klassisches Azure-Bereitstellungsmodell
description: Das klassische Bereitstellungsmodell, das nun durch das Resource Manager-Modell abgelöst wurde, erzwingt eine globale vCPU-Kontingentgrenze für virtuelle Computer und VM-Skalierungsgruppen.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745603"
---
# <a name="classic-deployment-model"></a>Klassisches Bereitstellungsmodell

Das klassische Bereitstellungsmodell ist das Azure-Bereitstellungsmodell der älteren Generation. Es setzt einen globalen Kontingentgrenzwert für vCPUs für virtuelle Computer und Skalierungsgruppen für virtuelle Computer durch. Das klassische Bereitstellungsmodell wird nicht mehr empfohlen, das Resource Manager-Modell ist heute vorzuziehen.

Weitere Informationen zu diesen beiden Bereitstellungsmodellen und den Vorteilen von Resource Manager finden Sie unter [Resource Manager- und klassische Bereitstellung](../../azure-resource-manager/management/deployment-models.md).

Sobald ein Abonnement erstellt wird, wird diesem das Standardkontingent für vCPUs zugewiesen. Bei jeder Bereitstellung eines neuen virtuellen Computers über das klassische Bereitstellungsmodell wird die neue und bereits vorhandene vCPU-Nutzung für alle Regionen addiert. Dabei darf das für das klassische Bereitstellungsmodell zur Verfügung stehende vCPU-Kontingent nicht überschritten werden.

Weitere Informationen zu diesen Kontingenten finden Sie unter [Einschränkungen für Azure-Abonnements und -Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Sie können eine Erhöhung der vCPU-Kontingentgrenze für das klassische Bereitstellungsmodell anfordern. Verwenden Sie **Hilfe und Support** oder **Nutzung + Kontingente** im Azure-Portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Anfordern einer Erhöhung des vCPU-Kontingents pro VM-Serie auf Abonnementebene mithilfe von „Hilfe und Support“

Führen Sie unten stehenden Anleitungen aus, um eine Supportanfrage zu erstellen, indem Sie **Hilfe und Support** im Azure-Portal verwenden.

1. Wählen Sie auf dem Menü des [Azure-Portals](https://portal.azure.com) die Option **Hilfe und Support** aus.

   ![Auswählen von „Hilfe und Support“ im Azure-Portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Wählen Sie **Neue Supportanfrage** aus.

   ![Erstellen einer neuen Supportanfrage im Azure-Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Wählen Sie als **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.

   ![Auswählen von Kontingenten als Problemtyp](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Wählen Sie das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Auswählen des Abonnements, für das ein Kontingent erhöht werden soll](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Wählen Sie für **Kontingenttyp** die Option **Abonnementlimit für Compute/VM (Kerne/vCPUs) erhöhen** aus.

   ![Auswählen des Kontingenttyps für die Erhöhung](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Wählen Sie **Problemdetails** aus, um zusätzliche Informationen anzugeben.

   ![Angeben von Details zur Unterstützung Ihrer Anforderung](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Wählen Sie unter **Kontingentdetails** die Option **Klassisch** und einen **Standort** aus.

   ![Hinzufügen von Details einschließlich Bereitstellungsmodell und Standort](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Wählen Sie für **SKU-Familie** mindestens eine SKU-Familie aus, die vergrößert werden soll.

   ![Angeben der zu erhöhenden SKU-Familie](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Geben Sie die gewünschten Grenzwerte für das Abonnement ein. Um eine Zeile zu entfernen, deaktivieren Sie die SKU unter **SKU-Familie**, oder wählen Sie das Symbol „X“ zum Verwerfen aus. Nachdem Sie ein Kontingent für jede SKU-Familie eingegeben haben, wählen Sie **Speichern und fortfahren** in **Kontingentdetails** aus, um mit der Supportanfrage fortzufahren.

   ![Anfordern neuer Grenzwerte](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Anfordern einer vCPU-Kontingenterhöhung pro VM-Serie auf Abonnementebene mithilfe von „Nutzung + Kontingente“

Führen Sie die unten stehenden Anleitungen aus, um eine Supportanfrage über **Nutzung + Kontingente** im Azure-Portal zu erstellen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   ![Im Azure-Portal zu Abonnements navigieren](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Wählen Sie das Abonnement aus, dessen Kontingent Sie erhöhen möchten.

   ![Auswählen des zu ändernden Abonnements](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Wählen Sie **Nutzung + Kontingente** aus.

   ![Auswählen von Nutzung und Kontingenten für ein Abonnement](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Klicken Sie in der Ecke oben rechts auf **Erhöhung anfordern**.

   ![Auswählen, um das Kontingent zu erhöhen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Wählen Sie als **Kontingenttyp** die Option **Compute-VM (cores/vCPUs) subscription limit increases** (Erhöhung der Grenzwerte für Compute-/VM-Abonnements (Kerne/vCPUs)) aus.

   ![Auswählen des Kontingenttyps](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Klicken Sie auf **Weiter: Lösungen**, um **PROBLEMDETAILS** zu öffnen. Wählen Sie **Problemdetails** aus, um zusätzliche Informationen anzugeben.

   ![Angeben von Details für Ihre Anforderung](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Wählen Sie unter **Kontingentdetails** die Option **Klassisch** und einen **Standort** aus.

   ![Auswählen von Kontingentdetails einschließlich Bereitstellungsmodell und Standort](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Wählen Sie mindestens eine SKU-Familie für die Erhöhung aus.

   ![Auswählen einer SKU-Familie für die Erhöhung](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Geben Sie die gewünschten Grenzwerte für das Abonnement ein. Um eine Zeile zu entfernen, deaktivieren Sie die SKU unter **SKU-Familie**, oder wählen Sie das Symbol „X“ zum Verwerfen aus. Nachdem Sie ein Kontingent für jede SKU-Familie eingegeben haben, wählen Sie **Speichern und fortfahren** in **Kontingentdetails** aus, um mit der Supportanfrage fortzufahren.

   ![Eingeben des neuen Kontingents](./media/resource-manager-core-quotas-request/new-limits-classic.png)

