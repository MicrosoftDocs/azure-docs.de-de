---
title: Aktivieren von Azure Monitor für einen einzelnen virtuellen Computer oder eine einzelne VM-Skalierungsgruppe im Azure-Portal
description: Hier erfahren Sie, wie Sie Azure Monitor für VMs für einen einzelnen virtuellen Azure-Computer oder eine einzelne VM-Skalierungsgruppe im Azure-Portal aktivieren.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 55e5ff2af62c903efeab5c4932eae0c9dc9b535c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842310"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Aktivieren von Azure Monitor für einen einzelnen virtuellen Computer oder eine einzelne VM-Skalierungsgruppe im Azure-Portal
In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs für einen virtuellen Computer oder eine VM-Skalierungsgruppe im Azure-Portal aktivieren. Dieses Verfahren kann für Folgendes verwendet werden:

- Virtueller Azure-Computer
- Azure-VM-Skalierungsgruppe
- Hybrid-VM, die mit Azure Arc verbunden ist

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen und konfigurieren Sie einen Log Analytics-Arbeitsbereich](vminsights-configure-workspace.md). Alternativ können Sie während dieses Prozesses einen neuen Arbeitsbereich erstellen.
- Beachten Sie die Informationen unter [Unterstützte Betriebssysteme](vminsights-enable-overview.md#supported-operating-systems), um sicherzustellen, dass das Betriebssystem der VM bzw. der VM-Skalierungsgruppe, die Sie aktivieren, unterstützt wird. 

## <a name="enable-azure-monitor-for-vms"></a>Aktivieren von Azure Monitor für VMs

Wählen Sie im Azure-Portal die Option **Virtuelle Computer**, **VM-Skalierungsgruppen** oder **Server – Azure Arc** aus. Dann wählen Sie eine Ressource aus der Liste aus. Wählen Sie im Bereich **Überwachung** des Menüs den Eintrag **Insights** und dann **Aktivieren** aus. Im folgenden Beispiel ist ein virtueller Azure-Computer abgebildet, das Menü für eine Azure-VM-Skalierungsgruppe oder für Azure Arc ist jedoch ähnlich.

![Aktivieren von Azure Monitor für VMs für eine VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Wenn der virtuelle Computer noch nicht mit einem Log Analytics-Arbeitsbereich verbunden ist, werden Sie aufgefordert, einen auszuwählen. Wenn Sie zuvor noch keinen [Arbeitsbereich erstellt](../../azure-monitor/learn/quick-create-workspace.md) haben, können Sie einen Standardwert für den Speicherort auswählen, an dem der virtuelle Computer oder die VM-Skalierungsgruppe im Abonnement bereitgestellt wird. Dieser Arbeitsbereich wird erstellt und konfiguriert, wenn er noch nicht vorhanden ist. Wenn Sie einen vorhandenen Arbeitsbereich auswählen, wird er für Azure Monitor für VMs konfiguriert, sofern das nicht bereits geschehen ist.

> [!NOTE]
> Wenn Sie einen Arbeitsbereich auswählen, der noch nicht für Azure Monitor für VMs konfiguriert wurde, wird diesem Arbeitsbereich das Management Pack *VMInsights* hinzugefügt. Dieses wird auf alle Agents angewendet, die bereits mit dem Arbeitsbereich verbunden sind, unabhängig davon, ob sie für Azure Monitor für VMs aktiviert sind. Es werden Leistungsdaten von diesen virtuellen Computern gesammelt und in der Tabelle *InsightsMetrics* gespeichert.

![Arbeitsbereich auswählen](media/vminsights-configure-workspace/select-workspace.png)

Beim Ausführen der Konfiguration werden Statusmeldungen angezeigt.

>[!NOTE]
>Wenn Sie ein manuelles Upgrademodell für Ihre VM-Skalierungsgruppe verwenden, führen Sie ein Upgrade für die Instanzen durch, um die Einrichtung abzuschließen. Sie können die Upgrades über die Seite **Instanzen** im Abschnitt **Einstellungen** starten.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Nächste Schritte

* Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Verwenden des Zuordnungsfeatures in Azure Monitor für VMs](vminsights-maps.md). 
* Informationen zum Erkennen von Engpässen, der Gesamtauslastung und der Leistung Ihrer VM finden Sie unter [Darstellen der Leistung mit Azure Monitor für VMs](vminsights-performance.md).
