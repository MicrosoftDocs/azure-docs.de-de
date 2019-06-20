---
title: Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten
description: Mit dieser Lösung für die VM-Verwaltung werden Ihre virtuellen Azure Resource Manager-Computer nach einem Zeitplan gestartet und beendet und mit Azure Monitor-Protokolle proaktiv überwacht.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4e1ad106b928c41bd6940d7c3713b5fb34afe3a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389112"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten in Azure Automation

Mit der Lösung VMs außerhalb der Geschäftszeiten starten/beenden können Sie Ihre virtuellen Azure-Computer nach benutzerdefinierten Zeitplänen starten und beenden. Zudem können Sie über Azure Monitor-Protokolle Erkenntnisse aus Ihre Daten ziehen und durch die Nutzung von [Aktionsgruppen](../azure-monitor/platform/action-groups.md) optional E-Mails senden. Die Lösung unterstützt in den meisten Szenarien sowohl Azure Resource Manager-VMs als auch klassische VMs.

> [!NOTE]
> Die Lösung „VMs außerhalb der Geschäftszeiten starten/beenden“ wurde mit den Azure-Modulen getestet, die bei der Bereitstellung der Lösung in Ihr Azure Automation-Konto importiert werden. Die Lösung funktioniert derzeit nicht mit neueren Versionen des Azure-Moduls. Dies betrifft nur das Azure Automation-Konto, mit dem Sie die Lösung „VMs außerhalb der Geschäftszeiten starten/beenden“ ausführen. Sie können in Ihren anderen Azure Automation-Konten weiterhin neuere Versionen des Azure-Moduls verwenden, wie in [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](automation-update-azure-modules.md) beschrieben.

Diese Lösung bietet eine dezentrale und kostengünstige Automatisierungsoption für Benutzer, die ihre VM-Kosten optimieren möchten. Mit dieser Lösung haben Sie folgende Möglichkeiten:

- Starten und Beenden von VMs nach Zeitplan
- Starten und Beenden von VMs nach Zeitplan in aufsteigender Reihenfolge mithilfe von Azure-Tags (wird für klassische VMs nicht unterstützt)
- Automatisches Beenden von VMs bei geringer CPU-Auslastung

Die aktuelle Lösung hat die folgenden Einschränkungen:

- Mit dieser Lösung können VMs in allen Regionen verwaltet werden. Allerdings lässt sie sich nur unter demselben Abonnement wie Ihr Azure Automation-Konto verwenden.
- Diese Lösung ist in Azure und Azure Government für jede Region verfügbar, die einen Log Analytics-Arbeitsbereich, ein Azure Automation-Konto und Warnungen unterstützt. Azure Government-Regionen unterstützen derzeit keine E-Mail-Funktionalität.

