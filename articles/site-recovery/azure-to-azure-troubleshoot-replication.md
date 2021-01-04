---
title: Behandeln von Problemen bei der Replikation virtueller Azure-Computer mit Azure Site Recovery
description: Behandeln von Problemen bei der Replikation während der Notfallwiederherstellung von Azure-VMs mit Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007357"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Behandeln von Problemen bei der Replikation während der Notfallwiederherstellung von Azure-VMs

In diesem Artikel werden allgemeine Probleme in Azure Site Recovery bei der Replikation und Wiederherstellung von virtuellen Azure-Computern (VMs) zwischen verschiedenen Regionen beschrieben. Darüber hinaus wird die Behandlung häufig auftretender Probleme beschrieben. Weitere Informationen zu unterstützten Konfigurationen finden Sie unter [Azure Site Recovery support matrix for replicating from Azure to Azure](./azure-to-azure-support-matrix.md) (Azure Site Recovery-Supportmatrix zum Replizieren aus Azure in Azure).

Azure Site Recovery repliziert Daten konsistent von der Quellregion zur Notfallwiederherstellungsregion. Außerdem wird alle 5 Minuten ein absturzkonsistenter Wiederherstellungspunkt erstellt. Wenn Site Recovery 60 Minuten lang keine Wiederherstellungspunkte erstellen kann, werden Sie mit diesen Informationen gewarnt:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

In den folgenden Abschnitten werden Ursachen und Lösungen beschrieben.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Hohe Datenänderungsrate auf dem virtuellen Quellcomputer

Azure Site Recovery erstellt ein Ereignis, wenn die Datenänderungsrate auf dem virtuellen Quellcomputer die unterstützten Grenzwerte übersteigt. Um festzustellen, ob das Problem auf eine hohe Änderungsrate zurückzuführen ist, navigieren Sie zu **Replizierte Elemente** > **VM** > **Ereignisse – letzte 72 Stunden**.
Das Ereignis **Die Datenänderungsrate überschreitet die unterstützten Grenzwerte** sollte angezeigt werden:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Azure Site Recovery-Seite mit einer zu hohen Datenänderungsrate":::

Wenn Sie das Ereignis auswählen, sehen Sie die genauen Datenträgerinformationen:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Seite mit Details zum Datenänderungsraten-Ereignis":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-Grenzwerte

Die folgende Tabelle enthält die Azure Site Recovery-Grenzwerte. Diese Grenzwerte basieren auf unseren Tests, können aber nicht alle möglichen Eingabe-Ausgabe-Kombinationen (E/A) für Anwendungen abdecken. Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die Anwendungen variieren.

Es gibt zwei Einschränkungen zu berücksichtigen: die Datenänderungen pro Datenträger und die Datenänderung pro virtuellem Computer. Als Beispiel betrachten wir den Premium-P20-Datenträger in der folgenden Tabelle. Für einen einzelnen virtuellen Computer kann Site Recovery eine Änderungsrate von 5 MB/s pro Datenträger bei maximal fünf Datenträgern bewältigen. Site Recovery unterstützt eine maximale Gesamtänderungsrate pro virtuellem Computer von 54 MB/s.

**Replikationsspeicherziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate pro Tag der Quelldatenträger**
---|---|---|---
Standardspeicher | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |    336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1\.684 GB pro Datenträger

### <a name="solution"></a>Lösung

Für Azure Site Recovery gelten Grenzwerte für die Datenänderungsrate abhängig vom Typ des Datenträgers. Um herauszufinden, ob sich dieses Problem wiederholt oder ob es vorübergehend auftritt, ermitteln Sie die Datenänderungsrate des betroffenen virtuellen Computers. Navigieren Sie zu dem virtuellen Quellcomputer, suchen Sie die Metriken unter **Überwachung**, und fügen Sie die Metriken wie in diesem Screenshot dargestellt hinzu:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Seite mit den drei Schritten zum Ermitteln der Datenänderungsrate":::

1. Wählen Sie **Metrik hinzufügen** aus, und fügen Sie **Auf den Betriebssystemdatenträger geschriebene Bytes/s** und **Auf den Datenträger geschriebene Bytes/s** hinzu.
1. Überwachen Sie die im Screenshot dargestellte Spitze.
1. Zeigen Sie die Gesamtanzahl an Schreibvorgängen an, die auf Betriebssystemdatenträgern und allen Datenträgern in Kombination auftreten. Diese Metriken bietet Ihnen möglicherweise nicht die Informationen auf Datenträgerebene, aber sie geben das gesamte Muster der Datenänderungsrate an.

