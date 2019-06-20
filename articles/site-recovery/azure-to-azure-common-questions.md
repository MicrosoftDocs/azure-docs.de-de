---
title: Häufige Fragen zu Azure-zu-Azure-Notfallwiederherstellung mit Azure Site Recovery
description: Dieser Artikel beantwortet häufig gestellte Fragen zur Notfallwiederherstellung von virtuellen Azure-Computern in eine andere Azure-Region mithilfe von Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgan
ms.openlocfilehash: 271e3c31c3e08d170add84ca4995f4876d4d3a33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753773"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Häufig gestellte Fragen sind: Azure-zu-Azure-Notfallwiederherstellung

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Notfallwiederherstellung von virtuellen Azure-Computern in eine andere Azure-Region mithilfe von [Site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Allgemein

### <a name="how-is-site-recovery-priced"></a>Wie werden Site Recovery-Preise kalkuliert?
Nähere Informationen finden Sie unter [Site Recovery – Preise](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Wie funktioniert der Free-Tarif für Azure Site Recovery?
Jede Instanz, die mit Azure Site Recovery geschützt wird, ist für die ersten 31 Tage kostenlos. Ab dem 32. Tag wird der Schutz für die Instanz zu den oben aufgeführten Tarifen berechnet.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Fallen während der ersten 31 Tage irgendwelche anderen Azure-Gebühren an?
Ja. Auch wenn Azure Site Recovery während der ersten 31 Tage einer geschützten Instanz kostenlos ist, können für Azure Storage, Speichertransaktionen und Datenübertragungen Gebühren anfallen. Auch für einen wiederhergestellten virtuellen Computer können Azure-Computegebühren anfallen. [Hier](https://azure.microsoft.com/pricing/details/site-recovery) erhalten Sie ausführliche Informationen zu den Preisen.

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Wo finde ich bewährte Methoden für die Notfallwiederherstellung von virtuellen Azure-Computern? 
1. [Grundlegendes zur Azure-zu-Azure-Architektur](azure-to-azure-architecture.md)
2. [Review the supported and not-supported configurations (Überprüfen der unterstützten und nicht unterstützten Konfigurationen)](azure-to-azure-support-matrix.md)
3. [Set up disaster recovery for Azure VMs (Einrichten der Notfallwiederherstellung für Azure-VMs)](azure-to-azure-how-to-enable-replication.md)
4. [Ausführen eines Testfailovers](azure-to-azure-tutorial-dr-drill.md)
5. [Failover und Failback auf die primäre Region](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Wie wird die Kapazität in der Zielregion garantiert?
Das Site Recovery-Team arbeitet mit dem Azure-Kapazitätsverwaltungsteam zusammen, um eine ausreichende Infrastrukturkapazität zu planen und sicherzustellen, dass durch Site Recovery geschützte virtuelle Computer in der Zielregion erfolgreich bereitgestellt werden, wenn ein Failover initiiert wird.

## <a name="replication"></a>Replikation

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kann ich virtuelle Computer replizieren, die für die Azure Disk Encryption aktiviert sind?
Ja, Sie können diese replizieren. Lesen Sie den Artikel [Replizieren von ADE-fähigen (Azure Disk Encryption) virtuellen Computern in einer anderen Azure-Region](azure-to-azure-how-to-enable-replication-ade-vms.md). Derzeit unterstützt Azure Site Recovery nur virtuelle Azure-Computer, auf denen das Windows-Betriebssystem ausgeführt wird und die für die Verschlüsselung mit Azure AD-Apps aktiviert sind.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kann ich VMs in ein anderes Abonnement replizieren?
Ja, Sie können virtuelle Azure-Computer in ein anderes Abonnement im selben Azure AD-Mandanten replizieren.
Das [abonnementübergreifende](https://azure.microsoft.com/blog/cross-subscription-dr) Konfigurieren der Notfallwiederherstellung ist einfach. Sie können zum Zeitpunkt der Replikation ein anderes Abonnement auswählen.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kann ich an Zonen geheftete virtuelle Azure-Computer in eine andere Region replizieren?
Ja, Sie können [an Zonen geheftete virtuelle Computer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) in eine andere Region replizieren.

### <a name="can-i-exclude-disks"></a>Kann ich Datenträger ausschließen?

Ja, Sie können Datenträger zum Zeitpunkt des Schutzes mithilfe von PowerShell ausschließen. Weitere Informationen finden Sie in diesem [Artikel](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kann ich neue Datenträger zu replizierten virtuellen Computern hinzufügen und die Replikation dafür aktivieren?

Ja. Dies wird bei virtuellen Azure-Computern mit verwalteten Datenträgern unterstützt. Wenn Sie einem virtuellen Azure-Computer, für den die Replikation aktiviert wurde, einen neuen Datenträger hinzufügen, zeigt die Replikationsintegrität für den Computer eine Warnung mit einem Hinweis an, dass ein oder mehrere Datenträger auf dem Computer für den Schutz zur Verfügung stehen. Sie können die Replikation für hinzugefügte Datenträger aktivieren.
- Wenn Sie den Schutz für die hinzugefügten Datenträger aktivieren, wird die Warnung nach der ersten Replikation nicht mehr angezeigt.
- Wenn Sie wählen, dass die Replikation für den Datenträger nicht aktiviert werden soll, können Sie die Warnung wahlweise verwerfen.
- Beim Failover eines virtuellen Computers, dem Sie einen Datenträger hinzugefügt und dafür die Replikation aktiviert haben, zeigen Replikationspunkte die Datenträger an, die zur Wiederherstellung zur Verfügung stehen. Wenn beispielsweise ein virtueller Computer über einen einzelnen Datenträger verfügt und Sie einen neuen hinzufügen, zeigen Replikationspunkte, die vor dem Hinzufügen des Datenträgers erstellt wurden, an, dass der Replikationspunkt aus „1 von 2 Datenträgern“ besteht.

Site Recovery unterstützt kein „Entfernen eines Datenträgers im laufendem Betrieb“ aus einem replizierten virtuellen Computer. Wenn Sie einen VM-Datenträger entfernen, müssen Sie die Replikation für den virtuellen Computer deaktivieren und dann erneut aktivieren.


### <a name="how-often-can-i-replicate-to-azure"></a>Wie oft kann ich zu Azure replizieren?
Die Replikation ist fortlaufend, wenn Sie virtuelle Azure-Computer in eine andere Azure-Region replizieren. Weitere Informationen finden Sie unter [Architektur der Azure-zu-Azure-Replikation](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Kann ich virtuelle Computer innerhalb einer Region replizieren? Ich benötige das zum Migrieren von virtuellen Computern.
Sie können eine Lösung für die Azure-zu-Azure-Notfallwiederherstellung nicht verwenden, um virtuelle Computer innerhalb einer Region zu replizieren.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Kann ich VMs in jede Azure-Region replizieren?
Mit Site Recovery können Sie VMs zwischen zwei beliebigen Regionen im gleichen geografischen Cluster replizieren und wiederherstellen. Geografische Cluster werden unter Berücksichtigung von Datenlatenz und Souveränität definiert. Weitere Informationen finden Sie unter [Unterstützung für Regionen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) für Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Ist für Site Recovery eine Internetverbindung erforderlich?

Nein, für Site Recovery ist keine Internetverbindung erforderlich. Benötigt wird jedoch ein Zugriff auf die URLs und IP-Adressbereiche von Site Recovery, wie [in diesem Artikel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) erwähnt.

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Kann ich die Anwendung mit einer separaten Ressourcengruppe für verschiedene Ebenen replizieren?
Ja, Sie können die Anwendung replizieren und die Notfallwiederherstellungskonfiguration auch in einer separaten Ressourcengruppe speichern.
Angenommen, Sie verwenden eine Anwendung, bei der die App-, DB- und Web-Ebenen sich jeweils in einer separaten Ressourcengruppe befinden. Dann müssen Sie dreimal auf den [Replikations-Assistenten](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) klicken, um alle Ebenen zu schützen. Site Recovery repliziert diese drei Ebenen in drei verschiedenen Ressourcengruppen.

## <a name="replication-policy"></a>Replikationsrichtlinie

### <a name="what-is-a-replication-policy"></a>Was ist eine Replikationsrichtlinie?
Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf von Wiederherstellungspunkten und die Häufigkeit von anwendungskonsistenten Momentaufnahmen. Standardmäßig erstellt Azure Site Recovery eine neue Replikationsrichtlinie mit folgenden Standardeinstellungen:

* 24 Stunden für den Aufbewahrungsverlauf von Wiederherstellungspunkten.
* 60 Minuten für die Häufigkeit anwendungskonsistenter Momentaufnahmen.

[Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)

### <a name="what-is-a-crash-consistent-recovery-point"></a>Was ist ein absturzkonsistenter Wiederherstellungspunkt?
Ein absturzkonsistenter Wiederherstellungspunkt stellt die Daten auf einem Datenträger so dar, als ob zum Zeitpunkt der Momentaufnahme der virtuelle Computer abgestürzt oder der Server vom Strom getrennt worden wäre. Er enthält nichts, was sich zum Zeitpunkt der Momentaufnahme im Arbeitsspeicher befand.

Heutzutage können die meisten Anwendungen aus absturzkonsistenten Momentaufnahmen gut wiederhergestellt werden. Ein absturzkonsistenter Wiederherstellungspunkt ist normalerweise ausreichend für Betriebssysteme ohne Datenbank und Anwendungen wie Dateiserver, DHCP-Server und Druckerserver.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Mit welcher Häufigkeit wird ein absturzkonsistenter Wiederherstellungspunkt generiert?
Site Recovery erstellt alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Was ist ein anwendungskonsistenter Wiederherstellungspunkt?
Anwendungskonsistente Wiederherstellungspunkte werden aus anwendungskonsistenten Momentaufnahmen erstellt. Anwendungskonsistente Wiederherstellungspunkte erfassen dieselben Daten wie absturzkonsistente Momentaufnahmen. Zudem werden alle Daten im Arbeitsspeicher und alle laufenden Transaktionen erfasst.
Durch diesen zusätzlichen Inhalt sind anwendungskonsistente Momentaufnahmen am stärksten involviert, und ihre Ausführung dauert am längsten. Anwendungskonsistente Wiederherstellungspunkte werden für Betriebssysteme mit Datenbank und Anwendungen wie SQL Server empfohlen.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Welche Auswirkungen haben anwendungskonsistente Wiederherstellungspunkte auf die Anwendungsleistung?
In Anbetracht der Tatsache, dass anwendungskonsistente Wiederherstellungspunkte alle Daten im Arbeitsspeicher und bei der Verarbeitung erfassen, muss das Framework (z.B. VSS auf Windows) die Anwendung stilllegen. Dies kann bei sehr häufiger Ausführung zu Leistungseinbußen führen, wenn die Auslastung bereits sehr hoch ist. Es wird in der Regel empfohlen, keine niedrige Frequenz für anwendungskonsistente Wiederherstellungspunkte für Nicht-Datenbank-Workloads zu verwenden, und selbst für Datenbankworkloads genügt eine Stunde.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mit welcher Mindesthäufigkeit wird ein anwendungskonsistenter Wiederherstellungspunkt generiert?
Site Recovery kann einen anwendungskonsistenten Wiederherstellungspunkt mit einer Mindesthäufigkeit von 1 pro Stunde erstellen.

### <a name="how-are-recovery-points-generated-and-saved"></a>Wie werden Wiederherstellungspunkte generiert und gespeichert?
Um zu verstehen, wie Site Recovery Wiederherstellungspunkte generiert, nehmen wir als Beispiel eine Replikationsrichtlinie, die für Wiederherstellungspunkte ein Aufbewahrungsfenster von 24 Stunden vorsieht und jede Stunde eine anwendungskonsistente Momentaufnahme anfertigt.

Site Recovery erstellt alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt. Der Benutzer kann diese Häufigkeit nicht ändern. Somit verfügt der Benutzer für die letzte Stunde über eine Auswahl von 12 absturzkonsistenten Wiederherstellungspunkten und einen anwendungskonsistenten Wiederherstellungspunkt. Im Lauf der Zeit löscht Site Recovery alle Wiederherstellungspunkte, die älter als 1 Stunde sind, und speichert nur einen Wiederherstellungspunkt pro Stunde.

Der folgende Screenshot veranschaulicht dieses Beispiel. Im Screenshot:

1. Für die Zeit innerhalb der letzten Stunde sind Wiederherstellungspunkte im Intervall von 5 Minuten vorhanden.
2. Für den Zeitraum vor der letzten Stunde bewahrt Site Recovery nur einen Wiederherstellungspunkt auf.

   ![Liste der generierten Wiederherstellungspunkte](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Wie weit kann ich bei der Wiederherstellung zurückgehen?
Der älteste Wiederherstellungspunkt, den Sie verwenden können, ist 72 Stunden alt.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Was passiert, wenn ich über eine Replikationsrichtlinie von 24 Stunden verfüge und Site Recovery aufgrund eines Problems für mehr als 24 Stunden keinen Wiederherstellungspunkt generiert hat? Gehen meine vorherigen Wiederherstellungspunkte verloren?
Nein. Site Recovery bewahrt alle Ihre vorherigen Wiederherstellungspunkte auf. Abhängig vom Aufbewahrungszeitfenster für Wiederherstellungspunkte (in diesem Fall: 24 Stunden) ersetzt Site Recovery den ältesten Punkt nur, wenn neue Punkte generiert werden. Da in diesem Fall aufgrund eines Problems kein neuer Wiederherstellungspunkt generiert wird, bleiben bei Erreichen des Aufbewahrungszeitfensters alle alten Punkte intakt.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Wie ändere ich die Replikationsrichtlinie, nachdem die Replikation auf einer VM aktiviert wurde?
Navigieren Sie zu **Site Recovery-Tresor** > **Site Recovery-Infrastruktur** > **Replikationsrichtlinien**. Wählen Sie die Richtlinie aus, die Sie bearbeiten möchten, und speichern Sie die Änderungen. Alle Änderungen werden auch auf alle vorhandenen Replikationen angewendet.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Sind alle Wiederherstellungspunkte vollständige Kopien des virtuellen Computers oder gibt es Unterschiede?
Der erste Wiederherstellungspunkt, der generiert wird, ist eine vollständige Kopie. Alle nachfolgenden Wiederherstellungspunkte enthalten nur die geänderten Daten (Deltaänderungen).

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Erhöhen sich durch eine längere Aufbewahrungsdauer der Wiederherstellungspunkte die Speicherkosten?
Ja. Wenn Sie die Aufbewahrungsdauer von 24 auf 72 Stunden erhöhen, speichert Site Recovery die Wiederherstellungspunkte für zusätzliche 48 Stunden. Durch die zusätzliche Zeit fallen Speicherkosten an. Wenn zum Beispiel ein einzelner Wiederherstellungspunkt Deltaänderungen von 10 GB enthält und jedes GB pro Monat 0,16 US-Dollar kostet, beliefen sich die zusätzlichen Gebühren auf 1,6 US-Dollar × 48 pro Monat.

## <a name="multi-vm-consistency"></a>Multi-VM-Konsistenz

### <a name="what-is-multi-vm-consistency"></a>Was versteht man unter der Konsistenz mehrerer virtueller Computer?
Sie stellt sicher, dass der Wiederherstellungspunkt über alle replizierten virtuellen Computer konsistent ist.
Site Recovery bietet die Option „Multi-VM-Konsistenz“, bei deren Auswahl eine Replikationsgruppe erstellt wird, um alle zur Gruppe gehörenden Computer gemeinsam zu replizieren.
Diese virtuellen Computer verfügen alle beim Failover über gemeinsame absturz- und anwendungskonsistente Wiederherstellungspunkte.
Schauen Sie sich hierzu das Tutorial [Aktivieren von Multi-VM-Konsistenz](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm) an.

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kann für einen einzelnen virtuellen Computer in einer Replikationsgruppe für Multi-VM-Konsistenz ein Failover ausgeführt werden?
Durch Auswahl der Option „Multi-VM-Konsistenz“ geben Sie an, dass die Anwendung von allen virtuellen Computern innerhalb einer Gruppe abhängig ist. Daher ist ein Failover für einen einzelnen virtuellen Computer nicht zulässig.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Wie viele virtuelle Computer können als Teil einer Replikationsgruppe für Multi-VM-Konsistenz repliziert werden?
In einer Replikationsgruppe können 16 virtuelle Computer zusammen repliziert werden.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Wann sollte ich Multi-VM-Konsistenz aktivieren?
Das Aktivieren von Multi-VM-Konsistenz kann die Workloadleistung beeinträchtigen, da diese Option CPU-intensiv ist. Sie sollte nur verwendet werden, wenn Computer dieselbe Workload ausführen und Konsistenz für mehrere Computer erforderlich ist. Wenn Sie beispielsweise in einer Anwendung zwei SQL Server-Instanzen und zwei Webserver haben, sollten Sie die Multi-VM-Konsistenz nur für die SQL Server-Instanzen einrichten.


## <a name="failover"></a>Failover

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Wie wird die Kapazität in der Zielregion für Azure VMs garantiert?
Das Site Recovery-Team arbeitet mit dem Azure-Kapazitätsverwaltungsteam zusammen, um eine ausreichende Infrastrukturkapazität zu planen und so sicherzustellen, dass virtuelle Computer, für die die Notfallwiederherstellung aktiviert wurde, in der Zielregion erfolgreich bereitgestellt werden, wenn ein Failover initiiert wird.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?

Ein Failover erfolgt nicht automatisch. Sie starten Failover mit einem Mausklick im Portal, oder Sie können [PowerShell](azure-to-azure-powershell.md) verwenden, um ein Failover auszulösen.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Kann ich nach dem Failover eine öffentliche IP-Adresse beibehalten?

Die öffentliche IP-Adresse einer Produktionsanwendung kann nach einem Failover nicht beibehalten werden.
- Workloads, die als Teil eines Failoverprozesses entstehen, müssen einer in der Zielregion verfügbaren öffentlichen Azure-IP-Ressource zugewiesen werden.
- Sie können dies manuell durchführen oder mit einem Wiederherstellungsplan automatisieren.
- Erfahren Sie mehr über das [Einrichten von öffentlichen IP-Adressen nach einem Failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Kann ich eine private IP-Adresse während des Failovers beibehalten?
Ja. Sie können eine private IP-Adresse beibehalten. Wenn Sie die Notfallwiederherstellung für virtuelle Azure-Computer aktivieren, werden die Zielressourcen in Site Recovery basierend auf den Einstellungen für die Quellressourcen erstellt. – Für virtuelle Azure-Computer, die mit statischen IP-Adressen konfiguriert wurden, wird in Site Recovery nach Möglichkeit die gleiche IP-Adresse für den virtuellen Zielcomputer bereitgestellt, sofern sie nicht bereits verwendet wird.
Erfahren Sie mehr über das [Beibehalten von IP-Adressen während des Failovers](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Warum wird dem Server nach einem Failover eine neue IP-Adresse zugewiesen?

Site Recovery versucht, die IP-Adresse zum Zeitpunkt des Failovers bereitzustellen. Wenn jedoch ein anderer virtueller Computer diese Adresse übernimmt, legt Site Recovery die nächste verfügbare IP-Adresse als Ziel fest.
Erfahren Sie mehr über das [Einrichten von Netzwerkzuordnung und die IP-Adressierung für VNets](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Was bedeutet **Letzte (niedrigste RPO)** bei Wiederherstellungspunkten?
Mit der Option **Letzte (niedrigste RPO)** werden zuerst alle Daten verarbeitet, die an den Site Recovery-Dienst gesendet wurden, um vor dem Failover einen Wiederherstellungspunkt für jeden virtuellen Computer zu erstellen. Diese Option stellt die niedrigste Recovery Point Objective (RPO) bereit, da der nach dem Failover erstellte virtuelle Computer über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Wirken sich Wiederherstellungspunkte des Typs **Letzte (niedrigste RPO)** auf die Failover-RTO aus?
Ja. Site Recovery verarbeitet vor dem Failover alle ausstehenden Daten. Daher hat diese Option eine höhere Recovery Time Objective (RTO) im Vergleich zu anderen Optionen.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Was bedeutet die Option **Letzte Verarbeitung** bei Wiederherstellungspunkten?
Mit der Option **Letzte Verarbeitung** wird ein Failover aller virtuellen Computer im Plan auf den Wiederherstellungspunkt ausgeführt, den Site Recovery zuletzt verarbeitet hat. Um den letzten Wiederherstellungspunkt für eine bestimmte VM zu finden, überprüfen Sie **Letzte Wiederherstellungspunkte** in den Einstellungen der VM. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Was geschieht, wenn in meiner primären Region ein unerwarteter Ausfall auftritt?
Sie können nach dem Ausfall ein Failover auslösen. Site Recovery benötigt keine Verbindung mit der primären Region, um das Failover auszuführen.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Was ist eine RTO eines VM-Failovers?
Site Recovery besitzt eine [RTO SLA von 2 Stunden](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). In den meisten Fällen erfolgt ein Failover virtueller Computer durch Site Recovery jedoch innerhalb weniger Minuten. Sie können den RTO-Wert berechnen, indem Sie zu den Failoveraufträgen wechseln, wo die Zeit angezeigt wird, die es gedauert hat, den virtuellen Computer wieder verfügbar zu machen. Informationen zum Wiederherstellungsplan-RTO finden Sie weiter unten.

## <a name="recovery-plans"></a>Wiederherstellungspläne

### <a name="what-is-a-recovery-plan"></a>Was ist ein Wiederherstellungsplan?
Ein Wiederherstellungsplan in Site Recovery orchestriert die Wiederherstellung von virtuellen Computern nach einem Failover. Sie tragen zu einer durchgängig exakten, wiederholbaren und automatisierten Wiederherstellung bei. Ein Wiederherstellungsplan kümmert sich für den Benutzer um Folgendes:

- Definieren einer Gruppe virtueller Computer für ein gemeinsames Failover
- Definieren der Abhängigkeiten zwischen virtuellen Computern, damit die Anwendung korrekt funktioniert
- Automatisieren der Wiederherstellung zusammen mit benutzerdefinierten manuellen Aktionen, damit auch andere Vorgänge als das Failover der virtuellen Computer erreicht werden können

[hier](site-recovery-create-recovery-plans.md) .

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Wie wird die Sequenzierung in einem Wiederherstellungsplan erreicht?

In einem Wiederherstellungsplan können Sie mehrere Gruppen für die Sequenzierung erstellen. Für jede Gruppe wird jeweils ein Failover ausgeführt. Für die virtuellen Computer, die zur selben Gruppe gehören, wird gemeinsam ein Failover ausgeführt, darauf folgt dann eine weitere Gruppe. Unter [Informationen zu Wiederherstellungsplänen](recovery-plan-overview.md#model-apps) erfahren Sie, wie Sie eine Anwendung mithilfe eines Wiederherstellungsplans modellieren.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Wie kann ich die RTO eines Wiederherstellungsplans finden?
Um die RTO eines Wiederherstellungsplans zu prüfen, führen Sie ein Testfailover für den Wiederherstellungsplan aus, und navigieren Sie zu **Site Recovery-Aufträge**.
Im folgenden Beispiel hat der Auftrag namens „SAPTestRecoveryPlan“ 8 Minuten und 59 Sekunden für ein Failover aller virtuellen Computer und für das Ausführen spezieller Aktionen benötigt.

![Liste der Site Recovery-Aufträge](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kann ich Automation-Runbooks zum Wiederherstellungsplan hinzufügen?
Ja, Sie können Azure Automation-Runbooks in Ihren Wiederherstellungsplan integrieren. [Weitere Informationen](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Erneuter Schutz und Failback

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Sind virtuelle Computer in einer Notfallwiederherstellungsregion nach einem Failover aus der primären Region in die Notfallwiederherstellungsregion automatisch geschützt?
Nein. Wenn Sie ein [Failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) für virtuelle Azure-Computer von einer Region in eine andere durchführen, werden die virtuellen Computer in der Notfallwiederherstellungsregion in einem ungeschützten Zustand gestartet. Für ein Failback der VMs in die primäre Region müssen Sie die VMs in der sekundären Region [erneut schützen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect).

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Repliziert Site Recovery zum Zeitpunkt des erneuten Schutzes alle Daten aus der sekundären Region in die primäre Region?
Dies hängt von der Situation ab. Wenn zum Beispiel der virtuelle Computer der Quellregion vorhanden ist, werden nur Änderungen zwischen dem Quelldatenträger und dem Zieldatenträger synchronisiert. Site Recovery berechnet die Unterschiede durch Vergleichen der Datenträger und überträgt dann die Daten. Dieser Vorgang dauert normalerweise einige Stunden. Unter [Erneutes Schützen von virtuellen Azure-Computern, für die ein Failover zur primären Region durchgeführt wurde]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) erfahren Sie, was bei einem erneuten Schutz passiert.

### <a name="how-much-time-does-it-take-to-fail-back"></a>Wie lange dauert ein Failback?
Nach dem erneuten Schützen ist die Zeitspanne für ein Failback allgemein mit der benötigten Zeit für das Failover von der primären Region in eine sekundäre Region vergleichbar.

## <a name="capacity"></a>Kapazität

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Wie wird die Kapazität in der Zielregion für Azure VMs garantiert?
Das Site Recovery-Team arbeitet mit dem Azure-Kapazitätsverwaltungsteam zusammen, um eine ausreichende Infrastrukturkapazität zu planen und so sicherzustellen, dass virtuelle Computer, für die die Notfallwiederherstellung aktiviert wurde, in der Zielregion erfolgreich bereitgestellt werden, wenn ein Failover initiiert wird.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Kann Site Recovery bei reservierten Instanzen verwendet werden?
Ja. Sie können [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) in der Region für die Notfallwiederherstellung erwerben, die dann von den Site Recovery-Failovervorgängen verwendet werden. </br> Es ist keine zusätzliche Konfiguration erforderlich.


## <a name="security"></a>Sicherheit

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Werden Replikationsdaten an den Site Recovery-Dienst gesendet?
Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern ausgeführt wird. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.  
Site Recovery ist nach ISO 27001:2013, 27018, HIPAA und DPA zertifiziert und durchläuft gerade die Prüfungen für SOC2 und FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Verschlüsselt Site Recovery die Replikation?
Ja. Sowohl Verschlüsselung bei der Übertragung als auch [Verschlüsselung im Ruhezustand in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) wird unterstützt.

## <a name="next-steps"></a>Nächste Schritte
* [Informieren](azure-to-azure-support-matrix.md) Sie sich über die Supportanforderungen.
* [Einrichten](azure-to-azure-tutorial-enable-replication.md) der Azure-zu-Azure-Replikation.
- Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
