---
title: Konfigurieren des Azure Automation-Features „VMs außerhalb der Geschäftszeiten starten/beenden“
description: In diesem Artikel erfahren Sie, wie Sie das Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ so konfigurieren, dass es unterschiedliche Anwendungsfälle oder Szenarien unterstützt.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: 552cac01cd492229a19bdbc297665eb7d3ccb401
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751290"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Konfigurieren von „VMs außerhalb der Geschäftszeiten starten/beenden“

In diesem Artikel wird beschrieben, wie Sie das Feature [VMs außerhalb der Geschäftszeiten starten/beenden](automation-solution-vm-management.md) so konfigurieren, dass es unterschiedliche Anwendungsfälle oder Szenarien unterstützt. Außerdem lernen Sie Folgendes:

* [Konfigurieren von E-Mail-Benachrichtigungen](#configure-email-notifications)
* [Hinzufügen eines virtuellen Computers](#add-a-vm)
* [Ausschließen eines virtuellen Computers](#exclude-a-vm)
* [Ändern der Zeitpläne für das Starten und Herunterfahren](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Szenario 1: Starten/Beenden von VMs nach Zeitplan

Dieses Szenario ist die Standardkonfiguration bei der ersten Bereitstellung von „VMs außerhalb der Geschäftszeiten starten/beenden“. Beispielsweise können Sie das Feature so konfigurieren, dass alle VMs in einem Abonnement zum Feierabend beendet und am Morgen wieder gestartet werden, wenn Sie wieder ins Büro kommen. Wenn Sie die Zeitpläne **Scheduled-StartVM** und **Scheduled-StopVM** während der Bereitstellung konfigurieren, werden Ziel-VMs nach diesen Zeitplänen gestartet und beendet. 

Sie können das Feature auch so konfigurieren, dass VMs nur beendet werden. Weitere Informationen zum Konfigurieren eines benutzerdefinierten Zeitplans finden Sie unter [Ändern der Zeitpläne für das Starten und Herunterfahren](#modify-the-startup-and-shutdown-schedules).

> [!NOTE]
> Die für das Feature verwendete Zeitzone entspricht Ihrer aktuellen Zeitzone zum Zeitpunkt der Konfiguration des Parameters für den geplanten Zeitpunkt. Azure Automation verwendet für die Speicherung jedoch das UTC-Format in Azure Automation. Sie müssen keine Zeitzonenkonvertierung durchführen, da dies während der Bereitstellung der Computer erfolgt.

Um die VMs im Gültigkeitsbereich zu steuern, konfigurieren Sie die Variablen `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` und `External_ExcludeVMNames`.

Sie können die Aktion entweder für ein Abonnement und eine Ressourcengruppe oder für eine spezifische Liste von VMs aktivieren, aber nicht für beides.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Festlegen der Aktionen zum Starten und Beenden für ein Abonnement und eine Ressourcengruppe

1. Konfigurieren Sie die Variablen `External_Stop_ResourceGroupNames` und `External_ExcludeVMNames`, um die Ziel-VMs anzugeben.

2. Aktivieren und aktualisieren Sie die Zeitpläne **Scheduled-StartVM** und **Scheduled-StopVM**.

3. Führen Sie das Runbook **ScheduledStartStop_Parent** aus. Legen Sie dabei das Parameterfeld **ACTION** auf **start** und das Parameterfeld **WHATIF** auf TRUE fest, um für die Änderungen eine Vorschau anzuzeigen.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Festlegen der Aktionen zum Starten und Beenden über eine VM-Liste

1. Führen Sie das **ScheduledStartStop_Parent**-Runbook aus, wobei **ACTION** auf **start** festgelegt ist.

2. Fügen Sie eine durch Trennzeichen getrennte Liste mit virtuellen Computern (ohne Leerzeichen) im **VMList**-Parameterfeld hinzu. Eine Beispielliste ist `vm1,vm2,vm3`.

3. Legen Sie das Parameterfeld **WHATIF** auf TRUE fest, um eine Vorschau der Änderungen anzuzeigen.

4. Konfigurieren Sie die Variable `External_ExcludeVMNames` mit einer durch Kommas getrennten Liste von VMs (VM1,VM2,VM3) ohne Leerzeichen zwischen den durch Trennzeichen getrennten Werten.

5. In diesem Szenario werden die Variablen `External_Start_ResourceGroupNames` und `External_Stop_ResourceGroupnames` nicht berücksichtigt. Für dieses Szenario müssen Sie Ihren eigenen Automation-Zeitplan erstellen. Ausführliche Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](shared-resources/schedules.md).

    > [!NOTE]
    > Der Wert für **ResourceGroup-Zielnamen** wird als der Wert für sowohl `External_Start_ResourceGroupNames` als auch `External_Stop_ResourceGroupNames` gespeichert. Für weitere Granularität können Sie jede dieser Variablen so ändern, dass sie für unterschiedliche Ressourcengruppen gelten. Verwenden Sie für Startaktion `External_Start_ResourceGroupNames` und für die Beendigungsaktion `External_Stop_ResourceGroupNames`. VMs werden automatisch den Zeitplänen zum Starten und Beenden hinzugefügt.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Szenario 2: Starten/Beenden von VMs der Reihe nach mithilfe von Tags

In einer Umgebung mit mehreren Komponenten auf mehreren VMs, die eine verteilte Workload unterstützen, spielt die Reihenfolge, in der Komponenten nacheinander gestartet und beendet werden, eine wichtige Rolle. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Festlegen der Aktionen zum Starten und Beenden für ein Abonnement und eine Ressourcengruppe

1. Fügen Sie den Ziel-VMs aus `External_Start_ResourceGroupNames` und `External_Stop_ResourceGroupNames` die Tags `sequencestart` und `sequencestop` mit einem positiven ganzzahligen Wert hinzu. Die Aktionen zum Starten und Beenden werden in aufsteigender Reihenfolge durchgeführt. Informationen zum Kennzeichnen von VMs finden Sie unter [Markieren eines virtuellen Windows-Computers in Azure](../virtual-machines/tag-portal.md) und [Markieren eines virtuellen Linux-Computers in Azure](../virtual-machines/tag-cli.md).

2. Ändern Sie die Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** in das Datum und den Zeitpunkt, die Ihren Anforderungen entsprechen, und aktivieren Sie die Zeitpläne.

3. Führen Sie das Runbook **SequencedStartStop_Parent** aus. Legen Sie dabei **ACTION** auf **start** und **WHATIF** auf TRUE fest, um für die Änderungen eine Vorschau anzuzeigen.

4. Zeigen Sie für die Aktion eine Vorschau an, und nehmen Sie alle erforderlichen Änderungen vor, bevor Sie die Implementierung für Produktions-VMs durchführen. Nach Abschluss des Vorgangs können Sie das Runbook manuell ausführen, wobei Sie den Parameter auf **False** festlegen. Sie können alternativ auch die Automation-Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** entsprechend Ihrem vorgegebenen Zeitplan automatisch ausführen.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Festlegen der Aktionen zum Starten und Beenden über eine VM-Liste

1. Fügen Sie den VMs, die Sie dem Parameter `VMList` hinzufügen möchten, die Tags `sequencestart` und `sequencestop` mit einem positiven ganzzahligen Wert hinzu.

2. Führen Sie das **SequencedStartStop_Parent**-Runbook aus, wobei **ACTION** auf **start** festgelegt ist.

3. Fügen Sie eine durch Trennzeichen getrennte Liste mit virtuellen Computern (ohne Leerzeichen) im **VMList**-Parameterfeld hinzu. Eine Beispielliste ist `vm1,vm2,vm3`.

4. Legen Sie **WHATIF** auf TRUE fest, um eine Vorschau der Änderungen anzuzeigen. 

5. Konfigurieren Sie die Variable `External_ExcludeVMNames` mit einer durch Kommas getrennten Liste von VMs ohne Leerzeichen zwischen den durch Trennzeichen getrennten Werten.

6. In diesem Szenario werden die Variablen `External_Start_ResourceGroupNames` und `External_Stop_ResourceGroupnames` nicht berücksichtigt. Für dieses Szenario müssen Sie Ihren eigenen Automation-Zeitplan erstellen. Ausführliche Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](shared-resources/schedules.md).

7. Zeigen Sie für die Aktion eine Vorschau an, und nehmen Sie alle erforderlichen Änderungen vor, bevor Sie die Implementierung für Produktions-VMs durchführen. Wenn Sie soweit sind, führen Sie das **monitoring-and-diagnostics/monitoring-action-groupsrunbook** mit dem auf **False** festgelegten Parameter manuell aus. Alternativ können Sie die Automation-Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** gemäß Ihrem vorgegebenen Zeitplan automatisch ausführen lassen.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Szenario 3: Automatisches Starten oder Beenden basierend auf der CPU-Auslastung

Das Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ kann einen Beitrag zur Verwaltung der Kosten für die Ausführung von Azure Resource Manager- und klassischen virtuellen Computern in Ihrem Abonnement leisten, indem VMs, die außerhalb der Spitzenzeiten (z. B. nach Geschäftsschluss) nicht verwendet werden, ausgewertet und automatisch beendet werden, wenn die Prozessorauslastung unter einem angegebenen Prozentsatz liegt.

Standardmäßig ist das Feature so vorkonfiguriert, dass die Metrik zur CPU-Auslastung in Prozent ausgewertet wird, um zu ermitteln, ob die durchschnittliche Auslastung kleiner oder gleich 5 % ist. Dieses Szenario wird mit den folgenden Variablen gesteuert und kann geändert werden, wenn die Standardwerte nicht Ihren Anforderungen entsprechen:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Sie können die Aktion für ein Abonnement und eine Ressourcengruppe oder für eine spezifische Liste von VMs aktivieren und darauf zielgerichtet anwenden.

Wenn Sie das Runbook **AutoStop_CreateAlert_Parent** ausführen, überprüft es, ob das Zielabonnement, die Zielressourcengruppen und die Ziel-VMs vorhanden sind. Wenn die VMs vorhanden sind, ruft das Runbook dann das Runbook **AutoStop_CreateAlert_Child** für jede vom übergeordneten Runbook überprüfte VM auf. Dieses untergeordnete Runbook führt folgende Aktionen aus:

* Erstellt eine Metrikwarnungsregel für jede überprüfte VM.
* Löst das Runbook **AutoStop_VM_Child** für eine bestimmte VM aus, wenn die CPU-Auslastung unter den konfigurierten Schwellenwert für das angegebene Zeitintervall fällt. 
* Versucht, die VM zu beenden.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Festlegen der automatische Beendigungsaktion für alle VMs in einem Abonnement

1. Stellen Sie sicher, dass die Variable `External_Stop_ResourceGroupNames` leer oder auf „*“ (Platzhalterzeichen) festgelegt ist.

2. [Optional:] Wenn Sie einige VMs vom automatischen Beenden ausschließen möchten, können Sie der Variable `External_ExcludeVMNames` eine durch Trennzeichen getrennte Liste von VM-Namen hinzufügen.

3. Aktivieren Sie die Ausführung des Zeitplans **Schedule_AutoStop_CreateAlert_Parent**, um die erforderlichen Metrikwarnungsregeln für das Beenden von VMs für alle VMs in Ihrem Abonnement zu erstellen. Durch Ausführen dieses Zeitplantyps können Sie neue Metrikwarnungsregeln erstellen, sobald dem Abonnement neue VMs hinzugefügt werden.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Festlegen der automatischen Beendigungsaktion für alle VMs in einer oder mehreren Ressourcengruppen

1. Fügen Sie der Variablen `External_Stop_ResourceGroupNames` eine durch Trennzeichen getrennte Liste von Ressourcengruppennamen hinzu.

2. Wenn Sie einige VMs vom automatischen Beenden ausschließen möchten, können Sie der Variable `External_ExcludeVMNames` eine durch Trennzeichen getrennte Liste von VM-Namen hinzufügen.

3. Aktivieren Sie die Ausführung des Zeitplans **Schedule_AutoStop_CreateAlert_Parent**, um die erforderlichen Metrikwarnungsregeln für das Beenden von VMs für alle VMs in Ihren Ressourcengruppen zu erstellen. Wenn Sie diesen Vorgang nach einem Zeitplan ausführen, können Sie neue Metrikwarnungsregeln erstellen, wenn Ressourcengruppen neue VMs hinzugefügt werden.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Festlegen der automatische Beendigungsaktion für eine Liste von VMs

1. Erstellen Sie einen neuen [Zeitplan](shared-resources/schedules.md#create-a-schedule), und verknüpfen Sie ihn mit dem Runbook **AutoStop_CreateAlert_Parent**, wobei Sie dem Parameter `VMList` eine durch Trennzeichen getrennte Liste von VM-Namen hinzufügen.

2. Wenn Sie einige VMs vom automatischen Beenden ausschließen möchten, können Sie optional der Variable `External_ExcludeVMNames` eine durch Trennzeichen getrennte Liste (ohne Leerzeichen) von VM-Namen hinzufügen.

## <a name="configure-email-notifications"></a>Konfigurieren von E-Mail-Benachrichtigungen

Um E-Mail-Benachrichtigungen nach dem Bereitstellen von „VMs außerhalb der Geschäftszeiten starten/beenden“ zu ändern, bearbeiten Sie die Aktionsgruppe, die während der Bereitstellung erstellt wurde.  

> [!NOTE]
> Abonnements in der Azure Government-Cloud unterstützen die E-Mail-Funktionalität dieses Features nicht.

1. Navigieren Sie im Azure-Portal zu **Überwachung** > **Aktionsgruppen**. Wählen Sie die Aktionsgruppe **StartStop_VM_Notication** aus.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Screenshot der Seite „Azure Monitor-Aktionsgruppen“":::

2. Klicken Sie auf der Seite „StartStop_VM_Notification“ unter **Details** auf **Details bearbeiten**. Dadurch wird die Seite „Email/SMS/Push/Voice“ geöffnet. Aktualisieren Sie die E-Mail-Adresse, und klicken Sie auf **OK**, um Ihre Änderungen zu speichern.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Screenshot der Seite „E-Mail/SMS/Push/Sprache“ mit aktualisierter Beispiel-E-Mail-Adresse":::

    Alternativ können Sie der Aktionsgruppe weitere Aktionen hinzufügen. Weitere Informationen über Aktionsgruppen finden Sie unter [Aktionsgruppen](../azure-monitor/platform/action-groups.md)

Die folgende Beispiel-E-Mail wird gesendet, wenn das Feature virtuelle Computer herunterfährt.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Screenshot einer Beispiel-E-Mail, die gesendet wird, wenn das Feature virtuelle Computer herunterfährt":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Hinzufügen oder Ausschließen von VMs

Das Feature ermöglicht Ihnen das Hinzufügen oder Ausschließen von VMs als Ziel. 

### <a name="add-a-vm"></a>Hinzufügen eines virtuellen Computers

Es gibt zwei Möglichkeiten, sicherzustellen, dass ein virtueller Computer beim Ausführen des Features eingeschlossen wird:

* Jedes der übergeordneten [Runbooks](automation-solution-vm-management.md#runbooks) des Features weist einen `VMList`-Parameter auf. Sie können diesem Parameter eine durch Trennzeichen getrennte Liste von VM-Namen (ohne Leerzeichen) übergeben, wenn Sie das für Ihre Situation geeignete übergeordnete Runbook planen, sodass diese VMs beim Ausführen des Features berücksichtigt werden.

* Um mehrere VMs auszuwählen, legen Sie für `External_Start_ResourceGroupNames` und `External_Stop_ResourceGroupNames` die Namen der Ressourcengruppen fest, die die VMs enthalten, die Sie starten oder beenden möchten. Sie können die Variablen auch auf den Wert `*` festlegen, damit das Feature für alle Ressourcengruppen im Abonnement ausgeführt wird.

### <a name="exclude-a-vm"></a>Ausschließen eines virtuellen Computers

Wenn Sie einen virtuellen Computer vom Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ ausschließen möchten, können Sie seinen Namen der Variable `External_ExcludeVMNames` hinzufügen. Diese Variable ist eine durch Trennzeichen getrennte Liste von bestimmten VMs (ohne Leerzeichen), die vom Feature ausgeschlossen werden sollen. Diese Liste ist auf 140 virtuelle Computer beschränkt. Wenn Sie dieser Liste mehr als 140 VMs hinzufügen, kann es bei VMs, für die der Ausschluss festgelegt wurde, zu einem unbeabsichtigten Starten oder Beenden kommen.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändern der Zeitpläne für das Starten und Herunterfahren

Bei der Verwaltung der Zeitpläne für das Starten und Herunterfahren werden bei diesem Feature die gleichen Schritte ausgeführt, die unter [Planen eines Runbooks in Azure Automation](shared-resources/schedules.md) beschrieben werden. Zum Starten und Beenden von VMs sind separate Zeitpläne erforderlich.

Das Feature kann auch so konfiguriert werden, dass VMs zu einem bestimmten Zeitpunkt beendet werden. In diesem Szenario erstellen Sie nur einen Zeitplan für das Beenden und keinen entsprechenden Zeitplan für das Starten. 

1. Vergewissern Sie sich, dass Sie der Variable `External_Stop_ResourceGroupNames` die Ressourcengruppen für die zu beendenden VMs hinzugefügt haben.

2. Erstellen Sie Ihren eigenen Zeitplan für den Zeitpunkt, an dem die VMs heruntergefahren werden sollen.

3. Navigieren Sie zum Runbook **ScheduledStartStop_Parent**, und klicken Sie auf **Zeitplan**. Dadurch können Sie den Zeitplan auswählen, den Sie im vorherigen Schritt erstellt haben.

4. Wählen Sie **Parameter und Ausführungseinstellungen** aus, und legen Sie das Feld **ACTION** auf **Stop** fest.

5. Klicken Sie zum Speichern der Änderungen auf **OK** .

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Überwachen des Features während der Ausführung finden Sie unter [Abfragen von Protokollen über „VMs außerhalb der Geschäftszeiten starten/beenden“](automation-solution-vm-management-logs.md).
* Informationen zum Behandeln von Problemen bei der VM-Verwaltung finden Sie unter [Behandeln von Problemen mit „VMs außerhalb der Geschäftszeiten starten/beenden“](troubleshoot/start-stop-vm.md).
