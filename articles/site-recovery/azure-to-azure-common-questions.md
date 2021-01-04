---
title: Häufige Fragen zu Notfallwiederherstellung virtueller Azure-Computer mit Azure Site Recovery
description: In diesem Artikel werden häufig gestellte Fragen zur Notfallwiederherstellung von virtuellen Azure-Computern bei Verwendung von Azure Site Recovery beantwortet.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397949"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Häufig gestellte Fragen sind: Azure-zu-Azure-Notfallwiederherstellung

In diesem Artikel häufig gestellte Fragen zur Notfallwiederherstellung von Azure-VMs in einer anderen Azure-Region mithilfe des [Azure Site Recovery](site-recovery-overview.md)-Diensts beantwortet.

## <a name="general"></a>Allgemein

### <a name="how-is-site-recovery-priced"></a>Wie werden Site Recovery-Preise kalkuliert?

Erfahren Sie mehr über die [Kosten](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) für die Notfallwiederherstellung von Azure-VMs.

### <a name="how-does-the-free-tier-work"></a>Wie funktioniert der Free-Tarif?

Jede Instanz, die mit Site Recovery geschützt wird, ist für die ersten 31 Tage kostenlos. Nach diesem Zeitraum gelten für den Schutz der einzelnen Instanzen die in der [Preisübersicht](https://azure.microsoft.com/pricing/details/site-recovery/) zusammengefassten Preise. Sie können die Kosten mit dem [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=site-recovery) abschätzen.

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Fallen während der ersten 31 Tage andere Azure-Gebühren an?

Ja. Auch wenn Azure Site Recovery während der ersten 31 Tage einer geschützten Instanz kostenlos ist, können für Azure Storage, Speichertransaktionen und Datenübertragungen Gebühren anfallen. Auch für eine wiederhergestellte VM können Azure-Computegebühren anfallen. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Wie beginne ich mit der Nutzung der Notfallwiederherstellung von Azure-VMs?

1. [Informieren Sie sich](azure-to-azure-architecture.md) über die Architektur für die Notfallwiederherstellung von Azure-VMs.
2. [Informieren](azure-to-azure-support-matrix.md) Sie sich über die Supportanforderungen.
3. [Richten Sie die Notfallwiederherstellung für Azure-VMs ein.](azure-to-azure-how-to-enable-replication.md)
4. [Führen Sie mit einem Testfailover eine Notfallwiederherstellungsübung durch.](azure-to-azure-tutorial-dr-drill.md)
5. [Führen Sie ein vollständiges Failover zu einer sekundären Azure-Region aus.](azure-to-azure-tutorial-failover-failback.md)
6. Führen Sie ein [Failback](azure-to-azure-tutorial-failback.md) von der sekundären Region in die primäre Region durch.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Wie wird die Kapazität in der Zielregion sichergestellt?

Das Site Recovery-Team und das Azure-Kapazitätsverwaltungsteam planen eine ausreichende Infrastrukturkapazität. Wenn Sie ein Failover starten, können die Teams auch dabei helfen, sicherzustellen, dass die von Site Recovery geschützten VM-Instanzen in der Zielregion bereitgestellt werden.

## <a name="replication"></a>Replikation

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Kann ich VMs mit Datenträgerverschlüsselung replizieren?

Ja. Site Recovery unterstützt die Notfallwiederherstellung von VMs, für die Azure Disk Encryption (ADE) aktiviert ist. Wenn Sie die Replikation aktivieren, werden von Azure sämtliche erforderlichen Schlüssel und Geheimnisse für die Datenträgerverschlüsselung im Benutzerkontext aus der Quellregion in die Zielregion kopiert. Wenn Sie nicht über die erforderlichen Berechtigungen verfügen, kann Ihr Sicherheitsadministrator die Schlüssel und Geheimnisse mithilfe eines Skripts kopieren.

- Site Recovery unterstützt ADE für virtuelle Azure-Computer unter Windows.
- Site Recovery unterstützt:
    - ADE-Version 0.1, die über ein Schema verfügt, das Azure Active Directory (Azure AD) erfordert
    - ADE-Version 1.1, die Azure AD nicht erfordert. Bei Version 1.1 müssen Azure-VMs mit Windows über verwaltete Datenträger verfügen.
    - [Weitere Informationen](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema) zu den Erweiterungsschemas.

Weitere Informationen zum Aktivieren der Replikation für verschlüsselte virtuelle Computer finden Sie [hier](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Kann ich ein Automation-Konto aus einer anderen Ressourcengruppe auswählen?

Wenn Sie Site Recovery das Verwalten von Updates für die auf replizierten Azure-VMs ausgeführte Mobilitätsdienst-Erweiterung gestatten, stellt der Dienst über ein Azure Automation-Konto ein globales Runbook bereit, das von Azure-Diensten verwendet wird. Sie können das von Site Recovery erstellte Automation-Konto verwenden oder ein vorhandenes Automation-Konto auswählen. 

Derzeit können Sie im Portal nur ein Automation-Konto in derselben Ressourcengruppe wie der Tresor auswählen. Mit PowerShell können Sie ein Automation-Konto aus einer anderen Ressourcengruppe auswählen. [Weitere Informationen](azure-to-azure-autoupdate.md#enable-automatic-updates)

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Kann ich das Standardrunbook löschen, wenn ich ein Automation-Konto eines Kunden verwende, das nicht zur Ressourcengruppe des Tresors gehört?

Ja, Sie können es löschen, wenn Sie es nicht benötigen. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kann ich VMs in ein anderes Abonnement replizieren?

Ja, Sie können Azure-VMs in ein beliebiges Abonnement im selben Azure AD-Mandanten replizieren. Wenn Sie die Notfallwiederherstellung für VMs aktivieren, wird als Zielabonnement standardmäßig das der Quell-VM angezeigt. Sie können das Zielabonnement ändern. Die anderen Einstellungen (z. B. die Ressourcengruppe und das virtuelle Netzwerk) werden automatisch aus dem ausgewählten Abonnement aufgefüllt.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Kann ich VMs in einer Verfügbarkeitszone in eine andere Region replizieren?

Ja, Sie können VMs in Verfügbarkeitszonen in eine andere Azure-Region replizieren. Die Ziel-VM kann als Einzelinstanz, in einer Verfügbarkeitsgruppe oder in einer Verfügbarkeitszone bereitgestellt werden, sofern dies in der Zielregion unterstützt wird. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Kann ich VMs ohne Zone in eine Zone innerhalb derselben Region replizieren? 

Dieser Vorgang wird im Portal nicht unterstützt. Sie können dazu die REST-API oder PowerShell verwenden.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Kann ich VMs aus einer Zone in eine andere Zone in derselben Region replizieren?

Dies wird lediglich in einigen wenigen Regionen unterstützt. [Weitere Informationen](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)

### <a name="can-i-exclude-disks-from-replication"></a>Kann ich Datenträger von der Replikation ausschließen?

Ja, Sie können Datenträger ausschließen, wenn Sie die Replikation mithilfe von PowerShell einrichten. [Weitere Informationen](azure-to-azure-exclude-disks.md)

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Können neue Datenträger repliziert werden, die replizierten VMs hinzugefügt wurden?

Bei replizierten VMs mit verwalteten Datenträgern können Sie neue Datenträger hinzufügen und für diese die Replikation aktivieren. Wenn Sie einen neuen Datenträger hinzufügen, wird auf der replizierten VM eine Warnmeldung angezeigt, dass für mindestens einen Datenträger auf der VM Schutzmaßnahmen verfügbar sind. 

- Wenn Sie die Replikation für die hinzugefügten Datenträger aktivieren, wird die Warnung nach der ersten Replikation nicht mehr angezeigt.
- Wenn Sie die Replikation für den Datenträger nicht aktivieren möchten, können Sie die Warnung verwerfen.
- Wenn Sie ein Failover für eine VM mit hinzugefügten Datenträgern ausführen, zeigen die Replikationspunkte die für die Wiederherstellung verfügbaren Datenträger an. Wenn Sie z. B. einer VM mit einem Datenträger einen zweiten Datenträger hinzufügen, wird an einem Replikationspunkt, der vor dem Hinzufügen erstellt wurde, „1 von 2 Datenträgern“ angezeigt.

Site Recovery unterstützt kein „Entfernen eines Datenträgers im laufendem Betrieb“ aus einer replizierten VM. Wenn Sie einen VM-Datenträger entfernen, müssen Sie die Replikation für die VM deaktivieren und dann erneut aktivieren.

### <a name="how-often-can-i-replicate-to-azure"></a>Wie oft kann ich zu Azure replizieren?

Die Replikation ist fortlaufend, wenn Azure-VMs in eine andere Azure-Region repliziert werden. [Weitere Informationen zur Funktionsweise der Replikation](./azure-to-azure-architecture.md#replication-process)

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Kann ich virtuelle Computer innerhalb einer Region replizieren? 

Sie können Site Recovery nicht für das Replizieren von Datenträgern innerhalb einer Region verwenden.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kann ich VM-Instanzen in jede Azure-Region replizieren?

Sie können virtuelle Computer zwischen zwei beliebigen Regionen im gleichen geografischen Cluster replizieren. Geografische Cluster werden unter Berücksichtigung von Datenlatenz und Souveränität definiert. [Weitere Informationen zur regionalen Unterstützung.](./azure-to-azure-support-matrix.md#region-support)

### <a name="does-site-recovery-need-internet-connectivity"></a>Ist für Site Recovery eine Internetverbindung erforderlich?

Nein, VMs benötigen jedoch Zugriff auf URLs und IP-Adressbereiche von Site Recovery. [Weitere Informationen](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Kann ich eine Anwendung replizieren, die auf mehrere Ressourcengruppen verteilt ist?

Ja, Sie können die App replizieren und die Konfiguration für die Notfallwiederherstellung in einer separaten Ressourcengruppe speichern.

Wenn die App z. B. drei Schichten (Anwendung/Datenbank/Web) in verschiedenen Ressourcengruppen umfasst, müssen Sie die Replikation dreimal aktivieren, um alle Schichten zu schützen. Site Recovery repliziert diese drei Schichten in drei verschiedene Ressourcengruppen.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Kann ich Speicherkonten über Ressourcengruppen hinweg verschieben?

Nein, dies wird nicht unterstützt. Wenn Sie versehentlich Speicherkonten in eine andere Ressourcengruppe verschieben und die ursprüngliche Ressourcengruppe löschen, können Sie eine neue Ressourcengruppe mit dem gleichen Namen wie die alte Ressourcengruppe erstellen und dann das Speicherkonto in diese Ressourcengruppe verschieben.

## <a name="replication-policy"></a>Replikationsrichtlinie

### <a name="what-is-a-replication-policy"></a>Was ist eine Replikationsrichtlinie?

Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf von Wiederherstellungspunkten und die Häufigkeit von anwendungskonsistenten Momentaufnahmen.  Site Recovery erstellt eine Standardreplikationsrichtlinie mit folgenden Einstellungen:

- Wiederherstellungspunkte werden für 24 Stunden beibehalten.
- Alle vier Stunden werden App-konsistente Momentaufnahmen erstellt.

[Weitere Informationen zu Replikationseinstellungen](azure-to-azure-how-to-enable-replication.md#customize-target-resources)

### <a name="whats-a-crash-consistent-recovery-point"></a>Was ist ein absturzkonsistenter Wiederherstellungspunkt?

Ein absturzkonsistenter Wiederherstellungspunkt enthält die Daten auf dem Datenträger, als ob Sie das Netzkabel während der Momentaufnahme vom Server abgezogen hätten. Er weist keinen Inhalt des Arbeitsspeichers zum Zeitpunkt der Momentaufnahme auf.

Heutzutage können die meisten Apps gut aus absturzkonsistenten Momentaufnahmen wiederhergestellt werden. Ein absturzkonsistenter Wiederherstellungspunkt ist normalerweise ausreichend für Betriebssysteme ohne Datenbank und Apps wie Dateiserver, DHCP-Server und Druckserver.

Site Recovery erstellt automatisch alle fünf Minuten einen absturzkonsistenten Wiederherstellungspunkt.

### <a name="whats-an-application-consistent-recovery-point"></a>Was ist ein anwendungskonsistenter Wiederherstellungspunkt?

App-konsistente Wiederherstellungspunkte werden aus App-konsistenten Momentaufnahmen erstellt. Sie erfassen dieselben Daten wie absturzkonsistente Momentaufnahmen. Zudem werden alle Daten im Arbeitsspeicher und alle laufenden Transaktionen erfasst.

Durch diesen zusätzlichen Inhalt sind App-konsistente Momentaufnahmen am stärksten involviert, und ihre Ausführung dauert am längsten. App-konsistente Wiederherstellungspunkte werden für Datenbank-Betriebssysteme und Apps wie SQL Server empfohlen. Unter Windows wird für App-konsistente Momentaufnahmen der Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS) verwendet.

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Wirken sich App-konsistente Wiederherstellungspunkte auf die Leistung aus?

 Da bei App-konsistenten Wiederherstellungspunkten alle Daten im Arbeitsspeicher und in der Verarbeitung erfasst werden, kann die Leistung beeinträchtigt werden, wenn die Workload bereits hoch ist und häufige Erfassungen erfolgen. Es wird nicht empfohlen, bei Workloads ohne Datenbanken zu häufig eine Erfassung durchzuführen. Selbst bei Datenbankworkloads sollte einmal pro Stunde ausreichen.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Wie häufig sollten App-konsistente Wiederherstellungspunkte mindestens erstellt werden?

Site Recovery kann App-konsistente Wiederherstellungspunkte mit einer Mindesthäufigkeit von einmal pro Stunde erstellen.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Kann ich die App-konsistente Replikation für Linux-VMs aktivieren?

Ja. Der Mobilitäts-Agent für Linux unterstützt benutzerdefinierte Skripts für die App-Konsistenz. Der Agent verwendet ein benutzerdefiniertes Skript mit Optionen für die Vor- und Nachbearbeitung. [Weitere Informationen](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Wie werden Wiederherstellungspunkte generiert und gespeichert?

Sehen Sie sich das folgende Beispiel an, um zu verstehen, wie Site Recovery Wiederherstellungspunkte generiert. 

- Eine Replikationsrichtlinie bewahrt Wiederherstellungspunkte 24 Stunden lang auf und erstellt jede Stunde eine App-konsistente Momentaufnahme.
- Site Recovery erstellt alle fünf Minuten einen absturzkonsistenten Wiederherstellungspunkt. Der Benutzer kann diese Häufigkeit nicht ändern.
- Site Recovery löscht Wiederherstellungspunkte nach einer Stunde und bewahrt damit einen Punkt pro Stunde auf.

Für die letzte Stunde können Sie zwischen 12 absturzkonsistenten und einem App-konsistenten Punkt auswählen, wie in der folgenden Grafik gezeigt.

   ![Liste der generierten Wiederherstellungspunkte](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Wie weit kann ich bei der Wiederherstellung zurückgehen?

Der älteste Wiederherstellungspunkt, den Sie verwenden können, ist 72 Stunden alt.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Was passiert, wenn Site Recovery für mehr als 24 Stunden keine Wiederherstellungspunkte generieren kann? 

Wenn Ihre Replikationsrichtlinie 24 Stunden umfasst und Site Recovery länger als 24 Stunden keine Wiederherstellungspunkte generieren kann, bleiben die alten Wiederherstellungspunkte erhalten. Site Recovery ersetzt den ältesten Punkt nur, wenn neue Punkte generiert werden. Bis neue Wiederherstellungspunkte vorhanden sind, bleiben alle alten Punkte erhalten, auch wenn das Aufbewahrungszeitfenster abgelaufen ist.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Kann ich die Replikationsrichtlinie noch ändern, nachdem die Replikation aktiviert wurde?

Ja. Wählen Sie im Tresor **Site Recovery-Infrastruktur** > **Replikationsrichtlinien** und dann die Richtlinie aus, um sie zu bearbeiten. Änderungen gelten auch für vorhandene Richtlinien.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Bilden alle Wiederherstellungspunkte zusammen eine vollständige VM-Kopie?

Der erste Wiederherstellungspunkt, der generiert wird, ist eine vollständige Kopie. Alle nachfolgenden Wiederherstellungspunkte enthalten nur die geänderten Daten (Deltaänderungen).

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Werden die Speicherkosten bei einer längeren Aufbewahrungsdauer für Wiederherstellungspunkte erhöht?

Ja. Wenn Sie die Aufbewahrungsdauer z. B. von 24 auf 72 Stunden erhöhen, speichert Site Recovery die Wiederherstellungspunkte für zusätzliche 48 Stunden. Diese zusätzliche Zeit verursacht Speichergebühren. Wenn beispielsweise ein einzelner Wiederherstellungspunkt Deltaänderungen von 10 GB enthält und jedes GB pro Monat 0,16 US-Dollar kostet, belaufen sich die zusätzlichen Gebühren auf 1,60 US-Dollar × 48 pro Monat.

## <a name="multi-vm-consistency"></a>Multi-VM-Konsistenz

### <a name="what-is-multi-vm-consistency"></a>Was versteht man unter Multi-VM-Konsistenz?

Multi-VM-Konsistenz stellt sicher, dass Wiederherstellungspunkte über alle replizierten VMs konsistent sind.

- Wenn Sie die Multi-VM-Konsistenz aktivieren, erstellt Site Recovery eine Replikationsgruppe aller Computer, für die diese Option aktiviert ist. 
- Alle Computer in einer Replikationsgruppe verfügen beim Failover über absturz- und App-konsistente Wiederherstellungspunkte.

[Weitere Informationen zum Aktivieren der Multi-VM-Konsistenz](azure-to-azure-tutorial-enable-replication.md#enable-replication)

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Kann ich ein Failover für eine einzelne VM in einer Replikationsgruppe ausführen?

Nein. Wenn Sie Multi-VM-Konsistenz aktivieren, weist eine App eine Abhängigkeit von allen VMs in der Replikationsgruppe auf, sodass kein Failover mit einer einzelnen VM zulässig ist.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Wie viele VMs kann ich zusammen in einer Gruppe replizieren?

In einer Replikationsgruppe können 16 VMs zusammen repliziert werden.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Wann sollte ich Multi-VM-Konsistenz aktivieren?

Multi-VM-Konsistenz ist CPU-intensiv und kann die Workloadleistung beeinträchtigen. Sie sollte nur aktiviert werden, wenn VMs dieselbe Workload ausführen und Konsistenz auf mehreren Computern erforderlich ist. Wenn Sie beispielsweise in einer Anwendung zwei SQL Server-Instanzen und zwei Webserver haben, sollten Sie die Multi-VM-Konsistenz nur für die SQL Server-Instanzen aktivieren.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Kann ich einer Replikationsgruppe eine replizierte VM hinzufügen?

Wenn Sie die Replikation für eine VM aktivieren, können Sie diese einer vorhandenen Gruppe hinzufügen. Sie können jedoch keine VM hinzufügen, die bereits in einer Gruppe repliziert wird. 
 
## <a name="failover"></a>Failover

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Wie wird die Kapazität in der Zielregion sichergestellt?

Das Site Recovery-Team und das Azure-Kapazitätsverwaltungsteam planen eine ausreichende Infrastrukturkapazität. Wenn Sie ein Failover starten, können die Teams auch dabei helfen, sicherzustellen, dass die von Site Recovery geschützten VM-Instanzen in der Zielregion bereitgestellt werden.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?

Ein Failover erfolgt nicht automatisch. Sie können ein Failover mit einem Mausklick im Portal starten oder [PowerShell](azure-to-azure-powershell.md) verwenden, um ein Failover auszulösen.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Kann ich nach einem Failover eine öffentliche IP-Adresse beibehalten?

Die öffentliche IP-Adresse einer Produktions-App kann nach einem Failover nicht mehr beibehalten werden.

Wenn Sie eine Workload im Rahmen des Failovers hochfahren, müssen Sie ihr eine öffentliche Azure-IP-Adressressource zuweisen. Die Ressource muss in der Zielregion verfügbar sein. Sie können die öffentliche Azure-IP-Adressressource manuell zuweisen oder dies mit einem Wiederherstellungsplan automatisieren. Erfahren Sie mehr über das [Einrichten von öffentlichen IP-Adressen nach einem Failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Kann ich eine private IP-Adresse nach einem Failover beibehalten?

Ja. Wenn Sie die Notfallwiederherstellung für Azure-VMs aktivieren, werden die Zielressourcen in Site Recovery basierend auf den Einstellungen für die Quellressourcen erstellt. Für virtuelle Azure-Computer, die mit statischen IP-Adressen konfiguriert sind, wird in Site Recovery nach Möglichkeit die gleiche IP-Adresse für den virtuellen Zielcomputer bereitgestellt, sofern sie nicht bereits verwendet wird.
Erfahren Sie mehr über das [Beibehalten von IP-Adressen nach einem Failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Warum wird einer VM nach einem Failover eine neue IP-Adresse zugewiesen?

Site Recovery versucht, die IP-Adresse zum Zeitpunkt des Failovers bereitzustellen. Wenn jedoch eine andere VM diese Adresse verwendet, legt Site Recovery die nächste verfügbare IP-Adresse als Ziel fest.

Erfahren Sie mehr über das [Einrichten von Netzwerkzuordnung und die IP-Adressierung für virtuelle Netzwerke](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="whats-the-latest-recovery-point"></a>Was ist der *letzte* Wiederherstellungspunkt?

Die Option *Letzte (niedrigste RPO)* für den Wiederherstellungspunkt hat folgende Auswirkungen:

1. Zuerst werden alle an Site Recovery gesendeten Daten verarbeitet.
2. Nachdem der Dienst die Daten verarbeitet hat, wird ein Wiederherstellungspunkt für jede VM erstellt, bevor ein Failover zur VM durchgeführt wird. Diese Option bietet die niedrigste RPO (Recovery Point Objective).
3. Die nach dem Failover erstellte VM enthält alle in Site Recovery replizierten Daten zum Zeitpunkt der Auslösung des Failovers.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Wirken sich die *letzten* Wiederherstellungspunkte auf die Failover-RTO aus?

Ja. Site Recovery verarbeitet vor dem Failover alle ausstehenden Daten. Daher hat diese Option eine höhere Recovery Time Objective (RTO) im Vergleich zu anderen Optionen.

### <a name="whats-the-latest-processed-recovery-option"></a>Was ist die Wiederherstellungsoption *Letzte Verarbeitung*?

Die Option *Letzte Verarbeitung* hat folgende Auswirkungen:

1. Es wird ein Failover der VMs auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Was passiert, wenn in der primären Region ein unerwarteter Ausfall auftritt?

Sie können ein Failover starten. Site Recovery benötigt keine Verbindung mit der primären Region, um das Failover auszuführen.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Was ist die RTO eines VM-Failovers?

Für Site Recovery gilt eine RTO-SLA von [2 Stunden](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). In den meisten Fällen erfolgt ein Failover der VMs durch Site Recovery innerhalb weniger Minuten. Sie können den RTO-Wert berechnen, indem Sie den Failoverauftrag überprüfen. Dieser weist die Zeit auf, die es gedauert hat, die VM wieder verfügbar zu machen. 

## <a name="recovery-plans"></a>Wiederherstellungspläne

### <a name="whats-a-recovery-plan"></a>Was ist ein Wiederherstellungsplan?

Ein [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) in Site Recovery orchestriert das Failover und die Wiederherstellung von VMs. Er trägt zu einer durchgängig exakten, wiederholbaren und automatisierten Wiederherstellung bei. Sie führt die folgenden Aktionen aus:

- Definieren einer Gruppe von VMs, für die zusammen ein Failover ausgeführt wird
- Definieren der Abhängigkeiten zwischen VMs, damit die Anwendung ordnungsgemäß verfügbar gemacht wird
- Automatisieren der Wiederherstellung mit der Möglichkeit, benutzerdefinierte manuelle Aktionen für andere Tasks als das VM-Failover festzulegen 


### <a name="how-does-sequencing-work"></a>Wie funktioniert die Sequenzierung?

Sie können in einem Wiederherstellungsplan mehrere VM-Gruppen für die Sequenzierung erstellen. Für Gruppen – und damit auch für die enthaltenen VMs – wird das Failover gleichzeitig ausgeführt. [Weitere Informationen](recovery-plan-overview.md#model-apps)

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Wie kann ich die RTO eines Wiederherstellungsplans finden?

Um die RTO eines Wiederherstellungsplans zu prüfen, führen Sie ein Testfailover für den Wiederherstellungsplan aus. Überprüfen Sie unter **Site Recovery-Aufträge** die Dauer des Testfailovers. Im Beispielscreenshot dauerte der Testfailoverauftrag **SAPTestRecoveryPlan** 8 Minuten und 59 Sekunden.

![Auflisten von Aufträgen mit der Dauer des Testfailovers für die RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Kann ich Wiederherstellungsplänen Automation-Runbooks hinzufügen?

Ja. [Weitere Informationen](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Erneuter Schutz und Failback

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Sind VMs in der sekundären Region nach einem Failover automatisch geschützt? 

Nein. Wenn Sie ein Failover für VMs von einer Region in eine andere ausführen, werden die VMs in der Zielregion für die Notfallwiederherstellung in einem ungeschützten Zustand gestartet. Um die VMs in der sekundären Region [erneut zu schützen](./azure-to-azure-how-to-reprotect.md), aktivieren Sie die Replikation zurück in die primäre Region.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Werden beim erneuten Schützen alle Daten aus der sekundären Region in die primäre repliziert? 

Dies hängt von der Situation ab. Wenn die VM der Quellregion vorhanden ist, werden nur Änderungen zwischen dem Quelldatenträger und dem Zieldatenträger synchronisiert. Site Recovery berechnet die Unterschiede durch Vergleichen der Datenträger und überträgt dann die Daten. Dieser Vorgang dauert normalerweise einige Stunden. [Weitere Informationen](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)

### <a name="how-long-does-it-take-fail-back"></a>Wie lange dauert ein Failback?

Nach dem erneuten Schützen dauert das Failback ungefähr die gleiche Zeit wie das Failover von der primären Region in eine sekundäre Region.

## <a name="capacity"></a><a name="capacity"></a>Kapazität

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Wie wird die Kapazität in der Zielregion sichergestellt?

Das Site Recovery-Team und das Azure-Kapazitätsverwaltungsteam planen eine ausreichende Infrastrukturkapazität. Wenn Sie ein Failover starten, können die Teams auch dabei helfen, sicherzustellen, dass die von Site Recovery geschützten VM-Instanzen in der Zielregion bereitgestellt werden.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Kann Site Recovery bei reservierten Instanzen verwendet werden?

Ja. Sie können [reservierte Azure-VMs](https://azure.microsoft.com/pricing/reserved-vm-instances/) in der Region für die Notfallwiederherstellung erwerben, die dann von den Site Recovery-Failovervorgängen verwendet werden. Es ist keine zusätzliche Konfiguration erforderlich.

## <a name="security"></a>Sicherheit

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Werden Replikationsdaten an den Site Recovery-Dienst gesendet?

Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern ausgeführt wird. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.

Site Recovery ist zertifiziert nach ISO 27001:2013, 27018, HIPAA und DPA. Der Dienst durchläuft Bewertungen unter SOC2 und FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Verschlüsselt Site Recovery die Replikation?

Ja. Sowohl Verschlüsselung bei der Übertragung als auch [Verschlüsselung im Ruhezustand in Azure](../storage/common/storage-service-encryption.md) wird unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen der Azure-zu-Azure-Supportanforderungen](azure-to-azure-support-matrix.md).
- [Einrichten der Azure-zu-Azure-Replikation](azure-to-azure-tutorial-enable-replication.md).
- Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese auf der [Microsoft F&A-Seite für Azure Recovery Services](/answers/topics/azure-site-recovery.html).
