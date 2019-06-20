---
title: Azure Site Recovery-Behandlung von akuten Problemen bei der Azure-zu-Azure-Replikation | Microsoft-Dokumentation
description: Behandlung von Fehlern und Problemen beim Replizieren von virtuellen Azure-Computern für die Notfallwiederherstellung
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: bf24b2d1395e128dc73361670ea93ac938574146
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258782"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Behandlung von akuten Problemen bei der Azure-zu-Azure-VM-Replikation

In diesem Artikel werden allgemeine Probleme in Azure Site Recovery bei der Replikation und Wiederherstellung von virtuellen Azure-Computern zwischen verschiedenen Regionen beschrieben. Darüber hinaus wird die Problembehandlung beschrieben. Weitere Informationen zu unterstützten Konfigurationen finden Sie unter [Azure Site Recovery support matrix for replicating from Azure to Azure](site-recovery-support-matrix-azure-to-azure.md) (Azure Site Recovery-Supportmatrix zum Replizieren aus Azure in Azure).

Azure Site Recovery repliziert Daten konsistent von der Quellregion zur Notfallwiederherstellungsregion und erstellt alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt. Wenn Site Recovery 60 Minuten lang keine Wiederherstellungspunkte erstellen kann, werden Sie mit diesen Informationen gewarnt:

Fehlermeldung: „In den letzten 60 Minuten stand kein absturzkonsistenter Wiederherstellungspunkt für den virtuellen Computer zur Verfügung.“</br>
Fehler-ID: 153007 </br>

In den folgenden Abschnitten werden Ursachen und Lösungen beschrieben.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hohe Datenänderungsrate auf dem virtuellen Quellcomputer
Azure Site Recovery löst ein Ereignis aus, wenn die Datenänderungsrate auf dem virtuellen Quellcomputer die unterstützten Grenzwerte übersteigt. Um festzustellen, ob das Problem auf eine hohe Änderungsrate zurückzuführen ist, navigieren Sie zu **Replizierte Elemente** > **VM** > **Ereignisse – letzte 72 Stunden**.
Das Ereignis „Die Datenänderungsrate überschreitet die unterstützten Grenzwerte.“ sollte angezeigt werden:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Wenn Sie das Ereignis auswählen, sehen Sie die genauen Datenträgerinformationen:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-Grenzwerte
Die folgende Tabelle enthält die Azure Site Recovery-Grenzwerte. Diese Grenzwerte basieren auf unseren Tests, können aber nicht alle möglichen E/A-Kombinationen für Anwendungen abdecken. Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die Anwendungen variieren. 

Es gibt zwei Einschränkungen zu berücksichtigen, die Datenänderungen pro Datenträger und die Datenänderung pro virtuellem Computer. Als Beispiel betrachten wir den Premium-P20-Datenträger in der folgenden Tabelle. Site Recovery kann aufgrund des Limits von maximal 25MB/s für alle Änderungen pro virtuellem Computer Änderungen mit 5MB/s pro Datenträger und maximal fünf solcher Datenträger pro VM verarbeiten.

**Replikationsspeicherziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate pro Tag der Quelldatenträger**
---|---|---|---
Standardspeicher | 8 KB | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB  | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |  336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |10 MB/s | 842 GB pro Datenträger

### <a name="solution"></a>Lösung
Für Azure Site Recovery gelten Grenzwerte für die Datenänderungsrate basierend auf dem Typ des Datenträgers. Um herauszufinden, ob sich dieses Problem wiederholt oder ob es vorübergehend auftritt, ermitteln Sie die Datenänderungsrate des betroffenen virtuellen Computers. Navigieren Sie zu dem virtuellen Quellcomputer, suchen Sie die Metriken unter **Überwachung**, und fügen Sie die Metriken wie in diesem Screenshot dargestellt hinzu:

