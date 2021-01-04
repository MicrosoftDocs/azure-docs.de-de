---
title: Aktivieren des Features „Änderungsnachverfolgung und Bestand“ von Azure Automation über ein Automation-Konto
description: In diesem Artikel erfahren Sie, wie Sie „Änderungsnachverfolgung und Bestand“ über ein Automation-Konto aktivieren.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 50188ad5fea0ee34a6896f0045e3bbcbfb553aaa
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677303"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Aktivieren von Änderungsnachverfolgung und Bestand über ein Automation-Konto

In diesem Artikel erfahren Sie, wie Sie [Änderungsnachverfolgung und Bestand](overview.md) mithilfe eines Automation-Kontos für virtuelle Computer in Ihrer Umgebung aktivieren. Wenn Sie virtuelle Azure-Computer im großen Stil aktivieren möchten, müssen Sie einen vorhandenen virtuellen Computer mithilfe von „Änderungsnachverfolgung und Bestand“ aktivieren.

> [!NOTE]
> Wenn Sie „Änderungsnachverfolgung und Bestand“ aktivieren, werden nur bestimmte Regionen für die Verknüpfung eines Log Analytics-Arbeitsbereichs und eines Automation-Kontos unterstützt. Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](../index.yml) zum Verwalten von Computern.
* Ein [virtueller Computer](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="enable-change-tracking-and-inventory"></a>Aktivieren der Lösung für Änderungsnachverfolgung und Bestand

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Konfigurationsverwaltung** eine der Optionen **Bestand** oder **Änderungsnachverfolgung** aus.

2. Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und klicken Sie auf **Aktivieren** , um „Änderungsnachverfolgung und Bestand“ zu aktivieren. Die Einrichtung dauert bis zu 15 Minuten.

    ![Aktivieren der Lösung für Änderungsnachverfolgung und Bestand](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Aktivieren von Azure-VMs

1. Wählen Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** die Option **Bestand** oder **Änderungsnachverfolgung** aus.

2. Klicken Sie auf **+ Azure-VMs hinzufügen** , und wählen Sie mindestens einen virtuellen Computer aus der Liste aus. Virtuelle Computer, die nicht aktiviert werden können, sind abgeblendet und können nicht ausgewählt werden. Virtuelle Azure-Computer können sich unabhängig vom Standort Ihres Automation-Kontos in einer beliebigen Region befinden. 

3. Klicken Sie auf **Aktivieren** , um die ausgewählten virtuellen Computer der gespeicherten Computergruppensuche für das Feature hinzuzufügen. Weitere Informationen finden Sie unter [Einschränken des Bereitstellungsumfangs für „Änderungsnachverfolgung und Bestand“](manage-scope-configurations.md).

      [ ![Aktivieren von Azure-VMs](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Aktivieren Azure-fremder virtueller Computer

Computer, die nicht in Azure enthalten sind, müssen manuell hinzugefügt werden. Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtlinie „ [Log Analytics-Agent auf Azure Arc-Computern unter *Linux* oder *Windows* bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring)“ zuzuweisen. Wenn Sie auch die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Initiative [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

1. Wählen Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** die Option **Bestand** oder **Änderungsnachverfolgung** aus.

2. Klicken Sie auf **Nicht-Azure-Computer hinzufügen** . Daraufhin wird ein neues Browserfenster mit [Anweisungen zum Installieren und Konfigurieren des Log Analytics-Agents für Windows](../../azure-monitor/platform/log-analytics-agent.md) geöffnet, sodass der Computer damit beginnen kann, Vorgänge für „Änderungsnachverfolgung und Bestand“ zu melden. Wenn Sie einen Computer aktivieren, der aktuell von Operations Manager verwaltet wird, ist kein neuer Agent erforderlich. Die Arbeitsbereichsinformationen werden in den vorhandenen Agent eingegeben.

## <a name="enable-machines-in-the-workspace"></a>Aktivieren von Computern im Arbeitsbereich

Manuell installierte Computer oder Computer, von denen bereits Berichte für Ihren Arbeitsbereich erstellt werden, müssen Azure Automation hinzugefügt werden, damit „Änderungsnachverfolgung und Bestand“ aktiviert werden kann.

1. Wählen Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** die Option **Bestand** oder **Änderungsnachverfolgung** aus.

2. Wählen Sie **Computer verwalten** aus. Die Option **Computer verwalten** ist möglicherweise abgeblendet, wenn Sie zuvor die Option **Auf allen verfügbaren und zukünftigen Computern aktivieren** ausgewählt haben.

    ![Gespeicherte Suchvorgänge](media/enable-from-automation-account/manage-machines.png)

3. Wenn Sie „Änderungsnachverfolgung und Bestand“ für alle verfügbaren Computer aktivieren möchten, wählen Sie auf der Seite **Computer verwalten** die Option **Auf allen verfügbaren Computern aktivieren** aus. Mit dieser Aktion wird das Steuerelement zum einzelnen Hinzufügen von Computern deaktiviert, und alle Computer werden hinzugefügt, die Berichte für den Arbeitsbereich und die Computergruppe der gespeicherten Suchabfrage erstellen. Wenn diese Aktion ausgewählt ist, deaktiviert sie die Option **Computer verwalten** .

4. Wenn Sie das Feature für alle verfügbaren und zukünftigen Computer aktivieren möchten, wählen Sie **Auf allen verfügbaren und zukünftigen Computern aktivieren** aus. Mit dieser Option werden die gespeicherte Suche und die Bereichskonfiguration aus dem Arbeitsbereich gelöscht, und das Feature wird für alle Azure-Computer und Azure-fremden Computer geöffnet, von denen Berichte an den Arbeitsbereich übermittelt werden. Bei Verwendung dieser Aktion wird die Option **Computer verwalten** dauerhaft deaktiviert, da keine Bereichskonfiguration mehr vorhanden ist.

    > [!NOTE]
    > Da diese Option die gespeicherte Suche und Bereichskonfiguration in Log Analytics löscht, müssen Sie vor ihrer Aktivierung unbedingt alle Löschsperren im Log Analytics-Arbeitsbereich entfernen. Andernfalls können die Konfigurationen nicht durch die Option entfernt werden, und Sie müssen sie manuell entfernen.

5. Die Bereichskonfiguration kann bei Bedarf durch erneutes Hinzufügen der anfänglich gespeicherten Suche wieder hinzugefügt werden. Weitere Informationen finden Sie unter [Einschränken des Bereitstellungsumfangs für „Änderungsnachverfolgung und Bestand“](manage-scope-configurations.md).

6. Wenn Sie das Feature für einen oder mehrere Computer aktivieren möchten, wählen Sie **Auf ausgewählten Computern aktivieren** aus, und klicken Sie neben jedem Computer, für den das Feature aktiviert werden soll, auf **Hinzufügen** . Dadurch werden die Namen der ausgewählten Computer der gespeicherten Computergruppensuchabfrage für das Feature hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zur Verwendung des Features finden Sie unter [Verwalten der Änderungsnachverfolgung und des Bestands](manage-change-tracking.md) und [Verwalten der Bestandserfassung von virtuellen Computern](manage-inventory-vms.md).

* Informationen zur Behandlung allgemeiner Probleme mit dem Feature finden Sie unter [Behandeln von Problemen mit Änderungsnachverfolgung und Bestand](../troubleshoot/change-tracking.md).
