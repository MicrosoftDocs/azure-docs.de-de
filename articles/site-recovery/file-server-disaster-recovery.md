---
title: Schützen eines Dateiservers mit Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie einen Dateiserver mit Azure Site Recovery schützen.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 51754021f5029a751be90bfc4194ac6347c1e278
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772154"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Schützen eines Dateiservers mit Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategien für Geschäftskontinuität und Notfallwiederherstellung, indem die Verfügbarkeit Ihrer Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Site Recovery verwaltet und orchestriert die Notfallwiederherstellung von lokalen Computern sowie virtuellen Azure-Computern (VMs). Die Notfallwiederherstellung beinhaltet die Replikation, das Failover und die Wiederherstellung von verschiedenen Workloads.

Dieser Artikel beschreibt, wie Sie einen Dateiserver mithilfe von Azure Site Recovery schützen, und enthält weitere Empfehlungen für verschiedene Umgebungen. 

- [Empfehlung zur Notfallwiederherstellung für virtuelle Azure IaaS-Computer](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replizieren eines lokalen Dateiservers mit Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Dateiserverarchitektur
Das Ziel eines offenen verteilten Dateifreigabesystems ist die Bereitstellung einer Umgebung, in der eine Gruppe von geografisch verteilten Benutzern zusammenarbeiten kann, um Dateien auf effiziente Weise zu bearbeiten – mit der Gewissheit, dass ihre Anforderungen in Bezug auf die Integrität durchgesetzt werden. In einem typischen lokalen Dateiserver-Ökosystem, das eine große Anzahl von gleichzeitigen Benutzern und Inhaltselementen unterstützt, wird für die Planung der Replikation und Bandbreitendrosselung die DFS-Replikation (Distributed File System Replication, DFSR) verwendet. 

DFSR nutzt einen als Remotedifferenzialkomprimierung (Remote Differential Compression, RDC) bezeichneten Komprimierungsalgorithmus, mit dem Dateien in einem Netzwerk mit begrenzter Bandbreite effizient aktualisiert werden können. Er erkennt Einfügungen, Entfernungen und Neuanordnungen von Daten in Dateien. DFSR wird aktiviert, um beim Aktualisieren von Dateien nur die geänderten Dateiblöcke zu replizieren. Es gibt auch Dateiserverumgebungen, in denen zur Vorbereitung auf Notfälle tägliche Sicherungen außerhalb der Spitzenzeiten erstellt werden. DFSR ist nicht implementiert.

Das folgende Diagramm zeigt die Dateiserverumgebung mit DFSR-Implementierung.
                
![DFSR-Architektur](media/site-recovery-file-server/dfsr-architecture.JPG)

Im obigen Diagramm sind mehrere als Mitglieder bezeichnete Dateiserver aktiv an der Replikation von Dateien in einer Replikationsgruppe beteiligt. Der Inhalt des replizierten Ordners ist für alle Clients verfügbar, die Anforderungen an eines der Mitglieder senden. Dies gilt auch dann, wenn ein Mitglied offline geht.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Empfehlungen zur Notfallwiederherstellung für Dateiserver

* **Replizieren eines Dateiservers mit Site Recovery**: Dateiserver können mithilfe von Site Recovery in Azure repliziert werden. Wenn der Zugriff auf einen oder mehrere lokale Dateiserver nicht möglich ist, können die Wiederherstellungs-VMs in Azure aktiviert werden. Die VMs können dann lokal Anforderungen von Clients verarbeiten, sofern Site-to-Site-VPN-Konnektivität besteht und Active Directory in Azure konfiguriert ist. Sie können diese Methode für eine mit DFSR konfigurierte Umgebung oder eine einfache Dateiserverumgebung ohne DFSR verwenden. 

* **Erweitern von DFSR auf eine Azure-IaaS-VM**: In einer geclusterten Dateiserverumgebung, in der DFSR implementiert ist, können Sie die lokale DFSR-Implementierung auf Azure ausweiten. Eine Azure-VM kann dann die Dateiserverrolle übernehmen. 

    * Nach dem Behandeln der Abhängigkeiten von der Site-to-Site-VPN-Konnektivität und Active Directory sowie dem Implementieren von DFSR können Clients im Fall der Nichtverfügbarkeit von lokalen Dateiservern eine Verbindung mit der Azure-VM herstellen, die die Anforderungen verarbeitet.

    * Sie können diesen Ansatz verwenden, wenn die Konfigurationen Ihrer VMs nicht von Site Recovery unterstützt werden. Ein Beispiel ist ein freigegebener Clusterdatenträger, der manchmal in Dateiserverumgebungen gemeinsam verwendet wird. DFSR funktioniert auch gut in Umgebungen mit geringer Bandbreite und mittlerer Änderungsrate. Hierbei müssen Sie die zusätzlichen Kosten der Nutzung einer Azure-VM, die immer ausgeführt wird, berücksichtigen. 

* **Verwenden der Azure-Dateisynchronisierung zum Replizieren Ihrer Dateien**: Wenn Sie die Nutzung der Cloud planen oder bereits eine Azure-VM einsetzen, können Sie die Azure-Dateisynchronisierung verwenden. Die Azure-Dateisynchronisierung bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)-Protokoll (SMB) – ein Protokoll nach Industriestandard – zugegriffen werden kann. Azure-Dateifreigaben können dann gleichzeitig über Cloudbereitstellungen oder lokale Bereitstellungen von Windows, Linux und macOS eingebunden werden. 

