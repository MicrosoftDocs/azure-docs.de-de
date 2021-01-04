---
title: Verwenden von Diagnoseeinstellungen für Recovery Services-Tresore
description: In diesem Artikel wird beschrieben, wie die alten und neuen Diagnoseereignisse für Azure Backup verwendet werden.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 3d10053bae5148f33dba6d1207a81bdb16c37577
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002884"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Verwenden von Diagnoseeinstellungen für Recovery Services-Tresore

Azure Backup sendet Diagnoseereignisse, die gesammelt und dann für die Analyse, Warnung und Berichterstellung verwendet werden können.

Sie können Diagnoseereignisse für einen Recovery Services-Tresor im Azure-Portal konfigurieren, indem Sie zum Tresor navigieren und **Diagnoseeinstellungen** auswählen. Wenn Sie **+ Diagnoseeinstellung hinzufügen** auswählen, können Sie ein oder mehrere Diagnoseereignisse an ein Speicherkonto, einen Event Hub, oder einen Log Analytics-Arbeitsbereich senden.

![Bereich „Diagnoseeinstellungen“](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Für Azure Backup-Benutzer verfügbare Diagnoseereignisse

Azure Backup stellt die folgenden Diagnoseereignisse bereit. Jedes Ereignis bietet ausführliche Daten zu einer bestimmten Reihe sicherungsbezogener Artefakte:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Wenn Sie das [Legacy-Ereignis](#legacy-event) AzureBackupReport weiterhin verwenden, empfiehlt es sich, zu den oben beschriebenen Ereignissen zu wechseln.

Weitere Informationen finden Sie unter [Datenmodell für Azure Backup-Diagnoseereignisse](./backup-azure-reports-data-model.md).

Daten für diese Ereignisse können an ein Speicherkonto, einen Log Analytics-Arbeitsbereich oder Event Hub gesendet werden. Wenn Sie diese Daten an einen Log Analytics-Arbeitsbereich senden, wählen Sie auf dem Bildschirm **Diagnoseeinstellungen** den Schalter **Ressourcenspezifisch** aus. Weitere Informationen finden Sie in den folgenden Abschnitten.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Verwenden von Diagnoseeinstellungen mit Log Analytics

Azure Backup ermöglicht nun für die Sicherung das Übertragen von Tresordiagnosedaten in dedizierte Log Analytics-Tabellen. Diese Tabellen werden als [ressourcenspezifische Tabellen](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) bezeichnet.

So senden Sie Tresordiagnosedaten an Log Analytics

1. Navigieren Sie zum Tresor, und wählen Sie **Diagnoseeinstellungen** aus. Wählen Sie **+ Diagnoseeinstellung hinzufügen** aus.
1. Geben Sie für die Diagnoseeinstellung einen Namen ein.
1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**, und wählen Sie einen Log Analytics-Arbeitsbereich aus.
1. Wählen Sie den Umschalter **Ressourcenspezifisch** und die folgenden sechs Ereignisse aus: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** und **AddonAzureBackupProtectedInstance**.
1. Wählen Sie **Speichern** aus.

   ![Ressourcenspezifischer Modus](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Sobald Daten im Log Analytics-Arbeitsbereich eingehen, werden im Arbeitsbereich für jedes dieser Ereignisse dedizierte Tabellen erstellt. Sie können jede dieser Tabellen direkt abfragen. Sie können bei Bedarf für diese Tabellen Joins oder Vereinigungen durchführen.

> [!IMPORTANT]
> Die sechs Ereignisse (CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy und AddonAzureBackupStorage) werden *nur* im ressourcenspezifischen Modus in [Sicherungsberichten](./configure-reports.md) unterstützt. *Beim Versuch, Daten für diese sechs Ereignisse im Azure-Diagnosemodus zu senden, werden in Sicherungsberichten keine Daten angezeigt.*

## <a name="legacy-event"></a>Legacy-Ereignis

Bisher waren alle sicherungsbezogenen Diagnosedaten für einen Tresor in einem einzelnen Ereignis namens AzureBackupReport enthalten. Die sechs hier beschriebenen Ereignisse sind im Wesentlichen eine Aufschlüsselung aller Daten, die in AzureBackupReport enthalten sind.

Derzeit unterstützen wir das Ereignis AzureBackupReport weiterhin aus Gründen der Abwärtskompatibilität, wenn Benutzer benutzerdefinierte Abfragen für dieses Ereignis haben. Beispiele sind benutzerdefinierte Protokollwarnungen und Visualisierungen. *Es wird empfohlen, dass Sie so früh wie möglich auf die [neuen Ereignisse](#diagnostics-events-available-for-azure-backup-users) umstellen.* Die Vorteile der neuen Ereignisse:

* Vereinfachen das Arbeiten mit den Daten in Protokollabfragen
* Sorgen für eine bessere Erkennbarkeit von Schemas und ihrer Struktur
* Verbessern die Leistung sowohl hinsichtlich Erfassungslatenz als auch Abfragezeiten

*Das Legacy-Ereignis im Azure-Diagnosemodus wird in naher Zukunft als veraltet markiert. Wenn Sie sich für die neuen Ereignisse entscheiden, können Sie möglicherweise komplexe Migrationen zu einem späteren Zeitpunkt vermeiden*. Für unsere mit Log Analytics arbeitende [Berichterstellungslösung](./configure-reports.md) wird die Unterstützung von Daten aus dem Legacy-Ereignis ebenfalls eingestellt.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Schritte zum Umstieg auf die neuen Diagnoseeinstellungen für einen Log Analytics-Arbeitsbereich

1. Ermitteln Sie mithilfe des Legacy-Ereignisses, welche Tresore Daten an die Log Analytics-Arbeitsbereiche senden und zu welchen Abonnements sie gehören. Führen Sie die folgende Abfrage in allen Arbeitsbereichen aus, um diese Tresore und Abonnements zu ermitteln.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Hier sehen Sie einen Screenshot der Abfrage, die in einem der Arbeitsbereiche ausgeführt wird:

    ![Arbeitsbereich und Abfrage](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Verwenden Sie die in Azure Backup [integrierten Azure Policy-Definitionen](./azure-policy-configure-diagnostics.md), um für alle Tresore in einem angegebenen Geltungsbereich eine neue Diagnoseeinstellung hinzuzufügen. Diese Richtlinie fügt Tresoren, die entweder nicht über eine Diagnoseeinstellung verfügen oder nur eine Legacy-Diagnoseeinstellung aufweisen, eine neue Diagnoseeinstellung hinzu. Diese Richtlinie kann jeweils nur einem gesamten Abonnement oder einer Ressourcengruppe zugewiesen werden. Sie benötigen für jedes Abonnement, dem die Richtlinie zugewiesen ist, die Zugriffsberechtigung „Besitzer“.

Sie können separate Diagnoseeinstellungen für AzureBackupReport und die sechs neuen Ereignisse verwenden, bis Sie alle benutzerdefinierten Abfragen migriert haben, um Daten aus den neuen Tabellen zu verwenden. Die folgende Abbildung zeigt ein Beispiel eines Tresors mit zwei Diagnoseeinstellungen. Die erste Einstellung mit dem Namen **Setting1** sendet Daten des AzureBackupReport-Ereignisses an einen Log Analytics-Arbeitsbereich im Azure-Diagnosemodus. Die zweite Einstellung mit dem Namen **Setting2** sendet Daten der sechs neuen Azure Backup-Ereignisse an einen Log Analytics-Arbeitsbereich im ressourcenspezifischen Modus.

![Beide Einstellungen](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Das AzureBackupReport-Ereignis wird *nur* im Azure-Diagnosemodus unterstützt. *Beim Versuch, Daten für dieses Ereignis im ressourcenspezifischen Modus zu senden, gehen keine Daten im Log Analytics-Arbeitsbereich ein.*

> [!NOTE]
> Der Umschalter für **Azure-Diagnose** oder **Ressourcenspezifisch** wird nur angezeigt, wenn der Benutzer die Option **An Log Analytics senden** aktiviert. Um Daten an ein Speicherkonto oder einen Event Hub zu senden, wählt der Benutzer das gewünschte Ziel aus und aktiviert die Kontrollkästchen für jedes der gewünschten Ereignisse, und zwar ohne weitere Eingaben. Auch hier wird empfohlen, das Legacy-Ereignis „AzureBackupReport“ zukünftig nicht auszuwählen.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Senden von Azure Site Recovery-Ereignissen an Log Analytics

Azure Backup- und Azure Site Recovery-Ereignisse werden vom selben Recovery Services-Tresor gesendet. Azure Site Recovery ist derzeit nicht für ressourcenspezifische Tabellen verfügbar. Benutzer, die Azure Site Recovery-Ereignisse an Log Analytics senden möchten, werden angewiesen, *nur* den Azure-Diagnosemodus zu verwenden, wie in der Abbildung dargestellt. *Wenn Sie für die Azure Site Recovery-Ereignisse den ressourcenspezifischen Modus auswählen, wird verhindert, dass die erforderlichen Daten an den Log Analytics-Arbeitsbereich gesendet werden.*

![Site Recovery-Ereignisse](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Zusammenfassung:

* Wenn Sie die Log Analytics-Diagnose bereits mit der Azure-Diagnose eingerichtet haben und zusätzlich über benutzerdefinierte Abfragen verfügen, *behalten Sie diese Einstellung bei*, bis Sie Ihre Abfragen so migrieren, dass Daten aus den neuen Ereignissen verwendet werden.
* Wenn Sie, wie von uns empfohlen, neue Tabellen nutzen möchten, erstellen Sie eine **neue** Diagnoseeinstellung. Wählen Sie **Ressourcenspezifisch** und die sechs neuen Ereignisse aus.
* Wenn Sie derzeit Azure Site Recovery-Ereignisse an Log Analytics senden, wählen Sie für diese Ereignisse *nicht* den ressourcenspezifischen Modus aus. Andernfalls werden die Daten für diese Ereignisse nicht in Ihren Log Analytics-Arbeitsbereich übertragen. Erstellen Sie stattdessen eine zusätzliche Diagnoseeinstellung. Wählen Sie **Azure-Diagnose** und die entsprechenden Azure Site Recovery-Ereignisse aus.

Die folgende Abbildung zeigt ein Beispiel eines Benutzers mit drei Diagnoseeinstellungen für einen Tresor. Die erste Einstellung mit dem Namen **Setting1** sendet Daten aus einem AzureBackupReport-Ereignis an einen Log Analytics-Arbeitsbereich im Azure-Diagnosemodus. Die zweite Einstellung mit dem Namen **Setting2** sendet Daten aus den sechs neuen Azure Backup-Ereignissen an einen Log Analytics-Arbeitsbereich im ressourcenspezifischen Modus. Die dritte Einstellung mit dem Namen **Setting3** sendet Daten aus den Azure Site Recovery-Ereignissen an einen Log Analytics-Arbeitsbereich im Azure-Diagnosemodus.

![Drei Einstellungen](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Nächste Schritte

[Informationen zum Log Analytics-Datenmodell für Diagnoseereignisse](./backup-azure-reports-data-model.md)
