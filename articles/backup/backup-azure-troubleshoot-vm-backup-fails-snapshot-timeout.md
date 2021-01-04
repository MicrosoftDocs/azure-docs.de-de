---
title: Beheben von Probleme mit Agents und Erweiterungen
description: Erfahren Sie mehr über die Symptome, Ursachen und Lösungen von Azure Backup-Fehlern in Verbindung mit dem Agent, der Erweiterung und Datenträgern.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: dd6f4d7884b120d2f8b5ea3f3ccb8d5385dd0880
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377104"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Behandeln von Azure Backup-Fehlern: Probleme mit dem Agent oder der Erweiterung

Dieser Artikel enthält Schritte für die Problembehandlung, mit denen Sie Azure Backup-Fehler bei der Kommunikation zwischen dem VM-Agent und der Erweiterung beheben können.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Ausführliche Anleitung zum Beheben von Sicherungsfehlern

Die häufigsten Sicherungsfehler können mithilfe der unten aufgeführten Schritte selbst gelöst werden:

### <a name="step-1-check-azure-vm-health"></a>Schritt 1: Überprüfen der Integrität der Azure-VM

- **Sicherstellen, dass der Bereitstellungsstatus der Azure-VM „Wird ausgeführt“ ist:** Wenn der [VM-Bereitstellungsstatus](../virtual-machines/states-lifecycle.md#provisioning-states) dem Zustand **„Beendet“, „Zuweisung aufgehoben“ oder „Aktualisiert“** entspricht, wird der Sicherungsvorgang beeinträchtigt. Öffnen Sie das *Azure-Portal > VM > Übersicht*, und überprüfen Sie den VM-Status, um sicherzustellen, dass dieser **Wird ausgeführt** lautet. Wiederholen Sie anschließend den Sicherungsvorgang.
- **Überprüfen ausstehender Betriebssystemupdates oder Neustarts:** Stellen Sie sicher, dass es auf der VM keine ausstehenden Betriebssystemupdates oder Neustarts gibt.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Schritt 2: Überprüfen der Dienstintegrität des Gast-Agents auf der Azure-VM

- **Sicherstellen, dass der Gast-Agent-Dienst auf der Azure-VM gestartet wurde und aktuell ist:**
  - Auf einer Windows-VM:
    - Navigieren Sie zu **services.msc**, und stellen Sie sicher, dass der **Gast-Agent-Dienst auf der Microsoft Azure-VM** ausgeführt wird. Stellen Sie außerdem sicher, dass die [neueste Version](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) installiert ist. Weitere Informationen finden Sie unter [Der Agent ist auf dem virtuellen Computer installiert, reagiert aber nicht (bei virtuellen Windows-Computern)](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - Der Azure-VM-Agent wird standardmäßig auf allen Windows-VMs installiert, die über ein Azure Marketplace-Image über das Portal, PowerShell, die Befehlszeilenschnittstelle oder eine Azure Resource Manager-Vorlage bereitgestellt werden. Eine [manuelle Installation des Agents](../virtual-machines/extensions/agent-windows.md#manual-installation) kann erforderlich sein, wenn Sie ein benutzerdefiniertes VM-Image erstellen, das in Azure bereitgestellt wird.
    - Überprüfen Sie die Unterstützungsmatrix, um zu prüfen, ob die VM auf dem [unterstützten Windows-Betriebssystem](backup-support-matrix-iaas.md#operating-system-support-windows) ausgeführt wird.
  - Auf einer Linux-VM:
    - Indem Sie den Befehl `ps-e` ausführen, können Sie sicherstellen, dass der Gast-Agent-Dienst auf der Azure-VM ausgeführt wird. Stellen Sie außerdem sicher, dass die [neueste Version](../virtual-machines/extensions/update-linux-agent.md) installiert ist. Weitere Informationen finden Sie unter [Der auf dem virtuellen Computer installierte Agent ist veraltet (bei virtuellen Linux-Computern)](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Stellen Sie sicher, dass die [Linux-VM-Agent-Abhängigkeiten von Systempaketen](../virtual-machines/extensions/agent-linux.md#requirements) die unterstützte Konfiguration aufweisen. Beispiel: Die unterstützte Python-Version ist Version 2.6 oder höher.
    - Überprüfen Sie die Unterstützungsmatrix, um zu prüfen, ob die VM auf dem [unterstützten Linux-Betriebssystem](backup-support-matrix-iaas.md#operating-system-support-linux) ausgeführt wird.

### <a name="step-3-check-azure-vm-extension-health"></a>Schritt 3: Überprüfen der Integrität der Azure-VM-Erweiterung

- **Sicherstellen, dass alle Azure-VM-Erweiterungen den Status „Bereitstellung erfolgreich“ aufweisen:** Wenn eine Erweiterung den Status „Fehler“ aufweist, kann die Sicherung beeinträchtigt werden.
- Navigieren Sie im Azure-Portal zu *VM > Einstellungen > Erweiterungen > Status der Erweiterungen*, und überprüfen Sie, ob der Zustand aller Erweiterungen **Bereitstellung erfolgreich** lautet.
- Stellen Sie sicher, dass alle [Erweiterungsprobleme](../virtual-machines/extensions/overview.md#troubleshoot-extensions) gelöst sind, und wiederholen Sie den Sicherungsvorgang.
- **Stellen Sie sicher, dass die COM+-Systemanwendung** ausgeführt wird. Außerdem sollte der **Distributed Transaction Coordinator-Dienst** als **Netzwerkdienstkonto** ausgeführt werden. Führen Sie die Schritte in diesem Artikel aus, um [COM+- und MSDTC-Fehler zu beheben](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Schritt 4: Überprüfen der Integrität der Azure Backup-VM-Erweiterung

Azure Backup führt mithilfe der VM-Momentaufnahmenerweiterung eine anwendungskonsistente Sicherung des virtuellen Azure-Computers durch. Azure Backup installiert die Erweiterung als Teil der ersten geplanten Sicherung, die nach der Aktivierung der Sicherung ausgelöst wird.

- **Sicherstellen, dass die VMSnapshot-Erweiterung keinen fehlerhaften Status aufweist:** Führen Sie die in diesem [Abschnitt](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) aufgeführten Schritte aus, um sicherzustellen, dass die Azure Backup-Erweiterung fehlerfrei ist.

- **Überprüfen, ob die Erweiterung durch Antivirussoftware blockiert wird:** Die Ausführung von Erweiterungen kann durch bestimmte Antivirussoftware verhindert werden.
  
  Überprüfen Sie zum Zeitpunkt des Sicherungsfehlers, ob in **_Ereignisanzeige-Anwendungsprotokollen_** Protokolleinträge mit dem _*_fehlerhaften Anwendungsnamen „IaaSBcdrExtension.exe“_*_ vorhanden sind. Wenn Einträge angezeigt werden, kann es sein, dass das auf der VM konfigurierte Antivirenprogramm die Ausführung der Sicherungserweiterung einschränkt. Testen Sie dies, indem Sie die folgenden Verzeichnisse in der Antivirenkonfiguration ausschließen, und wiederholen Sie den Sicherungsvorgang.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Überprüfen, ob Netzwerkzugriff erforderlich ist**: Erweiterungspakete werden aus dem Azure Storage-Erweiterungsrepository heruntergeladen, und Uploads des Erweiterungsstatus werden in Azure Storage gepostet. [Weitere Informationen](../virtual-machines/extensions/features-windows.md#network-access)
  - Wenn Sie eine nicht unterstützte Version des Agents verwenden, müssen Sie in dieser Region den von der VM ausgehenden Zugriff auf Azure Storage zulassen.
  - Wenn Sie den Zugriff auf `168.63.129.16` mit der Gastfirewall oder einem Proxy blockiert haben, treten bei den Erweiterungen unabhängig von den oben beschriebenen Szenarios Fehler auf. Die Ports 80, 443 und 32526 sind erforderlich. [Hier finden Sie weitere Informationen](../virtual-machines/extensions/features-windows.md#network-access).

- **Sicherstellen, dass das Dynamic Host Configuration-Protokoll auf der Gast-VM aktiviert ist:** Dies ist erforderlich, damit die Host- oder Fabricadresse des Dynamic Host Configuration-Protokolls (DHCP) für die IaaS-VM-Sicherung funktioniert. Wenn Sie eine statische private IP-Adresse benötigen, sollten Sie diese im Azure-Portal oder mithilfe von PowerShell konfigurieren und sicherstellen, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist. [Hier finden Sie weitere Informationen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Sicherstellen, dass der VSS Writer-Dienst ausgeführt wird:** Führen Sie diese Schritte aus, um [Probleme mit VSS Writer zu beheben](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Berücksichtigen bewährter Methoden für Sicherungen:** Lesen Sie die [bewährten Methoden zum Aktivieren von Azure-VM-Sicherungen](backup-azure-vms-introduction.md#best-practices).
- **Überprüfen der Richtlinien für verschlüsselte Datenträger:** Wenn Sie die Sicherung für VMs mit verschlüsseltem Datenträger aktivieren, stellen Sie sicher, dass Sie alle erforderlichen Berechtigungen bereitgestellt haben. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen eines verschlüsselten virtuellen Azure-Computers](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable: VM Agent unable to communicate with Azure Backup (VM-Agent kann nicht mit Azure Backup kommunizieren).

**Fehlercode**: UserErrorGuestAgentStatusUnavailable <br>
**Fehlermeldung**: VM-Agent kann nicht mit Azure Backup kommunizieren<br>

Der Azure-VM-Agent wurde möglicherweise angehalten, ist veraltet, befindet sich in einem inkonsistenten Zustand oder ist nicht installiert. Diese Zustände verhindern, dass der Azure Backup-Dienst Momentaufnahmen auslöst.

- Navigieren Sie im Azure-Portal zu **VM** > **Einstellungen**, und stellen Sie sicher, dass auf dem Blatt **Eigenschaften** der VM-Status **Wird ausgeführt** und der Agent-Status **Bereit** lautet. Wenn der VM-Agent beendet wurde oder sich in einem inkonsistenten Zustand befindet, starten Sie den Agent neu.<br>
  - Führen Sie für virtuelle Windows-Computer die folgenden [Schritte](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) aus, um den Gast-Agent zu starten.<br>
  - Führen Sie für virtuelle Linux-Computer die folgenden [Schritte](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) aus, um den Gast-Agent zu starten.
- Navigieren Sie im Azure-Portal zu **VM > Einstellungen > Erweiterungen**, und stellen Sie sicher, dass der Zustand aller Erweiterungen **Bereitstellung erfolgreich** lautet. Führen Sie andernfalls [diese Schritte](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) aus, um das Problem zu beheben.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError: Could not communicate with the VM agent for snapshot status (Kommunikation mit dem VM-Agent für Momentaufnahmestatus nicht möglich).

**Fehlercode**: GuestAgentSnapshotTaskStatusError<br>
**Fehlermeldung**: Keine Kommunikation mit dem VM-Agent zum Abrufen des Momentaufnahmestatus möglich <br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup gestartet, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  

**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Ursache 3: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Ursache 4: [Konfigurationsoptionen für den VM-Agent sind nicht festgelegt (für virtuelle Linux-Computer)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Ursache 5: [Die Anwendungssteuerungslösung blockiert „iaasbcdrextension.exe“](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed: Der virtuelle Computer befindet sich im Zustand „Fehler bei der Bereitstellung“

**Fehlercode**: UserErrorVmProvisioningStateFailed<br>
**Fehlermeldung**: Der VM befindet sich im Zustand „Fehler bei der Bereitstellung“<br>

Dieser Fehler tritt auf, wenn einer der Erweiterungsfehler dazu führt, dass der virtuelle Computer in den Zustand „Fehler bei der Bereitstellung“ versetzt wird.<br>Navigieren Sie im Azure-Portal zu **VM > Einstellungen > Erweiterungen > Status der Erweiterungen**, und überprüfen Sie, ob der Zustand aller Erweiterungen **Bereitstellung erfolgreich** lautet. Weitere Informationen finden Sie unter [Bereitstellungszustände](../virtual-machines/states-lifecycle.md#provisioning-states).

- Wenn eine Erweiterung den Status „Fehler“ aufweist, kann die Sicherung beeinträchtigt werden. Stellen Sie sicher, dass diese Erweiterungsprobleme gelöst sind, und wiederholen Sie den Sicherungsvorgang.
- Wenn der Status der VM-Bereitstellung „Wird aktualisiert“ lautet, kann die Sicherung beeinträchtigt werden. Stellen Sie sicher, dass sie fehlerfrei ist, und wiederholen Sie den Sicherungsvorgang.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached: The Restore Point collection max limit has reached (Maximale Grenze der Wiederherstellungspunktsammlung wurde erreicht).

**Fehlercode**: UserErrorRpCollectionLimitReached <br>
**Fehlermeldung**: Der maximale Grenzwert für die Sammlung von Wiederherstellungspunkten wurde erreicht. <br>

- Dieses Problem kann auftreten, wenn eine Sperre für den Wiederherstellungspunkt der Ressourcengruppe besteht, die eine automatische Bereinigung von Wiederherstellungspunkten verhindert.
- Dieses Problem kann auch auftreten, wenn mehrere Sicherungen pro Tag ausgelöst werden. Zurzeit wird nur eine Sicherung pro Tag empfohlen, da die Wiederherstellungspunkte zur sofortigen Wiederherstellung ein bis fünf Tage lang entsprechend der konfigurierten Aufbewahrung von Momentaufnahmen beibehalten werden. Außerdem können nur 18 Wiederherstellungspunkte zur sofortigen Wiederherstellung gleichzeitig mit einem virtuellen Computer verknüpft werden. <br>
- Die Anzahl der Wiederherstellungspunkte über Wiederherstellungspunktsammlungen und Ressourcengruppen für einen virtuellen Computer darf 18 nicht überschreiten. Löschen Sie vorhandene Wiederherstellungspunkte, um einen neuen Wiederherstellungspunkt zu erstellen.

Empfohlene Maßnahme:<br>
Um dieses Problem zu beheben, entfernen Sie die Sperre für die Ressourcengruppe der VM, und wiederholen Sie den Vorgang, um die Bereinigung auszulösen.
> [!NOTE]
> Der Backup-Dienst erstellt eine separate Ressourcengruppe neben der Ressourcengruppe des virtuellen Computers zum Speichern der Wiederherstellungspunktsammlung. Sie sollten die für die Verwendung durch den Backup-Dienst erstellte Ressourcengruppe nicht sperren. Das Namensformat der vom Backup-Dienst erstellten Ressourcengruppe sieht folgendermaßen aus: AzureBackupRG_`<Geo>`_`<number>`. Beispiel: *AzureBackupRG_northeurope_1*

**Schritt 1: [Entfernen der Sperre von der Wiederherstellungspunkt-Ressourcengruppe](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Schritt 2: [Bereinigen der Wiederherstellungspunktsammlung](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured: Backup verfügt nicht über ausreichende Berechtigungen für den Schlüsseltresor zur Sicherung verschlüsselter virtueller Computer.

**Fehlercode**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fehlermeldung**: Backup verfügt nicht über ausreichende Berechtigungen für den Schlüsseltresor zur Sicherung verschlüsselter virtueller Computer. <br>

Damit der Sicherungsvorgang auf verschlüsselten virtuellen Computern erfolgreich ist, benötigt er Berechtigungen zum Zugriff auf den Schlüsseltresor. Berechtigungen können über das [Azure-Portal](./backup-azure-vms-encryption.md) oder mithilfe von [PowerShell](./backup-azure-vms-automation.md#enable-protection) festgelegt werden.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork: Snapshot operation failed due to no network connectivity on the virtual machine (Fehler beim Momentaufnahmevorgang aufgrund fehlender Netzwerkkonnektivität auf dem virtuellen Computer).

**Fehlercode**: ExtensionSnapshotFailedNoNetwork<br>
**Fehlermeldung**: Fehler beim Momentaufnahmevorgang aufgrund fehlender Netzwerkkonnektivität auf dem virtuellen Computer<br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup gestartet, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie den folgenden Problembehandlungsschritt aus, und wiederholen Sie dann den Vorgang:

**[Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks – Fehler beim Vorgang der VMSnapshot-Erweiterung

**Fehlercode**: ExtensionOperationFailedForManagedDisks <br>
**Fehlermeldung**: Fehler beim Vorgang der VMSnapshot-Erweiterung<br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup gestartet, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  
**Ursache 1: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Ursache 2: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Ursache 3: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2: Backup fails, with an internal error (Interner Fehler bei der Sicherung).

**Fehlercode**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fehlermeldung**: Interner Fehler bei der Sicherung – versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. <br>

Nachdem Sie eine VM für den Azure Backup-Dienst registriert und geplant haben, wird der Auftrag von Backup initiiert, indem die Kommunikation mit der VM-Sicherungserweiterung durchgeführt wird, um eine Zeitpunkt-Momentaufnahme zu erstellen. Jede der folgenden Bedingungen kann verhindern, dass die Momentaufnahme ausgelöst wird. Wenn die Momentaufnahme nicht ausgelöst wird, kann bei der Sicherung ein Fehler auftreten. Führen Sie die folgenden Problembehandlungsschritte in der angegebenen Reihenfolge aus, und versuchen Sie dann erneut, den Vorgang auszuführen:  
**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Ursache 4: [Der Backup-Dienst ist aufgrund einer Ressourcengruppensperre nicht berechtigt, die alten Wiederherstellungspunkte zu löschen](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize: Die konfigurierte(n) Datenträgergröße(n) wird bzw. werden von Azure Backup derzeit nicht unterstützt

**Fehlercode**: UserErrorUnsupportedDiskSize <br>
**Fehlermeldung**: Die konfigurierte(n) Datenträgergröße(n) wird/werden von Azure Backup derzeit nicht unterstützt. <br>

Wenn Sie eine VM auf einer Datenträgergröße von mehr als 32 TB sichern, könnte der Sicherungsvorgang fehlschlagen. Auch die Sicherung verschlüsselter Datenträger mit einer Größe von mehr als 4 TB wird derzeit nicht unterstützt. Stellen Sie deshalb sicher, dass die Datenträgergröße(n) kleiner oder gleich dem unterstützten Limit ist/sind, indem Sie den/die Datenträger aufteilen.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress: Sicherung kann nicht initiiert werden, da derzeit ein anderer Sicherungsvorgang ausgeführt wird.

**Fehlercode**: UserErrorBackupOperationInProgress <br>
**Fehlermeldung**: Sicherung kann nicht initiiert werden, da derzeit ein anderer Sicherungsvorgang ausgeführt wird.<br>

Bei Ihrem zuletzt ausgeführten Sicherungsauftrag ist ein Fehler aufgetreten, weil gerade ein vorhandener Sicherungsauftrag ausgeführt wird. Es ist nicht möglich, einen neuen Sicherungsauftrag zu starten, bevor der aktuelle Auftrag abgeschlossen ist. Stellen Sie sicher, dass der derzeit ausgeführte Sicherungsvorgang abgeschlossen wurde, bevor Sie weitere Sicherungsvorgänge auslösen oder planen. Führen Sie die folgenden Schritte aus, um den Status der Sicherungsaufträge zu überprüfen:

1. Melden Sie sich beim Azure-Portal an, und wählen Sie die Option **Alle Dienste** aus. Geben Sie „Recovery Services“ ein, und wählen Sie **Recovery Services-Tresore** aus. Die Liste mit den Recovery Services-Tresoren wird angezeigt.
2. Wählen Sie in der Liste mit den Recovery Services-Tresoren einen Tresor aus, bei dem die Sicherung konfiguriert wurde.
3. Wählen Sie im Dashboard-Menü des Tresorraums **Sicherungsaufträge** aus, um alle Sicherungsaufträge anzuzeigen.
   - Falls gerade ein Sicherungsauftrag ausgeführt wird, müssen Sie auf den Abschluss warten oder den Auftrag abbrechen.
     - Klicken Sie zum Abbrechen des Sicherungsauftrags mit der rechten Maustaste darauf, und wählen Sie dann **Abbrechen** aus, oder verwenden Sie [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Wenn Sie die Sicherung in einem anderen Tresor neu konfiguriert haben, sollten Sie sicherstellen, dass im alten Tresor keine Sicherungsaufträge ausgeführt werden. Wenn ein Sicherungsauftrag vorhanden ist, brechen Sie ihn ab.
     - Klicken Sie zum Abbrechen des Sicherungsauftrags mit der rechten Maustaste darauf, und wählen Sie dann **Abbrechen** aus, oder verwenden Sie [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
4. Führen Sie den Sicherungsvorgang erneut durch.

Wenn der geplante Sicherungsvorgang länger dauert und dadurch mit der nächsten Sicherungskonfiguration in Konflikt steht, lesen Sie [Bewährte Methoden](backup-azure-vms-introduction.md#best-practices), [Backupleistung](backup-azure-vms-introduction.md#backup-performance) und [Aspekte bei der Wiederherstellung](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError: Fehler bei der Sicherung. Weitere Informationen finden Sie in den Details zur Auftragsfehlermeldung.

**Fehlercode**: UserErrorCrpReportedUserError <br>
**Fehlermeldung**: Fehler bei der Sicherung. Weitere Informationen finden Sie in den Details zur Auftragsfehlermeldung.

Dieser Fehler wird von der IaaS-VM gemeldet. Um die Grundursache des Problems zu ermitteln, gehen Sie zu den Recovery Services-Tresoreinstellungen. Wählen Sie im Abschnitt **Überwachung** die Option **Sicherungsaufträge** aus, um den Status zu filtern und anzuzeigen. Wählen Sie **Fehler** aus, um die Details zur zugrunde liegenden Fehlermeldung zu überprüfen. Ergreifen Sie weitere Maßnahmen entsprechend den Empfehlungen auf der Fehlerdetailseite.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent – Sicherungsfehler: Diese VM wird nicht (aktiv) über Azure Backup geschützt.

**Fehlercode**: UserErrorBcmDatasourceNotPresent <br>
**Fehlermeldung**: Sicherungsfehler: Diese VM wird nicht (aktiv) über Azure Backup geschützt.

Überprüfen Sie, ob die angegebene VM aktiv durch Azure Backup geschützt wird (sich nicht im Zustand „Angehalten“ befindet). Starten Sie zur Behebung dieses Problem die VM neu, und versuchen Sie es noch mal.

## <a name="causes-and-solutions"></a>Ursachen und Lösungen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Der Agent ist auf dem virtuellen Computer installiert, reagiert aber nicht (bei virtuellen Windows-Computern)

#### <a name="solution-for-this-error"></a>Lösung für diesen Fehler

Der VM-Agent wurde möglicherweise beschädigt, oder der Dienst wurde angehalten. Durch Neuinstallation des VM-Agents erhalten Sie die neueste Version. Dadurch wird auch die Kommunikation mit dem Dienst neu gestartet.

1. Ermitteln Sie, ob der Microsoft Azure-Gast-Agent-Dienst in den VM-Diensten ausgeführt wird (services.msc). Starten Sie den Microsoft Azure-Gast-Agent-Dienst neu, und initiieren Sie die Sicherung.
2. Wenn der Microsoft Azure-Gast-Agent-Dienst in den Diensten nicht angezeigt wird, wechseln Sie in der Systemsteuerung zu **Programme und Funktionen**, um zu ermitteln, ob der Microsoft Azure-Gast-Agent-Dienst installiert ist.
3. Wenn der Microsoft Azure-Gast-Agent unter **Programme und Funktionen** angezeigt wird, deinstallieren Sie ihn.
4. Laden Sie die [aktuelle Version der Agent-MSI-Datei](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie sie. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
5. Überprüfen Sie, ob der Microsoft Azure-Gast-Agent-Dienst in den Diensten angezeigt wird.
6. Führen Sie eine bedarfsgesteuerten Sicherung aus:
   - Wählen Sie im Portal die Option **Jetzt sichern** aus.

Überprüfen Sie auch, ob [Microsoft .NET 4.5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) auf dem virtuellen Computer installiert ist. .NET 4.5 ist für die Kommunikation des VM-Agents mit dem Dienst erforderlich.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Der auf dem virtuellen Computer installierte Agent ist veraltet (bei virtuellen Linux-Computern).

#### <a name="solution"></a>Lösung

Die meisten Fehler im Zusammenhang mit Agents oder Erweiterungen bei virtuellen Linux-Computern werden durch Probleme verursacht, die einen veralteten VM-Agent betreffen. Befolgen Sie diese allgemeinen Richtlinien, um dieses Problem zu beheben:

1. Folgen Sie den Anweisungen unter [Aktualisieren des Linux-VM-Agents ](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Wir *empfehlen dringend*, den Agent ausschließlich über ein Verteilungsrepository zu aktualisieren. Wir raten davon ab, den Agent-Code direkt von GitHub herunterzuladen und die Aktualisierung durchzuführen. Falls der aktuelle Agent für Ihre Distribution nicht verfügbar ist, können Sie sich an den zuständigen Support wenden, um Informationen zur Installation zu erhalten. Eine Prüfung auf den aktuellen Agent können Sie auf der Seite für den [Windows Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) im GitHub-Repository durchführen.

2. Stellen Sie mit dem folgenden Befehl sicher, dass der Azure-Agent auf dem virtuellen Computer ausgeführt wird: `ps -e`

   Wenn der Prozess nicht ausgeführt wird, starten Sie ihn mit den folgenden Befehlen neu:

   - Für Ubuntu: `service walinuxagent start`
   - Für andere Distributionen: `service waagent start`

3. [Konfigurieren Sie den Agent für den automatischen Neustart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Führen Sie eine neue Testsicherung aus. Beziehen Sie die folgenden Protokolle vom virtuellen Computer, falls der Fehler weiterhin auftritt:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Führen Sie die folgenden Schritte aus, falls die ausführliche Protokollierung für waagent erforderlich ist:

1. Suchen Sie in der Datei „/etc/waagent.conf“ nach der folgenden Zeile: **Enable verbose logging (y|n)**
2. Ändern Sie den Wert für **Logs.Verbose** von *n* in *y*.
3. Speichern Sie die Änderung, und starten Sie waagent neu, indem Sie die weiter oben in diesem Abschnitt beschriebenen Schritte ausführen.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Konfigurationsoptionen für den VM-Agent sind nicht festgelegt (für virtuelle Linux-Computer)

Eine Konfigurationsdatei (/etc/waagent.conf) steuert die Aktionen von waagent. Die Konfigurationsdateioption **Extensions.Enable** muss auf **y** und die Option **Provisioning.Agent** auf **auto** festgelegt sein, damit die Sicherung funktioniert.
Eine vollständige Liste der Optionen für die VM-Agent-Konfigurationsdatei finden Sie unter <https://github.com/Azure/WALinuxAgent#configuration-file-options>.

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Die Anwendungssteuerungslösung blockiert „iaasbcdrextension.exe“

Wenn Sie [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (oder eine andere Anwendungssteuerungslösung) ausführen und die Regeln auf dem Herausgeber oder Pfad basieren, blockieren sie ggf. das Ausführen der ausführbaren Datei **IaaSBcdrExtension.exe**.

#### <a name="solution-to-this-issue"></a>Lösung für dieses Problem

Schließen Sie den Pfad `/var/lib` oder die ausführbare Datei **IaaSBcdrExtension.exe** von AppLocker (oder anderer Anwendungssteuerungssoftware) aus.

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Der Momentaufnahmestatus kann nicht abgerufen werden, oder es kann keine Momentaufnahme erstellt werden

Die VM-Sicherung basiert auf dem Ausführen eines Momentaufnahmenbefehls für das zugrunde liegende Speicherkonto. Bei der Sicherung können Fehler auftreten, weil kein Zugriff auf das Speicherkonto besteht oder weil sich der Momentaufnahmetask verzögert.

#### <a name="solution-for-this-issue"></a>Lösung für dieses Problem

Die folgenden Umstände können zu Fehlern bei Momentaufnahmetasks führen:

| Ursache | Lösung |
| --- | --- |
| Der VM-Status wird falsch gemeldet, weil der virtuelle Computer im Remotedesktopprotokoll (RDP) heruntergefahren ist. | Wenn Sie den virtuellen Computer im Remotedesktopprotokoll herunterfahren, überprüfen Sie im Portal, ob der VM-Status richtig angezeigt wird. Falls nicht, fahren Sie den virtuellen Computer im Portal mithilfe der Option **Herunterfahren** auf dem VM-Dashboard herunter. |
| Der virtuelle Computer kann die Host- oder Fabric-Adresse nicht aus DHCP abrufen. | Für die VM-Sicherung mithilfe von IaaS muss im Gastbetriebssystem die DHCP-Option aktiviert sein. Wenn der virtuelle Computer die Host- oder Fabric-Adresse nicht aus DHCP-Antwort 245 abrufen kann, können keine Erweiterungen heruntergeladen oder ausgeführt werden. Wenn Sie eine statische private IP-Adresse benötigen, sollten Sie diese im **Azure-Portal** oder mithilfe von **PowerShell** konfigurieren und sicherstellen, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist. [Hier erfahren Sie mehr](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) über das Einrichten einer statischen IP-Adresse mit PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Entfernen der Sperre von der Wiederherstellungspunkt-Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zur Option **Alle Ressourcen**, und wählen Sie die Wiederherstellungspunktsammlungs-Ressourcengruppe im folgenden Format aus: AzureBackupRG_`<Geo>`_`<number>`.
3. Wählen Sie im Abschnitt **Einstellungen** die Option **Sperren** aus, um die Sperren anzuzeigen.
4. Um die Sperre zu entfernen, wählen Sie die Auslassungspunkte und dann **Löschen** aus.

    ![Löschen der Sperre](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Bereinigen der Wiederherstellungspunktsammlung

Nach dem Entfernen der Sperre müssen die Wiederherstellungspunkte bereinigt werden.

Wenn Sie die Ressourcengruppe des virtuellen Computers oder die VM selbst löschen, bleiben die Momentaufnahmen für die sofortige Wiederherstellung verwalteter Datenträger aktiv und laufen gemäß der festgelegten Aufbewahrungsdauer ab. Um die Momentaufnahmen für die sofortige Wiederherstellung zu löschen (wenn Sie sie nicht mehr benötigen), die in der Wiederherstellungspunktsammlung gespeichert sind, bereinigen Sie die Wiederherstellungspunktsammlung gemäß den unten angegebenen Schritten.

Um die Wiederherstellungspunkte zu bereinigen, verwenden Sie eine der folgenden Methoden:<br>

- [Bereinigen der Wiederherstellungspunktsammlung durch Ausführen einer On-Demand-Sicherung](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Bereinigen der Wiederherstellungspunktsammlung über das Azure-Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Bereinigen der Wiederherstellungspunktsammlung durch Ausführen einer On-Demand-Sicherung

Nach dem Entfernen der Sperre lösen Sie eine On-Demand-Sicherung aus. Durch diese Aktion wird sichergestellt, dass die Wiederherstellungspunkte automatisch bereinigt werden. Dieser On-Demand-Vorgang schlägt beim ersten Mal meistens fehl. Es wird damit aber eine automatische Bereinigung statt manuellem Löschen von Wiederherstellungspunkten sichergestellt. Nach der Bereinigung sollte die nächste geplante Sicherung erfolgreich sein.

> [!NOTE]
> Die automatische Bereinigung erfolgt einige Stunden nach dem Auslösen der On-Demand-Sicherung. Wenn bei der geplanten Sicherung weiterhin ein Fehler auftritt, löschen Sie die Wiederherstellungspunktsammlung manuell, indem Sie die [hier](#clean-up-restore-point-collection-from-azure-portal) aufgeführten Schritte verwenden.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Bereinigen der Wiederherstellungspunktsammlung über das Azure-Portal <br>

Um die Wiederherstellungspunktsammlung, die aufgrund der Sperre der Ressourcengruppe nicht bereinigt wird, manuell zu bereinigen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Menü **Hub** die Option **Alle Ressourcen** und dann die Ressourcengruppe mit dem folgenden Format „AzureBackupRG_`<Geo>`_`<number>`“ aus, in der sich Ihr virtueller Computer befindet.

    ![Auswählen der Ressourcengruppe](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Wählen Sie die Ressourcengruppe aus. Der Bereich **Übersicht** wird angezeigt.
4. Wählen Sie die Option **Ausgeblendete Typen anzeigen** aus, um alle ausgeblendeten Ressourcen anzuzeigen. Wählen Sie die Wiederherstellungspunktsammlungen mit dem folgenden Format aus: AzureBackupRG_`<VMName>`_`<number>`.

    ![Auswählen der Wiederherstellungspunktsammlung](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Wählen Sie **Löschen** aus, um die Wiederherstellungspunktsammlung zu bereinigen.
6. Wiederholen Sie den Sicherungsvorgang erneut.

> [!NOTE]
 >Wenn die Ressource (RP-Sammlung) eine große Anzahl Wiederherstellungspunkte aufweist, kann beim Löschen über das Portal eine Zeitüberschreitung und damit ein Fehler auftreten. Dies ist ein bekanntes CRP-Problem, bei dem sämtliche Wiederherstellungspunkte in der vorgesehenen Zeit nicht gelöscht werden und beim Vorgang ein Timeout auftritt. Der Löschvorgang gelingt jedoch normalerweise nach zwei oder drei Wiederholungen.