Das folgende Diagramm hilft Ihnen beim Festlegen der Strategie für Ihre Dateiserverumgebung.

![Entscheidungsstruktur](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Bei Entscheidungen bezüglich der Notfallwiederherstellung in Azure zu berücksichtigende Faktoren

|Environment  |Empfehlung  |Zu berücksichtigende Punkte |
|---------|---------|---------|
|Dateiserverumgebung mit oder ohne DFSR|   [Verwenden von Site Recovery für die Replikation](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery unterstützt weder freigegebene Datenträgercluster noch Network Attached Storage (NAS). Falls diese Konfigurationen in Ihrer Umgebung genutzt werden, können Sie einen der anderen Ansätze verwenden. <br> Site Recovery unterstützt SMB 3.0 nicht. Die replizierte VM enthält die an Dateien vorgenommenen Änderungen nur, wenn diese am ursprünglichen Speicherort der Dateien aktualisiert werden.
|Dateiserverumgebung mit DFSR     |  [Erweitern von DFSR auf eine Azure-IaaS-VM](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR funktioniert gut in Umgebungen mit begrenzter Bandbreite. Dieser Ansatz erfordert eine Azure-VM, die immer ausgeführt wird. Sie müssen die Kosten für die VM in Ihrer Planung berücksichtigen.         |
|Azure-IaaS-VM     |     Dateisynchronisierung    |     Wenn Sie die Dateisynchronisierung in einem Notfallwiederherstellungsszenario verwenden, sind während eines Failovers manuelle Aktionen erforderlich, um sicherzustellen, dass die Dateifreigaben auf transparente Weise für die Clientcomputer zugänglich sind. Die Dateisynchronisierung erfordert, dass Port 445 für den Clientcomputer geöffnet ist.     |


### <a name="site-recovery-support"></a>Site Recovery-Unterstützung
Da die Site Recovery-Replikation anwendungsunabhängig ist, gelten diese Empfehlungen voraussichtlich auch für die folgenden Szenarien.

| `Source`    |Sekundärer Standort    |Azure
|---------|---------|---------|
|Azure| -|Ja|
|Hyper-V|   Ja |Ja
|VMware |Ja|   Ja
|Physischer Server|   Ja |Ja
 

> [!IMPORTANT]
> Bevor Sie mit einem der folgenden drei Ansätze fortfahren, sollten Sie sicherstellen, dass die folgenden Abhängigkeiten geregelt sind.

**Site-to-Site-Konnektivität**: Es muss eine direkte Verbindung zwischen dem lokalen Standort und dem Azure-Netzwerk hergestellt werden, um die Kommunikation zwischen Servern zu ermöglichen. Verwenden Sie eine sichere Site-to-Site-VPN-Verbindung mit einem virtuellen Azure-Netzwerk, das als Notfallwiederherstellungsstandort dient. Weitere Informationen finden Sie im Artikel zum [Herstellen einer Site-to-Site-VPN-Verbindung zwischen einem lokalen Standort und einem virtuellen Azure-Netzwerk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: DFSR ist von Active Directory abhängig. Dies bedeutet, dass die Active Directory-Gesamtstruktur mit lokalen Domänencontrollern auf den Notfallwiederherstellungsstandort in Azure ausgedehnt wird. Selbst wenn Sie DSFR nicht verwenden, müssen Sie diese Schritte ausführen, falls den entsprechenden Benutzern Zugriff gewährt oder der Zugriff dieser Benutzer überprüft werden muss. Weitere Informationen finden Sie unter [Verwenden von Azure Site Recovery zum Schützen von Active Directory und DNS](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Empfehlung zur Notfallwiederherstellung für virtuelle Azure IaaS-Computer

Wenn Sie die Notfallwiederherstellung von Dateiservern konfigurieren und verwalten, die auf Azure-IaaS-VMs gehostet werden, können Sie zwischen zwei Optionen wählen – je nachdem, ob Sie die Umstellung auf [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) durchführen möchten:

* [Verwenden der Dateisynchronisierung](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Verwenden von Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Verwenden der Dateisynchronisierung zum Replizieren von Dateien, die auf einer IaaS-VM gehostet werden

Azure Files kann herkömmliche lokale Dateiserver oder NAS-Geräte vollständig ersetzen bzw. erweitern. Azure-Dateifreigaben können auch per Dateisynchronisierung auf Windows-Server repliziert werden (lokal oder in der Cloud), um für die Daten am Ort ihrer Verwendung eine hohe Leistung und verteilte Zwischenspeicherung zu erzielen. In den folgenden Schritten wird die Empfehlung zur Notfallwiederherstellung für Azure-VMs beschrieben, die die gleichen Funktionen wie herkömmliche Dateiserver ausführen:
* Schützen Sie Computer mit Site Recovery. Führen Sie die Schritte unter [Replizieren einer Azure-VM in eine andere Azure-Region](azure-to-azure-quickstart.md) aus.
* Verwenden Sie die Dateisynchronisierung, um Dateien von der VM, die als Dateiserver dient, in die Cloud zu replizieren.
* Verwenden Sie das Feature [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) von Site Recovery, um Skripts zum [Einbinden der Azure-Dateifreigabe](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) und zum Zugreifen auf die Freigabe auf Ihrer VM hinzuzufügen.

In den folgenden Schritten wird die Verwendung der Dateisynchronisierung kurz beschrieben:

1. Führen Sie das [Erstellen eines Speicherkontos in Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) durch. Wenn Sie georedundanten Speicher mit Lesezugriff für Ihre Speicherkonten gewählt haben, verfügen Sie im Notfall über Lesezugriff auf Ihre Daten in der sekundären Region. Weitere Informationen finden Sie unter [Notfallwiederherstellung und erzwungenes Failover (Vorschau) in Azure Storage](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Erstellen Sie eine Dateifreigabe.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)
3. [Starten Sie die Dateisynchronisierung](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) auf Ihrem Azure-Dateiserver.
4. Erstellen Sie eine Synchronisierungsgruppe. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Eine Synchronisierungsgruppe muss mindestens einen Cloudendpunkt enthalten, der eine Azure-Dateifreigabe darstellt. Eine Synchronisierungsgruppe muss auch einen Serverendpunkt enthalten, der einen Pfad auf einem Windows-Server darstellt.
5. Ihre Dateien werden jetzt zwischen der Azure-Dateifreigabe und dem lokalen Server synchronisiert.
6. Führen Sie bei einem Notfall in Ihrer lokalen Umgebung ein Failover mit einem [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) durch. Fügen Sie das Skript zum [Einbinden der Azure-Dateifreigabe](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) und Zugreifen auf die Freigabe in der VM hinzu.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replizieren einer IaaS-Dateiserver-VM mit Site Recovery

Wenn Sie lokale Clients haben, die auf die IaaS-Dateiserver-VM zugreifen, führen Sie alle der folgenden Schritte aus. Fahren Sie andernfalls mit Schritt 3 fort.

1. Richten Sie eine Site-to-Site-VPN-Verbindung zwischen dem lokalen Standort und dem Azure-Netzwerk ein.
2. Erweitern Sie die lokale Active Directory-Instanz.
3. Führen Sie das [Einrichten der Notfallwiederherstellung](azure-to-azure-tutorial-enable-replication.md) für den IaaS-Dateiservercomputer in eine sekundäre Region durch.


Weitere Informationen zur Notfallwiederherstellung in einer sekundären Region finden Sie in [diesem Artikel](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replizieren eines lokalen Dateiservers mit Site Recovery

In den folgenden Schritten wird die Replikation für eine VMware-VM beschrieben. Die Schritte zum Replizieren einer Hyper-V-VM finden Sie in [diesem Tutorial](tutorial-hyper-v-to-azure.md).

1. Führen Sie das [Vorbereiten von Azure-Ressourcen](tutorial-prepare-azure.md) für die Replikation von lokalen Computern durch.
2. Richten Sie eine Site-to-Site-VPN-Verbindung zwischen dem lokalen Standort und dem Azure-Netzwerk ein. 
3. Erweitern Sie die lokale Active Directory-Instanz.
4. [Bereiten Sie lokale VMware-Server vor](tutorial-prepare-on-premises-vmware.md).
5. Führen Sie das [Einrichten der Notfallwiederherstellung](tutorial-vmware-to-azure.md) in Azure für lokale VMs durch.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Erweitern von DFSR auf eine Azure-IaaS-VM

1. Richten Sie eine Site-to-Site-VPN-Verbindung zwischen dem lokalen Standort und dem Azure-Netzwerk ein. 
2. Erweitern Sie die lokale Active Directory-Instanz.
3. [Erstellen Sie im virtuellen Azure-Netzwerk eine Dateiserver-VM, und stellen Sie sie bereit](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
Stellen Sie sicher, dass die VM dem virtuellen Azure-Netzwerk hinzugefügt wird, das über gegenseitige Konnektivität mit der lokalen Umgebung verfügt. 
4. Installieren und [konfigurieren Sie DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) unter Windows Server.
5. [Implementieren Sie einen DFS-Namespace](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Wenn der DFS-Namespace implementiert ist, kann das Failover von freigegebenen Ordnern von Produktions- zu Notfallwiederherstellungsstandorten durchgeführt werden, indem die Ordnerziele des DFS-Namespaces aktualisiert werden. Nachdem diese Änderungen des DFS-Namespaces über Active Directory repliziert wurden, werden Benutzer auf transparente Weise mit den entsprechenden Ordnerzielen verbunden.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Verwenden der Dateisynchronisierung zum Replizieren Ihrer lokalen Dateien
Sie können die Dateisynchronisierung verwenden, um Dateien in die Cloud zu replizieren. Sollte Ihr lokaler Dateiserver aufgrund eines Notfalls nicht verfügbar sein, können Sie die gewünschten Dateispeicherorte aus der Cloud einbinden und die Verarbeitung von Anforderungen von Clientcomputern fortsetzen.
So integrieren Sie die Dateisynchronisierung in Site Recovery:

* Schützen Sie die Dateiservercomputer mit Site Recovery. Führen Sie die Schritte in [diesem Tutorial](tutorial-vmware-to-azure.md) aus.
* Verwenden Sie die Dateisynchronisierung, um Dateien von dem Computer, der als Dateiserver dient, in die Cloud zu replizieren.
* Nutzen Sie das Feature „Wiederherstellungsplan“ von Site Recovery zum Hinzufügen von Skripts, um die Azure-Dateifreigabe auf der Dateiserver-VM in Azure einzubinden, für die das Failover durchgeführt wurde.

Gehen Sie zur Verwendung der Dateisynchronisierung wie folgt vor:

1. Führen Sie das [Erstellen eines Speicherkontos in Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) durch. Wenn Sie georedundanten Speicher mit Lesezugriff (empfohlen) für Ihre Speicherkonten gewählt haben, verfügen Sie im Notfall über Lesezugriff auf Ihre Daten in der sekundären Region. Weitere Informationen finden Sie unter [Notfallwiederherstellung und erzwungenes Failover (Vorschau) in Azure Storage](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Erstellen Sie eine Dateifreigabe.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)
3. [Stellen Sie die Dateisynchronisierung auf Ihrem lokalen Dateiserver bereit.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide)
4. Erstellen Sie eine Synchronisierungsgruppe. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Eine Synchronisierungsgruppe muss mindestens einen Cloudendpunkt enthalten, der eine Azure-Dateifreigabe darstellt. Die Synchronisierungsgruppe muss auch einen Serverendpunkt enthalten, der einen Pfad auf dem lokalen Windows-Server darstellt.
5. Ihre Dateien werden jetzt zwischen der Azure-Dateifreigabe und dem lokalen Server synchronisiert.
6. Führen Sie bei einem Notfall in Ihrer lokalen Umgebung ein Failover mit einem [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) durch. Fügen Sie das Skript zum Einbinden der Azure-Dateifreigabe und Zugreifen auf die Freigabe in der VM hinzu.

> [!NOTE]
> Stellen Sie sicher, dass der Port 445 geöffnet ist. Azure Files verwendet das SMB-Protokoll. SMB kommuniziert über den TCP-Port 445. Vergewissern Sie sich, dass Ihre Firewall den TCP-Port 445 nicht für einen Clientcomputer blockiert.


## <a name="do-a-test-failover"></a>Ausführen eines Testfailovers

1. Wechseln Sie zum Azure-Portal, und wählen Sie Ihren Recovery Services-Tresor aus.
2. Wählen Sie den Wiederherstellungsplan aus, der für die Dateiserverumgebung erstellt wurde.
3. Wählen Sie **Testfailover** aus.
4. Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um das Testfailover zu starten.
5. Führen Sie Ihre Prüfungen durch, wenn die sekundäre Umgebung aktiv ist.
6. Klicken Sie nach Abschluss der Prüfungen auf **Cleanup test failover** (Testfailover bereinigen). Die Testfailoverumgebung ist nun bereinigt.

Weitere Informationen zum Ausführen eines Testfailovers finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

Eine Anleitung zum Ausführen eines Testfailovers für Active Directory und DNS finden Sie in den [Überlegungen zum Testfailover für Active Directory und DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Ausführen eines Failovers

1. Wechseln Sie zum Azure-Portal, und wählen Sie Ihren Recovery Services-Tresor aus.
2. Wählen Sie den Wiederherstellungsplan aus, der für die Dateiserverumgebung erstellt wurde.
3. Wählen Sie **Failover** aus.
4. Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen zum Ausführen eines Failovers finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).
