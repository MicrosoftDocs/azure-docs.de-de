---
title: Ausschließen von Datenträgern von der Replikation mit Azure Site Recovery
description: Es wird beschrieben, wie Sie Datenträger mit Azure Site Recovery von der Replikation in Azure ausschließen.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008258"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Ausschließen von Datenträgern von der Notfallwiederherstellung

In diesem Artikel wird beschrieben, wie Datenträger während der Notfallwiederherstellung mit [Azure Site Recovery](site-recovery-overview.md) von der Replikation ausgeschlossen werden, die von einem lokalen Standort aus in Azure durchgeführt wird. Es kann verschiedene Gründe geben, warum Datenträger von der Replikation ausgeschlossen werden sollen:

- Sicherstellen, dass unwichtige Daten auf dem ausgeschlossenen Datenträger nicht repliziert werden
- Optimieren der für die Replikation verbrauchten Bandbreite oder von Zielressourcen
- Einsparen von Speicher- und Netzwerkressourcen, indem nicht benötigte Daten nicht repliziert werden
- Azure-VMs haben die Site Recovery-Replikationsgrenzwerte erreicht


## <a name="supported-scenarios"></a>Unterstützte Szenarios

Die Möglichkeiten zum Ausschließen von Datenträgern von der Replikation sind in der folgenden Tabelle zusammengefasst.

**Azure zu Azure** | **VMware zu Azure** | **Hyper-V in Azure** | **Physischer Server zu Azure**
--- | --- | --- | ---
Ja | Ja | Ja | Ja

## <a name="exclude-limitations"></a>Einschränkungen für den Ausschluss

**Einschränkung** | **Virtuelle Azure-Computer** | **Virtuelle VMware-Computer** | **Virtuelle Hyper-V-Computer**
--- | --- | ---
**Datenträgertypen** | Sie können Basisdatenträger von der Replikation ausschließen.<br/><br/> Sie können keine Betriebssystemdatenträger und keine dynamischen Datenträger ausschließen. Temporäre Datenträger sind standardmäßig ausgeschlossen. | Sie können Basisdatenträger von der Replikation ausschließen.<br/><br/> Sie können keine Betriebssystemdatenträger und keine dynamischen Datenträger ausschließen. | Sie können Basisdatenträger von der Replikation ausschließen.<br/><br/> Sie können keine Betriebssystemdatenträger ausschließen. Es wird empfohlen, keine dynamischen Datenträger auszuschließen. Site Recovery kann nicht feststellen, welche VHD auf der Gast-VM eine einfache oder dynamische VHD ist. Wenn nicht alle abhängigen dynamischen Volumedatenträger ausgeschlossen werden, wird der geschützte dynamische Datenträger auf einer Failover-VM zu einem fehlerhaften Datenträger, sodass die Daten auf diesem Datenträger nicht zugänglich sind.
**Datenträger während der Replikation** | Es ist nicht möglich, einen Datenträger auszuschließen, der gerade repliziert wird.<br/><br/> Deaktivieren Sie die Replikation für die VM, und aktivieren Sie sie anschließend wieder. |  Es ist nicht möglich, einen Datenträger auszuschließen, der gerade repliziert wird. |  Es ist nicht möglich, einen Datenträger auszuschließen, der gerade repliziert wird.
**Mobilitätsdienst (VMware)** | Nicht relevant | Sie können nur Datenträger auf VMs ausschließen, auf denen der Mobilitätsdienst installiert ist.<br/><br/> Dies bedeutet, dass Sie den Mobilitätsdienst manuell auf den VMs installieren müssen, für die sie Datenträger ausschließen möchten. Sie können den Mechanismus für die Pushinstallation nicht nutzen, weil der Mobilitätsdienst dabei erst nach der Aktivierung der Replikation installiert wird. | Nicht relevant.
**Hinzufügen/Entfernen** | Sie können verwaltete Datenträger auf replikationsaktivierten Azure-VMs mit verwalteten Datenträgern hinzufügen. Sie können Datenträger auf replikationsaktivierten Azure-VMs nicht entfernen. | Sie können keine Datenträger mehr hinzufügen oder entfernen, nachdem die Replikation aktiviert wurde. Deaktivieren Sie die Replikation, und aktivieren Sie sie anschließend wieder, um einen Datenträger hinzuzufügen. | Sie können keine Datenträger mehr hinzufügen oder entfernen, nachdem die Replikation aktiviert wurde. Deaktivieren Sie die Replikation, und aktivieren Sie Sie anschließend wieder.
**Failover** | Wenn eine App einen von Ihnen ausgeschlossenen Datenträger benötigt, müssen Sie diesen nach einem Failover manuell erstellen, damit die replizierte App ausgeführt werden kann.<br/><br/> Alternativ können Sie den Datenträger auch während des VM-Failovers erstellen, indem Sie Azure Automation in einen Wiederherstellungsplan integrieren. | Wenn Sie einen Datenträger ausschließen, der von einer App benötigt wird, sollten Sie ihn nach dem Failover in Azure manuell erstellen. | Wenn Sie einen Datenträger ausschließen, der von einer App benötigt wird, sollten Sie ihn nach dem Failover in Azure manuell erstellen.
**Manuell erstellte lokale Failbackdatenträger** | Nicht relevant | **Virtuelle Windows-Computer:** Für Datenträger, die in Azure manuell erstellt werden, wird kein Failback ausgeführt. Wenn Sie also beispielsweise ein Failover für drei Datenträger durchführen und zwei Datenträger direkt auf einem virtuellen Azure-Computer erstellen, erfolgt nur für die drei Datenträger, für die das Failover ausgeführt wurde, ein Failback.<br/><br/> **Virtuelle Linux-Computer:** Für Datenträger, die in Azure manuell erstellt werden, wird ein Failback ausgeführt. Wenn Sie beispielsweise ein Failover für drei Datenträger ausführen und zwei Datenträger auf einer Azure-VM erstellen, wird für alle fünf ein Failback ausgeführt. Sie können keine Datenträger von einem Failback ausschließen, die manuell erstellt wurden. | Für Datenträger, die in Azure manuell erstellt werden, wird kein Failback ausgeführt. Wenn Sie also beispielsweise ein Failover für drei Datenträger durchführen und zwei Datenträger direkt auf einer Azure-VM erstellen, erfolgt nur für die drei Datenträger, für die das Failover ausgeführt wurde, ein Failback.
**Lokale Datenträger, die vom Failback ausgeschlossen sind** | Nicht relevant | Wenn Sie ein Failback auf den ursprünglichen Computer ausführen, umfasst die Konfiguration des VM-Failbackdatenträgers nicht die ausgeschlossenen Datenträger. Datenträger, die von der Replikation von VMware zu Azure ausgeschlossen wurden, sind auf der Failback-VM nicht verfügbar. | Beim Failback zum ursprünglichen Hyper-V-Speicherort bleibt die Konfiguration des VM-Failbackdatenträgers mit der Konfiguration für den ursprünglichen VM-Quelldatenträger identisch. Datenträger, die von der Replikation vom Hyper-V-Standort zu Azure ausgeschlossen wurden, sind auf der Failback-VM verfügbar.



