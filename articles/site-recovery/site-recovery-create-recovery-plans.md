---
title: Erstellen und Anpassen von Wiederherstellungsplänen für die Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Wiederherstellungspläne für die Notfallwiederherstellung mit dem Azure Site Recovery-Dienst erstellen und anpassen | Microsoft-Dokumentation
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 866374df7d3a6973cfc5995afd5cc3c4b0145c48
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400005"
---
# <a name="create-and-customize-recovery-plans"></a>Erstellen und Anpassen von Wiederherstellungsplänen

Dieser Artikel beschreibt, wie Sie einen Wiederherstellungsplan in [Azure Site Recovery](site-recovery-overview.md) erstellen und anpassen. Bevor Sie beginnen, lesen Sie die [Informationen zu Wiederherstellungsplänen](recovery-plan-overview.md).

## <a name="create-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans

1. Wählen Sie im Recovery Services-Tresor nacheinander **Wiederherstellungspläne (Site Recovery)**  >  **+Wiederherstellungsplan** aus.
2. Geben Sie unter **Wiederherstellungsplan erstellen** einen Namen für den Plan an.
3. Wählen Sie eine Quelle und ein Ziel basierend auf den Computern im Plan aus, und wählen Sie dann **Ressourcen-Manager** für das Bereitstellungsmodell aus. Der Quellort muss Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind. 

   **Failover** | **Quelle** | **Ziel** 
   --- | --- | ---
   Azure zu Azure | Azure-Region |Azure-Region
   VMware zu Azure | Konfigurationsserver | Azure
   Physische Computer zu Azure | Konfigurationsserver | Azure   
   Mit VMM verwaltetes Hyper-V zu Azure  | VMM-Anzeigename | Azure
   Hyper-V ohne VMM zu Azure | Name der Hyper-V-Site | Azure
   VMM zu VMM |VMM-Anzeigename | VMM-Anzeigename 

   > [!NOTE]
   > Ein Wiederherstellungsplan kann Computer mit derselben Quelle und demselben Ziel aufweisen. Von VMM verwaltete virtuelle VMware- und Hyper-V-Computer dürfen nicht im gleichen Plan enthalten sein. Virtuelle VMware-Computer und physische Server können im gleichen Plan enthalten sein, bei dem die Quelle ein Konfigurationsserver ist.

2. Wählen Sie unter **Virtuelle Computer auswählen** die Computer (oder die Replikationsgruppe) aus, die Sie dem Plan hinzufügen möchten. Klicken Sie dann auf **OK**.
    - Computer werden der Standardgruppe (Gruppe 1) im Plan hinzugefügt. Nach einem Failover werden alle Computer in dieser Gruppe gleichzeitig gestartet.
    - Sie können nur Computer an den von Ihnen angegebenen Quell- und Zielorten auswählen. 
1. Klicken Sie auf **OK**, um den Plan zu erstellen.

## <a name="add-a-group-to-a-plan"></a>Hinzufügen einer Gruppe zu einem Plan

Sie erstellen zusätzliche Gruppen und fügen Computer zu verschiedenen Gruppen hinzu, sodass Sie für jede Gruppe ein anderes Verhalten festlegen können. Sie können beispielsweise angeben, wann Computer in einer Gruppe nach einem Failover gestartet werden sollen, oder benutzerdefinierte Aktionen pro Gruppe festlegen.

1. Klicken Sie unter **Wiederherstellungspläne** mit der rechten Maustaste auf den Plan und dann auf **Anpassen**. Standardmäßig befinden sich nach dem Erstellen eines Plans alle Computer, die Sie dem Plan hinzugefügt haben, in Standardgruppe 1.
2. Klicken Sie auf **+Gruppe**. Neue Gruppen werden standardmäßig in der Reihenfolge nummeriert, in der sie hinzugefügt werden. Es können bis zu sieben Gruppen vorhanden sein.
3. Wählen Sie den Computer aus, den Sie in die neue Gruppe verschieben möchten, klicken Sie auf **Gruppe ändern**, und wählen Sie dann die neue Gruppe aus. Sie können auch mit der rechten Maustaste auf den Gruppennamen klicken, dann auf **Geschütztes Element** klicken und anschließend Computer zur Gruppe hinzufügen. Ein Computer oder eine Replikationsgruppe kann nur einer Gruppe in einem Wiederherstellungsplan angehören.


## <a name="add-a-script-or-manual-action"></a>Hinzufügen eines Skripts oder einer manuellen Aktion

