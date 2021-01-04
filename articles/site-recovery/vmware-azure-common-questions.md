---
title: Allgemeine Fragen zu VMware-Notfallwiederherstellung mit Azure Site Recovery
description: Hier erhalten Sie Antworten auf allgemeine Fragen zur Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mithilfe von Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 8f292e7f624b80e8e13514a714c5759d88fbe57c
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379989"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Allgemeine Fragen zur VMware-zu-Azure-Replikation

Dieser Artikel beantwortet allgemeine Fragen, die aufkommen können, wenn Sie die Notfallwiederherstellung von lokalen virtuellen VMware-Computern (VMs) in Azure bereitstellen.

## <a name="general"></a>Allgemein

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Was benötige ich für die Notfallwiederherstellung von virtuellen VMware-Computern?

[Hier finden Sie Informationen zu den Komponenten der Notfallwiederherstellung](vmware-azure-architecture.md) von virtuellen VMware-Computern.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kann ich Site Recovery für die Migration von virtuellen VMware-Computern zu Azure verwenden?

Ja. Neben der Verwendung von Site Recovery zum Einrichten der vollständigen Notfallwiederherstellung für virtuelle VMware-Computer können Sie mit Site Recovery die Migration von lokalen virtuellen VMware-Computern zu Azure durchführen. In diesem Szenario replizieren Sie lokale VMware-VMs zu Azure Storage. Dann führen Sie ein Failover vom lokalen Standort zu Azure aus. Nach dem Failover sind Ihre Apps und Workloads verfügbar und werden auf virtuellen Azure-Computern ausgeführt. Der Vorgang ähnelt dem Einrichten der vollständigen Notfallwiederherstellung, mit der Ausnahme, dass in einer Migration kein Failback von Azure ausgeführt werden kann.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Benötigt mein Azure-Konto Berechtigungen zum Erstellen von virtuellen Computern?

Wenn Sie ein Abonnementadminstrator sind, besitzen Sie die erforderlichen Replikationsberechtigungen. Wenn Sie kein Administrator sind, benötigen Sie Berechtigungen, um diese Aktionen auszuführen:

- Erstellen einer Azure-VM in der Ressourcengruppe und dem virtuellen Netzwerk, die Sie angeben, wenn Sie Site Recovery konfigurieren.
- Schreiben in das ausgewählte Speicherkonto oder auf den verwalteten Datenträger auf Basis Ihrer Konfiguration.

[Erfahren Sie mehr](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) über die erforderlichen Berechtigungen.

### <a name="what-applications-can-i-replicate"></a>Welche Anwendungen kann ich replizieren?

Sie können jede auf einer VMware-VM ausgeführte App oder Workload replizieren, die den [Replikationsanforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) entspricht.

- Site Recovery unterstützt die anwendungsorientierte Replikation, sodass für Apps ein Failover und ein Failback zum „intelligenten Zustand“ ausgeführt werden kann.
- Site Recovery ist in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory integriert. Außerdem ist Site Recovery nahtlos mit Produkten führender Hersteller wie Oracle, SAP, IBM und Red Hat einsetzbar.

[Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kann ich eine Gastbetriebssystem-Serverlizenz in Azure verwenden?

Ja, Microsoft Software Assurance-Kunden können den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen, um Lizenzierungskosten für Windows Server-Computer zu sparen, die zu Azure migriert werden, oder um Azure für die Notfallwiederherstellung zu verwenden.

## <a name="security"></a>Sicherheit

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Welchen Zugriff auf VMware-Server benötigt Site Recovery?

Site Recovery benötigt Zugriff auf VMware-Server, um folgende Aufgaben durchzuführen:

- Einrichten eines virtuellen VMware-Computers, auf dem der Site Recovery-Konfigurationsserver ausgeführt wird.
- Ermitteln Sie automatisch virtuelle Computer für die Replikation.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Welchen Zugriff auf VMware-VMs benötigt Site Recovery?

- Zum Replizieren muss auf einer VMware-VM der Site Recovery Mobility Service installiert sein und ausgeführt werden. Sie können das Tool manuell bereitstellen oder angeben, dass Site Recovery eine Pushinstallation des Diensts ausführen soll, wenn Sie die Replikation für einen virtuellen Computer aktivieren.
- Während der Replikation kommunizieren virtuelle Computer wie folgt mit Site Recovery:
    - VMs kommunizieren mit dem Konfigurationsserver über HTTPS-Port 443 für die Replikationsverwaltung.
    - VMs senden Replikationsdaten über HTTPS-Port 9443 an den Prozessserver. (Diese Einstellung kann geändert werden.)
    - Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren VMs über den Port 20004 miteinander.

### <a name="is-replication-data-sent-to-site-recovery"></a>Werden Replikationsdaten an Site Recovery gesendet?

Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern ausgeführt wird. Replikationsdaten werden zwischen VMware-Hypervisoren und Azure Storage ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.

Site Recovery ist für ISO 27001:2013 und 27018, HIPAA und DPA zertifiziert. Die Bewertungen für SOC2 und FedRAMP JAB werden gerade durchgeführt.

## <a name="pricing"></a>Preise

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Wie kann ich die ungefähren Kosten für die VMware-Notfallwiederherstellung berechnen?

Verwenden Sie den [Preisrechner](https://aka.ms/asr_pricing_calculator), um die Kosten für die Verwendung von Site Recovery zu schätzen.

Für eine detaillierte Kostenschätzung können Sie das Bereitstellungsplanertool für [VMware](./site-recovery-deployment-planner.md) ausführen und den [Kostenschätzungsbericht](./site-recovery-vmware-deployment-planner-cost-estimation.md) verwenden.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Besteht ein Unterschied zwischen der Replikation im Speicher und direkt in verwalteten Datenträgern?

Die Berechnung der Kosten für verwaltete Datenträger unterscheidet sich etwas von der für Speicherkonten. [Weitere Informationen](https://azure.microsoft.com/pricing/details/managed-disks/) zu den Preisen für verwaltete Datenträger.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Gibt es einen Kostenunterschied bei der Replikation zu einem Speicherkonto vom Typ „Allgemein v2“?

Normalerweise treten erhöhte Transaktionskosten bei GPv2-Speicherkonten auf, da viele Transaktionen über Azure Site Recovery durchgeführt werden. [Erfahren Sie mehr](../storage/common/storage-account-upgrade.md#pricing-and-billing), um die Änderung einschätzen zu können.

## <a name="mobility-service"></a>Mobilitätsdienst

### <a name="where-can-i-find-the-mobility-service-installers"></a>Wo finde ich die Mobility Service-Installationsprogramme?

Die Installationsprogramme befinden sich auf dem Konfigurationsserver im Ordner „%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository“.

## <a name="how-do-i-install-the-mobility-service"></a>Wie installiere ich den Mobility Service?

Installieren Sie den Dienst auf jedem virtuellen Computer, den Sie replizieren möchten, mit einer der folgenden Methoden:

- [Pushinstallation](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuelle Installation](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) über die Benutzeroberfläche oder PowerShell.
- Bereitstellung mithilfe eines Bereitstellungstools wie z. B. [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Verwaltete Datenträger

### <a name="where-does-site-recovery-replicate-data-to"></a>Wo werden Daten mithilfe von Site Recovery repliziert?

Site Recovery repliziert lokale virtuelle VMware-Computer und physische Server in verwalteten Datenträgern in Azure.

- Der Site Recovery-Prozessserver schreibt Replikationsprotokolle in ein Cachespeicherkonto in der Zielregion.
- Diese Protokolle werden verwendet, um die Wiederherstellungspunkte auf verwalteten Azure-Datenträgern mit dem Präfix **asrseeddisk** zu erstellen.
- Bei einem Failover wird der ausgewählte Wiederherstellungspunkt verwendet, um einen neuen verwalteten Zieldatenträger zu erstellen. Dieser verwaltete Datenträger wird dem virtuellen Computer in Azure angefügt.
- Virtuelle Computer, die zuvor in einem Speicherkonto repliziert wurden (vor März 2019), sind nicht betroffen.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kann ich neue Computer in Speicherkonten replizieren?

Nein. Seit März 2019 können Sie im Azure-Portal die Replikation nur in verwalteten Azure-Datenträgern durchführen.

Die Replikation von neuen virtuellen Computern in einem Speicherkonto ist nur über PowerShell ([Version 1.4.5 des Az.RecoveryServices-Moduls](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) oder die REST-API (Version 2018-01-10 oder 2016-08-10) verfügbar. [Hier](./vmware-azure-disaster-recovery-powershell.md) erfahren Sie, wie Sie die Replikation mithilfe der PowerShell-Befehle einrichten.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Welche Vorteile hat die Replikation auf verwalteten Datenträgern?

[Hier](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) finden Sie Informationen dazu, wie die Notfallwiederherstellung mit verwalteten Datenträgern mithilfe von Site Recovery vereinfacht wird.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kann ich den Typ der verwalteten Datenträger ändern, sobald der Computer geschützt wird?

Ja, für laufende Replikationen können Sie mühelos [den Typ des verwalteten Datenträgers ändern](../virtual-machines/windows/convert-disk-storage.md). Bevor Sie den Typ ändern, stellen Sie sicher, dass keine SAS-URL auf dem verwalteten Datenträger generiert wird:

1. Wechseln Sie zu der Ressource **Verwalteter Datenträger** im Azure-Portal, und überprüfen Sie, ob auf dem Blatt **Übersicht** ein SAS-URL-Banner für Sie angezeigt wird.
1. Wenn das Banner vorhanden ist, wählen Sie es aus, um den fortlaufenden Export abzubrechen.
1. Ändern Sie den Typ des Datenträgers innerhalb der nächsten paar Minuten. Wenn Sie den Typ des verwalteten Datenträgers ändern, warten Sie, bis Azure Site Recovery neue Wiederherstellungspunkte generiert hat.
1. Verwenden Sie die neuen Wiederherstellungspunkte für zukünftige Testfailover oder Failover.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kann ich die Replikation von verwalteten Datenträgern in nicht verwaltete Datenträger ändern?

Nein. Das Wechseln von verwalteten zu nicht verwalteten Datenträgern wird nicht unterstützt.

## <a name="replication"></a>Replikation

### <a name="what-are-the-replicated-vm-requirements"></a>Welche Anforderungen stellt die Replikation an virtuelle Computer?

[Erfahren Sie mehr](vmware-physical-azure-support-matrix.md#replicated-machines) über Supportanforderungen für VMware-VMs und physische Server.

### <a name="how-often-can-i-replicate-to-azure"></a>Wie oft kann ich zu Azure replizieren?

Beim Replizieren von VMware-VMs zu Azure ist die Replikation fortlaufend.

### <a name="can-i-extend-replication"></a>Kann ich die Replikation erweitern?

Eine erweiterte oder verkettete Replikation wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kann ich eine erste Offlinereplikation durchführen?

Offlinereplikation wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Was ist asrseeddisk?

Für jeden Quelldatenträger werden Daten in einen verwalteten Datenträger in Azure repliziert. Dieser Datenträger hat das Präfix **asrseeddisk**. Er speichert die Kopie des Quelldatenträgers und alle Wiederherstellungspunkt-Momentaufnahmen.

### <a name="can-i-exclude-disks-from-replication"></a>Kann ich Datenträger von der Replikation ausschließen?

Ja, Sie können Datenträger ausschließen.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kann ich virtuelle Computer mit dynamischen Datenträgern replizieren?

Dynamische Datenträger können repliziert werden. Der Betriebssystem-Datenträger muss ein Basisdatenträger sein.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Wenn ich Replikationsgruppen für die Konsistenz mehrerer VMs verwende, kann ich einer vorhandenen Replikationsgruppe eine neue VM hinzufügen?

Ja, Sie können einer vorhandenen Replikationsgruppe neue VMs hinzufügen, wenn Sie die Replikation für sie aktivieren. Allerdings:

- Nach dem Beginn der Replikation können Sie einer vorhandenen Replikationsgruppe keinen virtuellen Computer mehr hinzufügen.
- Sie können keine Replikationsgruppe für vorhandene virtuelle Computer erstellen.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kann ich replizierende VMs durch Hinzufügen oder Ändern der Datenträgergröße ändern?

Für die VMware-Replikation in Azure können Sie die Datenträgergröße der Quell-VMs ändern. Wenn Sie neue Datenträger hinzufügen möchten, müssen Sie den Datenträger hinzufügen und den Schutz für die VM erneut aktivieren.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kann ich lokale Computer zu einem neuen vCenter-Server migrieren, ohne die laufende Replikation zu beeinträchtigen?

Informationen zum Migrieren von Computern zu einem neuen vCenter-Server finden Sie in unserem [Leitfaden](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server).

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kann ich die Replikation in einem Cache oder einem Zielspeicherkonto durchführen, für den bzw. das ein virtuelles Netzwerk (mit Azure Firewalls) konfiguriert ist?

Nein, Site Recovery unterstützt keine Replikation zu Azure Storage über virtuelle Netzwerke.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Wie häufig wird ein absturzkonsistenter Wiederherstellungspunkt generiert?

Site Recovery generiert alle 5 Minuten einen absturzkonsistenten Wiederherstellungspunkt.

## <a name="component-upgrade"></a>Upgrade von Komponenten

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Meine Version des Mobility Services-Agent oder des Konfigurationsservers ist alt, und beim Upgrade sind Fehler aufgetreten. Wie gehe ich vor?

Für Site Recovery gilt das Supportmodell N-4. [Hier](./service-updates-how-to.md#support-statement-for-azure-site-recovery) finden Sie Informationen zum Upgrade von sehr alten Versionen.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Wo finde ich die Versionshinweise und Updaterollups für Azure Site Recovery?

[Hier finden Sie Informationen zu neuen Updates](site-recovery-whats-new.md) und [hier Informationen zum Rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Wo finde ich Informationen zum Upgrade für die Notfallwiederherstellung in Azure?

Informationen zum Durchführen von Upgrades finden Sie [hier](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Muss ich die Quellcomputer für jedes Upgrade neu starten?

Ein Neustart wird zwar empfohlen, ist jedoch nicht für jedes Upgrade erforderlich. [Weitere Informationen](./service-updates-how-to.md#reboot-after-mobility-service-upgrade)

## <a name="configuration-server"></a>Konfigurationsserver

### <a name="what-does-the-configuration-server-do"></a>Was macht der Konfigurationsserver?

Der Konfigurationsserver führt die lokalen Site Recovery-Komponenten aus, einschließlich:

- des Konfigurationsservers selbst. Der Konfigurationsserver koordiniert die Kommunikation zwischen den lokalen Komponenten und Azure und verwaltet die Datenreplikation.
- Der Prozessserver, der als Replikationsgateway fungiert. Dieser Server:
    1. Empfängt Replikationsdaten.
    2. Er Optimiert die Daten mit Caching, Komprimierung und Verschlüsselung.
    3. Sendet die Daten an Azure Storage.
  Der Prozessserver führt auch eine Pushinstallation des Mobility Service auf virtuellen Computern sowie eine automatische Ermittlung auf lokalen virtuellen VMware-Computern durch.
- Der Masterzielserver, der die Replikationsdaten während des Failbacks von Azure verarbeitet.

[Erfahren Sie mehr](vmware-azure-architecture.md) zu den Komponenten und Prozessen von Konfigurationsservern.

### <a name="where-do-i-set-up-the-configuration-server"></a>Wo richte ich den Konfigurationsserver ein?

Für den Konfigurationsserver benötigen Sie eine einzelne hoch verfügbare lokale VMware-VM. Installieren Sie für die Notfallwiederherstellung von physischen Servern den Konfigurationsserver auf einem physischen Computer.

### <a name="what-do-i-need-for-the-configuration-server"></a>Was benötige ich für den Konfigurationsserver?

Überprüfen Sie die [Voraussetzungen](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kann ich den Konfigurationsserver manuell einrichten, statt eine Vorlage zu verwenden?

Es empfiehlt sich, [den virtuellen Computer des Konfigurationsservers](vmware-azure-deploy-configuration-server.md) mit der neuesten Version der OVF-Vorlage (Open Virtualization Format) zu erstellen. Wenn Sie die Vorlage nicht verwenden können (weil Sie z.B. keinen Zugriff auf den VMware-Server haben), [laden](physical-azure-set-up-source.md) Sie die Setupdatei über das Portal herunter, und richten Sie den Konfigurationsserver ein.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kann ein Konfigurationsserver zu mehreren Regionen replizieren?

Nein. Um zu mehreren Regionen zu replizieren, benötigen Sie in jeder Region einen Konfigurationsserver.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kann ich einen Konfigurationsserver in Azure hosten?

Dies ist zwar möglich, aber die Azure-VM, die den Konfigurationsserver ausführt, müsste mit Ihrer lokalen VMware-Infrastruktur und den virtuellen Computern kommunizieren. Diese Kommunikation führt zu höherer Latenz und wirkt sich auf die laufende Replikation aus.

### <a name="how-do-i-update-the-configuration-server"></a>Wie aktualisiere ich den Konfigurationsserver?

[Hier](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) finden Sie Informationen zum Aktualisieren des Konfigurationsservers.

- Die neuesten Updateinformationen finden Sie auf der [Seite mit den Azure-Updates](https://azure.microsoft.com/updates/?product=site-recovery).
- Sie können die neueste Version über das Portal herunterladen. Alternativ können Sie die neueste Version des Konfigurationsservers direkt aus dem [Microsoft Download Center](https://aka.ms/asrconfigurationserver) herunterladen.
- Wenn zwischen Ihrer Version und der aktuellen Version mehr als vier Versionen liegen, nutzen Sie den [Supporthinweis](./service-updates-how-to.md#support-statement-for-azure-site-recovery) als Upgradeanleitung.

### <a name="should-i-back-up-the-configuration-server"></a>Sollte ich den Konfigurationsserver sichern?

Es wird empfohlen, regelmäßige geplante Sicherungen des Konfigurationsservers durchzuführen.

- Für ein erfolgreiches Failback muss der betreffende virtuelle Computer in der Konfigurationsserverdatenbank vorhanden sein.
- Der Konfigurationsserver muss ausgeführt werden und verbunden sein.
- Weitere Informationen zu allgemeinen Aufgaben für die Verwaltung des Konfigurationsservers finden Sie [hier](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Kann ich beim Einrichten des Konfigurationsservers MySQL herunterladen und manuell installieren?

Ja. Laden Sie MySQL herunter, und speichern Sie die Daten im Ordner „C:\Temp\ASRSetup“. Führen Sie die Installation anschließend manuell durch. Wenn Sie die Konfigurationsserver-VM einrichten und die Bedingungen akzeptieren, wird MySQL unter **Herunterladen und installieren** als **Bereits installiert** aufgeführt.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kann ich das Herunterladen von MySQL vermeiden und Site Recovery die Installation überlassen?

Ja. Laden Sie das MySQL-Installationsprogramm herunter, und speichern Sie es im Ordner „C:\Temp\ASRSetup“. Akzeptieren Sie beim Einrichten der Konfigurationsserver-VM die Bedingungen, und wählen Sie **Herunterladen und installieren** aus. Im Portal wird dann das von Ihnen hinzugefügte Installationsprogramm verwendet, um MySQL zu installieren.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kann ich die Konfigurationsserver-VM für andere Zwecke nutzen?

Nein. Verwenden Sie die VM nur für den Konfigurationsserver.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kann ich einen Konfigurationsserver klonen und für die Orchestrierung verwenden?

Nein. Richten Sie einen neuen Konfigurationsserver ein, um Registrierungsprobleme zu vermeiden.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kann ich den Tresor ändern, in dem der Konfigurationsserver registriert ist?

Nein. Nachdem dem Konfigurationsserver ein Tresor zugeordnet wurde, sind daran keine Änderungen mehr möglich. [Hier finden Sie Informationen](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) zum Registrieren eines Konfigurationsservers mit einem anderen Tresor.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kann ich denselben Konfigurationsserver für die Notfallwiederherstellung von VMware-VMs und von physischen Servern nutzen?

Ja. Beachten Sie aber, dass für den physischen Computer nur ein Failback auf eine VMware-VM durchgeführt werden kann.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Wo kann ich die Passphrase für den Konfigurationsserver herunterladen?

[Hier](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) finden Sie Informationen zum Herunterladen der Passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Wo kann ich die Tresorregistrierungsschlüssel herunterladen?

Wählen Sie im Recovery Services-Tresor unter **Site Recovery-Infrastruktur** > **Verwalten** die Option **Konfigurationsserver** aus. Wählen Sie dann unter **Server** die Option **Registrierungsschlüssel herunterladen** aus, um die Datei mit den Tresoranmeldeinformationen herunterzuladen.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Kann ein einzelner Konfigurationsserver zum Schutz mehrerer vCenter-Instanzen verwendet werden?

Ja, ein einzelner Konfigurationsserver kann virtuelle Computer über mehrere vCenter-Instanzen hinweg schützen.  Es gibt keine Begrenzung, wie viele vCenter-Instanzen dem Konfigurationsserver hinzugefügt werden können, jedoch gelten die Begrenzungen für die Anzahl der virtuellen Computer, die ein einzelner Konfigurationsserver schützen kann.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Kann ein einzelner Konfigurationsserver mehrere Cluster in vCenter schützen?

Ja, Azure Site Recovery kann virtuelle Computer über verschiedene Cluster hinweg schützen.

## <a name="process-server"></a>Prozessserver

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Warum kann ich nicht den Prozessserver auswählen, wenn ich die Replikation aktiviere?

Updates in den Versionen 9.24 und höher zeigen jetzt die [Integrität des Prozessservers an, wenn Sie die Replikation aktivieren](vmware-azure-enable-replication.md#enable-replication). Dieses Feature trägt dazu bei, die Drosselung des Prozessservers zu vermeiden und die Verwendung fehlerhafter Prozessserver zu minimieren.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Wie aktualisiere ich den Prozessserver auf Version 9.24 oder höher, um genaue Integritätsinformationen zu erhalten?

Ab [Version 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups) wurden weitere Warnungen hinzugefügt, um die Integrität des Prozessservers anzuzeigen. [Aktualisieren Sie Ihre Site Recovery-Komponenten auf Version 9.24 oder neuer,](service-updates-how-to.md#links-to-currently-supported-update-rollups) damit alle Warnungen generiert werden.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Wie kann ich die Hochverfügbarkeit des Prozessservers sicherstellen?

Da das Design bietet durch die Möglichkeit, mehrere Prozessserver zu konfigurieren, die Flexibilität, geschützte Computer von einem fehlerhaften Prozessserver auf einen funktionierenden Prozessserver zu verschieben. Das Verschieben eines Computers von einem Prozessserver auf einen anderen muss explizit/manuell über die hier beschriebenen Schritte initiiert werden: [Verschieben von VMS zwischen Prozessservern](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Failover und Failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kann ich den lokalen Prozessserver für Failbacks verwenden?

Es wird dringend empfohlen, einen Prozessserver für Failbacks in Azure zu erstellen, um Wartezeiten bei der Datenübertragung zu vermeiden. Es ist außerdem wichtig, dass Sie den in Azure erstellten Prozessserver für Failbacks verwenden, wenn Sie das Quellnetzwerk der virtuellen Computer vom mit Azure verbundenen Netzwerk auf dem Konfigurationsserver getrennt haben.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kann ich die IP-Adresse beim Failover behalten?

Ja, Sie können die IP-Adresse beim Failover behalten. Stellen Sie sicher, dass Sie die Ziel-IP-Adresse vor dem Failover in den Einstellungen für **Compute und Netzwerk** für den virtuellen Computer angeben. Fahren Sie außerdem die Computer zum Zeitpunkt des Failovers herunter, um Konflikte der IP-Adressen beim Failback zu vermeiden.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kann ich die Größe oder den Typ der Ziel-VM vor dem Failover ändern?

Ja, Sie können den Typ oder die Größe des virtuellen Computers jederzeit vor dem Failover ändern. Verwenden Sie im Portal die **Compute und Netzwerk**-Einstellungen für den replizierten virtuellen Computer.

### <a name="how-far-back-can-i-recover"></a>Wie weit kann ich bei der Wiederherstellung zurückgehen?

Für VMware zu Azure kann der älteste Wiederherstellungspunkt, den Sie verwenden können, 72 Stunden zurückliegen.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Wie greife ich nach einem Failover auf virtuelle Azure-Computer zu?

Nach einem Failover können Sie über eine sichere Internetverbindung, eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute auf die Azure-VMs zugreifen. Um eine Verbindung herzustellen, müssen Sie mehrere Dinge vorbereiten. [Weitere Informationen](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Sind Daten, mit denen ein Failover ausgeführt wurde, stabil?

Azure ist auf Resilienz ausgelegt. Site Recovery ist für ein Failover zu einem sekundären Azure-Rechenzentrum gemäß der Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) für Azure konzipiert. Wenn ein Failover auftritt, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?

Ein [Failover](site-recovery-failover.md) erfolgt nicht automatisch. Sie starten ein Failover mit einer einzelnen Auswahl im Portal, oder Sie können [PowerShell](/powershell/module/az.recoveryservices) verwenden, um ein Failover auszulösen.

### <a name="can-i-fail-back-to-a-different-location"></a>Kann ich ein Failback zu einem anderen Speicherort ausführen?

Ja. Wenn Sie ein Failover zu Azure ausgeführt haben, können Sie ein Failback zu einem anderen Speicherort ausführen, wenn der ursprüngliche nicht verfügbar ist. [Weitere Informationen](concepts-types-of-failback.md#alternate-location-recovery-alr)

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Warum benötige ich ein VPN oder ExpressRoute mit privatem Peering für ein Failback?

Wenn Sie ein Failback von Azure ausführen, werden Daten aus Azure auf Ihren lokalen virtuellen Computer zurückkopiert und privater Zugriff ist erforderlich.


## <a name="automation-and-scripting"></a>Automatisierung und Skripterstellung

### <a name="can-i-set-up-replication-with-scripting"></a>Kann ich die Replikation mit Skripts einrichten?

Ja. Sie können Site Recovery-Workflows mithilfe von REST-API, PowerShell oder Azure SDK automatisieren. [Weitere Informationen](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>Leistung und Kapazität

### <a name="can-i-throttle-replication-bandwidth"></a>Kann ich die Replikationsbandbreite drosseln?

Ja. [Weitere Informationen](site-recovery-plan-capacity-vmware.md)

## <a name="next-steps"></a>Nächste Schritte

- [Informieren](vmware-physical-azure-support-matrix.md) Sie sich über die Supportanforderungen.
- [Richten Sie](vmware-azure-tutorial.md) die VMware-zu-Azure-Replikation ein.