## <a name="typical-scenarios"></a>Typische Szenarien

Beispiele für Datenänderungen, die gut für den Ausschluss geeignet sind, sind Schreibvorgänge in eine Auslagerungsdatei (pagefile.sys) und in die tempdb-Datei von Microsoft SQL Server. Je nach Workload und Speichersubsystem kann in der Auslagerungsdatei und der tempdb-Datei eine erhebliche Menge an Datenänderungen registriert werden. Die Replikation dieser Art von Daten in Azure ist ressourcenintensiv.

- Um die Replikation für eine VM mit nur einem virtuellen Datenträger zu optimieren, der sowohl das Betriebssystem als auch die Auslagerungsdatei enthält, können Sie wie folgt vorgehen:
    1. Unterteilen Sie den einzelnen virtuellen Datenträger in zwei virtuelle Datenträger. Ein virtueller Datenträger enthält das Betriebssystem und der andere die Auslagerungsdatei.
    2. Schließen Sie den Datenträger mit der Auslagerungsdatei von der Replikation aus.

- Um die Replikation für einen Datenträger zu optimieren, der sowohl die tempdb-Datei von Microsoft SQL Server als auch die Systemdatenbankdatei enthält, können Sie wie folgt vorgehen:
    1. Bewahren Sie die Systemdatenbank und die tempdb-Datei auf zwei unterschiedlichen Datenträgern auf.
    2. Schließen Sie den tempdb-Datenträger von der Replikation aus.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Beispiel 1: Ausschließen des tempdb-Datenträgers von SQL Server

Wir sehen uns nun an, wie der Ausschluss von Datenträgern, allgemeine Failovervorgänge sowie ein Failover für einen virtuellen SQL Server-Windows-Quellcomputer **SalesDB**, für den wir „tempdb“ ausschließen möchten, ausgeführt werden. 

