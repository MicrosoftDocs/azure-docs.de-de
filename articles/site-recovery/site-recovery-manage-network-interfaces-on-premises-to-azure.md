---
title: Verwalten von Netzwerkschnittstellen in Azure Site Recovery für die lokale Notfallwiederherstellung in Azure | Microsoft-Dokumentation
description: Hier wird das Verwalten von Netzwerkschnittstellen für die lokale Notfallwiederherstellung in Azure mit Azure Site Recovery beschrieben.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 5d5dd7bc3f6b60c2f9d7c2179f2bd356ca101dc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471753"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Verwalten von Netzwerkschnittstellen auf virtuellen Computern für die lokale Notfallwiederherstellung in Azure
An einen virtuellen Computer (VM) in Azure muss mindestens eine Netzwerkschnittstelle angefügt sein. Es können so viele Netzwerkschnittstellen angefügt sein, wie die VM-Größe unterstützt.

Standardmäßig wird die erste an einen virtuellen Azure-Computer angefügte Netzwerkschnittstelle als primäre Netzwerkschnittstelle definiert. Alle anderen Netzwerkschnittstellen auf dem virtuellen Computer sind sekundäre Netzwerkschnittstellen. Standardmäßig wird sämtlicher ausgehender Datenverkehr des virtuellen Computers über die IP-Adresse gesendet, die der primären IP-Konfiguration der primären Netzwerkschnittstelle zugewiesen ist.

In einer lokalen Umgebung können virtuelle Computer oder Server mehrere Netzwerkschnittstellen für verschiedene Netzwerke in der Umgebung aufweisen. In der Regel werden für spezifische Vorgänge wie Upgrades, Wartung und Zugriff auf das Internet unterschiedliche Netzwerke verwendet. Bei einer Migration oder einem Failover zu Azure aus einer lokalen Umgebung sollten Sie bedenken, dass Netzwerkschnittstellen auf demselben virtuellen Computer mit demselben virtuellen Netzwerk verbunden sein müssen.

Standardmäßig erstellt Azure Site Recovery so viele Netzwerkschnittstellen auf einem virtuellen Azure-Computer, wie mit dem lokalen Server verbunden sind. Sie können das Erstellen redundanter Netzwerkschnittstellen während der Migration oder des Failovers vermeiden, indem Sie die Einstellungen der Netzwerkschnittstelle unter den Einstellungen für den replizierten virtuellen Computer bearbeiten.

## <a name="select-the-target-network"></a>Auswählen des Zielnetzwerks

Für VMware und physische Computer sowie für Hyper-V-VMs (ohne System Center Virtual Machine Manager) können Sie das virtuelle Zielnetzwerk für einzelne virtuelle Computer angeben. Verwenden Sie für Hyper-V-VMs, die mit Virtual Machine Manager verwaltet werden, [Netzwerkzuordnung](site-recovery-network-mapping.md) zum Zuordnen von VM-Netzwerken auf einem Virtual Machine Manager-Quellserver und Azure-Zielnetzwerken.

1. Wählen Sie unter **Replizierte Elemente** in einem Recovery Dervices-Tresor ein beliebiges repliziertes Element aus, um auf die Einstellungen für dieses replizierte Element zuzugreifen.

2. Wählen Sie die Registerkarte **Compute und Netzwerk** aus, um auf die Netzwerkeinstellungen für das replizierte Element zuzugreifen.

3. Wählen Sie unter **Netzwerkeigenschaften** ein virtuelles Netzwerk in der Liste der verfügbaren Netzwerkschnittstellen aus.

    ![Netzwerkeinstellungen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Das Ändern des Zielnetzwerks wirkt sich auf alle Netzwerkschnittstellen für diesen virtuellen Computer aus.

In Virtual Machine Manager-Clouds wirkt sich das Ändern der Netzwerkzuordnung auf alle virtuellen Computer und deren Netzwerkschnittstellen aus.

## <a name="select-the-target-interface-type"></a>Auswählen des Zielschnittstellentyps

Im Abschnitt **Netzwerkschnittstellen** des Bereichs **Compute und Netzwerk** können Sie Netzwerkschnittstelleneinstellungen anzeigen und bearbeiten. Sie können auch den Ziel-Netzwerkschnittstellentyp angeben.

- Für Failover ist eine **primäre** Netzwerkschnittstelle erforderlich.
- Alle weiteren ausgewählten Netzwerkschnittstellen sind ggf. **sekundäre** Netzwerkschnittstellen.
- Wählen Sie **Nicht verwenden** aus, um eine Netzwerkschnittstelle von der Erstellung beim Failover auszuschließen.

Wenn Sie die Replikation aktivieren, wählt Site Recovery standardmäßig alle erkannten Netzwerkschnittstellen auf dem lokalen Server aus. Eine wird als **primär** markiert und alle anderen als **sekundär**. Alle auf dem lokalen Server nachfolgend hinzugefügten Schnittstellen sind standardmäßig als **Nicht verwenden** gekennzeichnet. Wenn Sie weitere Netzwerkschnittstellen hinzufügen, stellen Sie sicher, dass eine geeignete Zielgröße für den virtuellen Azure-Computer ausgewählt ist, um alle erforderlichen Netzwerkschnittstellen zu berücksichtigen.

## <a name="modify-network-interface-settings"></a>Ändern der Netzwerkschnittstelleneinstellungen

Sie können die Subnetz- und IP-Adresse für die Netzwerkschnittstellen eines replizierten Artikels ändern. Wenn keine IP-Adresse angegeben ist, weist Site Recovery der Netzwerkschnittstelle beim Failover die nächste verfügbare IP-Adresse aus dem Subnetz zu.

1. Wählen Sie eine beliebige verfügbare Netzwerkschnittstelle aus, um die Netzwerkschnittstelleneinstellungen zu öffnen.

2. Wählen Sie das gewünschte Subnetz in der Liste der verfügbaren Subnetze aus.

3. Geben Sie die gewünschte IP-Adresse ein (sofern erforderlich).

    ![Einstellungen von Netzwerkschnittstellen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Wählen Sie **OK** aus, um die Bearbeitung abzuschließen und zum Bereich **Compute und Netzwerk** zurückzukehren.

5. Wiederholen Sie die Schritte 1 bis 4 für andere Netzwerkschnittstellen.

6. Klicken Sie zum Speichern aller Änderungen auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte
  [Erfahren Sie mehr](../virtual-network/virtual-network-network-interface-vm.md) über Netzwerkschnittstellen für virtuelle Azure-Computer.
