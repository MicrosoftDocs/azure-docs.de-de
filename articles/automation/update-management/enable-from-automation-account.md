---
title: Aktivieren der Azure Automation-Updateverwaltung über ein Automation-Konto
description: In diesem Artikel erfahren Sie, wie Sie die Updateverwaltung über ein Automation-Konto aktivieren.
services: automation
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: b97e1e61401697204f79004e4678e6f2286f4a98
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380537"
---
# <a name="enable-update-management-from-an-automation-account"></a>Aktivieren der Updateverwaltung über ein Automation-Konto

In diesem Artikel wird beschrieben, wie Sie mit Ihrem Automation-Konto das Feature [Updateverwaltung](overview.md) für VMs in Ihrer Umgebung aktivieren können. Dies schließt auch Computer oder Server ein, die als [Azure Arc-fähige Server](../../azure-arc/servers/overview.md) registriert sind. Sie müssen eine vorhandene Azure-VM über die Updateverwaltung aktivieren, um Azure-VMs im großen Stil zu aktivieren.

> [!NOTE]
> Wenn Sie die Updateverwaltung aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt. Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](../index.yml) zum Verwalten von Computern.
* Ein [virtueller Azure-Computer](../../virtual-machines/windows/quick-create-portal.md) oder eine VM bzw. ein Server, die oder der bei Azure Arc-fähigen Servern registriert ist. Für Nicht-Azure-VMs oder -Server muss der [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md) für Windows oder Linux installiert sein und an den Arbeitsbereich, der mit dem Automation-Konto mit aktivierter Updateverwaltung verknüpft ist, Meldungen übermitteln. Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann mit Azure Policy die integrierte Richtlinie [Log Analytics-Agent für *Linux*-/*Windows*-Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuweisen. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

1. Wählen Sie in Ihrem Automation-Konto unter **Updateverwaltung** die Option **Updateverwaltung** aus.

2. Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und wählen Sie dann **Aktivieren** aus, um die Updateverwaltung zu aktivieren. Die Einrichtung dauert bis zu 15 Minuten.

    ![Aktivieren der Updateverwaltung](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Aktivieren von Azure-VMs

1. Wählen Sie in Ihrem Automation-Konto unter **Updateverwaltung** die Option **Updateverwaltung** aus.

2. Wählen Sie **+ Azure-VMs hinzufügen** aus, und wählen Sie mindestens einen virtuellen Computer aus der Liste aus. Virtuelle Computer, die nicht aktiviert werden können, sind abgeblendet und können nicht ausgewählt werden. Virtuelle Azure-Computer können sich unabhängig vom Standort Ihres Automation-Kontos in einer beliebigen Region befinden.

3. Wählen Sie **Aktivieren** aus, um die ausgewählten virtuellen Computer der gespeicherten Computergruppensuche für das Feature hinzuzufügen.

    ![Aktivieren von Azure-VMs](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Aktivieren Azure-fremder virtueller Computer

Wenn Sie Computer oder Server verwenden, die außerhalb von Azure gehostet werden – einschließlich solcher, die bei Azure Arc-fähigen Servern registriert sind –, führen Sie die folgenden Schritte aus, um sie für die Updateverwaltung zu aktivieren.  

1. Wählen Sie in Ihrem Automation-Konto unter **Updateverwaltung** die Option **Updateverwaltung** aus.

2. Wählen Sie **Nicht-Azure-Computer hinzufügen** aus. Diese Aktion öffnet ein neues Browserfenster mit [Anweisungen zum Installieren und Konfigurieren des Log Analytics-Agents für Windows](../../azure-monitor/platform/log-analytics-agent.md), sodass der Computer mit dem Erstellen von Berichten für die Updateverwaltung beginnen kann. Wenn Sie einen Computer aktivieren, der aktuell von Operations Manager verwaltet wird, ist kein neuer Agent erforderlich. Die Arbeitsbereichsinformationen werden der Konfiguration des Agents hinzugefügt.

## <a name="enable-machines-in-the-workspace"></a>Aktivieren von Computern im Arbeitsbereich

Manuell installierte Computer oder Computer, die bereits Berichte für Ihren Arbeitsbereich erstellen, müssen Azure Automation hinzugefügt werden, damit die Updateverwaltung aktiviert wird.

1. Wählen Sie in Ihrem Automation-Konto unter **Updateverwaltung** die Option **Updateverwaltung** aus.

2. Wählen Sie **Computer verwalten** aus. Die Schaltfläche **Computer verwalten** ist möglicherweise abgeblendet, wenn Sie zuvor die Option **Auf allen verfügbaren und zukünftigen Computern aktivieren** ausgewählt haben.

    ![Gespeicherte Suchvorgänge](media/enable-from-automation-account/managemachines.png)

3. Wenn Sie die Updateverwaltung für alle verfügbaren Computer aktivieren möchten, die Meldungen an den Arbeitsbereich übermitteln, wählen Sie auf der Seite „Computer verwalten“ die Option **Auf allen verfügbaren Computern aktivieren** aus. Mit dieser Aktion wird das Steuerelement zum einzelnen Hinzufügen von Computern deaktiviert, und alle Computer werden hinzugefügt, die Berichte für den Arbeitsbereich und die Computergruppe der gespeicherten Suchabfrage `MicrosoftDefaultComputerGroup` erstellen. Wenn diese Aktion ausgewählt ist, deaktiviert sie die Option **Computer verwalten**.

4. Wenn Sie das Feature für alle verfügbaren und zukünftigen Computer aktivieren möchten, wählen Sie **Auf allen verfügbaren und zukünftigen Computern aktivieren** aus. Mit dieser Option werden die gespeicherten Suchen und die Bereichskonfiguration aus dem Arbeitsbereich gelöscht. Außerdem erhält das Feature damit die Möglichkeit, alle Azure-Computer und Azure-fremden Computer hinzuzufügen, die derzeit oder zukünftig Meldungen an den Arbeitsbereich übermitteln. Bei Verwendung dieser Aktion wird die Option **Computer verwalten** dauerhaft deaktiviert, da keine Bereichskonfiguration verfügbar ist.

    > [!NOTE]
    > Da diese Option die gespeicherte Suche und Bereichskonfiguration in Log Analytics löscht, müssen Sie vor ihrer Aktivierung unbedingt alle Löschsperren im Log Analytics-Arbeitsbereich entfernen. Andernfalls können die Konfigurationen nicht durch die Option entfernt werden, und Sie müssen sie manuell entfernen.

5. Die Bereichskonfiguration kann bei Bedarf durch erneutes Hinzufügen der anfänglich gespeicherten Suchabfrage wieder hinzugefügt werden. Weitere Informationen finden Sie unter [Einschränken des Bereitstellungsbereichs für die Updateverwaltung](scope-configuration.md).

6. Wenn Sie die Funktion für einen oder mehrere Computer aktivieren möchten, wählen Sie **Auf ausgewählten Computern aktivieren** aus, und wählen Sie neben jedem Computer **Hinzufügen** aus. Dadurch werden die Namen der ausgewählten Computer der gespeicherten Computergruppensuchabfrage für das Feature hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* Wie Sie die Updateverwaltung für VMs verwenden, erfahren Sie unter [Verwalten von Updates und Patches für Ihre VMs](manage-updates-for-vm.md).

* Wenn Sie virtuelle Computer oder Server nicht mehr mit der Updateverwaltung verwalten möchten, finden Sie weitere Informationen unter [Entfernen virtueller Computer aus der Updateverwaltung](remove-vms.md).

* Informationen zum Behandeln von Fehlern bei der Updateverwaltung finden Sie unter [Behandeln von Problemen mit der Updateverwaltung](../troubleshoot/update-management.md).