Eine Spitze in der Datenänderungsrate könnte von einem gelegentlichen Datenburst herrühren. Wenn die Datenänderungsrate größer als 10 MB/s (bei Premium) bzw. 2 MB/s (bei Standard) ist und sinkt, holt die Replikation auf. Wenn die Änderung konstant weit über dem unterstützten Grenzwert liegt, sollten Sie eine der folgenden Optionen in Betracht ziehen:

- Schließen Sie den Datenträger aus, der eine hohe Datenänderungsrate verursacht: Deaktivieren Sie zunächst die Replikation. Anschließend können Sie den Datenträger mithilfe von [PowerShell](azure-to-azure-exclude-disks.md) ausschließen.
- Ändern Sie den Tarif des Speicherdatenträgers für die Notfallwiederherstellung: Diese Option ist nur möglich, wenn die Datenänderungsrate weniger als 20 MB/s beträgt. Beispiel: Eine VM mit einem P10-Datenträger weist eine Datenänderungsrate zwischen 8 MB/s und 10 MB/s auf. Wenn ein Kunde den P30-Datenträger während des Schutzes als Zielspeicher verwenden kann, kann das Problem gelöst werden. Diese Lösung ist nur für Computer mit Managed Disks Premium möglich. Folgen Sie diesen Schritten:

  1. Navigieren Sie zum Blatt **Datenträger** des betroffenen replizierten Computers, und kopieren Sie den Namen des Replikatdatenträgers.
  1. Navigieren Sie zum Replikat dieses verwalteten Datenträgers.
  1. Möglicherweise wird in der **Übersicht** ein Banner angezeigt, das besagt, dass eine SAS-URL generiert wurde. Wählen Sie dieses Banner aus, und brechen Sie den Export ab. Ignorieren Sie diesen Schritt, wenn das Banner nicht angezeigt wird.
  1. Nachdem die SAS-URL widerrufen wurde, navigieren Sie zu **Konfiguration** für den verwalteten Datenträger. Erhöhen Sie die Größe, sodass Site Recovery die beobachtete Änderungsrate auf dem Quelldatenträger unterstützt.

## <a name="network-connectivity-problems"></a>Probleme mit der Netzwerkkonnektivität

### <a name="network-latency-to-a-cache-storage-account"></a>Netzwerklatenz bei Verbindung mit Cachespeicherkonto

Site Recovery sendet replizierte Daten an das Cachespeicherkonto. Möglicherweise stellen Sie Netzwerklatenz fest, wenn das Hochladen der Daten aus einem virtuellen Computer in das Cachespeicherkonto langsamer ist als 4MB in 3 Sekunden.

Verwenden Sie [AzCopy](../storage/common/storage-use-azcopy-v10.md), um nach einem Problem im Zusammenhang mit der Latenz zu suchen. Mit diesem Befehlszeilen-Hilfsprogramm können Sie Daten vom virtuellen Computer in das Cachespeicherkonto hochladen. Wenn die Latenz hoch ist, überprüfen Sie, ob Sie ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) zum Steuern des ausgehenden Netzwerkdatenverkehrs von virtuellen Computern verwenden. Das virtuelle Netzwerkgerät wird möglicherweise gedrosselt, wenn der gesamte Replikationsdatenverkehr durch das virtuelle Netzwerkgerät läuft.