### <a name="exclude-disks-from-replication"></a>Ausschließen von Datenträgern von der Replikation

Diese Datenträger befinden sich auf dem virtuellen Windows-Quellcomputer „SalesDB“.

*Name des Datenträgers** | **Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Betriebssystemdatenträger
DB-Disk1| Disk1 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
DB-Disk2 (Datenträger von Schutz ausgeschlossen) | Disk2 | E:\ | Temporäre Dateien
DB-Disk3 (Datenträger von Schutz ausgeschlossen) | Disk3 | F:\ | tempdb-SQL-Datenbank<br/><br/> Ordnerpfad: F:\MSSQL\Data\. Notieren Sie sich den Ordnerpfad vor dem Failover.
DB-Disk4 | Disk4 |G:\ | Benutzerdatenbank 2

1. Wir aktivieren die Replikation für die VM „SalesDB“.
2. Wir schließen Disk2 und Disk3 von der Replikation aus, weil die Datenänderung auf diesen Datenträgern nur temporär ist. 


### <a name="handle-disks-during-failover"></a>Behandeln von Datenträgern während des Failovers

Da Datenträger nicht repliziert werden, sind sie beim Ausführen des Failovers zu Azure auf der Azure-VM, die nach dem Failover erstellt wird, nicht vorhanden. Die Azure-VM verfügt über die in dieser Tabelle aufgeführten Datenträger.

**Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | ---
Disk0 | C:\ | Betriebssystemdatenträger
Disk1 | E:\ | Temporärer Speicher<br/><br/>Dieser Datenträger wird von Azure hinzugefügt. Da „Disk2“ und „Disk3“ von der Replikation ausgeschlossen wurden, ist „E:“ der erste Laufwerkbuchstabe aus der verfügbaren Liste. Azure weist „E:“ dem temporären Speichervolume zu. Die anderen Laufwerkbuchstaben für replizierte Datenträger bleiben unverändert.
Disk2 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
Disk3 | G:\ | Benutzerdatenbank 2

Da Disk3 (SQL-Datenträger „tempdb“) von der Replikation ausgeschlossen wurde und auf der Azure-VM nicht verfügbar ist, befindet sich der SQL-Dienst in unserem Beispiel im angehaltenen Zustand und benötigt den Pfad „F:\MSSQL\Data“. Sie können diesen Pfad auf verschiedene Arten erstellen: 

- Fügen Sie nach dem Failover einen neuen Datenträger hinzu, und weisen Sie den tempdb-Ordnerpfad zu.
- Verwenden Sie einen vorhandenen temporären Speicherdatenträger für den tempdb-Ordnerpfad.

#### <a name="add-a-new-disk-after-failover"></a>Hinzufügen eines neuen Datenträgers nach dem Failover

1. Notieren Sie sich vor dem Failover den SQL-Pfad für „tempdb.mdf“ und „tempdb.ldf“.
2. Fügen Sie dem virtuellen Azure-Failovercomputer über das Azure-Portal einen neuen Datenträger hinzu. Der Datenträger sollte die gleiche Größe wie der tempdb-SQL-Quelldatenträger (Disk3) haben oder größer sein.
3. Melden Sie sich am virtuellen Azure-Computer an.
4. Initialisieren Sie über die Konsole für die Datenträgerverwaltung (diskmgmt.msc) den neu hinzugefügten Datenträger, und formatieren Sie sie.
5. Weisen Sie den gleichen Laufwerkbuchstaben zu, der für den tempdb-SQL-Datenträger verwendet wurde (F:).
6. Erstellen Sie auf dem Volume „F:“ einen tempdb-Ordner (F:\MSSQL\Data).
7. Starten Sie den SQL-Dienst über die Dienstkonsole.

#### <a name="use-an-existing-temporary-storage-disk"></a>Verwenden eines vorhandenen Datenträgers für die temporäre Speicherung 

1. Öffnen Sie eine Eingabeaufforderung.
2. Führen Sie SQL Server im Wiederherstellungsmodus über die Eingabeaufforderung aus.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Führen Sie den folgenden sqlcmd-Befehl aus, um den tempdb-Pfad in den neuen Pfad zu ändern.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Beenden Sie den Microsoft SQL Server-Dienst.

    ```console
    Net stop MSSQLSERVER
    ```

5. Starten Sie den Microsoft SQL Server-Dienst.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware-VMs: Datenträger während des Failbacks zum ursprünglichen Standort

