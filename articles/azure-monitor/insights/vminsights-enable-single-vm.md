---
title: Aktivieren von Azure Monitor für VMs (Vorschauversion) für die Auswertung | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs für eine oder mehrere Azure-VMs oder VM-Skalierungsgruppen für Auswertungszwecke aktivieren.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524038"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion) für die Auswertung

Wenn Sie Azure Monitor für VMs (Vorschauversion) für eine kleine Anzahl von Azure-VMs oder eine einzelne VM oder eine VM-Skalierungsgruppe auswerten möchten, wird die Überwachung am einfachsten und direktesten über das Azure-Portal aktiviert. Nachdem Sie diesen Prozess durchgeführt haben, haben Sie erfolgreich damit begonnen, die virtuellen Computer zu überwachen, und werden erfahren, ob Leistungs- oder Verfügbarkeitsprobleme bei diesen Computern auftreten. 

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](vminsights-enable-overview.md) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen auch diese Anforderungen erfüllen.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivieren der Überwachung für eine einzelne Azure-VM
Gehen Sie wie folgt vor, um die Überwachung Ihrer Azure-VM im Azure-Portal zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Virtuelle Computer**.

1. Wählen Sie einen virtuellen Computer in der Liste aus.

1. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

1. Wählen Sie auf der Seite **Insights (Vorschau)** **Jetzt testen** aus.

    ![Aktivieren von Azure Monitor for VMs für eine VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Wenn Sie im selben Abonnement einen Log Analytics-Arbeitsbereich haben, wählen Sie ihn auf der Seite **Azure Monitor Insights Onboarding** (Onboarding von Azure Monitor Insights) in der Dropdownliste aus.  

    Mit der Liste werden der Standardarbeitsbereich und der Speicherort, an dem die VM im Abonnement bereitgestellt wird, vorausgewählt. 

    >[!NOTE]
    >Befolgen Sie die Anweisungen unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md) für eine der [hier](vminsights-enable-overview.md#log-analytics) aufgeführten unterstützten Regionen, wenn Sie einen neuen Log Analytics-Arbeitsbereich zum Speichern der Überwachungsdaten aus der VM erstellen möchten.

Nach dem Aktivieren der Überwachung kann es ca. 10 Minuten dauern, bis die Integritätsmetriken für den virtuellen Computer angezeigt werden.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivieren der Überwachung für eine einzelne VM-Skalierungsgruppe

Gehen Sie wie folgt vor, um die Überwachung Ihrer Azure VM-Skalierungsgruppe im Azure-Portal zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Virtual Machine Scale Sets** aus.

3. Wählen Sie aus der Liste eine VM-Skalierungsgruppe aus.

4. Wählen Sie auf der Seite der VM-Skalierungsgruppe im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

5. Wenn Sie bereits einen Log Analytics-Arbeitsbereich haben, den Sie verwenden möchten, wählen Sie ihn auf der Seite **Insights (Vorschau)** aus der Dropdownliste aus.

    Mit der Liste werden der Standardarbeitsbereich und der Speicherort, an dem die VM im Abonnement bereitgestellt wird, vorausgewählt. 

    ![Aktivieren von Azure Monitor für VMs für eine VM-Skalierungsgruppe](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Befolgen Sie die Anweisungen unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md) für eine der [hier](vminsights-enable-overview.md#log-analytics) aufgeführten unterstützten Regionen, wenn Sie einen neuen Log Analytics-Arbeitsbereich zum Speichern der Überwachungsdaten aus der VM erstellen möchten.

Nachdem Sie die Überwachung aktiviert haben, kann es ca. 10 Minuten dauern, bis Sie die Überwachungsdaten für die Skalierungsgruppe ansehen können.

>[!NOTE]
>Wenn Sie ein manuelles Upgrademodell für Ihre Skalierungsgruppe verwenden, müssen Sie ein Upgrade für die Instanzen durchführen, um die Einrichtung abzuschließen.  Dies können Sie auf der Seite „Instanzen“ im Abschnitt **Einstellungen** tun.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihren virtuellen Computer oder Ihre VM-Skalierungsgruppe aktiviert wurde, stehen diese Informationen für die Analyse mit Azure Monitor für VMs zur Verfügung. Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md). Informationen zum Erkennen von Engpässen und Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [View Azure VM Performance](vminsights-performance.md) (Anzeigen der Leistung von Azure-VMs). Informationen zu erkannten Anwendungsabhängigkeiten finden Sie unter [View Azure Monitor for VMs Map](vminsights-maps.md) (Anzeigen der Zuordnung von Azure Monitor for VMs).