![Suchen der Datenänderungsrate in drei Schritten](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Wählen Sie **Metrik hinzufügen** aus, und fügen Sie **Auf den Betriebssystemdatenträger geschriebene Bytes/s** und **Auf den Datenträger geschriebene Bytes/s** hinzu.
2. Überwachen Sie die im Screenshot dargestellte Spitze.
3. Zeigen Sie die Gesamtanzahl an Schreibvorgängen an, die auf Betriebssystemdatenträgern und allen Datenträgern in Kombination auftreten. Diese Metriken bietet Ihnen möglicherweise nicht die Informationen auf Datenträgerebene, aber sie geben das gesamte Muster der Datenänderungsrate an.

Wenn gelegentlich eine große Datenmenge anfällt und die Datenänderungsrate zeitweise über 10MB/s (bei Premium) und 2MB/s (bei Standard) liegt und dann zurückgeht, kann die Replikation folgen. Wenn die Änderung in den meisten Fällen jedoch weit über dem unterstützten Grenzwert liegt, sollten Sie wenn möglich eine der folgenden Optionen berücksichtigen:

* **Schließen Sie den Datenträger aus, der eine hohe Datenänderungsrate verursacht**: Sie können den Datenträger mithilfe von [PowerShell](./azure-to-azure-exclude-disks.md) ausschließen. Um den Datenträger auszuschließen, müssen Sie zuerst die Replikation deaktivieren. 
* **Ändern Sie den Tarif des Speicherdatenträgers für die Notfallwiederherstellung**: Diese Option ist nur möglich, wenn die Datenänderungsrate weniger als 10MB/s beträgt. Nehmen wir an, eine VM mit einem P10-Datenträger weist eine Datenänderungsrate zwischen 8MB/s und 10MB/s auf. Wenn ein Kunde den P30-Datenträger während des Schutzes als Zielspeicher verwenden kann, kann das Problem gelöst werden.

## <a name="Network-connectivity-problem"></a>Probleme mit der Netzwerkkonnektivität

### <a name="network-latency-to-a-cache-storage-account"></a>Netzwerklatenz bei Verbindung mit Cachespeicherkonto
Site Recovery sendet replizierte Daten an das Cachespeicherkonto. Möglicherweise stellen Sie Netzwerklatenz fest, wenn das Hochladen der Daten aus einem virtuellen Computer in das Cachespeicherkonto langsamer ist als 4MB in 3 Sekunden. 

Verwenden Sie den Befehl [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) zum Hochladen von Daten vom virtuellen Computer in das Cachespeicherkonto, um zu überprüfen, ob ein Latenzproblem vorliegt. Wenn die Latenz hoch ist, überprüfen Sie, ob Sie ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) zum Steuern des ausgehenden Netzwerkdatenverkehrs von virtuellen Computern verwenden. Das virtuelle Netzwerkgerät wird möglicherweise gedrosselt, wenn der gesamte Replikationsdatenverkehr durch das virtuelle Netzwerkgerät läuft. 

