---
title: Beheben von Sicherungsfehlern mit Azure-VMs
description: In diesem Artikel erfahren Sie, wie Sie Fehler beheben können, die bei der Sicherung und Wiederherstellung von virtuellen Azure-Computern auftreten.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: cb25d9263648fbd92bc075751c1a8e627d03bd44
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325212"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Problembehandlung bei Sicherungsfehlern auf virtuellen Azure-Computern

Sie können Fehler, die bei der Verwendung von Azure Backup auftreten, anhand der nachstehend aufgeführten Informationen beheben:

## <a name="backup"></a>Backup

Dieser Abschnitt behandelt Fehler im Sicherungsvorgang für virtuelle Azure-Computer.

### <a name="basic-troubleshooting"></a>Grundlegendes zur Problembehandlung

* Stellen Sie sicher, dass der VM-Agent (WA-Agent) die [neueste Version](./backup-azure-arm-vms-prepare.md#install-the-vm-agent) aufweist.
* Stellen Sie sicher, dass die Betriebssystemversion des virtuellen Windows- oder Linux-Computers unterstützt wird. Entsprechende Informationen finden Sie in der [Supportmatrix zur IaaS-VM-Sicherung](./backup-support-matrix-iaas.md).
* Überprüfen Sie, ob kein anderer Sicherungsdienst ausgeführt wird.
  * Um sicherzustellen, dass keine Probleme bei der Momentaufnahmenerweiterung vorliegen, [deinstallieren Sie die Erweiterungen, um erneutes Laden zu erzwingen, und wiederholen Sie die Sicherung](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Überprüfen Sie, ob der virtuelle Computer über Internetkonnektivität verfügt.
  * Vergewissern Sie sich, dass kein anderer Sicherungsdienst ausgeführt wird.
* Stellen Sie über `Services.msc` sicher, dass sich der **Microsoft Azure-Gast-Agent-Dienst** im Status **Wird ausgeführt** befindet. Wenn der **Microsoft Azure-Gast-Agent-Dienst** nicht vorhanden ist, installieren Sie ihn aus [Sichern virtueller Azure-Computer in einem Recovery Services-Tresor](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Das **Ereignisprotokoll** zeigt möglicherweise Sicherungsfehler an, die aus anderen Sicherungsprodukten, z. B. der Windows Server-Sicherung, stammen und nicht auf Azure Backup zurückzuführen sind. Ermitteln Sie anhand der folgenden Schritte, ob das Problem bei Azure Backup liegt:
  * Wenn in der Ereignisquelle oder -meldung ein Fehler bei dem Eintrag **Sicherung** vorliegt, überprüfen Sie, ob die Sicherungen der Azure IaaS-VM-Sicherung erfolgreich waren und ob ein Wiederherstellungspunkt mit dem gewünschten Momentaufnahmetyp erstellt wurde.
  * Wenn Azure Backup funktioniert, liegt das Problem wahrscheinlich bei einer anderen Sicherungslösung.
  * Im Folgenden finden Sie ein Beispiel für einen Fehler 517 in der Ereignisanzeige, bei dem Azure Backup einwandfrei funktionierte, aber die „Windows Server-Sicherung“ fehlgeschlagen ist: ![Windows Server-Sicherung fehlgeschlagen](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Wenn Azure Backup fehlschlägt, suchen Sie den entsprechenden Fehlercode im Abschnitt „Häufige Fehler bei der VM-Sicherung“ in diesem Artikel.

## <a name="common-issues"></a>Häufige Probleme

Im Folgenden werden häufige Probleme bei Sicherungsfehlern auf virtuellen Azure-Computern erläutert.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError: Das auf der VM konfigurierte Antivirenprogramm schränkt die Ausführung der Sicherungserweiterung ein.

Fehlercode: VMRestorePointInternalError

Falls zum Zeitpunkt der Sicherung in den **Anwendungsprotokollen der Ereignisanzeige** ein ähnlicher Eintrag wie der folgende angezeigt wird, **Fehlerhafter Anwendungsname: IaaSBcdrExtension.exe**, wird die Ausführung der Sicherungserweiterung durch das auf der VM konfigurierte Antivirenprogramm tatsächlich eingeschränkt.
Schließen Sie zur Behebung dieses Problems die folgenden Verzeichnisse aus der Antivirenkonfiguration aus, und wiederholen Sie den Sicherungsvorgang.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – Timeout beim Kopieren gesicherter Daten aus dem Tresor

Fehlercode: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Fehlermeldung: Timeout beim Kopieren gesicherter Daten aus dem Tresor

Dies kann durch vorübergehende Speicherfehler oder einen Mangel an Speicherkonto-IOPS für den Sicherungsdienst zum Übertragen von Daten in den Tresor innerhalb des Timeoutzeitraums passieren. Konfigurieren Sie die VM-Sicherung mit diesen [bewährten Methoden](backup-azure-vms-introduction.md#best-practices), und wiederholen Sie den Sicherungsvorgang.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – Aufgrund des Zustands des virtuellen Computers sind keine Sicherungen möglich.

Fehlercode: UserErrorVmNotInDesirableState <br/>
Fehlermeldung: Aufgrund des Zustands des virtuellen Computers sind keine Sicherungen möglich.<br/>

Beim Sicherungsvorgang ist ein Fehler aufgetreten, da sich der virtuelle Computer im Status „Fehlerhaft“ befindet. Für eine erfolgreiche Sicherung muss sich der virtuelle Computer im Zustand „Wird ausgeführt“, „Beendet“ oder „Beendet (Zuordnung aufgehoben)“ befinden.

* Wenn sich der virtuelle Computer in einem Übergangszustand zwischen **Wird ausgeführt** und **Heruntergefahren** befindet, müssen Sie warten, bis der Zustand geändert wurde. Lösen Sie dann den Sicherungsauftrag aus.
* Schließen Sie im Falle eines virtuellen Linux-Computers mit dem Kernelmodul Security Enhanced Linux den Azure Linux-Agent-Pfad **/var/lib/waagent** aus der Sicherheitsrichtlinie aus, und stellen Sie sicher, dass die Azure Backup-Erweiterung installiert ist.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – Fehler beim Einfrieren mindestens eines Bereitstellungspunkts der VM zum Erfassen einer dateisystemkonsistenten Momentaufnahme

Fehlercode: UserErrorFsFreezeFailed <br/>
Fehlermeldung: Fehler beim Einfrieren mindestens eines Bereitstellungspunkts der VM zum Erfassen einer dateisystemkonsistenten Momentaufnahme.

* Heben Sie mit dem Befehl **umount** die Bereitstellung der Geräte auf, deren Dateisystemstatus nicht bereinigt wurde.
* Führen Sie mit dem Befehl **fsck** eine Dateisystem-Konsistenzprüfung für diese Geräte aus.
* Stellen Sie die Geräte erneut bereit, und versuchen Sie, die Sicherung auszuführen.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC – Fehler bei Installation/Betrieb der Erweiterung aufgrund eines COM+-Fehlers

Fehlercode: ExtensionSnapshotFailedCOM <br/>
Fehlermeldung: Fehler bei Momentaufnahmevorgang aufgrund eines COM+-Fehlers

Fehlercode: ExtensionInstallationFailedCOM  <br/>
Fehlermeldung: Fehler bei Installation/Betrieb der Erweiterung aufgrund eines COM+-Fehlers

Fehlercode: ExtensionInstallationFailedMDTC <br/>
Fehlermeldung: Die Erweiterungsinstallation ist mit dem Fehler "COM+ konnte keine Daten mit dem Microsoft Distributed Transaction Coordinator austauschen" fehlgeschlagen. <br/>

Fehler beim Sicherungsvorgang aufgrund eines Problems mit dem Windows-Dienst **COM+-Systemanwendung**.  Gehen Sie folgendermaßen vor, um das Problem zu beheben:

* Versuchen Sie, den Windows-Dienst **COM+-Systemanwendung** zu starten / neu zu starten (über eine Eingabeaufforderung mit erhöhten Rechten **- net start COMSysApp**).
* Stellen Sie sicher, dass der **Distributed Transaction Coordinator**-Dienst als **Netzwerkdienst**-Konto ausgeführt wird. Wenn nicht, ändern Sie sie zur Ausführung als **Netzwerkdienst**-Konto, und starten Sie **COM+-Systemanwendung** neu.
* Wenn Sie den Dienst nicht neu starten können, installieren Sie den Dienst **Distributed Transaction Coordinator** (MS DTC) mit folgenden Schritten neu:
  * Beenden Sie den Dienst „MS DTC“.
  * Öffnen Sie eine Eingabeaufforderung (cmd).
  * Führen Sie den Befehl `msdtc -uninstall` aus.
  * Führen Sie den Befehl `msdtc -install` aus.
  * Starten Sie den Dienst „MS DTC“.
* Starten Sie den Windows-Dienst **COM+-Systemanwendung**. Lösen Sie nach dem Starten des Diensts **COM+-Systemanwendung** einen Sicherungsauftrag über das Azure-Portal aus.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – Fehler beim Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands von VSS Writer-Instanzen

Fehlercode: ExtensionFailedVssWriterInBadState <br/>
Fehlermeldung: Fehler beim Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands von VSS Writer-Instanzen.

Dieser Fehler tritt auf, weil die VSS Writer in einem fehlerhaften Zustand waren. Azure Backup-Erweiterungen interagieren mit VSS Writern, um Momentaufnahmen von den Datenträgern zu erstellen. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

Schritt 1: Starten Sie die in einem fehlerhaften Zustand befindlichen VSS Writer-Instanzen neu.

* Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten den Befehl ```vssadmin list writers``` aus.
* Die Ausgabe enthält alle VSS Writer-Instanzen und deren Zustand. Starten Sie für jeden VSS Writer mit einem Zustand, der nicht **[1] Stabil** lautet, den entsprechenden VSS Writer-Dienst neu.
* Führen Sie die folgenden Befehle an einer Eingabeaufforderung mit erhöhten Rechten aus, um den Dienst neu zu starten:

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Wenn Sie einige Dienste neu starten, kann dies Auswirkungen auf Ihre Produktionsumgebung haben. Stellen Sie sicher, dass der Genehmigungsprozess befolgt wird und der Dienst zur geplanten Downtime neu gestartet wird.

Schritt 2: Falls das Problem durch das Neustarten der VSS Writer-Instanzen nicht behoben werden konnte, sollten Sie den folgenden Befehl über eine Eingabeaufforderung mit erhöhten Rechten (als Administrator) ausführen, um zu verhindern, dass die Threads für Blobmomentaufnahmen erstellt werden.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Schritt 3: Falls das Problem mit den Schritten 1 und 2 nicht behoben werden konnte, kann der Grund für den Fehler sein, dass für die VSS Writer-Instanzen aufgrund einer IOPS-Begrenzung ein Timeout auftritt.<br>

Navigieren Sie zur Überprüfung zu ***System- und Ereignisanzeige-Anwendungsprotokolle** _, und suchen Sie nach der folgenden Fehlermeldung:<br>
_Für den Schattenkopieanbieter ist ein Timeout aufgetreten, während für das Volume für den Schattenkopiervorgang die Schreibvorgänge angehalten wurden. This is probably due to excessive activity on the volume by an application or a system service. Wiederholen Sie den Vorgang später, wenn der Aktivitätsgrad auf dem Volume nicht mehr so hoch ist.*<br>

Lösung:

* Suchen Sie nach Möglichkeiten, die Last auf die VM-Datenträger zu verteilen. Hierdurch wird die Auslastung der einzelnen Datenträger reduziert. Sie können die [IOPS-Drosselung überprüfen, indem Sie Diagnosemetriken auf Speicherebene aktivieren](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm).
* Ändern Sie die Sicherungsrichtlinie so, dass die Sicherungsvorgänge außerhalb der Spitzenzeiten durchgeführt werden, wenn die Auslastung auf der VM am niedrigsten ist.
* Führen Sie ein Upgrade für die Azure-Datenträger durch, damit höhere IOPS-Werte unterstützt werden. [Weitere Informationen finden Sie hier](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – Fehler bei Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands des Volumeschattenkopie-Diensts

Fehlercode: ExtensionFailedVssServiceInBadState <br/>
Fehlermeldung: Fehler bei Momentaufnahmevorgang aufgrund eines fehlerhaften Zustands des Volumeschattenkopie-Diensts.

Dieser Fehler tritt auf, weil der VSS-Dienst in einem fehlerhaften Zustand war. Azure Backup-Erweiterungen interagieren mit dem VSS-Dienst, um Momentaufnahmen von den Datenträgern zu erstellen. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

Starten Sie den Volumeschattenkopie-Dienst neu.

* Navigieren Sie zu „services.msc“, und starten Sie den Volumeschattenkopie-Dienst neu.<br>
(oder)<br>
* Führen Sie die folgenden Befehle in einer Eingabeaufforderung mit erhöhten Rechten aus:

 ```net stop VSS``` <br>
 ```net start VSS```

Wenn das Problem weiterhin besteht, starten Sie den virtuellen Computer zur geplanten Downtime neu.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable – Bei der VM-Erstellung ist ein Fehler aufgetreten, da die ausgewählte VM-Größe nicht verfügbar ist.

Fehlercode: UserErrorSkuNotAvailable-Fehlermeldung: Bei der VM-Erstellung ist ein Fehler aufgetreten, da die ausgewählte VM-Größe nicht verfügbar ist.

Dieser Fehler tritt auf, weil die während des Wiederherstellungsvorgangs ausgewählte VM-Größe nicht unterstützt wird. <br>

Um dieses Problem zu beheben, verwenden Sie während des Wiederherstellungsvorgangs die Option [Datenträger wiederherstellen](./backup-azure-arm-restore-vms.md#restore-disks). Verwenden Sie diese Datenträger, um einen virtuellen Computer aus der Liste der [verfügbaren unterstützten VM-Größen](./backup-support-matrix-iaas.md#vm-compute-support) mit [PowerShell-Cmdlets](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) zu erstellen.

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported – Bei der VM-Erstellung ist ein Fehler aufgetreten, weil keine Marketplace-Kaufanforderung vorhanden war

Fehlercode: UserErrorMarketPlaceVMNotSupported-Fehlermeldung: Bei der VM-Erstellung ist ein Fehler aufgetreten, weil keine Marketplace-Kaufanforderung vorhanden war.

Azure Backup unterstützt die Sicherung und Wiederherstellung von VMs, die im Azure Marketplace verfügbar sind. Dieser Fehler tritt auf, wenn Sie versuchen, eine VM (mit einer bestimmten Plan/Herausgeber-Einstellung) wiederherzustellen, die im Azure Marketplace nicht mehr verfügbar ist. [Weitere Informationen finden Sie hier](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Um dieses Problem zu beheben, verwenden Sie während des Wiederherstellungsvorgangs die Option [Datenträger wiederherstellen](./backup-azure-arm-restore-vms.md#restore-disks) und dann die [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)- oder [Azure CLI](./tutorial-restore-disk.md)-Cmdlets, um die VM mit den neuesten Marketplace-Informationen entsprechend der VM zu erstellen.
* Wenn der Herausgeber keine Marketplace-Informationen angibt, können Sie die Datenträger verwenden, um die Daten abzurufen, und Sie können diese an eine vorhandene VM anfügen.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – Fehler beim Analysieren der Konfigurationsdatei für die Sicherungserweiterung

Fehlercode: ExtensionConfigParsingFailure<br/>
Fehlermeldung: Fehler beim Analysieren der Konfigurationsdatei für die Sicherungserweiterung.

Dieser Fehler tritt aufgrund geänderter Berechtigungen für das Verzeichnis **MachineKeys** ( **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**) auf.
Führen Sie den folgenden Befehl aus, und stellen Sie sicher, dass für das Verzeichnis **MachineKeys** die Standardberechtigungen gelten `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys`.

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

   * [Öffnen Sie in der Konsole auf dem lokalen Computer die Zertifikate](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Löschen Sie unter **Eigene Zertifikate** > **Zertifikate** alle Zertifikate, für die **Ausgestellt für** das klassische Bereitstellungsmodell ist, oder löschen Sie **Microsoft Azure CRP Certificate Generator**.
3. Lösen Sie einen Sicherungsauftrag für den virtuellen Computer aus.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState – Sicherungsvorgang wird von Erweiterungsstatus nicht unterstützt

Fehlercode: ExtensionStuckInDeletionState <br/>
Fehlermeldung: Sicherungsvorgang wird von Erweiterungsstatus nicht unterstützt

Beim Sicherungsvorgang ist aufgrund eines inkonsistenten Status der Sicherungserweiterung ein Fehler aufgetreten. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

* Stellen Sie sicher, dass der Gast-Agent installiert ist und reagiert.
* Navigieren Sie vom Azure-Portal zu **Virtueller Computer** > **Alle Einstellungen** > **Erweiterungen**.
* Wählen Sie die Sicherungserweiterung „VmSnapshot“ oder „VmSnapshotLinux“ und dann **Deinstallieren** aus.
* Wiederholen Sie den Sicherungsvorgang, nachdem Sie die Sicherungserweiterung gelöscht haben.
* Mit dem nachfolgenden Sicherungsvorgang wird die neue Erweiterung mit dem gewünschten Status installiert.

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError – Fehler bei Momentaufnahmevorgang, weil für einige der angefügten Datenträger das Limit für Momentaufnahmen überschritten wurde

Fehlercode: ExtensionFailedSnapshotLimitReachedError  <br/>
Fehlermeldung: Fehler bei Momentaufnahmevorgang, weil für einige der angefügten Datenträger das Limit für Momentaufnahmen überschritten wurde

Beim Momentaufnahmevorgang ist ein Fehler aufgetreten, da das Momentaufnahmenlimit für einige der angefügten Datenträger überschritten wurde. Führen Sie die folgenden Problembehandlungsschritte aus, und wiederholen Sie dann den Vorgang.

* Löschen Sie die nicht benötigten Datenträgerblob-Momentaufnahmen. Achten Sie darauf, dass Sie keine Datenträgerblobs löschen. Nur Momentaufnahmenblobs sollten gelöscht werden.
* Wenn „Vorläufiges Löschen“ für die VM-Datenträgerspeicherkonten aktiviert wurde, konfigurieren Sie die Aufbewahrung von vorläufigen Löschungen so, dass vorhandene Momentaufnahmen zu jedem Zeitpunkt kleiner als maximal zulässig sind.
* Wenn Azure Site Recovery auf dem gesicherten virtuellen Computer aktiviert ist, führen Sie die folgenden Schritte aus:

  * Vergewissern Sie sich, dass in „/etc/azure/vmbackup.conf“ für **isanysnapshotfailed** der Wert „false“ festgelegt ist.
  * Planen Sie Azure Site Recovery für einen anderen Zeitpunkt, damit es beim Sicherungsvorgang zu keinem Konflikt kommt.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive – Fehler beim Momentaufnahmevorgang aufgrund nicht ausreichender VM-Ressourcen.

Fehlercode: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Fehlermeldung: Fehler beim Momentaufnahmevorgang aufgrund nicht ausreichender VM-Ressourcen.

Fehler beim Sicherungsvorgang auf der VM aufgrund von Netzwerkaufrufen beim Durchführen des Momentaufnahmevorgangs. Führen Sie Schritt 1 aus, um dieses Problem zu lösen. Führen Sie die Schritte 2 und 3 aus, falls das Problem weiterhin besteht.

**Schritt 1:** Erstellen einer Momentaufnahme über den Host

Führen Sie an einer Eingabeaufforderung auf höherer Ebene (Administrator) den folgenden Befehl aus:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

So wird sichergestellt, dass die Momentaufnahmen nicht über den Gast, sondern über den Host erstellt werden. Wiederholen Sie den Sicherungsvorgang.

**Schritt 2:** Versuchen Sie, den Sicherungszeitplan auf einen Zeitpunkt zu ändern, zu dem die VM eine geringere Auslastung (wie z. B. weniger CPU oder IOPS) aufweist.

**Schritt 3:** Versuchen Sie, [die Größe der VM zu erhöhen](../virtual-machines/windows/resize-vm.md) und den Vorgang dann erneut durchzuführen.

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound: Der Vorgang konnte nicht ausgeführt werden, weil der virtuelle Computer nicht mehr vorhanden ist./400094, BCMV2VMNotFound: Der virtuelle Computer ist nicht vorhanden/Der virtuelle Azure-Computer wurde nicht gefunden.

Fehlercode: 320001, ResourceNotFound <br/> Fehlermeldung: Der Vorgang konnte nicht ausgeführt werden, da der virtuelle Computer nicht mehr vorhanden ist. <br/> <br/> Fehlercode: 400094, BCMV2VMNotFound <br/> Fehlermeldung: Der virtuelle Computer ist nicht vorhanden <br/>
Der virtuelle Azure-Computer wurde nicht gefunden.

Dieser Fehler tritt auf, wenn der primäre virtuelle Computer gelöscht wird, die Sicherungsrichtlinie jedoch weiterhin einen zu sichernden virtuellen Computer sucht. Führen Sie zum Beheben dieses Fehlers die folgenden Schritte aus:

* Erstellen Sie den virtuellen Computer mit dem gleichen Namen und dem gleichen Ressourcengruppennamen (**Clouddienstname**) neu.<br>oder
* Beenden Sie den Schutz für den virtuellen Computer mit oder ohne Löschung der Sicherungsdaten. Weitere Informationen finden Sie unter [Beenden des Schutzes für virtuelle Computer](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError: Die Momentaufnahme des virtuellen Computers konnte nicht kopiert werden, weil das Speicherkonto nicht über genügend freien Speicherplatz verfügt.

Fehlercode: UserErrorBCMPremiumStorageQuotaError<br/> Fehlermeldung: Die Momentaufnahme des virtuellen Computers konnte nicht kopiert werden, weil das Speicherkonto nicht über genügend freien Speicherplatz verfügt

 Bei Premium-VMs mit dem VM-Sicherungsstapel V1 wird die Momentaufnahme in das Speicherkonto kopiert. Durch diesen Schritt wird sichergestellt, dass der Sicherungsverwaltungsdatenverkehr für die Momentaufnahme nicht die Anzahl der IOPS begrenzt, die der Anwendung zur Verfügung stehen, die Premium-Datenträger verwendet. <br><br>Wir empfehlen Ihnen, nur 50 % (17,5 TB) des gesamten Speicherplatzes des Speicherkontos zuzuordnen. Der Azure Backup-Dienst kann dann die Momentaufnahme in das Speicherkonto kopieren und Daten von diesem Kopierspeicherort in das Speicherkonto des Tresors übertragen.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline: Fehler beim Installieren der Microsoft Recovery Services-Erweiterung, da der virtuelle Computer nicht ausgeführt wird.

Fehlercode: 380008, AzureVmOffline <br/> Fehlermeldung: Fehler beim Installieren der Microsoft Recovery Services-Erweiterung, da der virtuelle Computer nicht ausgeführt wird

Der VM-Agent ist eine erforderliche Komponente für die Azure Recovery Services-Erweiterung. Installieren Sie den Agent für virtuelle Azure-Computer, und starten Sie den Registrierungsvorgang erneut. <br> <ol> <li>Überprüfen Sie, ob der VM-Agent ordnungsgemäß installiert ist. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration richtig festgelegt wurde.</ol> Erfahren Sie mehr über das Installieren des VM-Agents und das Überprüfen der VM-Agent-Installation.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError: Der Momentaufnahmevorgang ist mit dem folgenden Vorgangsfehler des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) fehlgeschlagen.

Fehlercode: ExtensionSnapshotBitlockerError <br/> Fehlermeldung: Der Momentaufnahmevorgang ist mit dem folgenden Vorgangsfehler des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) fehlgeschlagen: **Dieses Laufwerk ist durch die BitLocker-Laufwerkverschlüsselung gesperrt. Das Laufwerk muss mithilfe der Systemsteuerung entsperrt werden.**

Deaktivieren Sie BitLocker für alle Laufwerke auf dem virtuellen Computer, und überprüfen Sie, ob der VSS-Fehler behoben wurde.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState: VM befindet sich nicht in einem Zustand, der Sicherungen zulässt.

Fehlercode: VmNotInDesirableState <br/> Fehlermeldung:  Der Zustand des virtuellen Computers lässt keine Sicherungen zu.

* Wenn sich der virtuelle Computer in einem Übergangszustand zwischen **Wird ausgeführt** und **Heruntergefahren** befindet, müssen Sie warten, bis der Zustand geändert wurde. Lösen Sie dann den Sicherungsauftrag aus.
* Schließen Sie im Falle eines virtuellen Linux-Computers mit dem Kernelmodul Security Enhanced Linux den Azure Linux-Agent-Pfad **/var/lib/waagent** aus der Sicherheitsrichtlinie aus, und stellen Sie sicher, dass die Azure Backup-Erweiterung installiert ist.

* Der VM-Agent ist auf dem virtuellen Computer nicht vorhanden: <br>Installieren Sie alle erforderlichen Komponenten und den VM-Agent. Wiederholen Sie dann den Vorgang. Weitere Informationen zur VM-Agent-Installation sowie zur Überprüfung der VM-Agent-Installation finden Sie [hier](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork: Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Erstellen eines sicheren Netzwerkkommunikationskanals fehlgeschlagen.

Fehlercode: ExtensionSnapshotFailedNoSecureNetwork <br/> Fehlermeldung: Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Erstellen eines sicheren Netzwerkkommunikationskanals fehlgeschlagen.

* Öffnen Sie den Registrierungs-Editor, indem Sie **regedit.exe** im Modus mit erhöhten Rechten ausführen.
* Identifizieren Sie alle auf Ihrem System vorhandenen Versionen von .NET Framework. Sie werden unter der Hierarchie des Registrierungsschlüssels **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** aufgeführt.
* Fügen Sie für jede im Registrierungsschlüssel vorhandene .NET Framework-Version den folgenden Schlüssel hinzu: <br> **SchUseStrongCrypto"=dword:00000001** </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure: Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Installieren von Visual C++ Redistributable für Visual Studio 2012 fehlgeschlagen.

Fehlercode: ExtensionVCRedistInstallationFailure <br/> Fehlermeldung: Der Momentaufnahmevorgang ist aufgrund eines Fehlers beim Installieren von Visual C++ Redistributable für Visual Studio 2012 fehlgeschlagen.

* Navigieren Sie zu `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion`, und installieren Sie vcredist2013_x64.<br/>Stellen Sie sicher, dass der richtige Registrierungsschlüsselwert zum Zulassen der Dienstinstallation festgelegt wird. Das heißt, legen Sie den Wert für **Start** in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** auf **3** und nicht auf **4** fest. <br><br>Wenn immer noch Probleme bei der Installation bestehen, starten Sie den Installationsdienst neu, indem Sie an einer Eingabeaufforderung mit erhöhten Rechten den Befehl **MSIEXEC /UNREGISTER** und dann **MSIEXEC /REGISTER** ausführen.
* Überprüfen Sie das Ereignisprotokoll auf Zugriffsprobleme. Beispiel: *Product: Microsoft Visual C++ 2013 x64 Minimum Runtime - 12.0.21005 -- Error 1401.Could not create key: Software\Classes.  System error 5.  Verify that you have sufficient access to that key, or contact your support personnel.* (Produkt: Microsoft Visual C++ 2013 x64 Minimum Runtime, 12.0.21005, Fehler 1401. Schlüssel Software\Classes konnte nicht erstellt werden. Systemfehler 5. Überprüfen Sie, ob Sie ausreichende Zugriffsrechte für diesen Schlüssel besitzen, oder setzen Sie sich mit dem Support in Verbindung.) <br><br> Stellen Sie sicher, dass das Administrator- oder Benutzerkonto über ausreichende Berechtigungen zum Aktualisieren des Registrierungsschlüssels **HKEY_LOCAL_MACHINE\SOFTWARE\Classes** verfügt. Erteilen Sie ausreichende Berechtigungen, und starten Sie den Windows Azure-Gast-Agent neu.<br><br> <li> Wenn Sie Antivirusprodukte eingerichtet haben, stellen Sie sicher, dass die richtigen Ausschlussregeln festgelegt wurden, um die Installation zu ermöglichen.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy: Auf dem virtuellen Computer ist eine ungültige Richtlinie konfiguriert. Dadurch wird der Momentaufnahmevorgang verhindert.

Fehlercode:  UserErrorRequestDisallowedByPolicy <BR> Fehlermeldung: Auf der VM wurde eine ungültige Richtlinie konfiguriert, die den Momentaufnahmevorgang verhindert.

Erwägen Sie bei einer Azure Policy-Richtlinie, mit der die [Tag-Governance in Ihrer Umgebung verwaltet wird](../governance/policy/tutorials/govern-tags.md), entweder die Änderung der Richtlinie von der [Auswirkung „Deny“](../governance/policy/concepts/effects.md#deny) in die [Auswirkung „Modify“](../governance/policy/concepts/effects.md#modify), oder erstellen Sie die Ressourcengruppe manuell gemäß des [für Azure Backup erforderlichen Benennungsschemas](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Aufträge

| Fehlerdetails | Problemumgehung |
| --- | --- |
| Ein Abbruch wird für diesen Auftragstyp nicht unterstützt: <br>Warten Sie, bis der Auftrag abgeschlossen ist. |Keine |
| Der Auftrag kann in diesem Status nicht abgebrochen werden: <br>Warten Sie, bis der Auftrag abgeschlossen ist. <br>**or**<br> Der ausgewählte Auftrag kann in diesem Status nicht abgebrochen werden: <br>Warten Sie auf den Abschluss des Auftrags. |Wahrscheinlich ist der Auftrag fast vollständig abgeschlossen. Warten Sie, bis der Auftrag vollständig abgeschlossen wurde.|
| Azure Backup kann den Auftrag nicht abbrechen, weil sich dieser nicht in Bearbeitung befindet: <br>Ein Abbruch wird nur für in Bearbeitung befindliche Aufträge unterstützt. Versuchen Sie, einen in Bearbeitung befindlichen Auftrag abzubrechen. |Dieser Fehler tritt aufgrund eines Übergangszustands auf. Warten Sie eine Minute, und wiederholen Sie den Abbruchvorgang. |
| Azure Backup konnte den Auftrag nicht abbrechen: <br>Warten Sie, bis der Auftrag abgeschlossen ist. |Keine |

## <a name="restore"></a>Restore

### <a name="disks-appear-offline-after-file-restore"></a>Datenträger werden nach der Dateiwiederherstellung offline angezeigt

Gehen Sie wie folgt vor, falls Sie nach der Wiederherstellung feststellen, dass sich die Datenträger im Offlinezustand befinden:

* Vergewissern Sie sich, dass der Computer, auf dem das Skript ausgeführt wird, die Betriebssystemanforderungen erfüllt. [Weitere Informationen](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Stellen Sie sicher, dass Sie die Wiederherstellung nicht auf derselben Quelle durchführen ([weitere Informationen](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound: Fehler bei Wiederherstellung. Momentaufnahme der VM nicht gefunden

Fehlercode: UserErrorInstantRpNotFound <br>
Fehlermeldung: Fehler bei Wiederherstellung. Momentaufnahme der VM nicht gefunden. Überprüfen Sie, ob die Momentaufnahme möglicherweise gelöscht wurde.<br>

Dieser Fehler tritt auf, wenn Sie versuchen, eine Wiederherstellung aus einem Wiederherstellungspunkt durchzuführen, der nicht in den Tresor übertragen und in der Momentaufnahmephase gelöscht wurde. 
<br>
Um dieses Problem zu beheben, versuchen Sie, den virtuellen Computer von einem anderen Wiederherstellungspunkt aus wiederherzustellen.<br>

#### <a name="common-errors"></a>Häufige Fehler 
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

#### <a name="windows-vms---set-up-the-agent"></a>Virtuelle Windows-Computer – Einrichten des Agents

* Laden Sie den [Agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)herunter, und installieren Sie ihn. Sie benötigen Administratorberechtigungen, um die Installation ausführen zu können.
* [Aktualisieren Sie die VM-Eigenschaft](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) auf mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computern, um anzugeben, dass der Agent installiert wurde. Dieser Schritt ist nicht für virtuelle Azure Resource Manager-Computer erforderlich.

#### <a name="linux-vms---set-up-the-agent"></a>Virtuelle Linux-Computer – Einrichten des Agents

* Installieren Sie die neueste Version des Agents über das Repository der Distribution. Ausführliche Informationen zum Paketnamen finden Sie im [Linux-Agent-Repository](https://github.com/Azure/WALinuxAgent).
* [Aktualisieren Sie die VM-Eigenschaft](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) auf mit dem klassischen Bereitstellungsmodell erstellten VMs, und stellen Sie sicher, dass der Agent installiert ist. Dieser Schritt ist nicht für virtuelle Azure Resource Manager-Computer erforderlich.

### <a name="update-the-vm-agent"></a>Aktualisieren des VM-Agents

#### <a name="windows-vms---update-the-agent"></a>Virtuelle Windows-Computer – Aktualisieren des Agents

* Installieren Sie die [Binärdateien für den Agent des virtuellen Computers](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) neu, um den Agent zu aktualisieren. Stellen Sie vor dem Aktualisieren des Agents sicher, dass während des Agent-Updates für den virtuellen Computer keine Sicherungsvorgänge durchgeführt werden.

#### <a name="linux-vms---update-the-agent"></a>Virtuelle Linux-Computer – Aktualisieren des Agents

* Aktualisieren Sie den Agent des virtuellen Linux-Computers gemäß den Anweisungen im Artikel [Aktualisieren des Azure Linux-Agents](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Verwenden Sie zum Aktualisieren des Agents immer das Repository der Distribution.

    Laden Sie den Agent-Code nicht von GitHub herunter. Wenn der aktuelle Agent für Ihre Distribution nicht verfügbar ist, wenden Sie sich an den Distributionssupport, um zu erfahren, wie Sie den aktuellen Agent erhalten können. Sie können sich auch im GitHub-Repository über den aktuellen [Microsoft Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) informieren.

### <a name="validate-vm-agent-installation"></a>Überprüfen der VM-Agent-Installation

Gehen Sie wie folgt vor, um die VM-Agent-Version auf virtuellen Windows-Computern zu überprüfen:

1. Melden Sie sich beim virtuellen Azure-Computer an, und navigieren Sie zum Ordner **C:\WindowsAzure\Packages**. Darin befindet sich die Datei **WaAppAgent.exe**.
2. Klicken Sie mit der rechten Maustaste auf die Datei, und wechseln Sie zu **Eigenschaften**. Wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der **Produktversion** sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

## <a name="troubleshoot-vm-snapshot-issues"></a>Behandeln von Problemen mit VM-Momentaufnahmen

Bei der VM-Sicherung werden Momentaufnahmenbefehle an den zugrunde liegenden Speicher ausgegeben. Wenn Sie bei der Ausführung von Momentaufnahmeaufgaben keinen Zugriff auf Storage haben oder dabei Verzögerungen auftreten, kann es beim Sicherungsauftrag zu Fehlern kommen. Die folgenden Umstände können zu Fehlern bei Momentaufnahmeaufgaben führen:

* **Virtuelle Computer mit konfigurierter SQL Server-Sicherung können Momentaufnahmeaufgaben verzögern**. Standardmäßig wird bei der Sicherung virtueller Computer eine vollständige VSS-Sicherung auf virtuellen Windows-Computern erstellt. Bei virtuellen Computern mit SQL Server und einer konfigurierten SQL Server-Sicherung kann es zu Verzögerungen bei Momentaufnahmen kommen. Wenn Verzögerungen bei Momentaufnahmen Sicherungsfehler verursachen, legen Sie den folgenden Registrierungsschlüssel fest:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Der VM-Status wird falsch gemeldet, da der virtuelle Computer im RDP heruntergefahren ist**. Wenn Sie den virtuellen Computer über eine Remotedesktopverbindung heruntergefahren haben, überprüfen Sie im Portal, ob der VM-Status korrekt ist. Wenn dies nicht der Fall ist, fahren Sie den virtuellen Computer mit der Option **Herunterfahren** über das Dashboard des virtuellen Computers im Portal herunter.
* **Wenn mehr als vier virtuelle Computer denselben Clouddienst gemeinsam nutzen, müssen Sie die VMs über mehrere Sicherungsrichtlinien verteilen**. Legen Sie die Sicherungszeiten so fest, dass Sicherungen für maximal vier virtuelle Computer gleichzeitig ausgeführt werden. In den Richtlinien sollte zwischen den Startzeiten mindestens ein Zeitraum von einer Stunde liegen.
* **Der virtuelle Computer wird bei hoher CPU- oder Arbeitsspeicherauslastung ausgeführt**. Wenn die Arbeitsspeicher- oder CPU-Auslastung des virtuellen Computers hoch ist (mehr als 90 Prozent beträgt), wird die Momentaufnahmeaufgabe in die Warteschlange eingereiht und deren Ausführung verzögert. Schließlich tritt ein Timeout auf. Wenn dieses Problem auftritt, sollten Sie eine bedarfsgesteuerte Sicherung ausführen.

## <a name="networking"></a>Netzwerk

Für die VM-Sicherung mithilfe von IaaS muss auf dem Gastsystem DHCP aktiviert sein. Wenn Sie eine statische private IP-Adresse benötigen, konfigurieren Sie diese im Azure-Portal oder mithilfe von PowerShell. Stellen Sie sicher, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist.
Weitere Informationen zum Einrichten einer statischen IP-Adresse mithilfe von PowerShell finden Sie unter:

* [Hinzufügen einer statischen internen IP-Adresse zu einem vorhandenen virtuellen Computer](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Ändern der Zuordnungsmethode für eine private IP-Adresse, die einer Netzwerkschnittstelle zugewiesen ist](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)