Sie können einen Wiederherstellungsplan anpassen, indem Sie ein Skript oder eine manuelle Aktion hinzufügen. Beachten Sie Folgendes:

- Wenn Sie zu Azure replizieren, können Sie Azure Automation-Runbooks in Ihren Wiederherstellungsplan integrieren. [Weitere Informationen](site-recovery-runbook-automation.md)
- Wenn Sie von System Center VMM verwaltete virtuelle Hyper-V-Computer replizieren, können Sie ein Skript auf dem lokalen VMM-Server erstellen und in den Wiederherstellungsplan einfügen.
- Hierbei wird für die Gruppe ein neuer Satz mit Aktionen hinzugefügt. Eine Gruppe von Vorabschritten für „Group 1“ wird beispielsweise mit dem Namen *Group 1: pre-steps* erstellt. Alle Vorabschritte werden in dieser Gruppe aufgelistet. Sie können am primären Standort nur dann ein Skript hinzufügen, wenn Sie einen VMM-Server bereitgestellt haben.
- Wenn Sie eine manuelle Aktion hinzufügen, wird beim Ausführen des Wiederherstellungsplans an dem Punkt angehalten, an dem Sie die manuelle Aktion eingefügt haben. Ein Dialogfeld fordert Sie auf anzugeben, dass die manuelle Aktion abgeschlossen wurde.
- Zum Erstellen eines Skripts auf dem VMM-Server folgen Sie den Anweisungen in [diesem Artikel](hyper-v-vmm-recovery-script.md).
- Skripts können während eines Failovers zum sekundären Standort und während eines Failbacks vom sekundären zum primären Standard angewendet werden. Die Unterstützung hängt von Ihrem Replikationsszenario ab:
    
    **Szenario** | **Failover** | **Failback**
    --- | --- | --- 
    Azure zu Azure  | Runbook | Runbook
    VMware zu Azure | Runbook | Nicht verfügbar 
    Hyper-V mit VMM zu Azure | Runbook | Skript
    Hyper-V-Standort zu Azure | Runbook | Nicht verfügbar
    VMM zu sekundärem VMM | Skript | Skript

1. Klicken Sie im Wiederherstellungsplan auf den Schritt, dem die Aktion hinzugefügt werden soll, und geben Sie an, wann die Aktion stattfinden soll:
    1. Wenn die Aktion stattfinden soll, bevor die Computer in der Gruppe nach einem Failover gestartet werden, wählen Sie **Vorausgehende Aktion hinzufügen** aus.
    1. Wenn die Aktion stattfinden soll, nachdem die Computer in der Gruppe nach einem Failover gestartet wurden, wählen Sie **Nachfolgende Aktion hinzufügen** aus. Verwenden Sie die Schaltflächen **Nach oben** und **Nach unten**, um die Position der Aktion zu verschieben.
2. Wählen Sie unter **Aktion einfügen** die Option **Skript** oder **Manuelle Aktion** aus.
3. Zum Hinzufügen einer manuellen Aktion gehen Sie folgendermaßen vor:
    1. Geben Sie einen Namen und Anweisungen für die Aktion ein. Diese Anweisungen werden der Person angezeigt, die das Failover ausführt.
    1. Geben Sie an, ob Sie die manuelle Aktion für alle Failovertypen (Test, Failover, Geplantes Failover (falls zutreffend)) hinzufügen möchten. Klicken Sie dann auf **OK**.
4. Zum Hinzufügen eines Skripts gehen Sie folgendermaßen vor:
    1. Wählen Sie beim Hinzufügen eines VMM-Skripts die Option **Failover zu VMM-Skript** aus, und geben Sie unter **Skriptpfad** den relativen Pfad zur Freigabe ein. Wenn sich die Freigabe beispielsweise unter „\\\<VMMServerName>\MSSCVMMLibrary\RPScripts“ befindet, geben Sie den Pfad „\RPScripts\RPScript.PS1“ an.
    1. Wenn Sie ein Azure-Automatisierungsrunbook hinzufügen, geben Sie das **Azure Automation-Konto** an, in dem sich das Runbook befindet, und wählen das gewünschte **Azure-Runbookskript** aus.
5. Führen Sie ein Testfailover für den Wiederherstellungsplan aus, um sicherzustellen, dass das Skript wie erwartet funktioniert.

## <a name="watch-a-video"></a>Ansehen eines Videos

Sehen Sie sich ein Video an, in dem gezeigt wird, wie Sie einen Wiederherstellungsplan erstellen.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Ausführen von Failovern](site-recovery-failover.md).  

    