Es wird empfohlen, einen Netzwerk-Dienstendpunkt in Ihrem virtuellen Netzwerk für „Storage“ zu erstellen, damit der Replikationsdatenverkehr nicht an das virtuelle Netzwerkgerät geleitet wird. Weitere Informationen finden Sie unter [Konfiguration der virtuellen Netzwerkappliance](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Netzwerkverbindung
Damit die Site Recovery-Replikation funktioniert, ist für die VM die ausgehende Konnektivität zu bestimmten URLs oder IP-Bereichen erforderlich. Wenn sich Ihr virtueller Computer hinter einer Firewall befindet oder Netzwerksicherheitsgruppen-Regeln (NSG-Regeln) zum Steuern der ausgehenden Konnektivität verwendet werden, tritt ggf. eines dieser Probleme auf. Um sicherzustellen, dass alle URLs verbunden sind, informieren Sie sich unter [Ausgehende Konnektivität für IP-Adressbereiche](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fehler-ID 153006 – In den letzten „XXX“ Minuten stand kein anwendungskonsistenter Wiederherstellungspunkt für den virtuellen Computer zur Verfügung.

Einige der häufigsten Probleme sind nachfolgend aufgeführt:

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Ursache 1: Bekanntes Problem in SQL Server 2008/2008 R2 
**Problembehandlung**: Es gibt ein bekanntes Problem mit SQL Server 2008/2008 R2. Weitere Informationen finden Sie in diesem KB-Artikel: [Fehler bei Azure Site Recovery-Agent oder einer anderen komponentenfreien VSS-Sicherung für einen Server, der SQL Server 2008 R2 hostet](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>Ursache 2: Azure Site Recovery-Aufträge schlagen auf Servern fehl, die eine beliebige Version von SQL Server-Instanzen mit AUTO_CLOSE DBs hosten 
**Problembehandlung**: Referenz-KB-[Artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Ursache 3: Bekanntes Problem in SQL Server 2016 und 2017
**Problembehandlung**: Referenz-KB-[Artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Ursache 4: Sie verwenden die Konfiguration direkter Speicherplätze
**Problembehandlung**: Azure Site Recovery kann keinen anwendungskonsistenten Wiederherstellungspunkt für die Konfiguration von direkten Speicherplätzen erstellen. Weitere Informationen finden Sie im Artikel zum ordnungsgemäßen [Konfigurieren der Replikationsrichtlinie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-due-to-vss-related-issues"></a>Weitere Ursachen aufgrund von VSS-bezogenen Problemen:

Überprüfen Sie zur weiteren Problembehandlung die Dateien auf dem Quellcomputer, um den genauen Fehlercode zu erhalten:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Wie können die Fehler in der Datei gefunden werden?
Suchen Sie nach der Zeichenfolge „vacpError“, indem Sie die Datei „vacp.log“ in einem Editor öffnen.
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Im obigen Beispiel ist **2147754994** der Fehlercode, der Sie über den Fehler informiert, wie unten dargestellt.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS Writer ist nicht installiert – Fehler 2147221164 

*Problembehandlung*: Azure Site Recovery verwendet den Microsoft-VSS (Volumeschattenkopie-Dienst), um Tags für die Anwendungskonsistenz zu erstellen. Es installiert einen VSS-Anbieter für den Betrieb, um Momentaufnahmen zur Anwendungskonsistenz zu erstellen. Dieser VSS-Anbieter wird als Dienst installiert. Falls der VSS-Anbieterdienst nicht installiert ist, tritt bei der Erstellung der Momentaufnahme zur Anwendungskonsistenz ein Fehler mit der ID „0x80040154“ auf – „Klasse nicht registriert“. </br>
Weitere Informationen finden Sie im [Artikel zur Problembehandlung bei der VSS Writer-Installation.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS Writer ist deaktiviert – Fehler 2147943458

**Problembehandlung**: Azure Site Recovery verwendet den Microsoft-VSS (Volumeschattenkopie-Dienst), um Tags für die Anwendungskonsistenz zu erstellen. Es installiert einen VSS-Anbieter für den Betrieb, um Momentaufnahmen zur Anwendungskonsistenz zu erstellen. Dieser VSS-Anbieter wird als Dienst installiert. Falls der VSS-Anbieterdienst deaktiviert ist, tritt bei der Erstellung der Momentaufnahme zur Anwendungskonsistenz ein Fehler mit der ID „Der angegebene Dienst ist deaktiviert und kann nicht gestartet werden (0x80070422)“ auf. </br>

- Wenn VSS deaktiviert ist:
    - Stellen Sie sicher, dass der Starttyp des VSS-Anbieterdiensts auf **Automatic** (Automatisch) festgelegt ist.
    - Starten Sie die folgenden Dienste neu:
        - VSS-Dienst
        - Azure Site Recovery-VSS-Anbieter
        - VDS-Dienst

####  <a name="vss-provider-notregistered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED – Fehler 2147754756

**Problembehandlung**: Azure Site Recovery verwendet den Microsoft-VSS (Volumeschattenkopie-Dienst), um Tags für die Anwendungskonsistenz zu erstellen. Überprüfen Sie, ob der Azure Site Recovery VSS-Anbieterdienst installiert ist. </br>

- Wiederholen Sie die Installation des Anbieters mit den folgenden Befehlen:
- Vorhandenen Anbieter deinstallieren: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Neuinstallation von: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Stellen Sie sicher, dass der Starttyp des VSS-Anbieterdiensts auf **Automatic** (Automatisch) festgelegt ist.
    - Starten Sie die folgenden Dienste neu:
        - VSS-Dienst
        - Azure Site Recovery-VSS-Anbieter
        - VDS-Dienst