Als Nächstes wird beschrieben, wie Sie Datenträger auf VMware-VMs behandeln, wenn Sie ein Failback zu Ihrem ursprünglichen lokalen Standort ausführen.

- **In Azure erstellte Datenträger**: Da in unserem Beispiel eine Windows-VM verwendet wird, werden von Ihnen in Azure manuell erstellte Datenträger nicht wieder an Ihrem Standort repliziert, wenn Sie ein Failback ausführen oder eine VM erneut schützen.
- **Temporärer Speicherdatenträger in Azure**: Der Datenträger für die temporäre Speicherung wird nicht auf lokalen Hosts repliziert.
- **Ausgeschlossene Datenträger**: Datenträger, die von der Replikation von VMware zu Azure ausgeschlossen wurden, sind nach dem Failback nicht auf der lokalen VM verfügbar.

Bevor Sie für die VMware-VMs das Failback an den ursprünglichen Standort ausführen, lauten die Einstellungen für die Datenträger der Azure-VM wie folgt.

**Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | ---
Disk0 | C:\ | Betriebssystemdatenträger
Disk1 | E:\ | Temporärer Speicher
Disk2 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
Disk3 | G:\ | Benutzerdatenbank 2

Nach dem Failback enthält die VMware-VM am ursprünglichen Speicherort die in der Tabelle angegebenen Datenträger.

**Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | ---
Disk0 | C:\ | Betriebssystemdatenträger
Disk1 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
Disk2 | G:\ | Benutzerdatenbank 2


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V-VMs: Datenträger während des Failbacks zum ursprünglichen Standort

Als Nächstes wird beschrieben, wie Sie Datenträger auf Hyper-V-VMs behandeln, wenn Sie ein Failback zu Ihrem ursprünglichen lokalen Standort ausführen.

- **In Azure erstellte Datenträger**: Von Ihnen in Azure manuell erstellte Datenträger werden nicht wieder an Ihrem Standort repliziert, wenn Sie ein Failback ausführen oder eine VM erneut schützen.
- **Temporärer Speicherdatenträger in Azure**: Der Datenträger für die temporäre Speicherung wird nicht auf lokalen Hosts repliziert.
- **Ausgeschlossene Datenträger**: Nach dem Failback ist die Konfiguration der VM-Datenträger mit der Konfiguration der ursprünglichen VM-Datenträger identisch. Datenträger, die von der Replikation von Hyper-V zu Azure ausgeschlossen wurden, sind auf der Failback-VM verfügbar.

Bevor Sie für die Hyper-V-VMs das Failback an den ursprünglichen Standort ausführen, lauten die Einstellungen für die Datenträger der Azure-VM wie unten angegeben.

**Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | ---
Disk0 | C:\ | Betriebssystemdatenträger
Disk1 | E:\ | Temporärer Speicher
Disk2 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
Disk3 | G:\ | Benutzerdatenbank 2

Nach einem geplanten Failover (Failback) von Azure zur lokalen Hyper-V-Umgebung verfügt die Hyper-V-VM am ursprünglichen Standort über die Datenträger, die in der folgenden Tabelle angegeben sind.

