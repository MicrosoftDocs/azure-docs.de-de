---
title: Beheben von Sicherungsfehlern bei virtuellen Azure-Computern
description: Fehlerbehandlung bei der Sicherung und Wiederherstellung virtueller Azure-Computer
services: backup
author: srinathvasireddy
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 23137cd686bcdba59880ff705a43b16ced992b59
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303993"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Problembehandlung bei der Sicherung virtueller Azure-Computer
Sie können Fehler, die bei der Verwendung von Azure Backup auftreten, anhand der nachstehend aufgeführten Informationen beheben:

## <a name="backup"></a>Backup
Dieser Abschnitt behandelt Fehler im Sicherungsvorgang für virtuelle Azure-Computer.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – Timeout beim Kopieren gesicherter Daten aus dem Tresor

Fehlercode: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Fehlermeldung: Timeout beim Kopieren gesicherter Daten aus dem Tresor

Dies kann durch vorübergehende Speicherfehler oder einen Mangel an Speicherkonto-IOPS für den Sicherungsdienst zum Übertragen von Daten in den Tresor innerhalb des Timeoutzeitraums passieren. Konfigurieren Sie die VM-Sicherung mit diesen [bewährten Methoden](backup-azure-vms-introduction.md#best-practices), und wiederholen Sie den Sicherungsvorgang.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – Aufgrund des Zustands des virtuellen Computers sind keine Sicherungen möglich.

Fehlercode: UserErrorVmNotInDesirableState <br/>
Fehlermeldung: Aufgrund des Zustands des virtuellen Computers sind keine Sicherungen möglich.<br/>

Beim Sicherungsvorgang ist ein Fehler aufgetreten, da sich der virtuelle Computer im Status „Fehlerhaft“ befindet. Damit die Sicherung erfolgreich ist, sollte der virtuelle Computer sich im Status „Wird ausgeführt“, „Beendet“ oder „Beendet (Zuordnung aufgehoben)“ befinden.

* Wenn sich der virtuelle Computer in einem Übergangszustand zwischen **Wird ausgeführt** und **Heruntergefahren** befindet, müssen Sie warten, bis der Zustand geändert wurde. Lösen Sie dann den Sicherungsauftrag aus.
*  Schließen Sie im Falle eines virtuellen Linux-Computers mit dem Kernelmodul Security Enhanced Linux den Azure Linux-Agent-Pfad **/var/lib/waagent** aus der Sicherheitsrichtlinie aus, und stellen Sie sicher, dass die Azure Backup-Erweiterung installiert ist.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – Fehler beim Einfrieren mindestens eines Bereitstellungspunkts der VM zum Erfassen einer dateisystemkonsistenten Momentaufnahme

Fehlercode: UserErrorFsFreezeFailed <br/>
Fehlermeldung: Fehler beim Einfrieren mindestens eines Bereitstellungspunkts der VM zum Erfassen einer dateisystemkonsistenten Momentaufnahme.

* Überprüfen Sie den Dateisystemzustand aller eingebundenen Geräte mit dem **tune2fs**-Befehl, z.B. **tune2fs -l /dev/sdb1 \\** .\| grep **Dateisystemstatus**.
* Heben Sie mit dem Befehl **umount** die Bereitstellung der Geräte auf, deren Dateisystemstatus nicht bereinigt wurde.
* Führen Sie mit dem Befehl **fsck** eine Dateisystem-Konsistenzprüfung für diese Geräte aus.
* Stellen Sie die Geräte erneut bereit, und versuchen Sie, die Sicherung auszuführen.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC – Fehler bei Installation/Betrieb der Erweiterung aufgrund eines COM+-Fehlers

Fehlercode: ExtensionSnapshotFailedCOM <br/>
Fehlermeldung: Fehler bei Momentaufnahmevorgang aufgrund eines COM+-Fehlers

Fehlercode: ExtensionInstallationFailedCOM  <br/>
Fehlermeldung: Fehler bei Installation/Betrieb der Erweiterung aufgrund eines COM+-Fehlers

Fehlercode: ExtensionInstallationFailedMDTC <br/>
Fehlermeldung: Die Erweiterungsinstallation ist mit dem Fehler "COM+ konnte keine Daten mit dem Microsoft Distributed Transaction Coordinator austauschen" fehlgeschlagen. <br/>

Fehler beim Sicherungsvorgang aufgrund eines Problems mit dem Windows-Dienst **COM+-Systemanwendung**.  Gehen Sie folgendermaßen vor, um das Problem zu beheben:

* Versuchen Sie, den Windows-Dienst **COM+-Systemanwendung** zu starten / neu zu starten (über eine Eingabeaufforderung mit erhöhten Rechten **- net start COMSysApp**).
* Stellen Sie sicher, dass die **Distributed Transaction Coordinator**-Dienste als **Netzwerkdienst**-Konto ausgeführt werden. Wenn nicht, ändern Sie sie zur Ausführung als **Netzwerkdienst**-Konto, und starten Sie **COM+-Systemanwendung** neu.
* Wenn es nicht möglich ist, den Dienst neu zu starten, installieren Sie den **Distributed Transaction Coordinator**-Dienst mit folgenden Schritten neu:
    * Beenden Sie den Dienst „MS DTC“.
    * Öffnen Sie eine Eingabeaufforderung (cmd).
    * Führen Sie den Befehl „msdtc -uninstall“ aus.
    * Führen Sie den Befehl „msdtc -install“ aus.
    * Starten Sie den Dienst „MS DTC“.
* Starten Sie den Windows-Dienst **COM+-Systemanwendung**. Lösen Sie nach dem Starten des Diensts **COM+-Systemanwendung** einen Sicherungsauftrag über das Azure-Portal aus.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – Fehler beim Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands von VSS Writer-Instanzen

Fehlercode: ExtensionFailedVssWriterInBadState <br/>
Fehlermeldung: Fehler beim Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands von VSS Writer-Instanzen.

Starten Sie die in einem fehlerhaften Zustand befindlichen VSS Writer-Instanzen neu. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten den Befehl ```vssadmin list writers``` aus. Die Ausgabe enthält alle VSS Writer-Instanzen und deren Zustand. Führen Sie für jede VSS Writer-Instanz, deren Zustand nicht **[1] Stable** lautet, zum Neustarten der VSS Writer-Instanz die folgenden Befehle an einer Eingabeaufforderung mit erhöhten Rechten aus:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – Fehler beim Analysieren der Konfigurationsdatei für die Sicherungserweiterung

Fehlercode: ExtensionConfigParsingFailure<br/>
Fehlermeldung: Fehler beim Analysieren der Konfigurationsdatei für die Sicherungserweiterung.

Dieser Fehler tritt aufgrund geänderter Berechtigungen für das Verzeichnis **MachineKeys** ( **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**) auf.
Führen Sie den folgenden Befehl aus, und stellen Sie sicher, dass für das Verzeichnis **MachineKeys** die Standardberechtigungen gelten: **icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Die Standardberechtigungen lauten wie folgt:
* Jeder: Lesen/Schreiben (R, W)
* VORDEFINIERT\Administratoren: (F)

Wenn für das Verzeichnis **MachineKeys** andere Berechtigungen als die Standardberechtigungen festgelegt sind, führen Sie die folgenden Schritte aus, um die Berechtigungen zu korrigieren, das Zertifikat zu löschen und den Sicherungsvorgang auszulösen:

1. Korrigieren Sie die Berechtigungen für das Verzeichnis **MachineKeys**. Setzen Sie die Berechtigungen mithilfe von Explorer-Sicherheitseigenschaften und erweiterten Sicherheitseinstellungen für das Verzeichnis auf die Standardwerte zurück. Entfernen Sie alle Benutzerobjekte mit Ausnahme der Standardobjekte aus dem Verzeichnis, und stellen Sie sicher, dass für die Berechtigung **Jeder** die folgenden speziellen Zugriffsberechtigungen gelten:

    * Ordner auflisten/Daten lesen
    * Attribute lesen
    * Erweiterte Attribute lesen
    * Dateien erstellen/Daten schreiben
    * Ordner erstellen/Daten anhängen
    * Attribute schreiben
    * Erweiterte Attribute schreiben
    * Leseberechtigungen
2. Löschen Sie alle Zertifikate, für die **Ausgestellt für** das klassische Bereitstellungsmodell oder **Windows Azure CRP Certificate Generator** ist:
    * [Öffnen Sie in der Konsole auf dem lokalen Computer die Zertifikate](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Löschen Sie unter **Eigene Zertifikate** > **Zertifikate** alle Zertifikate, für die **Ausgestellt für** das klassische Bereitstellungsmodell ist, oder löschen Sie **Microsoft Azure CRP Certificate Generator**.
3. Lösen Sie einen Sicherungsauftrag für den virtuellen Computer aus.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState – Sicherungsvorgang wird von Erweiterungsstatus nicht unterstützt

Fehlercode: ExtensionStuckInDeletionState <br/>
Fehlermeldung: Sicherungsvorgang wird von Erweiterungsstatus nicht unterstützt

Beim Sicherungsvorgang ist aufgrund eines inkonsistenten Status der Sicherungserweiterung ein Fehler aufgetreten. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

* Stellen Sie sicher, dass der Gast-Agent installiert ist und reagiert.
* Navigieren Sie im Azure-Portal zu **Virtueller Computer** > **Alle Einstellungen** > **Erweiterungen**.
* Wählen Sie die Sicherungserweiterung VmSnapshot oder VmSnapshotLinux aus, und klicken Sie auf **Deinstallieren**.
* Wiederholen Sie den Sicherungsvorgang, nachdem Sie die Sicherungserweiterung gelöscht haben.
* Mit dem nachfolgenden Sicherungsvorgang wird die neue Erweiterung mit dem gewünschten Status installiert.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError – Fehler bei Momentaufnahmevorgang, weil für einige der angefügten Datenträger das Limit für Momentaufnahmen überschritten wurde

Fehlercode: ExtensionFailedSnapshotLimitReachedError  <br/>
Fehlermeldung: Fehler bei Momentaufnahmevorgang, weil für einige der angefügten Datenträger das Limit für Momentaufnahmen überschritten wurde

Beim Momentaufnahmevorgang ist ein Fehler aufgetreten, da das Momentaufnahmenlimit für einige der angefügten Datenträger überschritten wurde. Führen Sie die folgenden Problembehandlungsschritte aus, und wiederholen Sie dann den Vorgang.

* Löschen Sie die nicht erforderlichen Blobmomentaufnahmen von Datenträgern. Achten Sie darauf, dass Sie keine Datenträgerblobs löschen, nur Momentaufnahmenblobs sollten gelöscht werden.
* Wenn vorläufiges Löschen für die VM-Datenträger-Speicherkonten aktiviert ist, konfigurieren Sie die Aufbewahrung von vorläufigen Löschungen so, dass vorhandene Momentaufnahmen zu jedem Zeitpunkt kleiner als maximal zulässig sind.
* Wenn Azure Site Recovery auf dem gesicherten virtuellen Computer aktiviert ist, führen Sie Folgendes aus:

    * Vergewissern Sie sich, dass in „/etc/azure/vmbackup.conf“ für **isanysnapshotfailed** der Wert „false“ festgelegt ist.
    * Planen Sie Azure Site Recovery für einen anderen Zeitpunkt, sodass kein Konflikt mit dem Sicherungsvorgang auftritt.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive – Fehler beim Momentaufnahmevorgang aufgrund nicht ausreichender VM-Ressourcen.

Fehlercode: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Fehlermeldung: Fehler beim Momentaufnahmevorgang aufgrund nicht ausreichender VM-Ressourcen.

Fehler beim Sicherungsvorgang auf der VM aufgrund von Netzwerkaufrufen beim Durchführen des Momentaufnahmevorgangs. Führen Sie Schritt 1 aus, um dieses Problem zu lösen. Führen Sie die Schritte 2 und 3 aus, falls das Problem weiterhin besteht.

**Schritt 1:** Erstellen einer Momentaufnahme über den Host

Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten (Administrator) den folgenden Befehl aus:

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

So wird sichergestellt, dass die Momentaufnahmen nicht über den Gast, sondern über den Host erstellt werden. Wiederholen Sie den Sicherungsvorgang.

**Schritt 2:** Versuchen Sie, den Sicherungszeitplan in einen Zeitpunkt zu ändern, zu dem die VM eine geringere Auslastung aufweist (in Bezug auf CPU/IOPS usw.).

**Schritt 3:** Versuchen Sie, [die Größe der VM zu erhöhen](https://azure.microsoft.com/blog/resize-virtual-machines/) und den Vorgang dann erneut durchzuführen.

## <a name="common-vm-backup-errors"></a>Häufige Fehler bei der VM-Sicherung

| Fehlerdetails | Problemumgehung |
| ------ | --- |
| Fehlercode: 320001<br/> Fehlermeldung: Der Vorgang konnte nicht ausgeführt werden, da der virtuelle Computer nicht mehr vorhanden ist. <br/> <br/> Fehlercode: 400094 <br/> Fehlermeldung: Der virtuelle Computer ist nicht vorhanden <br/> <br/>  Der virtuelle Azure-Computer wurde nicht gefunden.  |Dieser Fehler tritt auf, wenn der primäre virtuelle Computer gelöscht wird, die Sicherungsrichtlinie jedoch weiterhin einen zu sichernden virtuellen Computer sucht. Führen Sie zum Beheben dieses Fehlers die folgenden Schritte aus: <ol><li> Erstellen Sie den virtuellen Computer mit dem gleichen Namen und dem gleichen Ressourcengruppennamen (**Clouddienstname**) neu.<br>**or**</li><li> Beenden Sie den Schutz für den virtuellen Computer mit oder ohne Löschung der Sicherungsdaten. Weitere Informationen finden Sie unter [Beenden des Schutzes für virtuelle Computer](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| Der VM befindet sich im Zustand „Fehler bei der Bereitstellung“: <br>Starten Sie den virtuellen Computer neu, und stellen Sie sicher, dass der VM ausgeführt oder heruntergefahren wird. | Dieser Fehler tritt auf, wenn einer der Erweiterungsfehler dazu führt, dass der virtuelle Computer in den Zustand „Fehler bei der Bereitstellung“ versetzt wird. Wechseln Sie zur Liste der Erweiterungen, überprüfen Sie, ob eine fehlerhafte Erweiterung vorliegt, entfernen Sie diese, und wiederholen Sie den Neustart des virtuellen Computers. Wenn alle Erweiterungen ausgeführt werden, überprüfen Sie, ob der VM-Agent-Dienst ausgeführt wird. Wenn dies nicht der Fall ist, starten Sie den VM-Agent-Dienst neu. |
|Fehlercode: UserErrorBCMPremiumStorageQuotaError<br/> Fehlermeldung: Die Momentaufnahme des virtuellen Computers konnte nicht kopiert werden, weil das Speicherkonto nicht über genügend freien Speicherplatz verfügt | Bei Premium-VMs mit dem VM-Sicherungsstapel V1 wird die Momentaufnahme in das Speicherkonto kopiert. Durch diesen Schritt wird sichergestellt, dass der Sicherungsverwaltungsdatenverkehr für die Momentaufnahme nicht die Anzahl der IOPS begrenzt, die der Anwendung zur Verfügung stehen, die Premium-Datenträger verwendet. <br><br>Wir empfehlen Ihnen, nur 50 % (17,5 TB) des gesamten Speicherplatzes des Speicherkontos zuzuordnen. Der Azure Backup-Dienst kann dann die Momentaufnahme in das Speicherkonto kopieren und Daten von diesem Kopierspeicherort in das Speicherkonto des Tresors übertragen. |
| Fehler beim Installieren der Microsoft Recovery Services-Erweiterung, da der virtuelle Computer nicht ausgeführt wird <br>Der VM-Agent ist eine erforderliche Komponente für die Azure Recovery Services-Erweiterung. Installieren Sie den Agent für virtuelle Azure-Computer, und starten Sie den Registrierungsvorgang erneut. |<ol> <li>Überprüfen Sie, ob der VM-Agent ordnungsgemäß installiert ist. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration richtig festgelegt wurde.</ol> Erfahren Sie mehr über das Installieren des VM-Agents und das Überprüfen der VM-Agent-Installation. |
| Der Momentaufnahmevorgang ist mit dem folgenden Vorgangsfehler des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) fehlgeschlagen: **Dieses Laufwerk ist durch die BitLocker-Laufwerkverschlüsselung gesperrt. Das Laufwerk muss mithilfe der Systemsteuerung entsperrt werden.** |Deaktivieren Sie BitLocker für alle Laufwerke auf dem virtuellen Computer, und überprüfen Sie, ob der VSS-Fehler behoben wurde. |
| Der Zustand des virtuellen Computers lässt keine Sicherungen zu. |<ul><li>Wenn sich der virtuelle Computer in einem Übergangszustand zwischen **Wird ausgeführt** und **Heruntergefahren** befindet, müssen Sie warten, bis der Zustand geändert wurde. Lösen Sie dann den Sicherungsauftrag aus. <li> Schließen Sie im Falle eines virtuellen Linux-Computers mit dem Kernelmodul Security Enhanced Linux den Azure Linux-Agent-Pfad **/var/lib/waagent** aus der Sicherheitsrichtlinie aus, und stellen Sie sicher, dass die Azure Backup-Erweiterung installiert ist.  |
| Der VM-Agent ist auf dem virtuellen Computer nicht vorhanden: <br>Installieren Sie alle erforderlichen Komponenten und den VM-Agent. Wiederholen Sie dann den Vorgang. |Erfahren Sie mehr über die [VM-Agent-Installation und das Überprüfen der VM-Agent-Installation](#vm-agent). |
| Azure Backup konnte zum Erfassen einer dateisystemkonsistenten Momentaufnahme mindestens einen Bereitstellungspunkt des virtuellen Computers nicht einfrieren. | Führen Sie den folgenden Schritt aus: <ul><li>Überprüfen Sie mit dem Befehl **tune2fs** den Dateisystemstatus aller bereitgestellten Geräte. Beispiel: **tune2fs -l /dev/sdb1 \\** .\| grep **Filesystem state**. <li>Heben Sie mit dem Befehl **umount** die Bereitstellung der Geräte auf, deren Dateisystemstatus nicht fehlerfrei ist. <li> Führen Sie mit dem Befehl **fsck** eine Dateisystemkonsistenzprüfung für diese Geräte aus. <li> Stellen Sie die Geräte erneut bereit, und versuchen Sie, die Sicherung auszuführen.</ol> |
| Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Erstellen eines sicheren Netzwerkkommunikationskanals fehlgeschlagen. | <ol><li> Öffnen Sie den Registrierungs-Editor, indem Sie **regedit.exe** im Modus mit erhöhten Rechten ausführen. <li> Identifizieren Sie alle auf Ihrem System vorhandenen Versionen von .NET Framework. Sie werden unter der Hierarchie des Registrierungsschlüssels **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** aufgeführt. <li> Fügen Sie für jede im Registrierungsschlüssel vorhandene .NET Framework-Version den folgenden Schlüssel hinzu: <br> **SchUseStrongCrypto"=dword:00000001** </ol>|
| Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Installieren von Visual C++ Redistributable für Visual Studio 2012 fehlgeschlagen. | Navigieren Sie zu „C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion“, und installieren Sie „vcredist2012_x64“.<br/>Stellen Sie sicher, dass der richtige Registrierungsschlüsselwert zum Zulassen der Dienstinstallation festgelegt wird. Das heißt, legen Sie den Wert für **Start** in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** auf **3** und nicht auf **4** fest. <br><br>Wenn immer noch Probleme bei der Installation bestehen, starten Sie den Installationsdienst neu, indem Sie an einer Eingabeaufforderung mit erhöhten Rechten den Befehl **MSIEXEC /UNREGISTER** und dann **MSIEXEC /REGISTER** ausführen.  |


## <a name="jobs"></a>Aufträge

| Fehlerdetails | Problemumgehung |
| --- | --- |
| Ein Abbruch wird für diesen Auftragstyp nicht unterstützt: <br>Warten Sie, bis der Auftrag abgeschlossen ist. |Keine |
| Der Auftrag kann in diesem Status nicht abgebrochen werden: <br>Warten Sie, bis der Auftrag abgeschlossen ist. <br>**or**<br> Der ausgewählte Auftrag kann in diesem Status nicht abgebrochen werden: <br>Warten Sie auf den Abschluss des Auftrags. |Wahrscheinlich ist der Auftrag fast vollständig abgeschlossen. Warten Sie, bis der Auftrag vollständig abgeschlossen wurde.|
| Azure Backup kann den Auftrag nicht abbrechen, weil sich dieser nicht in Bearbeitung befindet: <br>Ein Abbruch wird nur für in Bearbeitung befindliche Aufträge unterstützt. Versuchen Sie, einen in Bearbeitung befindlichen Auftrag abzubrechen. |Dieser Fehler tritt aufgrund eines Übergangszustands auf. Warten Sie eine Minute, und wiederholen Sie den Abbruchvorgang. |
| Azure Backup konnte den Auftrag nicht abbrechen: <br>Warten Sie, bis der Auftrag abgeschlossen ist. |Keine |

## <a name="restore"></a>Restore

| Fehlerdetails | Problemumgehung |
| --- | --- |
| Cloudinterner Fehler bei der Wiederherstellung. |<ol><li>Der Clouddienst, in dem Sie die Wiederherstellung ausführen möchten, ist mit DNS-Einstellungen konfiguriert. Sie können Folgendes überprüfen: <br>**$deployment = Get-AzureDeployment -ServiceName „Dienstname“ -Slot „Produktion“ Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Wenn eine **Adresse** konfiguriert wurde, sind die DNS-Einstellungen konfiguriert.<br> <li>Der Clouddienst, in dem Sie die Wiederherstellung ausführen möchten, ist mit **ReservedIP** konfiguriert, und vorhandene VMs im Clouddienst befinden sich im Zustand „Beendet“. Sie können mithilfe der folgenden PowerShell-Cmdlets überprüfen, ob ein Clouddienst eine IP-Adresse reserviert hat: **$deployment = Get-AzureDeployment -ServiceName „Dienstname“ -Slot „Produktion“ $dep.ReservedIPName**. <br><li>Sie versuchen, einen virtuellen Computer mit den folgenden speziellen Netzwerkkonfigurationen im selben Clouddienst wiederherzustellen: <ul><li>Virtuelle Computer unter Lastenausgleichskonfiguration, intern und extern.<li>Virtuelle Computer mit mehreren reservierten IP-Adressen. <li>Virtuelle Computer mit mehreren NICs. </ul><li>Wählen Sie einen neuen Clouddienst auf der Benutzeroberfläche aus, oder lesen Sie die [Überlegungen zu Wiederherstellungen](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) für virtuelle Computer mit speziellen Netzwerkkonfigurationen.</ol> |
| Der ausgewählte DNS-Name ist bereits vergeben: <br>Geben Sie einen anderen DNS-Namen an, und versuchen Sie es erneut. |Dieser DNS-Name bezieht sich auf den Clouddienstnamen, der in der Regel auf **.cloudapp.net** endet. Dieser Name muss eindeutig sein. Wenn dieser Fehler auftritt, müssen Sie während der Wiederherstellung einen anderen Namen für den virtuellen Computer auswählen. <br><br> Dieser Fehler wird nur Benutzern des Azure-Portals angezeigt. Der Wiederherstellungsvorgang über PowerShell ist erfolgreich, weil nur die Datenträger wiederhergestellt werden und kein virtueller Computer erstellt wird. Der Fehler tritt auf, wenn der virtuelle Computer nach Abschluss des Wiederherstellungsvorgangs für die Datenträger explizit von Ihnen erstellt wird. |
| Die angegebene Konfiguration des virtuellen Netzwerks ist nicht korrekt: <br>Geben Sie eine andere Konfiguration für das virtuelle Netzwerk an, und versuchen Sie es erneut. |Keine |
| Der angegebene Clouddienst verwendet eine reservierte IP-Adresse, die nicht mit der Konfiguration des wiederherzustellenden virtuellen Computers übereinstimmt: <br>Geben Sie einen anderen Clouddienst an, der keine reservierte IP-Adresse verwendet. Oder wählen Sie einen anderen Wiederherstellungspunkt aus. |Keine |
| Der Clouddienst hat den Grenzwert für die Anzahl von Eingabeendpunkten erreicht: <br>Wiederholen Sie den Vorgang, indem Sie einen anderen Clouddienst angeben oder einen vorhandenen Endpunkt verwenden. |Keine |
| Der Recovery Services-Tresor und das Zielspeicherkonto befinden sich in zwei unterschiedlichen Regionen: <br>Stellen Sie sicher, dass sich das im Wiederherstellungsvorgang angegebene Speicherkonto in derselben Azure-Region wie Ihr Recovery Services-Tresor befindet. |Keine |
| Das für den Wiederherstellungsvorgang angegebene Speicherkonto wird nicht unterstützt: <br>Es werden nur Basic- oder Standard-Speicherkonten mit lokal redundanten oder georedundanten Replikationseinstellungen unterstützt. Wählen Sie ein unterstütztes Speicherkonto aus. |Keine |
| Der Typ des für den Wiederherstellungsvorgang angegebenen Speicherkontos ist nicht online: <br>Stellen Sie sicher, dass das für den Wiederherstellungsvorgang angegebene Speicherkonto online ist. |Dieser Fehler kann aufgrund eines vorübergehenden Fehlers in Azure Storage oder aufgrund eines Ausfalls auftreten. Wählen Sie ein anderes Speicherkonto aus. |
| Das Ressourcengruppenkontingent wurde erreicht: <br>Löschen Sie im Azure-Portal einige Ressourcengruppen, oder wenden Sie sich an den Azure-Support, um die Grenzwerte zu erhöhen. |Keine |
| Das ausgewählte Subnetz ist nicht vorhanden: <br>Wählen Sie ein vorhandenes Subnetz aus. |Keine |
| Der Azure Backup-Dienst ist nicht zum Zugreifen auf Ressourcen in Ihrem Abonnement autorisiert. |Stellen Sie zum Beheben dieses Fehlers zuerst die Datenträger durch Ausführen der unter [Wiederherstellen von gesicherten Datenträgern](backup-azure-arm-restore-vms.md#restore-disks) aufgeführten Schritte wieder her. Führen Sie dann in PowerShell die unter [Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern](backup-azure-vms-automation.md#restore-an-azure-vm) beschriebenen Schritte aus. |

## <a name="backup-or-restore-takes-time"></a>Die Sicherung oder Wiederherstellung nimmt Zeit in Anspruch
Wenn die Sicherung länger als 12 Stunden oder die Wiederherstellung länger als 6 Stunden dauert, überprüfen Sie [bewährte Methoden](backup-azure-vms-introduction.md#best-practices) und [Überlegungen zur Leistung](backup-azure-vms-introduction.md#backup-performance).

## <a name="vm-agent"></a>VM-Agent
### <a name="set-up-the-vm-agent"></a>Einrichten des VM-Agents
Normalerweise ist der VM-Agent auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden. Auf virtuellen Computern, die aus lokalen Rechenzentren migriert werden, ist der VM-Agent jedoch nicht installiert. Auf diesen virtuellen Computern muss der VM-Agent explizit installiert werden.

#### <a name="windows-vms"></a>Virtuelle Windows-Computer

* Laden Sie den [Agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)herunter, und installieren Sie ihn. Sie benötigen Administratorberechtigungen, um die Installation ausführen zu können.
* [Aktualisieren Sie die VM-Eigenschaft](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) auf mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computern, um anzugeben, dass der Agent installiert wurde. Dieser Schritt ist nicht für virtuelle Azure Resource Manager-Computer erforderlich.

#### <a name="linux-vms"></a>Virtuelle Linux-Computer

* Installieren Sie die neueste Version des Agents über das Repository der Distribution. Ausführliche Informationen zum Paketnamen finden Sie im [Linux-Agent-Repository](https://github.com/Azure/WALinuxAgent).
* Aktualisieren Sie die VM-Eigenschaft auf mit dem klassischen Bereitstellungsmodell erstellten VMs [mithilfe dieses Blogs](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), und stellen Sie sicher, dass der Agent installiert ist. Dieser Schritt ist nicht für virtuelle Azure Resource Manager-Computer erforderlich.

### <a name="update-the-vm-agent"></a>Aktualisieren des VM-Agents
#### <a name="windows-vms"></a>Virtuelle Windows-Computer

* Installieren Sie die [Binärdateien für den Agent des virtuellen Computers](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) neu, um den Agent zu aktualisieren. Stellen Sie vor dem Aktualisieren des Agents sicher, dass während des Agent-Updates für den virtuellen Computer keine Sicherungsvorgänge durchgeführt werden.

#### <a name="linux-vms"></a>Virtuelle Linux-Computer

* Aktualisieren Sie den Agent des virtuellen Linux-Computers gemäß den Anweisungen im Artikel [Aktualisieren des Azure Linux-Agents](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Verwenden Sie zum Aktualisieren des Agents immer das Repository der Distribution.

    Laden Sie den Agent-Code nicht von GitHub herunter. Wenn der aktuelle Agent für Ihre Distribution nicht verfügbar ist, wenden Sie sich an den Distributionssupport, um zu erfahren, wie Sie den aktuellen Agent erhalten können. Sie können sich auch im GitHub-Repository über den aktuellen [Microsoft Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) informieren.

### <a name="validate-vm-agent-installation"></a>Überprüfen der VM-Agent-Installation

Gehen Sie wie folgt vor, um die VM-Agent-Version auf virtuellen Windows-Computern zu überprüfen:

1. Melden Sie sich beim virtuellen Azure-Computer an, und navigieren Sie zum Ordner **C:\WindowsAzure\Packages**. Darin befindet sich die Datei **WaAppAgent.exe**.
2. Klicken Sie mit der rechten Maustaste auf die Datei, und wechseln Sie zu **Eigenschaften**. Wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der **Produktversion** sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

## <a name="troubleshoot-vm-snapshot-issues"></a>Behandeln von Problemen mit VM-Momentaufnahmen
Bei der VM-Sicherung werden Momentaufnahmenbefehle an den zugrunde liegenden Speicher ausgegeben. Wenn Sie bei der Ausführung von Momentaufnahmeaufgaben keinen Zugriff auf Storage haben oder dabei Verzögerungen auftreten, kann es beim Sicherungsauftrag zu Fehlern kommen. Die folgenden Umstände können zu Fehlern bei Momentaufnahmeaufgaben führen:

- **Der Netzwerkzugriff auf Storage wird durch NSG blockiert**. Erfahren Sie mehr über das [Einrichten des Netzwerkzugriffs](backup-azure-arm-vms-prepare.md#establish-network-connectivity) auf Storage durch Hinzufügen einer Liste mit zulässigen IP-Adressen oder über einen Proxyserver.
- **Virtuelle Computer mit konfigurierter SQL Server-Sicherung können Momentaufnahmeaufgaben verzögern**. Standardmäßig wird bei der Sicherung virtueller Computer eine vollständige VSS-Sicherung auf virtuellen Windows-Computern erstellt. Bei virtuellen Computern mit SQL Server und einer konfigurierten SQL Server-Sicherung kann es zu Verzögerungen bei Momentaufnahmen kommen. Wenn Verzögerungen bei Momentaufnahmen Sicherungsfehler verursachen, legen Sie den folgenden Registrierungsschlüssel fest:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Der VM-Status wird falsch gemeldet, da der virtuelle Computer im RDP heruntergefahren ist**. Wenn Sie den virtuellen Computer über eine Remotedesktopverbindung heruntergefahren haben, überprüfen Sie im Portal, ob der VM-Status korrekt ist. Wenn dies nicht der Fall ist, fahren Sie den virtuellen Computer mit der Option **Herunterfahren** über das Dashboard des virtuellen Computers im Portal herunter.
- **Wenn mehr als vier virtuelle Computer denselben Clouddienst gemeinsam nutzen, müssen Sie die VMs über mehrere Sicherungsrichtlinien verteilen**. Legen Sie die Sicherungszeiten so fest, dass Sicherungen für maximal vier virtuelle Computer gleichzeitig ausgeführt werden. In den Richtlinien sollte zwischen den Startzeiten mindestens ein Zeitraum von einer Stunde liegen.
- **Der virtuelle Computer wird bei hoher CPU- oder Arbeitsspeicherauslastung ausgeführt**. Wenn die Arbeitsspeicher- oder CPU-Auslastung des virtuellen Computers hoch ist (mehr als 90 Prozent beträgt), wird die Momentaufnahmeaufgabe in die Warteschlange eingereiht und deren Ausführung verzögert. Schließlich tritt ein Timeout auf. Wenn dieses Problem auftritt, sollten Sie eine bedarfsgesteuerte Sicherung ausführen.

## <a name="networking"></a>Netzwerk
Wie bei allen Erweiterungen ist für Backup-Erweiterungen der Zugriff auf das öffentliche Internet erforderlich, damit sie funktionieren. Wenn kein Zugriff auf das öffentliche Internet besteht, kann dies zu unterschiedlichen Ergebnissen führen:

* Bei der Installation der Erweiterung kann ein Fehler auftreten.
* Bei Sicherungsvorgängen (z. B. bei Datenträgermomentaufnahmen) kann ein Fehler auftreten.
* Beim Anzeigen des Status des Sicherungsvorgangs kann ein Fehler auftreten.

Die Notwendigkeit zur Auflösung öffentlicher Internetadressen wird in [diesem Azure-Support-Blog](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx) erörtert. Überprüfen Sie die DNS-Konfigurationen für das VNET, und stellen Sie sicher, dass die Azure-URIs aufgelöst werden können.

Nachdem die Namensauflösung richtig eingerichtet wurde, muss auch der Zugriff auf die Azure-IP-Adressen bereitgestellt werden. Führen Sie einen der folgenden Schritte aus, um die Blockierung des Zugriffs auf die Azure-Infrastruktur aufzuheben:

- Liste von zulässigen IP-Adressbereichen im Azure-Rechenzentrum:
   1. Beschaffen Sie sich die Liste mit den [IP-Adressen des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653), die auf der Zulassungsliste stehen sollen.
   1. Heben Sie die Blockierung für die IP-Adressen mit dem Cmdlet [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) auf. Führen Sie dieses Cmdlet auf dem virtuellen Azure-Computer in einem PowerShell-Fenster mit erhöhten Rechten aus. Führen Sie diesen Vorgang als Administrator aus.
   1. Fügen Sie der Netzwerksicherheitsgruppe (NSG), falls vorhanden, Regeln hinzu, um den Zugriff auf die IP-Adressen zuzulassen.
- Erstellen Sie einen Pfad für den HTTP-Datenverkehr:
   1. Wenn Netzwerkeinschränkungen gelten, stellen Sie einen HTTP-Proxyserver zum Weiterleiten des Datenverkehrs bereit. Ein Beispiel für eine Einschränkung ist eine Netzwerksicherheitsgruppe. Die Schritte zum Bereitstellen eines HTTP-Proxyservers finden Sie unter [Herstellen der Netzwerkverbindung](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Fügen Sie der NSG (falls vorhanden) Regeln hinzu, um den Zugriff auf das Internet über den HTTP-Proxy zuzulassen.

> [!NOTE]
> Für die VM-Sicherung mithilfe von IaaS muss auf dem Gastsystem DHCP aktiviert sein. Wenn Sie eine statische private IP-Adresse benötigen, konfigurieren Sie diese im Azure-Portal oder mithilfe von PowerShell. Stellen Sie sicher, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist.
> Weitere Informationen zum Einrichten einer statischen IP-Adresse mithilfe von PowerShell finden Sie unter:
> - [Hinzufügen einer statischen internen IP-Adresse zu einem vorhandenen virtuellen Computer](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Ändern der Zuordnungsmethode für eine private IP-Adresse, die einer Netzwerkschnittstelle zugewiesen ist](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
