---
title: Aktivieren der Azure Automation-Updateverwaltung über das Azure-Portal
description: In diesem Artikel erfahren Sie, wie Sie die Updateverwaltung über das Azure-Portal aktivieren.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: ac402587c8346c14f34b683ba10832c7779b660b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182766"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Aktivieren der Updateverwaltung über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie die Funktion [Updateverwaltung](overview.md) für virtuelle Computer über das Azure-Portal aktivieren können. Sie müssen eine vorhandene Azure-VM über die Updateverwaltung aktivieren, um Azure-VMs im großen Stil zu aktivieren.

Die Anzahl von Ressourcengruppen, die für die Verwaltung Ihrer virtuellen Computer verwendet werden können, ist durch [Resource Manager-Bereitstellungsgrenzwerte](../../azure-resource-manager/templates/deploy-to-resource-group.md) eingeschränkt. Resource Manager-Bereitstellungen (nicht zu verwechseln mit Updatebereitstellungen) sind auf fünf Ressourcengruppen pro Bereitstellung beschränkt. Zwei dieser Ressourcengruppen sind für die Konfiguration des Log Analytics-Arbeitsbereichs, des Automation-Kontos und der zugehörigen Ressourcen reserviert. Somit können noch drei Ressourcengruppen für die Verwaltung durch die Updateverwaltung ausgewählt werden. Dieser Grenzwert gilt nur für die gleichzeitige Einrichtung, nicht für die Anzahl von Ressourcengruppen, die durch ein Automation-Feature verwaltet werden können.

> [!NOTE]
> Wenn Sie die Updateverwaltung aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt. Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](../index.yml) zum Verwalten von Computern.
* Ein [virtueller Computer](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer**.

2. Wählen Sie auf der Seite **Virtuelle Computer** mithilfe der Kontrollkästchen die VMs aus, die der Updateverwaltung hinzugefügt werden sollen. Es können gleichzeitig Computer für bis zu drei verschiedene Ressourcengruppen hinzugefügt werden. Virtuelle Azure-Computer können sich unabhängig vom Standort Ihres Automation-Kontos in einer beliebigen Region befinden.

    ![Liste der VMs](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Verwenden Sie die Filtersteuerelemente, um virtuelle Computer verschiedener Abonnements, Standorte und Ressourcengruppen auszuwählen. Durch Klicken auf das obere Kontrollkästchen können Sie alle virtuellen Computer in einer Liste auswählen.

    [ ![Aktivieren der Updateverwaltung](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Wählen Sie **Dienste** und dann **Updateverwaltung** aus, um das Feature „Updateverwaltung“ aufzurufen.

4. Die Liste der virtuellen Computer wird gefiltert, um nur die virtuellen Computer anzuzeigen, die sich in demselben Abonnement und Speicherort befinden. Wenn sich Ihr virtueller Computer in mehr als drei Ressourcengruppen befindet, werden die ersten drei Ressourcengruppen ausgewählt.

5. Standardmäßig sind ein vorhandener Log Analytics-Arbeitsbereich und ein neues Automation-Konto ausgewählt. Wenn Sie einen anderen Log Analytics-Arbeitsbereich und ein anderes Automation-Konto verwenden möchten, wählen Sie **BENUTZERDEFINIERT** aus, um sie auf der Seite für die benutzerdefinierte Konfiguration auszuwählen. Wenn Sie einen Log Analytics-Arbeitsbereich auswählen, wird überprüft, ob eine Verknüpfung mit einem Automation-Konto besteht. Sollte ein verknüpftes Automation-Konto gefunden werden, wird der folgende Bildschirm angezeigt. Wählen Sie dann die Option **OK**.

    [ ![Auswählen von Arbeitsbereich und Konto](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Wenn der ausgewählte Arbeitsbereich nicht mit einem Automation-Konto verknüpft ist, wird der folgende Bildschirm angezeigt. Wählen Sie ein Automation-Konto und anschließend **OK** aus.

    ![Kein Arbeitsbereich](media/enable-from-portal/no-workspace.png)

7. Deaktivieren Sie jeden virtuellen Computer, den Sie nicht aktivieren möchten. Die Auswahl für virtuelle Computer, die nicht aktiviert werden können, ist bereits aufgehoben.

8. Wählen Sie **Aktivieren** aus, um das Feature zu aktivieren. Nach dem Aktivieren der Updateverwaltung kann es ca. 15 Minuten dauern, bis Sie die Updatebewertung dort anzeigen können.

## <a name="next-steps"></a>Nächste Schritte

* Wie Sie die Updateverwaltung für VMs verwenden, erfahren Sie unter [Verwalten von Updates und Patches für Ihre VMs](manage-updates-for-vm.md).
* Informationen zum Behandeln von Fehlern bei der Updateverwaltung finden Sie unter [Behandeln von Problemen mit der Updateverwaltung](../troubleshoot/update-management.md).
* Informationen zum Behandeln von Problemen mit dem Windows Update-Agent finden Sie unter [Beheben von Problemen mit dem Windows Update-Agent](../troubleshoot/update-agent-issues.md).
* Informationen zum Behandeln von Problemen mit dem Linux-Update-Agent finden Sie unter [Beheben von Problemen mit dem Linux-Update-Agent](../troubleshoot/update-agent-issues-linux.md).