Es wird empfohlen, einen Netzwerk-Dienstendpunkt in Ihrem virtuellen Netzwerk für „Storage“ zu erstellen, damit der Replikationsdatenverkehr nicht an das virtuelle Netzwerkgerät geleitet wird. Weitere Informationen finden Sie unter [Konfiguration der virtuellen Netzwerkappliance](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Netzwerkkonnektivität

Damit die Site Recovery-Replikation funktioniert, muss die VM ausgehende Konnektivität zu bestimmten URLs oder IP-Bereichen bereitstellen. Möglicherweise befindet sich Ihr virtueller Computer hinter einer Firewall, oder es werden Netzwerksicherheitsgruppen-Regeln (NSG-Regeln) zum Steuern der ausgehenden Konnektivität verwendet. In diesem Fall treten möglicherweise Probleme auf. Um sicherzustellen, dass alle URLs verbunden sind, informieren Sie sich unter [Ausgehende Konnektivität für URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fehler-ID 153006: In den letzten „X“ Minuten stand kein anwendungskonsistenter Wiederherstellungspunkt für den virtuellen Computer zur Verfügung.

Einige der häufigsten Probleme sind nachfolgend aufgeführt.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Bekanntes Problem in SQL Server 2008/2008 R2

**Wie behebe ich das Problem?** Es gibt ein bekanntes Problem mit SQL Server 2008/2008 R2. Weitere Informationen finden Sie im Artikel [Fehler bei Azure Site Recovery-Agent oder einer anderen komponentenfreien VSS-Sicherung für einen Server, der SQL Server 2008 R2 hostet](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-Aufträge schlagen auf Servern fehl, die eine beliebige Version von SQL Server-Instanzen mit AUTO_CLOSE DBs hosten

**Wie behebe ich das Problem?** Lesen Sie den Artikel [Komponentenfreie VSS-Sicherungen wie Azure Site Recovery-Aufträge schlagen auf Servern fehl, die SQL Server-Instanzen mit AUTO_CLOSE-Datenbanken hosten](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Bekanntes Problem in SQL Server 2016 und 2017

**Problembehandlung**: Lesen Sie den Artikel [Kumulatives Update 16 für SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Sie verwenden die Konfiguration direkter Speicherplätze (Azure Storage Spaces Direct).

**Problembehandlung**: Azure Site Recovery kann keinen anwendungskonsistenten Wiederherstellungspunkt für die Konfiguration direkter Speicherplätze erstellen. [Konfigurieren Sie die Replikationsrichtlinie](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>App-Konsistenz ist auf Linux-Servern nicht aktiviert

**Problembehandlung**: Azure Site Recovery für Linux-Betriebssysteme unterstützt benutzerdefinierte Anwendungsskripts für App-Konsistenz. Das benutzerdefinierte Skript mit „Pre“- und „Post“-Optionen wird vom Mobilitäts-Agent von Azure Site Recovery für die App-Konsistenz verwendet. Die Schritte für die Aktivierung finden Sie [hier](./site-recovery-faq.md#replication).

### <a name="more-causes-because-of-vss-related-issues"></a>Weitere Ursachen aufgrund von VSS-bezogenen Problemen:

Überprüfen Sie zur weiteren Problembehandlung die Dateien auf dem Quellcomputer, um den genauen Fehlercode zu erhalten:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Um die Fehler zu finden, öffnen Sie die Datei _vacp.log_ in einem Text-Editor, und suchen Sie nach der Zeichenfolge **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

Im voranstehenden Beispiel ist **2147754994** der Fehlercode, der Sie über den Fehler informiert, wie nachfolgend dargestellt.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS Writer ist nicht installiert – Fehler 2147221164

**Problembehandlung**: Azure Site Recovery verwendet den Volumeschattenkopie-Dienst (VSS), um das Tag für die Anwendungskonsistenz zu erstellen. Site Recovery installiert einen VSS-Anbieter für den Betrieb, um Momentaufnahmen zur Anwendungskonsistenz zu erstellen. Azure Site Recovery installiert diesen VSS-Anbieter als Dienst. Wenn der VSS-Anbieter nicht installiert ist, schlägt die Erstellung der Anwendungskonsistenz-Momentaufnahme fehl. Die **Fehler-ID 0x80040154 „Klasse nicht registriert“** wird angezeigt. Weitere Informationen finden Sie im Artikel zur [Problembehandlung bei der VSS Writer-Installation](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS Writer ist deaktiviert – Fehler 2147943458

**Problembehandlung**: Azure Site Recovery verwendet den VSS, um das Tag für die Anwendungskonsistenz zu erstellen. Site Recovery installiert einen VSS-Anbieter für den Betrieb, um Momentaufnahmen zur Anwendungskonsistenz zu erstellen. Dieser VSS-Anbieter wird als Dienst installiert. Wenn Sie den VSS-Provider-Dienst nicht aktiviert haben, schlägt die Erstellung der Anwendungskonsistenz-Momentaufnahme fehl. Es wird der folgende Fehler angezeigt: **Der angegebene Dienst ist deaktiviert und kann nicht gestartet werden (0x80070422)** .

Wenn VSS deaktiviert ist:

- Stellen Sie sicher, dass der Starttyp des VSS-Anbieterdiensts auf **Automatic** (Automatisch) festgelegt ist.
- Starten Sie die folgenden Dienste neu:
  - VSS-Dienst
  - Azure Site Recovery-VSS-Anbieter
  - VDS-Dienst

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED – Fehler 2147754756

**Problembehandlung**: Azure Site Recovery verwendet den VSS, um das Tag für die Anwendungskonsistenz zu erstellen. Überprüfen Sie, ob der Azure Site Recovery VSS-Anbieterdienst installiert ist.

Verwenden Sie die folgenden Befehle, um den VSS-Anbieter erneut zu installieren:

1. Vorhandenen Anbieter deinstallieren:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Installieren Sie den VSS Provider erneut:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Stellen Sie sicher, dass der Starttyp des VSS-Anbieterdiensts auf **Automatic** (Automatisch) festgelegt ist.

Starten Sie die folgenden Dienste neu:

- VSS-Dienst
- Azure Site Recovery-VSS-Anbieter
- VDS-Dienst