> [!NOTE]
> Wenn Sie die Lösung für klassische virtuelle Computer verwenden, werden alle Ihre VMs nacheinander nach Clouddienst verarbeitet. Virtuelle Computer werden weiterhin parallel in verschiedenen Clouddiensten verarbeitet.
>
> Azure Cloud Solution Provider-Abonnements (Azure CSP) unterstützen nur das Azure Resource Manager-Modell. Dienste, die nicht auf Azure Resource Manager basieren, sind in diesem Programm nicht verfügbar. Wenn die Lösung zum Starten/Beenden ausgeführt wird, erhalten Sie möglicherweise Fehler, da sie Cmdlets zur Verwaltung klassischer Ressourcen enthält. Weitere Informationen zu CSP finden Sie unter [Verfügbare Dienste in CSP-Abonnements](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Wenn Sie ein CSP-Abonnement verwenden, sollten Sie die Variable [**External_EnableClassicVMs**](#variables) nach der Bereitstellung in **False** ändern.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für die Runbooks für diese Lösung wird ein [ausführendes Azure-Konto](automation-create-runas-account.md) verwendet. Das ausführende Konto ist die bevorzugte Authentifizierungsmethode, da anstelle eines Kennworts, das ablaufen oder sich häufig ändern kann, eine Zertifikatauthentifizierung verwendet wird.

Es wird empfohlen, für die Lösung zum Starten/Beenden von VMs ein separates Automation-Konto zu verwenden. Der Grund hierfür ist, dass die Azure-Modulversionen häufig aktualisiert werden und sich ihre Parameter ändern können. Die Lösung zum Starten/Beenden von VMs wird nicht mit derselben Häufigkeit aktualisiert, sodass sie eventuell nicht mit neueren Versionen der verwendeten Cmdlets funktioniert. Sie sollten zum Testen auf Modulupdates ein Automation-Testkonto verwenden, bevor Sie sie in Ihr Automation-Produktionskonto importieren.

### <a name="permissions-needed-to-deploy"></a>Für die Bereitstellung erforderliche Berechtigungen

Ein Benutzer muss über bestimmte Berechtigungen verfügen, um die Lösung für das Starten/Beenden von VMs außerhalb der Geschäftszeiten bereitzustellen. Diese Berechtigungen sind unterschiedlich – je nachdem, ob ein vorab erstelltes Automation-Konto und ein vorab erstellter Log Analytics-Arbeitsbereich verwendet oder beides während der Bereitstellung neu erstellt wird. Wenn Sie Mitwirkender im Abonnement und globaler Administrator in Ihrem Azure Active Directory-Mandanten sind, müssen Sie die folgenden Berechtigungen nicht konfigurieren. Wenn Sie nicht über diese Rechte verfügen oder eine benutzerdefinierte Rolle konfigurieren müssen, finden Sie weiter unten die erforderlichen Berechtigungen.

#### <a name="pre-existing-automation-account-and-log-analytics-account"></a>Bereits vorhandenes Automation- und Log Analytics-Konto

Um die Lösung für das Starten/Beenden von VMs außerhalb der Geschäftszeiten für ein Automation-Konto und einen Log Analytics-Arbeitsbereich bereitzustellen, muss der Benutzer, der die Lösung bereitstellt, über die folgenden Berechtigungen für die **Ressourcengruppe** verfügen. Weitere Informationen zu Rollen finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](../role-based-access-control/custom-roles.md).

| Berechtigung | `Scope`|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/variables/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/schedules/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/runbooks/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/connections/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/certificates/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/modules/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/modules/read | Ressourcengruppe |
| Microsoft.automation/automationAccounts/jobSchedules/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/jobs/write | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/jobs/read | Ressourcengruppe |
| Microsoft.OperationsManagement/solutions/write | Ressourcengruppe |
| Microsoft.OperationalInsights/workspaces/* | Ressourcengruppe |
| Microsoft.Insights/diagnosticSettings/write | Ressourcengruppe |
| Microsoft.Insights/ActionGroups/Write | Ressourcengruppe |
| Microsoft.Insights/ActionGroups/read | Ressourcengruppe |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ressourcengruppe |
| Microsoft.Resources/deployments/* | Ressourcengruppe |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Neues Automation-Konto und neuer Log Analytics-Arbeitsbereich

Um die Lösung für das Starten/Beenden von VMs außerhalb der Geschäftszeiten für ein neues Automation-Konto und einen neuen Log Analytics-Arbeitsbereich bereitzustellen, benötigt der Benutzer, der die Lösung bereitstellt, neben den im obigen Abschnitt beschriebenen Berechtigungen zusätzlich die folgenden Berechtigungen:

- Co-Administrator für das Abonnement: nur erforderlich, um das klassische ausführende Konto zu erstellen
- Mitglied der Rolle [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **Anwendungsentwickler**. Weitere Informationen zum Konfigurieren von ausführenden Konten finden Sie unter [Berechtigungen zum Konfigurieren von ausführenden Konten](manage-runas-account.md#permissions).
- Mitwirkender im Abonnement oder die folgenden Berechtigungen.

| Berechtigung |`Scope`|
| --- | --- |
| Microsoft.Authorization/Operations/read | Abonnement|
| Microsoft.Authorization/permissions/read |Abonnement|
| Microsoft.Authorization/roleAssignments/read | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Microsoft.Authorization/roleAssignments/delete | Abonnement |
| Microsoft.Automation/automationAccounts/connections/read | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/certificates/read | Ressourcengruppe |
| Microsoft.Automation/automationAccounts/write | Ressourcengruppe |
| Microsoft.OperationalInsights/workspaces/write | Ressourcengruppe |

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Führen Sie die folgenden Schritte aus, um die Lösung zum Starten/Beenden von VMs außerhalb der Kernzeit dem Automation-Konto hinzuzufügen und anschließend die Variablen zum Anpassen der Lösung zu konfigurieren.

1. Wählen Sie in einem Automation-Konto unter **Zugehörige Ressourcen** die Option **VM starten/beenden** aus. Dort können Sie **Weitere Informationen anzeigen und Lösung aktivieren** anklicken. Wenn Sie bereits eine Lösung zum Starten/Beenden virtueller Computer bereitgestellt haben, können Sie diese auswählen, indem Sie auf **Lösung verwalten** klicken und sie in der Liste auswählen.

   ![Aktivieren über ein Automation-Konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Sie können Ressourcen auch von überall im Azure-Portal erstellen, indem Sie auf **Ressource erstellen** klicken. Geben Sie auf der Seite „Marketplace“ ein Schlüsselwort ein, z.B. **Starten** oder **Starten/Beenden**. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Alternativ hierzu können Sie ein oder mehrere Schlüsselwörter des vollständigen Namens der Lösung eingeben und dann die EINGABETASTE drücken. Wählen Sie in den Suchergebnissen **VMs außerhalb der Geschäftszeiten starten/beenden** aus.

2. Überprüfen Sie auf der Seite **VMs außerhalb der Geschäftszeiten starten/beenden** die Zusammenfassung der ausgewählten Lösung, und klicken Sie dann auf **Erstellen**.

   ![Azure-Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Die Seite **Lösung hinzufügen** wird angezeigt. Sie werden aufgefordert, die Lösung vor dem Importieren in Ihr Automation-Abonnement zu konfigurieren.

   ![Seite „Lösung hinzufügen“ der VM-Verwaltung](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Wählen Sie auf der Seite **Lösung hinzufügen** die Option **Arbeitsbereich** aus. Wählen Sie einen Log Analytics-Arbeitsbereich aus, der mit demselben Azure-Abonnement verknüpft ist, unter dem sich das Automation-Konto befindet. Wählen Sie die Option **Neuen Arbeitsbereich erstellen**, wenn kein Arbeitsbereich vorhanden ist. Führen Sie auf der Seite **Log Analytics-Arbeitsbereich** die folgenden Schritte aus:
   - Geben Sie einen Namen für den neuen **Log Analytics-Arbeitsbereich** ein, z. B. „ContosoLAWorkspace“.
   - Wählen Sie ein **Abonnement** aus, mit dem eine Verknüpfung erstellt werden soll, indem Sie in der Dropdownliste einen anderen Eintrag auswählen, falls der Standardeintrag nicht geeignet ist.
   - Unter **Ressourcengruppe** können Sie eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe auswählen.
   - Wählen Sie einen **Speicherort**aus. Derzeit sind nur die Standorte **Australien, Südosten**, **Kanada, Mitte**, **Indien, Mitte**, **USA, Osten**, **Japan, Osten**, **Asien, Südosten**, **Vereinigtes Königreich, Süden**, **Europa, Westen** und **USA, Westen 2** verfügbar.
   - Wählen Sie einen **Tarif**aus. Wählen Sie die Option **Pro GB (eigenständig)** aus. Für Azure Monitor-Protokolle wurden die [Preise](https://azure.microsoft.com/pricing/details/log-analytics/) aktualisiert, und der Tarif „Pro GB“ ist die einzige Option.

   > [!NOTE]
   > Wenn Sie Lösungen aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt.
   >
   > Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](how-to/region-mappings.md).

5. Klicken Sie nach dem Bereitstellen der erforderlichen Informationen auf der Seite **Log Analytics-Arbeitsbereich** auf **Erstellen**. Sie können den Status unter **Benachrichtigungen** über das Menü nachverfolgen, und nach Abschluss des Vorgangs gelangen Sie zurück zur Seite **Lösung hinzufügen**.
6. Wählen Sie auf der Seite **Lösung hinzufügen** die Option **Automation-Konto** aus. Wenn Sie einen neuen Log Analytics-Arbeitsbereich erstellen, können Sie ein neues Automation-Konto erstellen, das diesem zugeordnet wird, oder ein vorhandenes Automation-Konto auswählen, das nicht bereits mit einem Log Analytics-Arbeitsbereich verknüpft ist. Wählen Sie ein vorhandenes Automation-Konto aus, oder klicken Sie auf **Automation-Konto erstellen**, und geben Sie auf der Seite **Automation-Konto hinzufügen** Folgendes an:
   - Geben Sie im Feld **Name** den Namen des Automation-Kontos ein.

     Alle anderen Optionen werden basierend auf dem Log Analytics-Arbeitsbereich automatisch aufgefüllt. Diese Optionen können nicht geändert werden. Ein ausführendes Azure-Konto ist die Standardmethode für die Authentifizierung von Runbooks in dieser Lösung. Nach dem Klicken auf **OK** werden die Konfigurationsoptionen überprüft, und das Automation-Konto wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

7. Wählen Sie abschließend auf der Seite **Lösung hinzufügen**die Option **Konfiguration** aus. Die Seite **Parameter** wird angezeigt.

   ![Seite „Parameter“ für die Lösung](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Hier erhalten Sie folgende Aufforderung:
   - Geben Sie einen Wert für die Zielressourcengruppennamen(**Target ResourceGroup Names**) ein. Dies sind Werte von Ressourcengruppen mit virtuellen Computern, die mit dieser Lösung verwaltet werden. Sie können mehrere Namen eingeben und die Namen jeweils durch ein Komma trennen (Groß-/Kleinschreibung wird nicht berücksichtigt). Die Verwendung eines Platzhalterzeichens wird unterstützt, wenn Sie einen Vorgang für VMs in allen Ressourcengruppen des Abonnements durchführen möchten. Dieser Wert wird in den Variablen **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupNames** gespeichert.
   - Geben Sie die **VM Exclude List (string)** (VM-Ausschlussliste (Zeichenfolge)) an. Dies ist der Wert von einem oder mehreren virtuellen Computern der Zielressourcengruppe. Sie können mehrere Namen eingeben und die Namen jeweils durch ein Komma trennen (Groß-/Kleinschreibung wird nicht berücksichtigt). Platzhalter können verwendet werden. Dieser Wert wird in der Variablen **External_ExcludeVMNames** gespeichert.
   - Wählen Sie einen **Zeitplan** aus. Dieser Wert ist ein wiederkehrendes Datum und eine Uhrzeit zum Starten und Beenden der virtuellen Computer in den Zielressourcengruppen. Der Zeitplan ist standardmäßig für den Zeitpunkt 30 Minuten nach der aktuellen Uhrzeit konfiguriert. Es kann keine andere Region ausgewählt werden. Falls Sie den Zeitplan nach dem Konfigurieren der Lösung für Ihre Zeitzone konfigurieren möchten, helfen Ihnen die Informationen unter [Ändern des Zeitplans für das Starten und Herunterfahren](#modify-the-startup-and-shutdown-schedules) weiter.
   - Um **E-Mail-Benachrichtigungen** von einer Aktionsgruppe zu empfangen, übernehmen Sie den Standardwert **Ja**, und geben Sie eine gültige E-Mail-Adresse an. Wenn Sie **Nein** auswählen, sich aber zu einem späteren Zeitpunkt entscheiden, dass Sie E-Mail-Benachrichtigungen erhalten möchten, können Sie die erstellte [Aktionsgruppe](../azure-monitor/platform/action-groups.md) mit gültigen E-Mail-Adressen, die durch Komma getrennt sind, aktualisieren. Außerdem müssen Sie die folgenden Warnungsregeln aktivieren:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Der Standardwert für **ResourceGroup-Zielnamen** ist ein **&ast;** . Dies betrifft alle VMs in einem Abonnement. Wenn die Lösung nicht alle VMs als Zielversion in Ihrem Abonnement festlegen soll, muss dieser Wert vor der Aktivierung der Zeitpläne auf eine Liste der Ressourcengruppennamen aktualisiert werden.

8. Nachdem Sie die erforderlichen Anfangseinstellungen für die Lösung konfiguriert haben, klicken Sie auf **OK**, um die Seite **Parameter** zu schließen, und wählen Sie dann **Erstellen** aus. Nachdem alle Einstellungen überprüft wurden, wird die Lösung für Ihr Abonnement bereitgestellt. Dieser Vorgang kann einige Sekunden dauern, und Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

> [!NOTE]
> Wenn Sie ein Abonnement für Azure Cloud Solution Provider (Azure CSP) haben, wechseln Sie nach Abschluss der Bereitstellung in Ihrem Automation-Konto unter **Freigegebene Ressourcen** zu **Variablen**, und legen Sie die Variable [**External_EnableClassicVMs**](#variables) auf **False** fest. Dadurch hört die Lösung auf, nach klassischen VM-Ressourcen zu suchen.

## <a name="scenarios"></a>Szenarien

Die Lösung umfasst drei unterschiedliche Szenarien. Es handelt sich um folgende Szenarien:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Szenario 1: Starten/Beenden von VMs nach Zeitplan

Dieses Szenario ist die Standardkonfiguration bei der ersten Bereitstellung der Lösung. Beispielsweise können Sie sie so konfigurieren, dass alle VMs in einem Abonnement zum Feierabend beendet und am Morgen wieder gestartet werden, wenn Sie wieder ins Büro kommen. Wenn Sie die Zeitpläne **Scheduled-StartVM** und **Scheduled-StopVM** während der Bereitstellung konfigurieren, werden Ziel-VMs nach diesen Zeitplänen gestartet und beendet. Die Lösung kann auch so konfiguriert werden, dass VMs nur beendet werden. Informationen zum Konfigurieren eines benutzerdefinierten Zeitplans finden Sie unter [Ändern der Zeitpläne für das Starten und Herunterfahren](#modify-the-startup-and-shutdown-schedules).

> [!NOTE]
> Die Zeitzone entspricht Ihrer aktuellen Zeitzone zum Zeitpunkt der Konfiguration des Parameters für den geplanten Zeitpunkt. Sie wird in Azure Automation aber im UTC-Format gespeichert. Sie müssen keine Zeitzonenkonvertierung durchführen, da dies während der Bereitstellung verarbeitet wird.

Sie bestimmen, welche VMs im Geltungsbereich liegen, indem Sie die folgenden Variablen konfigurieren: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** und **External_ExcludeVMNames**.

Sie können die Aktion entweder für ein Abonnement und eine Ressourcengruppe oder für eine spezifische Liste von VMs aktivieren, aber nicht für beides.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Festlegen der Aktionen zum Starten und Beenden für ein Abonnement und eine Ressourcengruppe

1. Konfigurieren Sie die Variablen **External_Stop_ResourceGroupNames** und **External_ExcludeVMNames**, um die Ziel-VMs anzugeben.
2. Aktivieren und aktualisieren Sie die Zeitpläne **Scheduled-StartVM** und **Scheduled-StopVM**.
3. Führen Sie das Runbook **ScheduledStartStop_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** und den Parameter WHATIF auf **True** fest, um für die Änderungen eine Vorschau anzuzeigen.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Festlegen der Aktionen zum Starten und Beenden über eine VM-Liste

1. Führen Sie das Runbook **ScheduledStartStop_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** fest, fügen Sie eine durch Trennzeichen getrennte Liste von VMs im Parameter *VMList* hinzu, und legen Sie den Parameter WHATIF auf **True** fest. Zeigen Sie eine Vorschau für die Änderungen an.
1. Konfigurieren Sie den Parameter **External_ExcludeVMNames** mit einer durch Kommas getrennten Liste von VMs (VM1, VM2, VM3).
1. In diesem Szenario werden die Variablen **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupnames** nicht berücksichtigt. Für dieses Szenario müssen Sie Ihren eigenen Automation-Zeitplan erstellen. Ausführliche Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Der Wert für **Target ResourceGroup Names** wird sowohl als Wert für **External_Start_ResourceGroupNames** als auch für **External_Stop_ResourceGroupNames** gespeichert. Für weitere Granularität können Sie jede dieser Variablen so ändern, dass sie für unterschiedliche Ressourcengruppen gelten. Verwenden Sie **External_Start_ResourceGroupNames** für Startaktionen und **External_Stop_ResourceGroupNames** für Beendigungsaktionen. VMs werden automatisch den Zeitplänen zum Starten und Beenden hinzugefügt.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Szenario 2: Starten/Beenden von VMs der Reihe nach mithilfe von Tags

In einer Umgebung mit mehreren Komponenten auf mehreren VMs, die eine verteilte Workload unterstützen, spielt die Reihenfolge, in der Komponenten nacheinander gestartet und beendet werden, eine wichtige Rolle. Führen Sie zum Erreichen dieses Szenarios die folgenden Schritte aus:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Festlegen der Aktionen zum Starten und Beenden für ein Abonnement und eine Ressourcengruppe

1. Fügen Sie ein **sequencestart**- und ein **sequencestop**-Tag mit einer positiven ganzen Zahl den virtuellen Computern hinzu, die in den Variablen **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupNames** als Ziel angegeben sind. Die Aktionen zum Starten und Beenden werden in aufsteigender Reihenfolge durchgeführt. Informationen zum Markieren von VMs finden Sie unter [Markieren eines virtuellen Windows-Computers in Azure](../virtual-machines/windows/tag.md) und [Markieren eines virtuellen Linux-Computers in Azure](../virtual-machines/linux/tag.md).
1. Ändern Sie die Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** in das Datum und den Zeitpunkt, die Ihren Anforderungen entsprechen, und aktivieren Sie die Zeitpläne.
1. Führen Sie das Runbook **SequencedStartStop_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** und den Parameter WHATIF auf **True** fest, um für die Änderungen eine Vorschau anzuzeigen.
1. Zeigen Sie für die Aktion eine Vorschau an, und nehmen Sie alle erforderlichen Änderungen vor, bevor Sie die Implementierung für Produktions-VMs durchführen. Nach Abschluss des Vorgangs können Sie das Runbook manuell ausführen, wobei Sie den Parameter auf **False** festlegen, oder Sie können die Automation-Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** entsprechend Ihrem vorgegebenen Zeitplan automatisch ausführen.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Festlegen der Aktionen zum Starten und Beenden über eine VM-Liste

1. Fügen Sie virtuellen Computern, die dem Parameter **VMList** hinzugefügt werden sollen, ein **sequencestart**- und ein **sequencestop**-Tag mit einem positiven Integerwert hinzu.
1. Führen Sie das Runbook **SequencedStartStop_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** fest, fügen Sie eine durch Trennzeichen getrennte Liste von VMs im Parameter *VMList* hinzu, und legen Sie den Parameter WHATIF auf **True** fest. Zeigen Sie eine Vorschau für die Änderungen an.
1. Konfigurieren Sie den Parameter **External_ExcludeVMNames** mit einer durch Kommas getrennten Liste von VMs (VM1, VM2, VM3).
1. In diesem Szenario werden die Variablen **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupnames** nicht berücksichtigt. Für dieses Szenario müssen Sie Ihren eigenen Automation-Zeitplan erstellen. Ausführliche Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](../automation/automation-schedules.md).
1. Zeigen Sie für die Aktion eine Vorschau an, und nehmen Sie alle erforderlichen Änderungen vor, bevor Sie die Implementierung für Produktions-VMs durchführen. Nach Abschluss des Vorgangs können Sie das monitoring-and-diagnostics/monitoring-action-groups-Runbook manuell ausführen, wobei Sie den Parameter auf **False** festlegen, oder Sie können die Automation-Zeitpläne **Sequenced-StartVM** und **Sequenced-StopVM** entsprechend Ihrem vorgegebenen Zeitplan automatisch ausführen.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Szenario 3: Automatisches Starten/Beenden basierend auf der CPU-Auslastung

Diese Lösung kann einen Beitrag zur Verwaltung der Kosten für die Ausführung von VMs in Ihrem Abonnement leisten, indem Azure-VMs, die außerhalb der Spitzenzeiten (z.B. nach Geschäftsschluss) nicht verwendet werden, ausgewertet und automatisch beendet werden, wenn die Prozessorauslastung unter x% liegt.

Standardmäßig ist die Lösung so vorkonfiguriert, dass die Metrik zur CPU-Auslastung in Prozent ausgewertet wird, um zu ermitteln, ob die durchschnittliche Auslastung kleiner oder gleich 5% ist. Dieses Szenario wird mit den folgenden Variablen gesteuert und kann geändert werden, wenn die Standardwerte nicht Ihren Anforderungen entsprechen:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Sie können die Aktion entweder für ein Abonnement und eine Ressourcengruppe oder für eine spezifische Liste von VMs aktivieren, aber nicht für beides.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Festlegen der Beendigungsaktion für ein Abonnement und eine Ressourcengruppe

1. Konfigurieren Sie die Variablen **External_Stop_ResourceGroupNames** und **External_ExcludeVMNames**, um die Ziel-VMs anzugeben.
1. Aktivieren und aktualisieren Sie den Zeitplan **Schedule_AutoStop_CreateAlert_Parent**.
1. Führen Sie das Runbook **AutoStop_CreateAlert_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** und den Parameter WHATIF auf **True** fest, um für die Änderungen eine Vorschau anzuzeigen.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Festlegen der Aktionen zum Starten und Beenden über eine VM-Liste

1. Führen Sie das Runbook **AutoStop_CreateAlert_Parent** aus. Legen Sie dabei den Parameter ACTION auf **start** fest, fügen Sie eine durch Trennzeichen getrennte Liste von VMs im Parameter *VMList* hinzu, und legen Sie den Parameter WHATIF auf **True** fest. Zeigen Sie eine Vorschau für die Änderungen an.
1. Konfigurieren Sie den Parameter **External_ExcludeVMNames** mit einer durch Kommas getrennten Liste von VMs (VM1, VM2, VM3).
1. In diesem Szenario werden die Variablen **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupnames** nicht berücksichtigt. Für dieses Szenario müssen Sie Ihren eigenen Automation-Zeitplan erstellen. Ausführliche Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](../automation/automation-schedules.md).

Nachdem Sie einen Zeitplan zum Beenden von VMs basierend auf der CPU-Auslastung erstellt haben, müssen Sie nun einen der folgenden Zeitpläne aktivieren, um sie zu starten.

- Festlegen der Startaktion nach Abonnement und Ressourcengruppe. Informationen zum Testen und Aktivieren von Zeitplänen vom Typ **Scheduled-StartVM** finden Sie in den im [Szenario 1](#scenario-1-startstop-vms-on-a-schedule) beschriebenen Schritten.
- Festlegen der Startaktion nach Abonnement, Ressourcengruppe und Tag. Informationen zum Testen und Aktivieren von Zeitplänen vom Typ **Sequenced-StartVM** finden Sie in den im [Szenario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) beschriebenen Schritten.

## <a name="solution-components"></a>Lösungskomponenten

Diese Lösung umfasst vorkonfigurierte Runbooks, Zeitpläne und die Integration in Azure Monitor-Protokolle, sodass Sie das Starten und Beenden der virtuellen Computer gemäß Ihren Geschäftsanforderungen anpassen können.

### <a name="runbooks"></a>Runbooks

In der folgenden Tabelle sind die mit dieser Lösung in Ihrem Automation-Konto bereitgestellten Runbooks aufgeführt. Nehmen Sie am Runbookcode keine Änderungen vor. Schreiben Sie stattdessen Ihr eigenes Runbook, um neue Funktionen nutzen zu können.

> [!IMPORTANT]
> Führen Sie keine Runbooks aus, an deren Name der Zusatz „child“ angefügt ist.

Alle übergeordneten Runbooks enthalten den Parameter _WhatIf_. Bei der Festlegung auf **True** unterstützt _WhatIf_ die Beschreibung des exakten Verhaltens des Runbooks, wenn es ohne den Parameter _WhatIf_ ausgeführt wird, und es wird überprüft, ob die richtigen VMs als Ziel verwendet werden. Ein Runbook führt seine zugehörigen definierten Aktionen nur aus, wenn der Parameter _WhatIf_ auf **False** festgelegt ist.

|Runbook | Parameter | BESCHREIBUNG|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Wird über das übergeordnete Runbook aufgerufen. Dieses Runbook erstellt für das AutoStop-Szenario Warnungen pro Ressource.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: „true“ oder „false“  | Erstellt oder aktualisiert Azure-Warnungsregeln auf VMs im Zielabonnement oder den Zielressourcengruppen. <br> VMList: Durch Kommas getrennte Liste mit VMs. Beispiel: _vm1, vm2, vm3_.<br> *WhatIf* überprüft die Runbooklogik ohne Ausführung.|
|AutoStop_Disable | none | Deaktiviert AutoStop-Warnungen und den Standardzeitplan.|
|AutoStop_StopVM_Child | WebHookData | Wird über das übergeordnete Runbook aufgerufen. Warnungsregeln rufen dieses Runbook auf, um den virtuellen Computer zu beenden.|
|Bootstrap_Main | none | Wird einmal zum Einrichten von Bootstrapkonfigurationen wie webhookURI verwendet, auf die normalerweise nicht über Azure Resource Manager zugegriffen werden kann. Dieses Runbook wird nach der erfolgreichen Bereitstellung automatisch entfernt.|
|ScheduledStartStop_Child | VMName <br> Aktion: Starten oder Beenden <br> ResourceGroupName | Wird über das übergeordnete Runbook aufgerufen. Führt für den geplanten Beendigungsvorgang eine Aktion zum Starten oder Beenden aus.|
|ScheduledStartStop_Parent | Aktion: Starten oder Beenden <br>VMList <br> WhatIf: „true“ oder „false“ | Diese Einstellung wirkt sich auf alle virtuellen Computer des Abonnements aus. Bearbeiten Sie **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupNames**, damit die Ausführung nur für diese Zielressourcengruppen erfolgt. Sie können zudem bestimmte VMs ausschließen, indem Sie die Variable **External_ExcludeVMNames** aktualisieren.<br> VMList: Durch Kommas getrennte Liste mit VMs. Beispiel: _vm1, vm2, vm3_.<br> _WhatIf_ überprüft die Runbooklogik ohne Ausführung.|
|SequencedStartStop_Parent | Aktion: Starten oder Beenden <br> WhatIf: „true“ oder „false“<br>VMList| Erstellen Sie auf jeder VM, für die Sie die Aktivität zum Starten/Beenden verwenden möchten, Tags mit den Namen **sequencestart** und **sequencestop**. Bei diesen Tagnamen wird zwischen Groß- und Kleinschreibung unterschieden. Der Wert des Tags muss eine positive ganze Zahl (1, 2, 3) sein, die der Reihenfolge entspricht, in der das Starten oder Beenden durchgeführt werden soll. <br> VMList: Durch Kommas getrennte Liste mit VMs. Beispiel: _vm1, vm2, vm3_. <br> _WhatIf_ überprüft die Runbooklogik ohne Ausführung. <br> **Hinweis**: VMs müssen sich in Ressourcengruppen befinden, die in Azure Automation-Variablen als „External_Start_ResourceGroupNames“, „External_Stop_ResourceGroupNames“ und „External_ExcludeVMNames“ definiert sind. Diese müssen über die entsprechenden Tags verfügen, damit Aktionen wirksam werden.|

### <a name="variables"></a>Variables

In der folgenden Tabelle sind die in Ihrem Automation-Konto erstellten Variablen aufgeführt. Ändern Sie nur Variablen, die über das Präfix **External** verfügen. Wenn Sie Variablen mit dem Präfix **Internal** ändern, hat dies unerwünschte Auswirkungen.

|Variable | BESCHREIBUNG|
|---------|------------|
|External_AutoStop_Condition | Der bedingte Operator, der zum Konfigurieren der Bedingung erforderlich ist, bevor eine Warnung ausgelöst wird. Zulässige Werte sind **GreaterThan**, **GreaterThanOrEqual**, **LessThan** und **LessThanOrEqual**.|
|External_AutoStop_Description | Die Warnung zum Beenden des virtuellen Computers, wenn der CPU-Prozentsatz den Schwellenwert überschreitet.|
|External_AutoStop_MetricName | Der Name der Leistungsmetrik, für die die Azure-Warnungsregel konfiguriert werden muss.|
|External_AutoStop_Threshold | Der Schwellenwert für die Azure-Warnungsregel, der in der Variablen _External_AutoStop_MetricName_ angegeben ist. Prozentwerte können zwischen 1 und 100 liegen.|
|External_AutoStop_TimeAggregationOperator | Der Zeitaggregationsoperator, der auf die ausgewählte Fenstergröße angewendet wird, um die Bedingung auszuwerten. Zulässige Werte sind **Average**, **Minimum**, **Maximum**, **Total** und **Last**.|
|External_AutoStop_TimeWindow | Die Größe des Fensters, in dem Azure ausgewählte Metriken zum Auslösen einer Warnung analysiert. Für diesen Parameter können Zeiträume eingegeben werden. Mögliche Werte reichen von 5 Minuten bis 6 Stunden.|
|External_EnableClassicVMs| Gibt an, ob die Lösung klassische virtuelle Computer als Ziel verwendet. Der Standardwert lautet „True“. Diese Variable sollte bei CSP-Abonnements auf „False“ festgelegt werden.|
|External_ExcludeVMNames | Geben Sie die Namen der auszuschließenden VMs ein, und trennen Sie Namen durch Kommas ohne Leerzeichen. Dies ist auf 140 virtuelle Computer beschränkt. Wenn Sie dieser durch Trennzeichen getrennten Liste mehr als 140 VMs hinzufügen, kann es bei VMs, für die Ausschluss festgelegt ist, zu unbeabsichtigtem Starten oder Beenden kommen.|
|External_Start_ResourceGroupNames | Gibt eine oder mehrere Zielressourcengruppen, deren Werte durch Kommas getrennt sind, für Startaktionen an.|
|External_Stop_ResourceGroupNames | Gibt eine oder mehrere Zielressourcengruppen, deren Werte durch Kommas getrennt sind, für Beendigungsaktionen an.|
|Internal_AutomationAccountName | Gibt den Namen des Automation-Kontos an.|
|Internal_AutoSnooze_WebhookUri | Gibt den Webhook-URI an, der für das AutoStop-Szenario aufgerufen wird.|
|Internal_AzureSubscriptionId | Gibt die Azure-Abonnement-ID an.|
|Internal_ResourceGroupName | Gibt den Ressourcengruppennamen des Automation-Kontos an.|

In allen Szenarien müssen die Variablen **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** und **External_ExcludeVMNames** für die Ziel-VMs angegeben werden, mit Ausnahme der Angabe einer durch Kommas getrennten Liste von VMs für die Runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** und **ScheduledStartStop_Parent**. Dies bedeutet, dass sich die VMs in Zielressourcengruppen befinden müssen, damit Aktionen zum Starten und Beenden durchgeführt werden. Die Logik ähnelt der von Azure-Richtlinien, da Sie das Abonnement oder die Ressourcengruppe als Ziel verwenden können und neu erstellte VMs Aktionen erben. Hierdurch wird vermieden, dass für jeden virtuellen Computer jeweils ein separater Zeitplan gepflegt werden muss und dass Aktionen zum Starten und Beenden für die Skalierung verwaltet werden müssen.

### <a name="schedules"></a>Zeitpläne

In der folgenden Tabelle sind die einzelnen in Ihrem Automation-Konto erstellten Standardzeitpläne aufgeführt. Sie können sie ändern oder Ihre eigenen benutzerdefinierten Zeitpläne erstellen. Standardmäßig sind alle Zeitpläne deaktiviert, mit Ausnahme von **Scheduled_StartVM** und **Scheduled_StopVM**.

Es ist nicht ratsam, alle Zeitpläne zu aktivieren, da dies zu sich überlappenden Zeitplanaktionen führen kann. Am besten ermitteln Sie, welche Optimierungen Sie ausführen möchten, und nehmen dann die entsprechenden Änderungen vor. Weitere Erläuterungen finden Sie in den Beispielszenerien im Übersichtsabschnitt.

|Zeitplanname | Frequency | BESCHREIBUNG|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Alle 8 Stunden | Führt das Runbook „AutoStop_CreateAlert_Parent“ alle acht Stunden aus. Hiermit werden dann die VM-basierten Werte unter „External_Start_ResourceGroupNames“, „External_Stop_ResourceGroupNames“ und „External_ExcludeVMNames“ in Azure Automation-Variablen beendet. Alternativ hierzu können Sie mithilfe des Parameters „VMList“ eine durch Kommas getrennte Liste mit VMs angeben.|
|Scheduled_StopVM | Benutzerdefiniert, täglich | Führt das Runbook „Scheduled_Parent“ mit dem Parameter _Stop_ jeden Tag zum angegebenen Zeitpunkt aus. Beendet automatisch alle VMs, für die die Regeln der Ressourcenvariablen erfüllt werden. Aktivieren Sie den dazugehörigen Zeitplan (**Scheduled-StartVM**).|
|Scheduled_StartVM | Benutzerdefiniert, täglich | Führt das Runbook „Scheduled_Parent“ mit dem Parameter _Start_ jeden Tag zum angegebenen Zeitpunkt aus. Startet automatisch alle VMs, die die Regeln der entsprechenden Variablen erfüllen. Aktivieren Sie den dazugehörigen Zeitplan (**Scheduled-StopVM**).|
|Sequenced-StopVM | 01:00 Uhr (UTC), jeden Freitag | Führt das Runbook „Sequenced_Parent“ mit dem Parameter _Stop_ jeden Freitag zum angegebenen Zeitpunkt aus. Beendet der Reihe nach (in aufsteigender Reihenfolge) alle VMs mit dem in den jeweiligen Variablen definierten Tag **SequenceStop**. Weitere Informationen zu Tagwerten und Ressourcenvariablen finden Sie im Abschnitt „Runbooks“. Aktivieren Sie den dazugehörigen Zeitplan (**Sequenced-StartVM**).|
|Sequenced-StartVM | 13:00 Uhr (UTC), jeden Montag | Führt das Runbook „Sequenced_Parent“ mit dem Parameter _Start_ jeden Montag zum angegebenen Zeitpunkt aus. Startet der Reihe nach (in absteigender Reihenfolge) alle VMs mit dem in den jeweiligen Variablen definierten Tag **SequenceStart**. Weitere Informationen zu Tagwerten und Ressourcenvariablen finden Sie im Abschnitt „Runbooks“. Aktivieren Sie den dazugehörigen Zeitplan (**Sequenced-StopVM**).|

## <a name="azure-monitor-logs-records"></a>Datensätze in Azure Monitor-Protokolle

Automation erstellt zwei Arten von Datensätzen im Log Analytics-Arbeitsbereich: Auftragsprotokolle und Auftragsdatenströme.

### <a name="job-logs"></a>Auftragsprotokolle

|Eigenschaft | BESCHREIBUNG|
|----------|----------|
|Caller |  Der Benutzer oder das System, von dem der Vorgang initiiert wurde. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System.|
|Category (Kategorie) | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobLogs“.|
|CorrelationId | Die GUID, bei der es sich um die Korrelations-ID des Runbookauftrags handelt.|
|JobId | Die GUID, bei der es sich um die ID des Runbookauftrags handelt.|
|operationName | Gibt den Typ des in Azure ausgeführten Vorgangs an. Für Automation lautet der Wert „Job“.|
|resourceId | Gibt den Ressourcentyp in Azure an. Für Automation ist der Wert das dem Runbook zugeordnete Automation-Konto.|
|ResourceGroup | Gibt den Namen der Ressourcengruppe des Runbookauftrags an.|
|ResourceProvider | Gibt den Azure-Dienst an, über den Sie die Ressourcen beziehen, die Sie bereitstellen und verwalten können. Für Automation lautet der Wert „Azure Automation“.|
|ResourceType | Gibt den Ressourcentyp in Azure an. Für Automation ist der Wert das dem Runbook zugeordnete Automation-Konto.|
|resultType | Der Status des Runbookauftrags. Mögliche Werte:<br>- Gestartet<br>- Beendet<br>- Ausgesetzt<br>- Fehler<br>- Erfolgreich|
|resultDescription | Beschreibt den resultierenden Zustand des Runbookauftrags. Mögliche Werte:<br>- Auftrag gestartet<br>- Fehler beim Ausführen des Auftrags<br>- Auftrag abgeschlossen|
|RunbookName | Gibt den Namen des Runbooks an.|
|SourceSystem | Gibt das Quellsystem für die gesendeten Daten an. Für Automation lautet der Wert „OpsManager“.|
|StreamType | Gibt den Typ des Ereignisses an. Mögliche Werte:<br>- Ausführlich<br>- Ausgabe<br>- Fehler<br>- Warnung|
|SubscriptionId | Gibt die Abonnement-ID des Auftrags an.
|Time | Ausführungsdatum und -uhrzeit des Runbookauftrags.|

### <a name="job-streams"></a>Auftragsdatenströme

|Eigenschaft | BESCHREIBUNG|
|----------|----------|
|Caller |  Der Benutzer oder das System, von dem der Vorgang initiiert wurde. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System.|
|Category (Kategorie) | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobStreams“.|
|JobId | Die GUID, bei der es sich um die ID des Runbookauftrags handelt.|
|operationName | Gibt den Typ des in Azure ausgeführten Vorgangs an. Für Automation lautet der Wert „Job“.|
|ResourceGroup | Gibt den Namen der Ressourcengruppe des Runbookauftrags an.|
|resourceId | Gibt die Ressourcen-ID in Azure an. Für Automation ist der Wert das dem Runbook zugeordnete Automation-Konto.|
|ResourceProvider | Gibt den Azure-Dienst an, über den Sie die Ressourcen beziehen, die Sie bereitstellen und verwalten können. Für Automation lautet der Wert „Azure Automation“.|
|ResourceType | Gibt den Ressourcentyp in Azure an. Für Automation ist der Wert das dem Runbook zugeordnete Automation-Konto.|
|resultType | Das Ergebnis des Runbookauftrags zum Zeitpunkt der Ereignisgenerierung. Ein möglicher Wert ist:<br>- InProgress|
|resultDescription | Enthält den Ausgabedatenstrom des Runbooks.|
|RunbookName | Der Name des Runbooks.|
|SourceSystem | Gibt das Quellsystem für die gesendeten Daten an. Für Automation lautet der Wert „OpsManager“.|
|StreamType | Der Typ des Auftragsstreams. Mögliche Werte:<br>- Status<br>- Ausgabe<br>- Warnung<br>- Fehler<br>- Debuggen<br>- Ausführlich|
|Time | Ausführungsdatum und -uhrzeit des Runbookauftrags.|

Wenn Sie Protokollsuchen durchführen, bei denen Kategoriedatensätze für **JobLogs** oder **JobStreams** zurückgegeben werden, können Sie die Ansicht **JobLogs** oder **JobStreams** wählen. Es werden dann mehrere Kacheln angezeigt, mit denen die von der Suche zurückgegebenen Updates zusammengefasst werden.

## <a name="sample-log-searches"></a>Beispiele für Protokollsuchen

Die folgende Tabelle enthält Beispiele für Protokollsuchen für Auftragsdatensätze, die mit dieser Lösung erfasst wurden.

|Abfragen | BESCHREIBUNG|
|----------|----------|
|Suchen nach Aufträgen für das Runbook „ScheduledStartStop_Parent“, die erfolgreich abgeschlossen wurden | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Suchen nach Aufträgen für das Runbook „SequencedStartStop_Parent“, die erfolgreich abgeschlossen wurden | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Anzeigen der Lösung

Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **ZUGEHÖRIGE RESSOURCEN** die Option **Arbeitsbereich** aus, um auf die Lösung zuzugreifen. Wählen Sie auf der Log Analytics-Seite unter **ALLGEMEIN** die Option **Lösungen** aus. Wählen Sie auf der Seite **Lösungen** in der Liste die Lösung **Start-Stop-VM [workspace]** aus.

Nach dem Auswählen der Lösung wird die Lösungsseite **Start-Stop-VM[Arbeitsbereich]** angezeigt. Hier können Sie wichtige Details überprüfen, z.B. die Kachel **StartStopVM**. Wie im Log Analytics-Arbeitsbereich auch, werden auf dieser Kachel die Anzahl und eine grafische Darstellung der Runbookaufträge für die Lösung angezeigt, die gestartet und erfolgreich abgeschlossen wurden.

![Seite der Automation-Lösung „Updateverwaltung“](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Hier können Sie eine weiter gehende Analyse der Auftragsdatensätze durchführen, indem Sie auf die Ringkachel klicken. Im Lösungsdashboard werden der Auftragsverlauf und vordefinierte Protokollsuchabfragen angezeigt. Wechseln Sie zum erweiterten Log Analytics-Portal, um basierend auf Ihren Suchabfragen eine Suche durchzuführen.

## <a name="configure-email-notifications"></a>Konfigurieren von E-Mail-Benachrichtigungen

Um E-Mail-Benachrichtigungen nach dem Bereitstellen der Projektmappe zu ändern, bearbeiten Sie die Aktionsgruppe, die während der Bereitstellung erstellt wurde.  

> [!NOTE]
> Abonnements in der Azure Government Cloud unterstützen die E-Mail-Funktionalität dieser Lösung nicht.

Navigieren Sie im Azure-Portal zu „Überwachung“ > „Aktionsgruppen“. Wählen Sie die Aktionsgruppe mit dem Titel **StartStop_VM_Notication** aus.

![Seite der Automation-Lösung „Updateverwaltung“](media/automation-solution-vm-management/azure-monitor.png)

Klicken Sie auf der Seite **StartStop_VM_Notification** unter **Details** auf **Details bearbeiten**. Dadurch wird die Seite **Email/SMS/Push/Voice** (E-Mail/SMS/Push/Sprache) geöffnet. Aktualisieren Sie die E-Mail-Adresse, und klicken Sie auf **OK**, um Ihre Änderungen zu speichern.

![Seite der Automation-Lösung „Updateverwaltung“](media/automation-solution-vm-management/change-email.png)

Alternativ können Sie der Aktionsgruppe weitere Aktionen hinzufügen. Weitere Informationen über Aktionsgruppen finden Sie unter [Aktionsgruppen](../azure-monitor/platform/action-groups.md)

Die folgende Beispiel-E-Mail wird gesendet, wenn die Lösung virtuelle Computer herunterfährt.

![Seite der Automation-Lösung „Updateverwaltung“](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Hinzufügen/Ausschließen von VMs

Die Lösung bietet die Möglichkeit, VMs hinzuzufügen, auf die die Lösung ausgerichtet ist, oder Computer gezielt von der Lösung auszuschließen.

### <a name="add-a-vm"></a>Hinzufügen eines virtuellen Computers

Es stehen einige Optionen zur Verfügung, mit denen Sie sicherstellen können, dass eine VM bei ihrer Ausführung in die Lösung zum Starten/Beenden aufgenommen wird.

* Jedes der übergeordneten [Runbooks](#runbooks) der Lösung weist einen **VMList**-Parameter auf. Sie können diesem Parameter eine durch Komma getrennte Liste von VM-Namen übergeben, wenn Sie das für Ihre Situation geeignete übergeordnete Runbook planen, und diese VMs beim Ausführen der Lösung berücksichtigt werden.

* Um mehrere VMs auszuwählen, legen Sie für **External_Start_ResourceGroupNames** und **External_Stop_ResourceGroupNames** die Namen der Ressourcengruppen fest, die die VMs enthalten, die Sie starten oder beenden möchten. Sie können diesen Wert auch auf `*` festlegen, damit die Lösung für alle Ressourcengruppen im Abonnement ausgeführt wird.

### <a name="exclude-a-vm"></a>Ausschließen eines virtuellen Computers

Wenn Sie eine VM von der Lösung ausschließen möchten, können Sie sie der Variablen **External_ExcludeVMNames** hinzufügen. Diese Variable ist eine durch Komma getrennte Liste von bestimmten VMs, die von der Lösung zum Starten/Beenden ausgeschlossen werden sollen. Diese Liste ist auf 140 virtuelle Computer beschränkt. Wenn Sie dieser durch Trennzeichen getrennten Liste mehr als 140 VMs hinzufügen, kann es bei VMs, für die Ausschluss festgelegt ist, zu unbeabsichtigtem Starten oder Beenden kommen.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändern der Zeitpläne für das Starten und Herunterfahren

Bei der Verwaltung der Zeitpläne für das Starten und Herunterfahren werden bei dieser Lösung die gleichen Schritte ausgeführt, die unter [Planen eines Runbooks in Azure Automation](automation-schedules.md) beschrieben werden. Es muss einen separaten Zeitplan für das Starten und Beenden von VMs geben.

Die Lösung kann auch so konfiguriert werden, dass VMs zu einem bestimmten Zeitpunkt beendet werden. In diesem Szenario erstellen Sie nur einen Zeitplan für **Beenden** und keinen entsprechenden Zeitplan für **Starten**. Gehen Sie hierzu wie folgt vor:

1. Vergewissern Sie sich, dass Sie die Ressourcengruppen für die zu beendenden VMs in der Variablen **External_Stop_ResourceGroupNames** hinzugefügt haben.
2. Erstellen Sie Ihren eigenen Zeitplan für den Zeitpunkt, an dem die VMs heruntergefahren werden sollen.
3. Navigieren Sie zum Runbook **ScheduledStartStop_Parent**, und klicken Sie auf **Zeitplan**. Dadurch können Sie den Zeitplan auswählen, den Sie im vorherigen Schritt erstellt haben.
4. Wählen Sie **Parameter und Ausführungseinstellungen** aus, und legen Sie den Parameter ACTION auf „Stop“ fest.
5. Klicken Sie zum Speichern der Änderungen auf **OK** .

## <a name="update-the-solution"></a>Aktualisieren der Lösung

Wenn Sie eine frühere Version dieser Lösung bereitgestellt haben, müssen Sie sie vor der Bereitstellung eines aktualisierten Release zunächst aus Ihrem Konto löschen. Führen Sie dazu die Schritte zum [Entfernen der Lösung](#remove-the-solution) und anschließend die Schritte oben zum [Bereitstellen der Lösung](#deploy-the-solution) aus.

## <a name="remove-the-solution"></a>Entfernen der Lösung

Wenn Sie die Lösung nicht mehr benötigen, können Sie sie aus dem Automation-Konto löschen. Beim Löschen der Lösung werden nur die Runbooks entfernt. Die Zeitpläne oder Variablen, die beim Hinzufügen der Lösung erstellt wurden, werden nicht gelöscht. Diese Objekte müssen Sie manuell löschen, wenn Sie sie nicht mit anderen Runbooks verwenden.

Führen Sie die folgenden Schritte aus, um die Lösung zu löschen:

1. Wählen Sie in Ihrem Automation-Konto unter **Verwandte Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.
1. Klicken Sie auf **Zu Arbeitsbereich wechseln**.
1. Klicken Sie unter **Allgemein** auf **Lösungen**. 
1. Wählen Sie auf der Seite **Lösungen** die Lösung **Start-Stop-VM[Workspace]** aus. Wählen Sie auf der Seite **VMManagementSolution[Workspace]** im Menü die Option **Löschen**.<br><br> ![Löschen der VM-Mgmt-Lösung](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Bestätigen Sie im Fenster **Lösung löschen**, dass Sie die Lösung löschen möchten.
1. Während die Informationen überprüft werden und die Lösung gelöscht wird, können Sie den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen. Nachdem der Vorgang zum Löschen der Lösung begonnen hat, kehren Sie zur Seite **Lösungen** zurück.

Das Automation-Konto und der Log Analytics-Arbeitsbereich werden bei diesem Vorgang nicht gelöscht. Wenn Sie den Log Analytics-Arbeitsbereich nicht beibehalten möchten, müssen Sie ihn manuell löschen. Dies ist über das Azure-Portal möglich:

1. Wählen Sie auf der Startseite des Azure-Portals die Option **Log Analytics-Arbeitsbereiche** aus.
1. Wählen Sie auf der Seite **Log Analytics-Arbeitsbereiche** den Arbeitsbereich aus.
1. Wählen Sie auf der Seite mit den Einstellungen für den Arbeitsbereich die Menüoption **Löschen** aus.

Wenn Sie die Komponenten des Azure Automation-Kontos nicht behalten möchten, können Sie diese manuell löschen. Die Liste der Runbooks, Variablen und Zeitpläne, die von der Lösung erstellt wurden, finden Sie unter den [Lösungskomponenten](#solution-components).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen verschiedener Suchabfragen und zur Überprüfung der Automation-Auftragsprotokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md).
- Weitere Informationen zum Ausführen von Runbooks, zum Überwachen von Runbookaufträgen sowie andere technische Details finden Sie unter [Verfolgen eines Runbookauftrags](automation-runbook-execution.md).
- Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/platform/collect-azure-metrics-logs.md).
