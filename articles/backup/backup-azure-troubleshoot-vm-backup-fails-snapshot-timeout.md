---
title: 'Behandeln von Azure Backup-Fehlern: Bereitstellungsstatus nicht verfügbar'
description: Erfahren Sie mehr über die Symptome, Ursachen und Lösungen von Azure Backup-Fehlern in Verbindung mit dem Agent, der Erweiterung und Datenträgern.
services: backup
author: genlin
manager: cshepard
keywords: Azure Backup; VM-Agent; Netzwerkkonnektivität;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: 826a38dd80db7cd0e6e500949d2c259c3808c0a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64914943"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Behandeln von Azure Backup-Fehlern: Probleme mit dem Agent oder der Erweiterung

Dieser Artikel enthält Schritte für die Problembehandlung, mit denen Sie Azure Backup-Fehler bei der Kommunikation zwischen dem VM-Agent und der Erweiterung beheben können.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable: VM Agent unable to communicate with Azure Backup (VM-Agent kann nicht mit Azure Backup kommunizieren).

**Fehlercode**: UserErrorGuestAgentStatusUnavailable <br>
**Fehlermeldung**: VM-Agent kann nicht mit Azure Backup kommunizieren<br>

Nachdem Sie einen virtuellen Computer für den Backup-Dienst registriert und geplant haben, initiiert Backup den Auftrag, indem der Dienst mit dem VM-Agent kommuniziert, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn eine Momentaufnahme nicht ausgelöst wird, kann dies zu einem Fehler bei der Sicherung führen. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:<br>
**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**Ursache 4: [Die Sicherungserweiterung wird nicht aktualisiert oder geladen](#the-backup-extension-fails-to-update-or-load)**  
**Ursache 5: [Die VM kann nicht auf das Internet zugreifen](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError: Could not communicate with the VM agent for snapshot status (Kommunikation mit dem VM-Agent für Momentaufnahmestatus nicht möglich).

**Fehlercode**: GuestAgentSnapshotTaskStatusError<br>
**Fehlermeldung**: Keine Kommunikation mit dem VM-Agent zum Abrufen des Momentaufnahmestatus möglich <br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup initiiert, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  
**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Die VM kann nicht auf das Internet zugreifen](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached: The Restore Point collection max limit has reached (Maximale Grenze der Wiederherstellungspunktsammlung wurde erreicht).

**Fehlercode**: UserErrorRpCollectionLimitReached <br>
**Fehlermeldung**: Der maximale Grenzwert für die Sammlung von Wiederherstellungspunkten wurde erreicht. <br>
* Dieses Problem kann auftreten, wenn eine Sperre für den Wiederherstellungspunkt der Ressourcengruppe besteht, die eine automatische Bereinigung des Wiederherstellungspunkts verhindert.
* Dieses Problem kann auch auftreten, wenn mehrere Sicherungen pro Tag ausgelöst werden. Zurzeit wird nur eine Sicherung pro Tag empfohlen, da die Wiederherstellungspunkte zur sofortigen Wiederherstellung ein bis fünf Tage lang entsprechend der konfigurierten Aufbewahrung von Momentaufnahmen beibehalten werden. Außerdem können nur 18 Wiederherstellungspunkte zur sofortigen Wiederherstellung gleichzeitig mit einem virtuellen Computer verknüpft werden. <br>

Empfohlene Maßnahme:<br>
Um dieses Problem zu beheben, entfernen Sie die Sperre für die Ressourcengruppe der VM, und wiederholen Sie den Vorgang, um die Bereinigung auszulösen.
> [!NOTE]
> Der Backup-Dienst erstellt eine separate Ressourcengruppe neben der Ressourcengruppe des virtuellen Computers zum Speichern der Wiederherstellungspunktsammlung. Kunden sollten die für die Verwendung durch den Backup-Dienst erstellte Ressourcengruppe nicht sperren. Das Namensformat der vom Backup-Dienst erstellten Ressourcengruppe lautet: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Schritt 1: [Entfernen der Sperre von der Wiederherstellungspunkt-Ressourcengruppe](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Schritt 2: [Bereinigen der Wiederherstellungspunktsammlung](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured: Backup verfügt nicht über ausreichende Berechtigungen für den Schlüsseltresor zur Sicherung verschlüsselter virtueller Computer.

**Fehlercode**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fehlermeldung**: Backup verfügt nicht über ausreichende Berechtigungen für den Schlüsseltresor zur Sicherung verschlüsselter virtueller Computer. <br>

Damit der Sicherungsvorgang auf verschlüsselten virtuellen Computern erfolgreich ist, benötigt er Berechtigungen zum Zugriff auf den Schlüsseltresor. Dies kann über das [Azure-Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) oder mithilfe von [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection) erfolgen.

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork: Snapshot operation failed due to no network connectivity on the virtual machine (Fehler beim Momentaufnahmevorgang aufgrund fehlender Netzwerkkonnektivität auf dem virtuellen Computer).

**Fehlercode**: ExtensionSnapshotFailedNoNetwork<br>
**Fehlermeldung**: Fehler beim Momentaufnahmevorgang aufgrund fehlender Netzwerkkonnektivität auf dem virtuellen Computer<br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup initiiert, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:    
**Ursache 1: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Ursache 2: [Die Sicherungserweiterung wird nicht aktualisiert oder geladen](#the-backup-extension-fails-to-update-or-load)**  
**Ursache 3: [Die VM kann nicht auf das Internet zugreifen](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks: VMSnapshot extension operation failed

**Fehlercode**: ExtentionOperationFailedForManagedDisks <br>
**Fehlermeldung**: Fehler beim Vorgang der VMSnapshot-Erweiterung<br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup initiiert, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  
**Ursache 1: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Ursache 2: [Die Sicherungserweiterung wird nicht aktualisiert oder geladen](#the-backup-extension-fails-to-update-or-load)**  
**Ursache 3: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Ursache 4: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2: Backup fails, with an internal error (Interner Fehler bei der Sicherung).

**Fehlercode**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fehlermeldung**: Interner Fehler bei der Sicherung – versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. <br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup initiiert, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  
**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Ursache 4: [Die Sicherungserweiterung wird nicht aktualisiert oder geladen](#the-backup-extension-fails-to-update-or-load)**  
**Ursache 5: Der Backup-Dienst ist aufgrund einer Ressourcengruppensperre nicht berechtigt, die alten Wiederherstellungspunkte zu löschen.** <br>
**Ursache 6: [Die VM kann nicht auf das Internet zugreifen](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize: Azure Backup unterstützt derzeit keine Datenträgergrößen von über 4.095 GB

**Fehlercode**: UserErrorUnsupportedDiskSize <br>
**Fehlermeldung**: Azure Backup unterstützt derzeit keine Datenträgergrößen von über 4.095 GB <br>

Beim Sichern von virtuellen Computern mit einer Datenträgergröße von über 4.095 GB können Fehler während des Sicherungsvorgangs auftreten. Unterstützung für große Datenträger ist bald verfügbar.  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress: Sicherung kann nicht initiiert werden, da derzeit ein anderer Sicherungsvorgang ausgeführt wird.

**Fehlercode**: UserErrorBackupOperationInProgress <br>
**Fehlermeldung**: Sicherung kann nicht initiiert werden, da derzeit ein anderer Sicherungsvorgang ausgeführt wird.<br>

Bei Ihrem zuletzt ausgeführten Sicherungsauftrag ist ein Fehler aufgetreten, weil gerade ein vorhandener Sicherungsauftrag ausgeführt wird. Es ist nicht möglich, einen neuen Sicherungsauftrag zu starten, bevor der aktuelle Auftrag abgeschlossen ist. Stellen Sie sicher, dass der derzeit ausgeführte Sicherungsvorgang abgeschlossen wurde, bevor Sie weitere Sicherungsvorgänge auslösen oder planen. Um den Status von Sicherungsaufträgen zu überprüfen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich am Azure-Portal an, und klicken Sie auf **Alle Dienste**. Geben Sie „Recovery Services“ ein, und klicken Sie auf **Recovery Services-Tresore**. Die Liste mit den Recovery Services-Tresoren wird angezeigt.
2. Wählen Sie in der Liste mit den Recovery Services-Tresoren einen Tresor aus, für den die Sicherung konfiguriert ist.
3. Klicken Sie im Tresordashboard-Menü auf **Sicherungsaufträge**, um alle Sicherungsaufträge anzuzeigen.

    * Falls gerade ein Sicherungsauftrag ausgeführt wird, müssen Sie auf den Abschluss warten oder den Auftrag abbrechen.
        * Klicken Sie zum Abbrechen des Sicherungsauftrags mit der rechten Maustaste darauf, und klicken Sie dann auf **Abbrechen**, oder verwenden Sie [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Wenn Sie die Sicherung in einem anderen Tresor neu konfiguriert haben, sollten Sie sicherstellen, dass im alten Tresor keine Sicherungsaufträge ausgeführt werden. Wenn ein Sicherungsauftrag vorhanden ist, brechen Sie ihn ab.
        * Klicken Sie zum Abbrechen des Sicherungsauftrags mit der rechten Maustaste darauf, und klicken Sie dann auf **Abbrechen**, oder verwenden Sie [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
4. Führen Sie den Sicherungsvorgang erneut durch.

Wenn der geplante Sicherungsvorgang länger mit der nächsten Sicherungskonfiguration in Konflikt steht, lesen Sie [Bewährte Methoden](backup-azure-vms-introduction.md#best-practices), [Backupleistung](backup-azure-vms-introduction.md#backup-performance) und [Aspekte bei der Wiederherstellung](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Ursachen und Lösungen

### <a name="the-vm-has-no-internet-access"></a>Der virtuelle Computer verfügt nicht über Internetzugriff
Gemäß Bereitstellungsanforderung verfügt der virtuelle Computer nicht über Internetzugriff. Möglicherweise gelten auch Einschränkungen für den Computer, die einen Zugriff auf die Azure-Infrastruktur verhindern.

Die Sicherungserweiterung muss eine Verbindung mit öffentlichen Azure-IP-Adressen herstellen können, damit sie richtig funktioniert. Die Erweiterung sendet Befehle an einen Azure Storage-Endpunkt (HTTPs-URL), um die Momentaufnahmen des virtuellen Computers zu verwalten. Wenn die Erweiterung keinen Zugriff auf das öffentliche Internet hat, tritt bei der Sicherung letztendlich ein Fehler auf.

####  <a name="solution"></a>Lösung
Informationen zum Beheben des Netzwerkproblems finden Sie unter [Herstellen der Netzwerkverbindung](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Der Agent ist auf dem virtuellen Computer installiert, reagiert aber nicht (bei virtuellen Windows-Computern)

#### <a name="solution"></a>Lösung
Der VM-Agent wurde möglicherweise beschädigt, oder der Dienst wurde angehalten. Durch Neuinstallation des VM-Agents erhalten Sie die neueste Version. Dadurch wird auch die Kommunikation mit dem Dienst neu gestartet.

1. Ermitteln Sie, ob der Microsoft Azure-Gast-Agent-Dienst in den VM-Diensten ausgeführt wird (services.msc). Starten Sie den Microsoft Azure-Gast-Agent-Dienst neu, und initiieren Sie die Sicherung.    
2. Wenn der Microsoft Azure-Gast-Agent-Dienst in den Diensten nicht angezeigt wird, wechseln Sie in der Systemsteuerung zu **Programme und Funktionen**, um zu ermitteln, ob der Microsoft Azure-Gast-Agent-Dienst installiert ist.
4. Wenn der Microsoft Azure-Gast-Agent unter **Programme und Funktionen** angezeigt wird, deinstallieren Sie ihn.
5. Laden Sie die [aktuelle Version der Agent-MSI-Datei](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie sie. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
6. Überprüfen Sie, ob der Microsoft Azure-Gast-Agent-Dienst in den Diensten angezeigt wird.
7. Führen Sie eine bedarfsgesteuerten Sicherung aus:
    * Wählen Sie im Portal die Option **Jetzt sichern** aus.

Überprüfen Sie auch, ob [Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) auf dem virtuellen Computer installiert ist. .NET 4.5 ist für die Kommunikation des VM-Agents mit dem Dienst erforderlich.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Der auf dem virtuellen Computer installierte Agent ist veraltet (bei virtuellen Linux-Computern).

#### <a name="solution"></a>Lösung
Die meisten Fehler im Zusammenhang mit Agents oder Erweiterungen bei virtuellen Linux-Computern werden durch Probleme verursacht, die einen veralteten VM-Agent betreffen. Befolgen Sie diese allgemeinen Richtlinien, um dieses Problem zu beheben:

1. Folgen Sie den Anweisungen unter [Aktualisieren des Linux-VM-Agents ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Wir *empfehlen dringend*, den Agent ausschließlich über ein Verteilungsrepository zu aktualisieren. Wir raten davon ab, den Agent-Code direkt von GitHub herunterzuladen und die Aktualisierung durchzuführen. Falls der aktuelle Agent für Ihre Distribution nicht verfügbar ist, können Sie sich an den zuständigen Support wenden, um Informationen zur Installation zu erhalten. Eine Prüfung auf den aktuellen Agent können Sie auf der Seite für den [Windows Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) im GitHub-Repository durchführen.

2. Stellen Sie mit dem folgenden Befehl sicher, dass der Azure-Agent auf dem virtuellen Computer ausgeführt wird: `ps -e`

   Wenn der Prozess nicht ausgeführt wird, starten Sie ihn mit den folgenden Befehlen neu:

   * Für Ubuntu: `service walinuxagent start`
   * Für andere Distributionen: `service waagent start`

3. [Konfigurieren Sie den Agent für den automatischen Neustart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Führen Sie eine neue Testsicherung aus. Beziehen Sie die folgenden Protokolle vom virtuellen Computer, falls der Fehler weiterhin auftritt:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Führen Sie die folgenden Schritte aus, falls die ausführliche Protokollierung für waagent erforderlich ist:

1. Suchen Sie in der Datei „/etc/waagent.conf“ nach der folgenden Zeile: **Enable verbose logging (y|n)**
2. Ändern Sie den Wert für **Logs.Verbose** von *n* in *y*.
3. Speichern Sie die Änderung, und starten Sie waagent neu, indem Sie die weiter oben in diesem Abschnitt beschriebenen Schritte ausführen.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden
Die VM-Sicherung basiert auf dem Ausführen eines Momentaufnahmenbefehls für das zugrunde liegende Speicherkonto. Bei der Sicherung können Fehler auftreten, weil kein Zugriff auf das Speicherkonto besteht oder weil sich der Momentaufnahmetask verzögert.

#### <a name="solution"></a>Lösung
Die folgenden Umstände können zu Fehlern bei Momentaufnahmetasks führen:

| Ursache | Lösung |
| --- | --- |
| Der VM-Status wird falsch gemeldet, weil der virtuelle Computer im Remotedesktopprotokoll (RDP) heruntergefahren ist. | Wenn Sie den virtuellen Computer im Remotedesktopprotokoll herunterfahren, überprüfen Sie im Portal, ob der VM-Status richtig angezeigt wird. Falls nicht, fahren Sie den virtuellen Computer im Portal mithilfe der Option **Herunterfahren** auf dem VM-Dashboard herunter. |
| Der virtuelle Computer kann die Host- oder Fabric-Adresse nicht aus DHCP abrufen. | Für die VM-Sicherung mithilfe von IaaS muss im Gastbetriebssystem die DHCP-Option aktiviert sein. Wenn der virtuelle Computer die Host- oder Fabric-Adresse nicht aus DHCP-Antwort 245 abrufen kann, können keine Erweiterungen heruntergeladen oder ausgeführt werden. Wenn Sie eine statische private IP-Adresse benötigen, sollten Sie diese im **Azure-Portal** oder mithilfe von **PowerShell** konfigurieren und sicherstellen, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist. [Hier erfahren Sie mehr](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) über das Einrichten einer statischen IP-Adresse mit PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Die Sicherungserweiterung wird nicht aktualisiert oder geladen.
Wenn Erweiterungen nicht geladen werden können, tritt bei der Sicherung ein Fehler auf, weil keine Momentaufnahme erstellt werden kann.

#### <a name="solution"></a>Lösung

Deinstallieren Sie die Erweiterung, um das erneute Laden der VMSnapshot-Erweiterung zu erzwingen. Beim nächsten Sicherungsversuch wird die Erweiterung neu geladen.

So deinstallieren Sie die Erweiterung:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu dem virtuellen Computer, auf dem der Sicherungsfehler auftritt.
2. Wählen Sie **Settings**aus.
3. Wählen Sie **Erweiterungen**.
4. Wählen Sie **VMSnapshot-Erweiterung** aus.
5. Wählen Sie **Deinstallieren**.

Wenn die VMSnapshot-Erweiterung im Azure-Portal nicht angezeigt wird, [aktualisieren Sie den Azure-Linux-Agent](../virtual-machines/linux/update-agent.md) und führen Sie dann die Sicherung aus.

Diese Schritte bewirken, dass die Erweiterung während der nächsten Sicherung neu installiert wird.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Entfernen der Sperre von der Wiederherstellungspunkt-Ressourcengruppe
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zur Option **Alle Ressourcen**, und wählen Sie die Wiederherstellungspunktsammlungs-Ressourcengruppe im folgenden Format aus: AzureBackupRG_`<Geo>`_`<number>`.
3. Wählen Sie im Abschnitt **Einstellungen** die Option **Sperren** aus, um die Sperren anzuzeigen.
4. Um die Sperre zu entfernen, wählen Sie die Auslassungspunkte aus, und klicken Sie dann auf **Löschen**.

    ![Löschen der Sperre](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Bereinigen der Wiederherstellungspunktsammlung
Nach dem Entfernen der Sperre müssen die Wiederherstellungspunkte bereinigt werden. Um die Wiederherstellungspunkte zu bereinigen, verwenden Sie eine der folgenden Methoden:<br>
* [Bereinigen der Wiederherstellungspunktsammlung durch Ausführen einer Ad-hoc-Sicherung](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Bereinigen der Wiederherstellungspunktsammlung über das Azure-Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Bereinigen der Wiederherstellungspunktsammlung durch Ausführen einer Ad-hoc-Sicherung
Nach dem Entfernen der Sperre lösen Sie eine Ad-hoc-/manuelle Sicherung aus. Dadurch wird sichergestellt, dass die Wiederherstellungspunkte automatisch bereinigt werden. Es ist davon auszugehen, dass dieser Ad-hoc-/manuelle Vorgang beim ersten Mal fehlschlägt. Er gewährleistet jedoch eine automatische Bereinigung anstelle des manuellen Löschens von Wiederherstellungspunkten. Nach der Bereinigung sollte die nächste geplante Sicherung erfolgreich sein.

> [!NOTE]
> Die automatische Bereinigung erfolgt einige Stunden nach dem Auslösen der Ad-hoc-/manuellen Sicherung. Wenn bei der geplanten Sicherung weiterhin ein Fehler auftritt, löschen Sie die Wiederherstellungspunktsammlung manuell, indem Sie die [hier](#clean-up-restore-point-collection-from-azure-portal) aufgeführten Schritte verwenden.

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Bereinigen der Wiederherstellungspunktsammlung über das Azure-Portal <br>

Um die Wiederherstellungspunktsammlung, die aufgrund der Sperre der Ressourcengruppe nicht bereinigt wird, manuell zu bereinigen, führen Sie die folgenden Schritte aus:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im Menü **Hub** auf **Alle Ressourcen**, und wählen Sie die Ressourcengruppe mit dem folgenden Format AzureBackupRG_`<Geo>`_`<number>` aus, in der sich Ihr virtueller Computer befindet.

    ![Löschen der Sperre](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicken Sie auf die Ressourcengruppe. Das Blatt **Übersicht** wird angezeigt.
4. Wählen Sie die Option **Ausgeblendete Typen anzeigen** aus, um alle ausgeblendeten Ressourcen anzuzeigen. Wählen Sie die Wiederherstellungspunktsammlungen mit dem folgenden Format aus: AzureBackupRG_`<VMName>`_`<number>`.

    ![Löschen der Sperre](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klicken Sie auf **Löschen**, um die Wiederherstellungspunktsammlung zu bereinigen.
6. Wiederholen Sie den Sicherungsvorgang erneut.

> [!NOTE]
 >Wenn die Ressource (RP-Sammlung) eine große Anzahl Wiederherstellungspunkte aufweist, kann beim Löschen über das Portal eine Zeitüberschreitung und damit ein Fehler auftreten. Dies ist ein bekanntes CRP-Problem, bei dem nicht alle Wiederherstellungspunkte in der veranschlagten gelöscht werden und ein Timeout des Vorgangs eintritt; allerdings gelingt der Löschvorgang normalerweise nach 2 oder 3 Wiederholungsversuchen.
