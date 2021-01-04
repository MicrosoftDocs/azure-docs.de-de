---
title: Problembehandlung bei der Hyper-V-Notfallwiederherstellung mit Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Probleme bei der Notfallwiederherstellung während der Hyper-V-Replikation in Azure mithilfe von Azure Site Recovery behoben werden.
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750801"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Problembehandlung bei der Hyper-V-zu-Azure-Replikation und Failover

In diesem Artikel werden häufig auftretende Probleme bei der Replikation von lokalen virtuellen Hyper-V-Computern in Azure mithilfe von [Azure Site Recovery](site-recovery-overview.md) beschrieben.

## <a name="enable-protection-issues"></a>Probleme beim Aktivieren des Schutzes

Überprüfen Sie die folgenden Empfehlungen, wenn beim Aktivieren des Schutzes für virtuelle Hyper-V-Computer Probleme auftreten:

1. Prüfen Sie, ob die Hyper-V-Hosts und -VMs alle [Anforderungen und Voraussetzungen](hyper-v-azure-support-matrix.md) erfüllen.
2. Wenn Hyper-V-Server sich in System Center Virtual Machine Manager-Clouds (VMM-Clouds) befinden, vergewissern Sie sich, dass Sie den [VMM-Server](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional) vorbereitet haben.
3. Stellen Sie sicher, dass der Hyper-V-Verwaltungsdienst für virtuelle Computer auf den Hyper-V-Hosts ausgeführt wird.
4. Überprüfen Sie auf Probleme, die bei der Hyper-V-VMMS\Admin-Anmeldung bei der VM angezeigt werden. Dieses Protokoll befindet sich unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows**.
5. Überprüfen Sie auf dem virtuellen Gastcomputer, ob WMI aktiviert und verfügbar ist.
   - [Informationen zu](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) grundlegenden WMI-Tests.
   - [Problembehandlung](/windows/win32/wmisdk/wmi-troubleshooting) bei WMI.
   - [Problembehandlung](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) bei WMI-Skripts und WMI-Diensten.