**Name des Datenträgers** | **Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Betriebssystemdatenträger
DB-Disk1 | Disk1 | D:\ | SQL-Systemdatenbank und Benutzerdatenbank 1
DB-Disk2 (ausgeschlossener Datenträger) | Disk2 | E:\ | Temporäre Dateien
DB-Disk3 (ausgeschlossener Datenträger) | Disk3 | F:\ | tempdb-SQL-Datenbank<br/><br/> Ordnerpfad (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | Benutzerdatenbank 2


## <a name="example-2-exclude-the-paging-file-disk"></a>Beispiel 2: Ausschließen des Datenträgers mit der Auslagerungsdatei

Wir sehen uns nun an, wie Sie den Datenträgerausschluss, allgemeine Failover und ein Failover für einen virtuellen Windows-Quellcomputer ausführen, für den wir den Datenträger mit der Datei „pagefile.sys“ auf dem Laufwerk D als auch auf einem anderen Laufwerk ausschließen möchten.


### <a name="paging-file-on-the-d-drive"></a>Auslagerungsdatei auf Laufwerk D

Auf der Quell-VM befinden sich die hier angegebenen Datenträger.

**Name des Datenträgers** | **Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Betriebssystem-Datenträger
DB-Disk1 (von Replikation ausgeschlossen) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Benutzerdaten 1
DB-Disk3 | Disk3 | F:\ | Benutzerdaten 2

Unsere Einstellungen für die Auslagerungsdatei auf der Quell-VM lauten wie folgt:

![Der Screenshot des Dialogfelds „Virtueller Speicher“ mit hervorgehobener Zeile „Laufwerk D: [Auslagerungsdateivolume]“, das eine Auslagerungsdatei mit einer Größe (MB) von 3000 bis 7000 zeigt.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Wir aktivieren die Replikation für die VM.
2. Wir schließen DB-Disk1 von der Replikation aus.

#### <a name="disks-after-failover"></a>Datenträger nach dem Failover

Nach dem Failover verfügt die Azure-VM über die Datenträger, die in der folgenden Tabelle aufgeführt sind.

**Name des Datenträgers** | **Gastbetriebssystemdatenträger** | **Laufwerkbuchstabe** | **Datentyp auf dem Datenträger**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Betriebssystem-Datenträger
DB-Disk1 | Disk1 | D:\ | Temporärer Speicher/pagefile.sys <br/><br/> Da „DB-Disk1 (D:)“ ausgeschlossen wurde, ist „D:“ der erste verfügbare Laufwerkbuchstabe.<br/><br/> Azure weist „D:“ dem temporären Speichervolume zu.<br/><br/> Da „D:“ verfügbar ist, bleibt die Einstellung für die VM-Auslagerungsdatei unverändert.
DB-Disk2 | Disk2 | E:\ | Benutzerdaten 1
DB-Disk3 | Disk3 | F:\ | Benutzerdaten 2

Unsere Einstellungen für die Auslagerungsdatei auf der Azure-VM lauten wie folgt:

![Einstellungen der Auslagerungsdatei auf dem virtuellen Azure-Computer](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Auslagerungsdatei auf einem anderen Laufwerk (nicht D:)

Wir sehen uns nun ein Beispiel an, in dem sich die Auslagerungsdatei nicht auf Laufwerk D befindet.  

Auf der Quell-VM befinden sich die hier angegebenen Datenträger.

**Name des Datenträgers** | **Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Betriebssystem-Datenträger
DB-Disk1 (von Replikation ausgeschlossen) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Benutzerdaten 1
DB-Disk3 | Disk3 | F:\ | Benutzerdaten 2

Unsere Einstellungen für die Auslagerungsdatei auf der lokalen VM lauten wie folgt:

![Einstellungen der Auslagerungsdatei auf dem lokalen virtuellen Computer](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Wir aktivieren die Replikation für die VM.
2. Wir schließen DB-Disk1 von der Replikation aus.

#### <a name="disks-after-failover"></a>Datenträger nach dem Failover

Nach dem Failover verfügt die Azure-VM über die Datenträger, die in der folgenden Tabelle aufgeführt sind.

**Name des Datenträgers** | **Datenträger mit Gastbetriebssystem** | **Laufwerkbuchstabe** | **Datentyp des Datenträgers**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Betriebssystem-Datenträger
DB-Disk1 | Disk1 | D:\ | Temporärer Speicher<br/><br/> Da „D:“ der erste verfügbare Laufwerkbuchstabe ist, weist Azure dem Volume für die temporäre Speicherung „D:“ zu.<br/><br/> Für alle replizierten Datenträger bleibt der Laufwerkbuchstabe gleich.<br/><br/> Da der Datenträger „G:“ nicht verfügbar ist, verwendet das System Laufwerk „C:“ für die Auslagerungsdatei.
DB-Disk2 | Disk2 | E:\ | Benutzerdaten 1
DB-Disk3 | Disk3 | F:\ | Benutzerdaten 2

Unsere Einstellungen für die Auslagerungsdatei auf der Azure-VM lauten wie folgt:

![Der Screenshot des Dialogfelds „Virtueller Speicher“ mit der hervorgehobenen Zeile „Laufwerk C:“ zeigt eine Einstellung der Größe der Auslagerungsdatei vom Typ „Systemverwaltet“.](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Richtlinien für den Datenträger zur temporären Speicherung:
    - [Informieren Sie sich](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) über die Verwendung von SSDs auf Azure-VMs zum Speichern der SQL Server-Datenbank „TempDB“ und von Pufferpoolerweiterungen.
    - [Informieren Sie sich](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) über die bewährten Methoden in Bezug auf die Leistung für SQL Server auf Azure-VMs.
- Nachdem die Bereitstellung eingerichtet wurde und ausgeführt wird, können Sie sich über die unterschiedlichen Failoverarten [informieren](failover-failback-overview.md) .