6. Stellen Sie auf dem virtuellen Gastcomputer sicher, dass die neueste Version von Integration Services ausgeführt wird.
    - [Überprüfen Sie](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services), ob die letzte Version installiert ist.
    - [Halten Sie](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integration Services auf dem neuesten Stand.

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>Der Schutz kann nicht aktiviert werden, weil der virtuelle Computer nicht hochverfügbar ist (Fehlercode 70094).

Wenn Sie Replikation für einen Computer aktivieren und eine Fehlermeldung mit dem Hinweis angezeigt wird, dass die Replikation nicht aktiviert werden kann, weil der Computer nicht hochverfügbar ist, führen Sie die folgenden Schritte aus, um das Problem zu beheben:

- Starten Sie den VMM-Dienst auf dem VMM-Server neu.
- Entfernen Sie en virtuellen Computer aus dem Cluster, und fügen Sie ihn dann erneut hinzu.

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>Fehler beim VSS Writer NTDS mit Status 11 und writerspezifischem Fehlercode 0x800423f4.

Wenn Sie versuchen, Replikation zu aktivieren, wird möglicherweise ein Fehler angezeigt, der Sie informiert, dass die Aktivierung der Replikation aufgrund eines NTDS-Fehlers fehlgeschlagen ist. Eine der möglichen Ursachen für dieses Problem ist, dass das Betriebssystem des virtuellen Computers Windows Server 2012 und nicht Windows Server 2012 R2 ist. Führen Sie die unten angegebenen Schritte aus, um dieses Problem zu beheben:

- Aktualisieren Sie auf Windows Server R2 mit angewendetem Fix 4072650.
- Stellen Sie sicher, dass der Hyper-V-Host ebenfalls Windows 2016 oder höher ausführt.

## <a name="replication-issues"></a>Probleme bei der Replikation

Beheben Sie Probleme bei der anfänglichen und laufenden Replikation wie folgt:

1. Stellen Sie sicher, dass die [neueste Version](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) der Site Recovery-Dienste ausgeführt wird.
2. Überprüfen Sie, ob die Replikation angehalten wurde:
   - Überprüfen Sie den VM-Integritätsstatus in der Hyper-V-Manager-Konsole.
   - Wenn dieser kritisch ist, klicken Sie mit der rechten Maustaste auf den virtuellen Computer > **Replikation** > **Replikationsstatus anzeigen**.
   - Wenn die Replikation angehalten wurde, klicken Sie auf **Replikation fortsetzen**.
3. Vergewissern Sie sich, dass die erforderlichen Dienste ausgeführt werden. Wenn dies nicht der Fall ist, starten Sie sie neu.
    - Wenn Sie Hyper-V ohne VMM replizieren, überprüfen Sie, ob folgende Dienste auf dem Hyper-V-Host ausgeführt werden:
        - Verwaltungsdienst für virtuelle Computer
        - Microsoft Azure Recovery Services-Agent
        - Microsoft Azure Site Recovery
        - WMI Provider Host
    - Wenn Sie die Replikation mit VMM in der Umgebung durchführen, überprüfen Sie, ob folgende Dienste ausgeführt werden:
        - Überprüfen Sie auf dem Hyper-V-Host, ob der Verwaltungsdienst für virtuelle Computer, der Microsoft Azure Recovery Services-Agent und der WMI Provider Host-Dienst ausgeführt werden.
        - Stellen Sie auf dem VMM-Server sicher, dass der System Center Virtual Machine Manager-Dienst ausgeführt wird.
4. Überprüfen Sie die Konnektivität zwischen dem Hyper-V-Server und Azure. Öffnen Sie zum Überprüfen der Konnektivität den Task-Manager auf dem Hyper-V-Host. Klicken Sie auf der Registerkarte **Leistung** auf **Ressourcenmonitor öffnen**. Überprüfen Sie auf der Registerkarte **Netzwerk** > **Prozess mit Netzwerkaktivität**, ob „cbengine.exe“ aktiv große Datenvolumen (MB) sendet.
5. Überprüfen Sie, ob die Hyper-V-Hosts eine Verbindung mit der Azure Storage Blob-URL herstellen können. Zum Überprüfen, ob der Host eine Verbindung herstellen kann, wählen und überprüfen Sie **cbengine.exe**. Öffnen Sie **TCP-Verbindungen**, um die Konnektivität zwischen dem Host und Azure Storage Blob zu prüfen.
6. Überprüfen Sie Leistungsprobleme entsprechend der folgenden Beschreibung.
    
### <a name="performance-issues"></a>Leistungsprobleme

Einschränkungen der Netzwerkbandbreite können sich auf die Replikation auswirken. Beheben Sie diese Probleme wie folgt:

1. [Überprüfen Sie](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage), ob in Ihrer Umgebung Einschränkungen der Bandbreite oder Drosselung festgelegt sind.
2. Führen Sie den [Bereitstellungsplanerprofiler](hyper-v-deployment-planner-run.md) aus.
3. Befolgen Sie nach dem Ausführen des Profilers die Empfehlungen zur [Bandbreite](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) und zum [Speicher](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation).
4. Überprüfen Sie die [Einschränkungen für Datenänderungen](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Wenn Sie auf einem virtuellen Computer große Datenänderungen feststellen, gehen Sie wie folgt vor:
   - Überprüfen Sie, ob der virtuelle Computer für die Neusynchronisierung markiert ist.
   - Führen Sie [diese Schritte](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) aus, um die Quelle der Datenänderungen zu untersuchen.
   - Datenänderungen können auftreten, wenn die HRL-Protokolldateien 50 % des verfügbaren Speicherplatzes überschreiten. Wenn dies das Problem ist, stellen Sie für alle betreffenden virtuellen Computer mehr Speicherplatz bereit.
   - Vergewissern Sie sich, dass die Replikation nicht angehalten wurde. Wenn dies der Fall ist, werden die Änderungen weiterhin in die HRL-Datei geschrieben. Dies kann zu einem höheren Dateigröße führen.
 

## <a name="critical-replication-state-issues"></a>Probleme aufgrund des Replikationsstatus „Kritisch“

1. Stellen Sie eine Verbindung mit der lokalen Hyper-V-Manager-Konsole her, wählen Sie den virtuellen Computer aus, und überprüfen Sie die Replikationsintegrität.

    ![Replikationsintegrität](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klicken Sie auf **Replikationsstatus anzeigen**, um die Details anzuzeigen:

    - Wenn die Replikation angehalten wurde, klicken Sie mit der rechten Maustaste auf den virtuellen Computer > **Replikation** > **Replikation fortsetzen**.
    - Wenn ein virtueller Computer auf einem in Site Recovery konfigurierten Hyper-V-Host zu einem anderen Hyper-V-Host im selben Cluster oder zu einem eigenständigen Computer migriert wird, hat dies keine Auswirkung auf die Replikation für den virtuellen Computer. Stellen Sie lediglich sicher, dass der neue Hyper-V-Host alle Voraussetzungen erfüllt und in Site Recovery konfiguriert wurde.

## <a name="app-consistent-snapshot-issues"></a>Probleme in Bezug auf App-konsistente Momentaufnahmen

Eine App-konsistente Momentaufnahme ist eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb des virtuellen Computers. VSS (Volume Shadow Copy Service, Volumeschattenkopie-Dienst) stellt sicher, dass Apps auf dem virtuellen Computer zum Zeitpunkt der Momentaufnahme konsistent sind.  In diesem Abschnitt werden einige häufig auftretende Probleme erläutert.

### <a name="vss-failing-inside-the-vm"></a>VSS-Fehler innerhalb des virtuellen Computers

1. Stellen Sie sicher, dass die neueste Version von Integration Services installiert ist und ausgeführt wird.  Überprüfen Sie, ob ein Update verfügbar ist. Führen Sie dazu auf dem Hyper-V-Host den folgenden Befehl an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten aus: **get-vm  | select Name, State, IntegrationServicesState**.
2. Vergewissern Sie sich, dass die VSS-Dienste ausgeführt werden und fehlerfrei sind:
   - Um die Dienste zu überprüfen, melden Sie sich bei der Gast-VM an. Öffnen Sie dann als Administrator eine Eingabeaufforderung, und führen Sie die folgenden Befehle aus, um zu überprüfen, ob alle VSS-Writer fehlerfrei sind.
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin list providers**
   - Überprüfen Sie die Ausgabe. Wenn sich Writer in einem fehlerhaften Zustand befinden, gehen Sie wie folgt vor:
       - Überprüfen Sie das Anwendungsereignisprotokoll auf dem virtuellen Computer auf Fehler bei VSS-Vorgängen.
   - Versuchen Sie, diese dem fehlerhaften Writer zugeordneten Dienste neu zu starten:
     - Volumeschattenkopie-Dienst
       - Azure Site Recovery-VSS-Anbieter
   - Warten Sie anschließend einige Stunden ab, ob App-konsistente Momentaufnahmen erfolgreich generiert werden.
   - Führen Sie als letzte Möglichkeit einen Neustart des virtuellen Computers durch. Dadurch werden möglicherweise Probleme mit Diensten behoben, die nicht reagieren.
3. Stellen Sie sicher, dass sich keine dynamischen Datenträger im virtuellen Computer befinden. Dies wird bei App-konsistenten Momentaufnahmen nicht unterstützt. Sie können dies in der Datenträgerverwaltung („diskmgmt.msc“) überprüfen.

    ![Dynamischer Datenträger](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Vergewissern Sie sich, dass kein iSCSI-Datenträger an den virtuellen Computer angefügt ist. Dies wird nicht unterstützt.
5. Überprüfen Sie, ob der Backup-Dienst aktiviert ist. Überprüfen Sie dies unter **Hyper-V-Einstellungen** > **Integration Services**.
6. Stellen Sie sicher, dass keine Konflikte mit Apps vorliegen, die VSS-Momentaufnahmen erstellen. Wenn mehrere Apps versuchen, gleichzeitig VSS-Momentaufnahmen zu erstellen, können Konflikte auftreten. Dies ist z.B. der Fall, wenn eine Backup-App VSS-Momentaufnahmen erstellt, in der Replikationsrichtlinie jedoch geplant ist, dass Site Recovery eine Momentaufnahme erstellt.   
7. Überprüfen Sie, ob der virtuelle Computer eine hohe Änderungsrate aufweist:
    - Sie können die tägliche Datenänderungsrate für die virtuellen Gastcomputer mithilfe von Leistungsindikatoren auf dem Hyper-V-Host messen. Aktivieren Sie den folgenden Leistungsindikator zum Messen der Datenänderungsrate. Aggregieren Sie eine Stichprobe dieses Werts für die VM-Datenträger in einem Zeitraum von 5 bis 15 Minuten, um die Änderungsrate des virtuellen Computers zu ermitteln.
        - Kategorie: „Virtuelles Hyper-V-Speichergerät“
        - Leistungsindikator: „Geschriebene Bytes/s“</br>
        - Die Datenänderungsrate erhöht sich oder bleibt hoch, abhängig von der Auslastung des virtuellen Computers oder der zugehörigen Apps.
        - Die durchschnittliche Datenänderung des Quelldatenträgers beläuft sich für den Standardspeicher für Site Recovery auf 2 MB/s. [Weitere Informationen](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Darüber hinaus können Sie [Skalierbarkeitsziele überprüfen](../storage/common/scalability-targets-standard-account.md).
8. Stellen Sie bei Verwendung eines Linux-basierten Servers sicher, auf dem Server App-Konsistenz zu aktivieren. [Weitere Informationen](./site-recovery-faq.md#replication)
9. Führen Sie den [Bereitstellungsplaner](hyper-v-deployment-planner-run.md) aus.
10. Sehen Sie die Empfehlungen für das [Netzwerk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) und den [Speicher](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) durch.


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS-Fehler innerhalb des Hyper-V-Hosts

1. Überprüfen Sie die Ereignisprotokolle auf VSS-Fehler und Empfehlungen:
    - Öffnen Sie auf dem Hyper-V-Hostserver das Hyper-V-Admin-Ereignisprotokoll unter **Ereignisanzeige** > **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Hyper-V** > **Admin**.
    - Überprüfen Sie, ob Ereignisse vorhanden sind, die auf Fehler bei App-konsistenten Momentaufnahmen hinweisen.
    - Eine typische Fehlermeldung lautet: Hyper-V konnte keinen VSS-Momentaufnahmesatz für die VM „XYZ“ generieren. Beim Writer ist ein dauerhafter Fehler aufgetreten. Restarting the VSS service might resolve issues if the service is unresponsive.“ (Hyper-V konnte keinen VSS-Momentaufnahmesatz für den virtuellen Computer „XYZ“ generieren: Beim Writer ist ein dauerhafter Fehler aufgetreten. Möglicherweise können die Probleme durch einen Neustart des VSS-Diensts behoben werden, wenn der Dienst nicht reagiert.)

2. Um VSS-Momentaufnahmen für den virtuellen Computer zu generieren, überprüfen Sie, ob Hyper-V Integration Services auf dem virtuellen Computer installiert sind und ob der Backup-Integrationsdienst (VSS) aktiviert ist.
    - Stellen Sie sicher, dass der Integration Services-VSS-Dienst bzw. die Daemons auf dem Gastcomputer ausgeführt werden und den Status **OK** aufweisen.
    - Dies können Sie in einer PowerShell-Sitzung mit erhöhten Rechten auf dem Hyper-V-Host mit dem Befehl **Get-VMIntegrationService -VMName\<VMName>-Name VSS** überprüfen. Außerdem können Sie diese Informationen durch Anmelden bei der Gast-VM abrufen. [Weitere Informationen](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)
    - Stellen Sie sicher, dass die Backup/VSS-Integrationsdienste auf dem virtuellen Computer ausgeführt werden und fehlerfrei sind. Starten Sie andernfalls diese Dienste und den Hyper-V-Volumeschattenkopie-Anfordererdienst auf dem Hyper-V-Hostserver neu.

### <a name="common-errors"></a>Häufige Fehler

**Fehlercode** | **Meldung** | **Details**
--- | --- | ---
**0x800700EA** | Hyper-V konnte keinen VSS-Momentaufnahmesatz für die VM generieren: Weitere Daten sind verfügbar. (0x800700EA). VSS snapshot set generation can fail if backup operation is in progress.<br/><br/> Beim Replikationsvorgang für den virtuellen Computer ist ein Fehler aufgetreten: Weitere Daten sind verfügbar. | Überprüfen Sie, ob auf dem virtuellen Computer ein dynamischer Datenträger aktiviert ist. Dies wird nicht unterstützt.
**0x80070032** | Hyper-V Volume Shadow Copy Requestor failed to connect to virtual machine <./VMname> because the version does not match the version expected by Hyper-V. (Der Hyper-V-Volumeschattenkopie-Anforderer konnte keine Verbindung mit dem virtuellen Computer <./VMname> herstellen, da die Version nicht der von Hyper-V erwarteten Version entspricht.) | Überprüfen Sie, ob die neuesten Windows-Updates installiert sind.<br/><br/> Führen Sie ein [Upgrade](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) auf die neueste Version von Integration Services durch.



## <a name="collect-replication-logs"></a>Erfassen der Replikationsprotokolle

Alle Ereignisse für die Hyper-V-Replikation werden im Protokoll „Hyper-V-VMMS\Admin“ unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** protokolliert. Außerdem können Sie wie folgt ein analytisches Protokoll für den Hyper-V-Verwaltungsdienst für virtuelle Computer aktivieren:

1. Blenden Sie die analytischen und Debugprotokolle in der Ereignisanzeige ein. Klicken Sie dazu in der Ereignisanzeige auf **Ansicht** > **Analytische und Debugprotokolle einblenden**. Das analytische Protokoll wird unter **Hyper-V-VMMS** angezeigt.
2. Klicken Sie im Bereich **Aktionen** auf **Protokoll aktivieren**. 

    ![Protokoll aktivieren](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Nach der Aktivierung wird das Protokoll in **Systemmonitor** als **Ereignisablaufverfolgungs-Sitzung** unter **Datensammlersätze** angezeigt. 
4. Zum Anzeigen der gesammelten Daten müssen Sie die Ablaufverfolgungssitzung beenden, um das Protokoll zu deaktivieren. Speichern Sie dann das Protokoll, und öffnen Sie es erneut in der Ereignisanzeige, oder konvertieren Sie es nach Bedarf mit anderen Tools.


### <a name="event-log-locations"></a>Speicherorte von Ereignisprotokollen

**Ereignisprotokoll** | **Details** |
--- | ---
**Anwendungs- und Dienstprotokolle/Microsoft/VirtualMachineManager/Server/Admin** (VMM-Server) | Protokolle für die Problembehandlung bei VMM-Problemen
**Anwendungs- und Dienstprotokolle/MicrosoftAzureRecoveryServices/Replikation** (Hyper-V-Host) | Protokolle für die Problembehandlung von Problemen, die beim Microsoft Azure Recovery Services-Agent auftreten können 
**Anwendungs- und Dienstprotokolle/Microsoft/Azure Site Recovery/Anbieter/Betriebsbereit** (Hyper-V-Host)| Protokolle für die Behandlung von Problemen mit dem Microsoft Azure Site Recovery-Dienst
**Anwendungs- und Dienstprotokolle/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-Host) | Protokolle für die Behandlung von Problemen bei der Verwaltung von virtuellen Hyper-V-Computern

### <a name="log-collection-for-advanced-troubleshooting"></a>Protokollsammlung für eine erweiterte Problembehandlung

Für die erweiterte Problembehandlung können folgende Tools verwendet werden:

-   Führen Sie für VMM eine Site Recovery-Protokollsammlung mithilfe des Tools [Support Diagnostics Platform (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) aus.
-   Laden Sie für Hyper-V ohne VMM [dieses Tool](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) herunter, und führen Sie es auf dem Hyper-V-Host aus, um die Protokolle zu sammeln.