---
title: Zuweisen eines neuen Hosts für eine Contoso-App durch Migration zu Azure-VMs und zur SQL Server Always On-Verfügbarkeitsgruppe | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen darüber, wie Contoso einer lokalen App einen neuen Host zuweist, indem diese zu Azure-VMs und zur SQL Server Always On-Verfügbarkeitsgruppe migriert wird
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 8573259a0e6667b37052a7be0e150727e52cdda3
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950061"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso-Migration: Zuweisen eines neuen Hosts für eine lokale App zu Azure-VMs und zur SQL Server Always On-Verfügbarkeitsgruppe

In diesem Artikel wird erläutert, wie Contoso der SmartHotel360-App in Azure einen neuen Host zuweist. Contoso migriert den virtuellen Front-End-Computer der App zu einem virtuellen Azure-Computer und die App-Datenbank zu einem virtuellen Azure SQL Server-Computer, die in einem Windows Server-Failovercluster mit SQL Server AlwaysOn-Verfügbarkeitsgruppen ausgeführt werden.

Dieses Dokument stammt aus einer Reihe von Artikeln, die zeigen, wie das fiktive Unternehmen Contoso lokale Ressourcen in die Microsoft Azure-Cloud migriert. Die Reihe enthält Hintergrundinformationen und Szenarios, die die Einrichtung einer Migrationsinfrastruktur veranschaulichen, die Eignung der lokalen Ressourcen für die Migration bewerten und verschiedene Migrationstypen durchführen. Die Komplexität der Szenarien erhöht sich hierbei immer mehr. Wir fügen im Laufe der Zeit weitere Artikel hinzu.

**Artikel** | **Details** | **Status**
--- | --- | ---
[Artikel 1: Übersicht](contoso-migration-overview.md) | Dies ist eine Übersicht über die Artikelreihe, die Migrationsstrategie von Contoso und die in der Reihe verwendeten Beispiel-Apps. | Verfügbar
[Artikel 2: Bereitstellen einer Azure-Infrastruktur](contoso-migration-infrastructure.md) | Contoso bereitet seine lokale Infrastruktur und die Azure-Infrastruktur für die Migration vor. Für alle Migrationsartikel der Reihe wird dieselbe Infrastruktur verwendet. | Verfügbar
[Artikel 3: Bewerten der lokalen Ressourcen für die Migration zu Azure](contoso-migration-assessment.md)  | Contoso führt eine Bewertung seiner lokalen App SmartHotel360 durch, die in VMware ausgeführt wird. Contoso bewertet virtuelle Computer der App mit dem Azure Migrate-Dienst und die SQL Server-Datenbank der App mit dem Datenmigrations-Assistenten. | Verfügbar
[Artikel 4: Rehosten einer App auf einer Azure-VM und einer verwalteten Azure SQL-Datenbank-Instanz](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso führt für seine lokale App SmartHotel360 eine Migration per Lift & Shift zu Azure aus. Contoso migriert den virtuellen Front-End-Computer der App mithilfe von [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migriert die App-Datenbank mit dem [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) zu einer verwalteten Azure SQL-Datenbank-Instanz. | Verfügbar   
[Artikel 5: Zuweisen eines neuen Hosts für eine App auf Azure-VMs](contoso-migration-rehost-vm.md) | Contoso migriert die VMs der App SmartHotel360 mithilfe des Site Recovery-Diensts zu Azure-VMs. | Verfügbar
Artikel 6: Zuweisen eines neuen Hosts für eine App auf Azure-VMs und in einer SQL Server Always On-Verfügbarkeitsgruppe | Contoso migriert die App SmartHotel360. Contoso verwendet Site Recovery, um die App-VMs zu migrieren. Der Database Migration Service wird verwendet, um die App-Datenbank zu einem SQL Server-Cluster zu migrieren, der mit einer Always On-Verfügbarkeitsgruppe geschützt ist. | Dieser Artikel
[Artikel 7: Zuweisen eines neuen Hosts für eine Linux-App auf Azure-VMs](contoso-migration-rehost-linux-vm.md) | Contoso führt mithilfe von Azure Site Recovery per Lift & Shift eine Migration der Linux-App „osTicket“ zu virtuellen Azure-Computern durch. | Verfügbar
[Artikel 8: Zuweisen eines neuen Hosts für eine Linux-App auf Azure-VMs und Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migriert die Linux-App „osTicket“ mithilfe von Azure Site Recovery zu virtuellen Azure-Computern und die App-Datenbank mithilfe von MySQL Workbench zu einer Azure MySQL Server-Instanz. | Verfügbar
[Artikel 9: Umgestalten einer App in Azure-Web-Apps und Azure SQL-Datenbank](contoso-migration-refactor-web-app-sql.md) | Contoso migriert die App SmartHotel360 zu einer Azure-Web-App und die App-Datenbank mithilfe des Datenbankmigrations-Assistenten zu einer Azure SQL Server-Instanz. | Verfügbar
[Artikel 10: Umgestalten einer Linux-App in Azure-Web-Apps und Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migriert die Linux-App „osTicket“ mithilfe von Azure Traffic Manager zu einer Azure-Web-App in mehreren Azure-Regionen. Zur Sicherstellung der Continuous Delivery erfolgt eine Integration in GitHub. Contoso migriert die App-Datenbank zu einer Azure Database for MySQL-Instanz. | Verfügbar 
[Artikel 11: Umgestalten einer TFS-Bereitstellung für Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso migriert die lokale Team Foundation Server-Bereitstellung zu Azure DevOps Services in Azure. | Verfügbar
[Artikel 12: Umstrukturieren einer App in Azure-Containern und Azure SQL-Datenbank](contoso-migration-rearchitect-container-sql.md) | Contoso migriert seine SmartHotel-App zu Azure. Anschließend wird die App-Webschicht zu einem Windows-Container umstrukturiert, der in Azure Service Fabric ausgeführt wird, und die Datenbank wird zu einer Azure SQL-Datenbank umstrukturiert. | Verfügbar
[Artikel 13: Neuerstellen einer App in Azure](contoso-migration-rebuild.md) | Contoso erstellt die SmartHotel-App mit verschiedenen Azure-Funktionen und -Diensten neu – z.B. Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services und Azure Cosmos DB. | Verfügbar
[Artikel 14: Skalieren einer Migration zu Azure](contoso-migration-scale.md) | Nachdem Contoso verschiedene Kombinationen für die Migration getestet hat, bereitet das Unternehmen sich jetzt auf eine vollständige Migration nach Azure in großem Umfang vor. | Verfügbar


In diesem Artikel wird erläutert, wie Contoso die auf virtuellen VMware-Computern ausgeführte zweistufige Windows .NET-App SmartHotel360 zu Azure migriert. Falls Sie diese App verwenden möchten: Sie wird als Open Source-App bereitgestellt und kann von [GitHub](https://github.com/Microsoft/SmartHotel360) heruntergeladen werden.

## <a name="business-drivers"></a>Business-Treiber

Das IT Leadership-Team hat eng mit den Geschäftspartnern zusammengearbeitet, um zu verstehen, was das Unternehmen mit dieser Migration erreichen möchte:

- **Unternehmenswachstum**: Contoso wächst, und daher geraten die lokalen Systeme und Infrastrukturen unter Druck.
- **Effizienzsteigerung**: Contoso muss unnötige Verfahren beseitigen und Prozesse für Entwickler und Benutzer optimieren.  Die IT-Abteilung muss schnell sein, weder Geld noch Zeit verschwenden, und Kundenanforderungen schneller bearbeiten.
- **Steigerung der Flexibilität:**  Die Contoso-IT-Abteilung muss schneller auf die Unternehmensanforderungen reagieren. Die IT-Experten müssen schneller reagieren als die Änderungen im Marketplace geschehen, um den Erfolg in einer globalen Wirtschaft zu garantieren.  IT darf nicht im Weg stehen oder zum Geschäftshindernis werden.
- **Skalierung**: Da das Unternehmen erfolgreich wächst, muss die Contoso-IT Systeme bereitstellen, die mit der gleichen Geschwindigkeit wachsen können.

## <a name="migration-goals"></a>Migrationsziele

Das Cloud-Team von Contoso hat sich Ziele für diese Migration gesetzt. Anhand dieser Ziele wird die beste Migrationsmethode bestimmt:

- Nach der Migration sollte die App in Azure die gleichen Leistungsmöglichkeiten aufweisen wie die lokale VMWare-Umgebung.  Die App bleibt lokal und in der Cloud gleichermaßen wichtig.
- Contoso möchte nicht in diese App investieren.  Die Investition ist wichtig für das Geschäft, in ihrer derzeitigen Form soll die App jedoch lediglich sicher in die Cloud verschoben werden.
- Bei der lokalen App-Datenbank gab es Verfügbarkeitsprobleme. Contoso möchte sie in Azure als Hochverfügbarkeitscluster mit Failoverfunktionen bereitstellen.
- Contoso möchte ein Upgrade von der aktuellen SQL Server 2008 R2-Plattform auf SQL Server 2017 durchführen.
- Contoso möchte für diese App keine Azure SQL-Datenbank verwenden und sucht nach Alternativen.


## <a name="solution-design"></a>Lösungsentwurf

Nachdem die Ziele und Anforderungen formuliert wurden, entwirft und überprüft Contoso eine Bereitstellungslösung und identifiziert den Migrationsprozess, einschließlich der für die Migration genutzten Azure-Dienste.

### <a name="current-architecture"></a>Aktuelle Architektur

- Die App ist auf zwei VMs aufgeteilt (WEBVM und SQLVM).
- Die VMs befinden sich auf dem VMware ESXi-Host **contosohost1.contoso.com** (Version 6.5)
- Die VMware-Umgebung wird von der vCenter Server 6.5-Software (**vcenter.contoso.com**) auf einer VM verwaltet.
- Contoso verfügt über ein lokales Rechenzentrum (contoso-datacenter) mit einem lokalen Domänencontroller (**contosodc1**).


### <a name="proposed-architecture"></a>Vorgeschlagene Architektur

Szenario:

- Contoso migriert das App-Front-End „WEBVM“ zu einem virtuellen Azure-IaaS-Computer.
    - Der virtuelle Front-End-Computer in Azure wird in der Ressourcengruppe „ContosoRG“ bereitgestellt (wird für Produktionsressourcen verwendet).
    -  Er wird sich im Azure-Produktionsnetzwerk (VNET-PROD-EUS2) in der primären Region „USA, Osten 2“ befinden.
- Die App-Datenbank wird zu einem virtuellen Azure SQL Server-Computer migriert.
    - Sie wird sich im Azure-Datenbanknetzwerk von Contoso (PROD-DB-EUS2) in der primären Region „USA, Osten 2“ befinden.
    - Sie wird in einem Windows Server-Failovercluster mit zwei Knoten platziert, der SQL Server AlwaysOn-Verfügbarkeitsgruppen verwendet.
    - In Azure werden die beiden virtuellen SQL Server-Computerknoten im Cluster in der Ressourcengruppe „ContosoRG“ bereitgestellt.
    - Die virtuellen Computerknoten werden sich im Azure-Produktionsnetzwerk (VNET-PROD-EUS2) in der primären Region „USA, Osten2“ befinden.
    - Auf VMs wird Windows Server 2016 mit der SQL Server 2017 Enterprise Edition ausgeführt. Contoso hat keine Lizenzen für dieses Betriebssystem. Daher verwendet das Unternehmen im Azure Marketplace ein Image, das die Lizenz als Gebühr für die Azure EA-Verpflichtung bereitstellt.
    - Abgesehen von eindeutigen Namen verwenden beide VMs die gleichen Einstellungen.
- Contoso stellt einen internen Lastenausgleich bereit, der den Cluster hinsichtlich des Datenverkehrs überwacht und diesen an den entsprechenden Clusterknoten weiterleitet.
    - Der interne Lastenausgleich wird in der Ressourcengruppe ContosoNetworkingRG bereitgestellt (wird für Netzwerkressourcen verwendet).
- Die lokalen VMs im Rechenzentrum von Contoso werden nach Abschluss der Migration außer Betrieb gesetzt.

![Szenarioarchitektur](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>Überlegungen zu Datenbanken

Im Rahmen des Lösungsentwurfs hat Contoso einen Featurevergleich zwischen Azure SQL-Datenbank und SQL Server durchgeführt. Hierbei haben die folgenden Überlegungen Contoso dabei unterstützt, sich für einen virtuellen Azure-IaaS-Computer zu entscheiden, auf dem SQL Server ausgeführt wird:

 - Der Einsatz eines virtuellen Azure-Computers mit SQL Server erscheint als optimale Lösung, wenn Contoso das Betriebssystem oder den Datenbankserver anpassen muss, oder wenn Drittanbieter-Apps auf demselben virtuellen Computer installiert und ausgeführt werden sollen.
 - Mithilfe des Datenmigrations-Assistenten kann Contoso problemlos eine Bewertung und eine Migration zu Azure SQL-Datenbank durchführen.
 

### <a name="solution-review"></a>Überprüfung der Lösung

Contoso wertet den vorgeschlagen Entwurf durch Erstellen einer Liste mit Vor- und Nachteilen aus.

**Aspekt** | **Details**
--- | ---
**Vorteile** | WEBVM wird ohne Änderungen nach Azure verlagert, was die Migration vereinfacht.<br/><br/> Die SQL Server-Schicht wird auf SQL Server 2017 und Windows Server 2016 ausgeführt. Dadurch wird das derzeitige Betriebssystem Windows Server 2008 R2 außer Betrieb genommen, und die Ausführung von SQL Server 2017 unterstützt die technischen Anforderungen und Ziele von Contoso. IT bietet 100%-ige Kompatibilität, während SQL Server 2008 R2 ausgemustert wird.<br/><br/> Contoso kann seine Investition in die Software Assurance mit dem Azure-Hybridvorteil nutzen.<br/><br/> Ein hoch verfügbare SQL Server-Bereitstellung in Azure bietet Fehlertoleranz, sodass die App-Datenschicht kein Single Point of Failover mehr ist.
**Nachteile** | Auf WEBVM wird Windows Server 2008 R2 ausgeführt. Das Betriebssystem wird für bestimmte Rollen von Azure unterstützt (Juli 2018). [Weitere Informationen](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)<br/><br/> Die Webschicht der App bleibt ein Single Point of Failover.</br><br/> Contoso muss die Webschicht weiterhin als virtuellen Azure-Computer unterstützen, anstatt auf einen verwalteten Dienst wie Azure App Service umzustellen.<br/><br/> Mit der gewählten Lösung muss Contoso weiterhin zwei virtuelle SQL Server-Computer verwalten, anstatt auf eine verwaltete Plattform wie die verwaltete Azure SQL-Datenbank-Instanz umzustellen. Zudem könnte Contoso mit Software Assurance seine vorhandenen Lizenzen gegen reduzierte Tarife der verwalteten Azure SQL-Datenbank-Instanz eintauschen.


### <a name="azure-services"></a>Azure-Dienste

**Service** | **Beschreibung** | **Kosten**
--- | --- | ---
[Datenbankmigrations-Assistent](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA wird auf dem lokalen SQL Server-Computer ausgeführt und migriert die Daten über ein Standort-zu-Standort-VPN zu Azure. | DMA ist ein kostenloses, herunterladbares Tool.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery orchestriert und verwaltet die Migration und Notfallwiederherstellung für Azure-VMs sowie lokale virtuelle Computer und physische Server.  | Während der Replikation in Azure fallen Gebühren für Azure Storage an.  Es werden Azure-VMs erstellt, und Gebühren fallen an, sobald ein Failover erfolgt. [Weitere Informationen](https://azure.microsoft.com/pricing/details/site-recovery/) zu Gebühren und Preisen.

 

## <a name="migration-process"></a>Migrationsprozess

Contoso-Administratoren migrieren die virtuellen Computer der App zu Azure.

- Es migriert die Front-End-VM mithilfe von Site Recovery zur Azure-VM:
    - Im ersten Schritt bereitet das Unternehmen Azure-Komponenten vor und richtet diese ein, und es bereitet die lokale VMware-Infrastruktur vor.
    - Wenn alle Vorbereitungen getroffen sind, kann Contoso mit dem Replizieren der VM beginnen.
    - Wenn die Replikation aktiviert wurde und funktioniert, migriert das Unternehmen die VM via Failover zu Azure.
- Die Datenbank wird mithilfe des Datenmigrations-Assistenten (DMA) zu einem SQL Server-Cluster in Azure migriert.
    - Im ersten Schritt müssen SQL Server-VMs in Azure bereitgestellt, der Cluster und ein interner Lastenausgleich eingerichtet und Always On-Verfügbarkeitsgruppen konfiguriert werden.
    - Anschließend kann die Datenbank migriert werden
- Nach der Migration wird der Always On-Schutz für die Datenbank aktiviert.

![Migrationsprozess](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Voraussetzungen

Für dieses Szenario benötigt Contoso Folgendes.

**Anforderungen** | **Details**
--- | ---
**Azure-Abonnement** | Contoso hat bereits in einem früheren Artikel dieser Reihe ein Abonnement erstellt. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.<br/><br/> Wenn Sie ein kostenloses Konto erstellen, sind Sie der Administrator Ihres Abonnements und können alle Aktionen durchführen.<br/><br/> Falls Sie ein vorhandenes Abonnement verwenden und nicht der Administrator sind, müssen Sie mit dem Administrator zusammenarbeiten, damit er Ihnen Berechtigungen vom Typ „Besitzer“ oder „Mitwirkender“ zuweist.<br/><br/> Wenn Sie detailliertere Berechtigungen benötigen, lesen Sie [diesen Artikel](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-Infrastruktur** | [Weitere Informationen](contoso-migration-infrastructure.md) zur Vorgehensweise von Contoso beim Einrichten einer Azure-Infrastruktur.<br/><br/> Erfahren Sie mehr zu spezifischen [Netzwerk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)- und [Speicheranforderungen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) für Site Recovery.
**Site Recovery (lokal)** | Der lokale vCenter-Server muss mit Version 5.5, 6.0 oder 6.5 ausgeführt werden.<br/><br/> Einen ESXi-Host mit Version 5.5, 6.0 oder 6.5.<br/><br/> Mindestens eine VMware-VM auf dem ESXi-Host.<br/><br/> VMs müssen die [Azure-Anforderungen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements) erfüllen.<br/><br/> Unterstützte Konfiguration für [Netzwerk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) und [Speicher](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage).<br/><br/> VMs, die repliziert werden sollen, müssen die [Azure-Anforderungen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements) erfüllen.



## <a name="scenario-steps"></a>Szenarioschritte

Contoso geht bei der Ausführung der Migration wie folgt vor:

> [!div class="checklist"]
> * **Schritt 1: Vorbereiten eines Clusters**: Erstellen Sie einen Cluster für die Bereitstellung von zwei virtuellen SQL Server-Knoten in Azure.
> * **Schritt 2: Bereitstellen und Einrichten des Clusters**: Bereiten Sie einen Azure SQL Server-Cluster vor.  Datenbanken werden in diesen vorab erstellten Cluster migriert.
> * **Schritt 3: Bereitstellen des Lastenausgleichs**: Stellen Sie einen Lastenausgleich für den Datenverkehr zu den SQL Server-Knoten bereit.
> * **Schritt 4: Vorbereiten von Azure für Site Recovery**: Erstellen Sie ein Azure-Speicherkonto zum Speichern replizierter Daten sowie einen Recovery Services-Tresor. 
> * **Schritt 5: Vorbereiten einer lokalen VMware-Instanz für Site Recovery**: Bereiten Sie Konten für die VM-Ermittlung und die Agent-Installation vor. Vorbereiten lokaler virtueller Computer, damit Benutzer nach der Migration eine Verbindung mit virtuellen Azure-Computern herstellen können
> * **Schritt 6: Replizieren von VMs**: Ermöglichen Sie die VM-Replikation in Azure.
> * **Schritt 7: Installieren des DMA**: Laden Sie den Datenmigrations-Assistenten herunter, und installieren Sie ihn.
> * **Schritt 7: Migrieren der Datenbank mit dem DMA**: Migrieren Sie die Datenbank zu Azure.
> * **Schritt 9: Schützen der Datenbank**: Erstellen Sie eine Always On-Verfügbarkeitsgruppe für den Cluster.
> * **Schritt 10: Migration der Web-App-VM**: Führen Sie ein Testfailover aus, um sicherzustellen, dass alles wie erwartet funktioniert. Anschließendes Ausführen eines vollständigen Failovers zu Azure. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Schritt 1: Vorbereiten eines Clusters für SQL Server Always On-Verfügbarkeitsgruppen

Contoso-Administratoren richten den Cluster wie folgt ein:

1. Das Unternehmen erstellt zwei SQL Server-VMs, indem es das Image SQL Server 2017 Enterprise Windows Server 2016 im Azure Marketplace auswählt. 

    ![SQL-VM-SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. Unter **Assistent für die Erstellung virtueller Computer** > **Grundlagen** konfiguriert das Unternehmen Folgendes:

    - Namen für die virtuellen Computer: **SQLAOG1** und **SQLAOG2**.
    - Da Computer unternehmenskritisch sind, aktiviert Contoso SSD für den VM-Datenträgertyp.
    - Es gibt Anmeldeinformationen für Computer an.
    - Das Unternehmen stellt die VMs in der primären Region „USA, Osten 2“ in der Ressourcengruppe ContosoRG bereit.

3. Unter **Größe** beginnt das Unternehmen bei beiden VMs mit der SKU „D2s_V3“. Nach Bedarf kann die Skalierung zu einem späteren Zeitpunkt durchgeführt werden.
4. Das Unternehmen führt unter **Einstellungen** die folgenden Schritte aus:

    - Da es sich bei diesen VMs um wichtige Datenbanken für die App handelt, verwenden sie verwaltete Datenträger.
    - Das Unternehmen ordnet die Computer im Produktionsnetzwerk der primären Region „USA, Osten 2“ (**VNET-PROD-EUS2**) im Subnetz der Datenbank (**PROD-DB-EUS2**) an.
    - Es wird eine neue Verfügbarkeitsgruppe erstellt: **SQLAOGAVSET** mit zwei Fehlerdomänen und fünf Updatedomänen.

      ![SQL-VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. Contoso begrenzt über die **SQL Server-Einstellungen** die SQL-Konnektivität mit dem virtuellen Netzwerk (privat) am Standardport 1433. Für die Authentifizierung verwendet es die gleichen Anmeldeinformationen wie vor Ort (**contosoadmin**).

    ![SQL-VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Benötigen Sie weitere Hilfe?**

- [Hilfe](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) zur Bereitstellung einer SQL Server-VM.
- [Weitere Informationen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) zum Konfigurieren von VMs für verschiedene SQL Server-SKUs.

## <a name="step-2-deploy-and-set-up-the-cluster"></a>Schritt 2: Bereitstellen und Einrichten des Clusters

Contoso-Administratoren gehen bei der Einrichtung des Clusters wie folgt vor:

1. Das Unternehmen richtet ein Azure-Speicherkonto ein, um als Cloudzeuge zu fungieren.
2. Es fügt die SQL Server-VMs zur Active Directory-Domäne im lokalen Contoso-Rechenzentrum hinzu.
3. Es erstellt den Cluster in Azure.
4. Es konfiguriert den Cloudzeugen.
5. Schließlich aktiviert das Unternehmen die Always On-Verfügbarkeitsgruppen von SQL.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Einrichten eines Speicherkontos als Cloudzeuge

Für die Einrichtung eines Cloudzeugen benötigt Contoso ein Azure-Speicherkonto, das die Blob-Datei für die Clustervermittlung enthält. Dasselbe Speicherkonto kann für die Einrichtung eines Cloudzeugen für mehrere Cluster verwendet werden. 

Contoso-Administratoren gehen bei der Erstellung eines Speicherkontos wie folgt vor:

1. Das Unternehmen gibt einen erkennbaren Namen für das Konto an (**contosocloudwitness**).
2. Es stellt ein allgemein einsetzbares Konto mit LRS bereit.
3. Contoso platziert das Konto in einer dritten Region: USA, Süden-Mitte. Es platziert das Konto außerhalb der primären und sekundären Region, damit es im Falle es regionalen Ausfalls verfügbar bleibt.
4. Das Unternehmen platziert das Konto in seiner Ressourcengruppe mit Infrastrukturressourcen: **ContosoInfraRG**.

    ![Cloudzeuge](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Wenn das Unternehmen das Speicherkonto erstellt, werden für dieses Konto primäre und sekundäre Zugriffsschlüssel generiert. Der primäre Zugriffsschlüssel wird für die Erstellung des Cloudzeugen benötigt. Der Schlüssel wird unter dem Namen des Speicherkontos > **Zugriffstasten** angezeigt.

    ![Zugriffsschüssel](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Hinzufügen von SQL Server-VMs zur Contoso-Domäne

1. Contoso fügt die VMs SQLAOG1 und SQLAOG2 zur Domäne contoso.com hinzu.
2. Anschließend werden auf jeder VM das Feature und die Tools des Windows-Failoverclusters installiert.

### <a name="set-up-the-cluster"></a>Einrichten des Clusters

Vor dem Einrichten des Clusters erstellen Contoso-Administratoren auf jedem Computer eine Momentaufnahme des Betriebssystemdatenträgers.

![Momentaufnahme](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. Anschließend führt das Unternehmen ein Skript aus, mit dem der Windows-Failovercluster erstellt werden soll.

    ![Cluster erstellen](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. Nach der Erstellung des Clusters überprüft das Unternehmen, ob die VMs als Clusterknoten angezeigt werden.

     ![Cluster erstellen](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurieren des Cloudzeugen

1. Contoso-Administratoren konfigurieren den Cloudzeugen mithilfe des **Assistenten für die Quorumkonfiguration** im Failovercluster-Manager.
2. Im Assistenten erstellt das Unternehmen einen Cloudzeugen mit dem Speicherkonto.
3. Nach der Konfiguration des Cloudzeugen wird dieser im Failovercluster-Manager-Snap-In angezeigt.

    ![Cloudzeuge](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>Aktivieren von SQL Server Always On-Verfügbarkeitsgruppen

Contoso-Administratoren können jetzt Always On aktivieren:

1. Das Unternehmen aktiviert im SQL Server-Konfigurations-Manager die **Always On-Verfügbarkeitsgruppen** für den Dienst **SQL Server (MSSQLSERVER)**.

    ![Aktivieren von Always On](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Das Unternehmen startet den Dienst neu, damit die Änderungen wirksam werden.

Nach der Aktivierung von Always On kann Contoso die Always On-Verfügbarkeitsgruppe für den Schutz der SmartHotel360-Datenbank einrichten.

**Benötigen Sie weitere Hilfe?**

- [Weitere Informationen](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) zum Cloudzeugen und zum Einrichten eines Speicherkontos für diesen Cloudzeugen.
- [Anweisungen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) zum Einrichten eines Clusters und zum Erstellen einer Verfügbarkeitsgruppe.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Schritt 3: Bereitstellen von Azure Load Balancer

Contoso-Administratoren möchten nun ein internes Lastenausgleichsmodul bereitstellen, das sich vor den Clusterknoten befindet. Das Lastenausgleichsmodul ist für Datenverkehr empfangsbereit und leitet diesen an den entsprechenden Knoten weiter.

![Lastenausgleich](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Contoso geht bei der Erstellung des Lastenausgleichs wie folgt vor:

1. Das Unternehmen richtet im Azure-Portal unter **Netzwerk** > **Lastenausgleich** einen neuen internen Lastenausgleich ein: **ILB-PROD-DB-EUS2-SQLAOG**.
2. Das Unternehmen ordnet den Lastenausgleich im Produktionsnetzwerk **VNET-PROD-EUS2** im Subnetz der Datenbank **PROD-DB-EUS2** an.
3. Dem Lastenausgleich wird eine statische IP-Adresse zugewiesen: 10.245.40.100.
4. Der Lastenausgleich wird als Netzwerkelement in der Netzwerkressourcengruppe **ContosoNetworkingRG** bereitgestellt.

    ![Lastenausgleich](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Nach der Bereitstellung des internen Lastenausgleichs muss dieser eingerichtet werden. Das Unternehmen erstellt einen Back-End-Adresspool, richtet einen Integritätstest ein und konfiguriert eine Regel für den Lastenausgleich.

### <a name="add-a-backend-pool"></a>Hinzufügen eines Back-End-Pools

Zum Verteilen von Datenverkehr an die virtuellen Computer im Cluster richten Contoso-Administratoren einen Back-End-Adresspool ein, der die IP-Adressen der NICs für virtuelle Computer enthält, die den Netzwerkdatenverkehr über den Lastenausgleich empfangen.

1. Contoso fügt im Portal in den Einstellungen für den Lastenausgleich einen Back-End-Pool hinzu: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Dem Pool wird die Verfügbarkeitsgruppe SQLAOGAVSET zugeordnet. Die VMs in der Gruppe (**SQLAOG1** und **SQLAOG2**) werden zum Pool hinzugefügt.

    ![Back-End-Pool](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Contoso-Administratoren erstellen einen Integritätstest, damit der Lastenausgleich den Status der App überwachen kann. Abhängig von der Reaktion auf Integritätstests werden durch den Integritätstest dynamisch VMs zur Lastenausgleichsrotation hinzugefügt oder daraus entfernt.

Das Unternehmen geht bei der Erstellung des Tests wie folgt vor: 

1. Contoso erstellt im Portal in den Einstellungen für den Lastenausgleich einen Integritätstest: **SQLAlwaysOnEndPointProbe**.
2. Das Unternehmen legt fest, dass bei dem Test VMs an TCP-Port 59999 überwacht werden sollen.
3. Es legt ein Intervall von 5 Sekunden zwischen den Tests und einen Schwellenwert von 2 fest. Wenn zwei Tests fehlschlagen, gilt eine VM als fehlerhaft.

    ![Test](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurieren des Lastenausgleichs für den Empfang von Datenverkehr


Contoso-Administratoren richten jetzt eine Regel für den Lastenausgleich ein, um zu definieren, wie Datenverkehr auf die virtuellen Computer verteilt wird.

- Die Front-End-IP-Adresse verarbeitet eingehenden Datenverkehr.
- Der Back-End-IP-Pool empfängt den Datenverkehr.

Das Unternehmen geht bei der Erstellung der Regel wie folgt vor:

1. Es fügt im Portal in den Einstellungen für den Lastenausgleich eine neue Regel für den Lastenausgleich hinzu: **SQLAlwaysOnEndPointListener**.
2. Es legt für den Empfang von eingehendem Datenverkehr des SQL-Clients an TCP-Port 1433 einen Front-End-Listener fest.
3. Es gibt den Back-End-Pool an, an den Datenverkehr weitergeleitet werden soll, und den Port, an dem die VMs für den Datenverkehr empfangsbereit sind.
4. Es aktiviert Floating IP (Direct Server Return). Dies ist für SQL Always On immer erforderlich.

    ![Test](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Benötigen Sie weitere Hilfe?**

- [Übersicht](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) über Azure Load Balancer.
- [Weitere Informationen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) zum Erstellen eines Lastenausgleichs.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Schritt 4: Vorbereiten von Azure für den Site Recovery-Dienst

Im Folgenden werden die Azure-Komponenten aufgeführt, die Contoso für die Bereitstellung von Site Recovery benötigt:

- Ein VNet, in dem sich VMs befinden, wenn diese während eines Failovers erstellt werden.
- Ein Azure-Speicherkonto für die Speicherung replizierter Daten. 
- Ein Recovery Services-Tresor in Azure.

Contoso-Administratoren gehen bei der Einrichtung dieser Komponenten wie folgt vor:

1.  Contoso hat bereits ein Netzwerk/Subnetz erstellt, das für Site Recovery verwendet werden kann, wenn das Unternehmen die [Azure-Infrastruktur bereitgestellt](contoso-migration-rehost-vm-sql-ag.md) hat.

    - Bei der SmartHotel360-App handelt es sich um eine Produktions-App. WEBVM wird zum Azure-Produktionsnetzwerk (VNET-PROD-EUS2) in der primären Region „USA, Osten 2“ migriert.
    - WEBVM wird in der Ressourcengruppe ContosoRG, die für Produktionsressourcen verwendet wird, und im Produktionssubnetz (PROD-FE EUS2) platziert.

2. Contoso-Administratoren erstellen ein Azure-Speicherkonto (contosovmsacc20180528) in der primären Region.

    - Contoso verwendet ein allgemeines Konto mit Standardspeicher und LRS-Replikation.
    - Das Konto muss sich in der gleichen Region wie der Tresor befinden.

      ![Site Recovery-Speicher](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Nach dem Einrichten des Netzwerks und des Speicherkontos kann Contoso nun einen Recovery Services-Tresor (**ContosoMigrationVault**) erstellen und diesen in der Ressourcengruppe **ContosoFailoverRG** in der primären Region „USA, Osten 2“ platzieren.

    ![Recovery Services-Tresor](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Benötigen Sie weitere Hilfe?**

[Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) zum Einrichten von Azure für Site Recovery.


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>Schritt 5: Vorbereiten einer lokalen VMware-Instanz für Site Recovery

Contoso-Administratoren bereiten lokal Folgendes vor:

- Ein Konto auf dem Server von vCenter Server oder dem vSphere ESXi-Host zum Automatisieren der VM-Ermittlung.
- Ein Konto für die automatische Installation von Mobility Service auf VMware-VMs, die Sie replizieren möchten.
- Lokale VM-Einstellungen, damit Contoso nach einem Failover eine Verbindung mit der replizierten Azure-VM herstellen kann.


### <a name="prepare-an-account-for-automatic-discovery"></a>Vorbereiten eines Kontos für die automatische Ermittlung

Site Recovery benötigt Zugriff auf VMware-Server, um folgende Aufgaben durchzuführen:

- Automatisches Ermitteln von VMs. 
- Orchestrieren von Replikation, Failover und Failback.
- Dafür ist mindestens ein Konto mit Lesezugriff erforderlich. Sie benötigen ein Konto, das berechtigt ist, Vorgänge wie das Erstellen und Entfernen von Datenträgern sowie das Einschalten virtueller Computer durchzuführen.

Contoso-Administratoren richten das Konto wie folgt ein:

1. Es wird eine Rolle auf vCenter-Ebene erstellt.
2. Anschließend werden dieser Rolle die erforderlichen Berechtigungen zugewiesen.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Vorbereiten eines Kontos für die Installation des Mobility Services

Mobility Service muss auf jeder VM installiert sein.

- Site Recovery kann eine automatische Pushinstallation dieser Komponente durchführen, wenn die Replikation für die VM aktiviert ist.
- Sie benötigen ein Konto, über das Site Recovery für die Pushinstallation auf die VM zugreifen kann. Sie geben dieses Konto an, wenn Sie die Replikation in der Azure-Konsole einrichten.
- Sie benötigen ein Domänenkonto oder ein lokales Konto mit Berechtigungen für die Installation auf der VM.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Nach dem Failover möchte Contoso eine Verbindung mit virtuellen Azure-Computern herstellen können. Dazu führen Contoso-Administratoren vor der Migration folgende Schritte aus:

1. Für den Zugriff über das Internet:

   - Es aktiviert vor dem Failover RDP auf der lokalen VM.
   - Sicherstellen, dass TCP- und UDP-Regeln zum **öffentlichen** Profil hinzugefügt wurden.
   - Bei allen Profilen unter **Windows-Firewall** > **Zugelassene Apps** überprüfen, ob RDP zulässig ist.
 
2. Für den Zugriff über Site-to-Site-VPN:

   - RDP auf dem lokalen Computer aktivieren.
   - RDP unter **Windows-Firewall** -> **Zugelassene Apps und Features** für **private und Domänennetzwerke** zulassen.
   - SAN-Richtlinie des Betriebssystems auf der lokalen VM auf **OnlineAll** festlegen.

Bei der Ausführung eines Failovers sind darüber hinaus folgende Aspekte zu überprüfen:

- Auf dem virtuellen Computer sollten keine ausstehenden Windows-Updates vorhanden sein, wenn Sie ein Failover auslösen. Andernfalls können sich Benutzer nach Abschluss des Updates nicht mehr beim virtuellen Computer anmelden.
- Nach dem Failover kann **Startdiagnose** aktiviert werden, um einen Screenshot der VM anzuzeigen. Falls dies nicht funktioniert, sollte überprüft werden, ob die VM ausgeführt wird, und die folgenden [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) gelesen werden.


**Benötigen Sie weitere Hilfe?**

- [Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) zum Erstellen und Zuweisen von Rollen für die automatische Ermittlung.
- [Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) zum Erstellen eines Kontos für die Pushinstallation von Mobility Service.


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Schritt 6: Replizieren der lokalen virtuellen Computer in Azure mit Site Recovery

Bevor Contoso-Administratoren eine Migration zu Azure durchführen können, müssen sie die Replikation einrichten und aktivieren.

### <a name="set-a-replication-goal"></a>Festlegen eines Replikationsziels

1. Im Tresor wird unter dem Tresornamen (ContosoVMVault) ein Replikationsziel ausgewählt (**Erste Schritte** > **Site Recovery** > **Infrastruktur vorbereiten**).
2. Das Unternehmen gibt an, dass sich seine Computer in einer lokalen Umgebung befinden, unter VMware ausgeführt und in Azure repliziert werden.

    ![Replikationsziel](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bestätigen der Bereitstellungsplanung

Damit das Unternehmen fortfahren kann, muss es durch Auswahl von **Ja, ist abgeschlossen** bestätigen, dass es die Bereitstellungsplanung abgeschlossen hat. Contoso migriert in diesem Szenario nur eine VM. Daher ist keine Bereitstellungsplanung erforderlich.

### <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

Contoso-Administratoren müssen die Quellumgebung konfigurieren. Hierzu wird eine OVF-Vorlage heruntergeladen, die für die Bereitstellung des Site Recovery-Konfigurationsservers als hoch verfügbare, lokale VMware-VM verwendet wird. Nachdem der Konfigurationsserver eingerichtet wurde und ausgeführt wird, wird er im Tresor registriert.

Auf dem Konfigurationsserver werden einige Komponenten ausgeführt:

- Die Konfigurationsserverkomponente, die die Kommunikation zwischen der lokalen Umgebung und Azure koordiniert und die Datenreplikation verwaltet.
- Der Prozessserver, der als Replikationsgateway fungiert. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage.
- Der Prozessserver installiert auch Mobility Service auf virtuellen Computern, die Sie replizieren möchten, und führt auf lokalen VMware-VMs eine automatische Ermittlung durch.

Contoso-Administratoren führen diese Schritte wie folgt aus:


1. Das Unternehmen lädt die OVF-Vorlage im Tresor von **Infrastruktur vorbereiten** > **Quelle** > **Konfigurationsserver** herunter.
    
    ![Herunterladen der OVF-Vorlage](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Das Unternehmen importiert die Vorlage in VMware, um die VM zu erstellen und bereitzustellen.

    ![OVF-Vorlage](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Wenn das Unternehmen die VM zum ersten Mal aktiviert, wird sie in einem Windows Server 2016-Installationsvorgang hochgefahren. Das Unternehmen akzeptiert die Lizenzvereinbarung und gibt ein Administratorkennwort ein.
4. Nach Abschluss der Installation meldet sich Contoso als Administrator bei der VM an. Bei der ersten Anmeldung wird das Azure Site Recovery-Konfigurationstool standardmäßig ausgeführt.
5. Das Unternehmen gibt im Tool einen Namen an, der für die Registrierung des Konfigurationsservers im Tresor verwendet werden soll.
6. Das Tool überprüft, ob der virtuelle Computer eine Verbindung mit Azure herstellen kann. Nachdem die Verbindung hergestellt wurde, meldet sich das Unternehmen bei seinem Azure-Abonnement an. Die Anmeldeinformationen müssen über Zugriff auf den Tresor verfügen, in dem Sie den Konfigurationsserver registrieren möchten.

    ![Konfigurationsserver registrieren](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Das Tool führt einige Konfigurationsaufgaben und anschließend einen Neustart durch.
8. Contoso meldet sich erneut am Computer an. Der Assistent für die Konfigurationsserververwaltung wird automatisch gestartet.
9. Im Assistenten wählt das Unternehmen die NIC zum Empfangen von Replikationsdatenverkehr aus. Diese Einstellung kann nach der Konfiguration nicht mehr geändert werden.
10. Das Unternehmen wählt das Abonnement, die Ressourcengruppe und den Tresor aus, in dem der Konfigurationsserver registriert werden soll.
        ![Tresor](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Anschließend werden MySQL Server und VMWare PowerCLI heruntergeladen und installiert. 
11. Nach der Überprüfung gibt Contoso den FQDN oder die IP-Adresse des Servers von vCenter Server oder des vSphere-Hosts an. Der Standardport wird beibehalten, und für den Server von vCenter Server wird ein Anzeigename angegeben.
12. Contoso gibt das für die automatische Ermittlung erstellte Konto und die Anmeldeinformationen für die automatische Installation von Mobility Service an. Für Windows-Computer benötigt das Konto lokale Administratorrechte auf den VMs.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Nach Abschluss der Registrierung im Azure-Portal überprüfen die Administratoren erneut, ob der Konfigurationsserver und der VMware-Server auf der Seite **Quelle** im Tresor aufgeführt werden. Die Ermittlung kann mindestens 15 Minuten dauern. 
8. Site Recovery stellt anschließend mithilfe der angegebenen Einstellungen eine Verbindung mit VMware-Servern her und ermittelt VMs.

### <a name="set-up-the-target"></a>Einrichten des Ziels

Contoso-Administratoren geben nun die Zielreplikationseinstellungen an.

1. Das Unternehmen wählt unter **Infrastruktur vorbereiten** > **Ziel** die Zieleinstellungen aus.
2. Site Recovery überprüft, ob das angegebene Ziel ein Azure-Speicherkonto und -Netzwerk enthält.

### <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

Jetzt können Contoso-Administratoren eine Replikationsrichtlinie erstellen.

1. Unter **Infrastruktur vorbereiten** > **Replikationseinstellungen** > **Replikationsrichtlinie** >  **Erstellen und zuordnen** erstellt das Unternehmen die Richtlinie **ContosoMigrationPolicy**.
2. Es werden die Standardeinstellungen verwendet:
    - **RPO-Schwellenwert**: Standardwert von 60 Minuten. Mit diesem Wert wird festgelegt, wie oft Wiederherstellungspunkte erstellt werden. Wenn dieser Grenzwert bei der fortlaufenden Replikation überschritten wird, wird eine Warnung generiert.
    - **Aufbewahrung des Wiederherstellungspunkts**. Standardwert von 24 Stunden. Dieser Wert gibt den Aufbewahrungszeitraum für die einzelnen Wiederherstellungspunkte an. Replizierte VMs können für jeden Punkt eines Zeitfensters wiederhergestellt werden.
    - **Häufigkeit von Momentaufnahmen für App-Konsistenz**. Der Standardwert ist eine Stunde. Dieser Wert gibt die Häufigkeit an, mit der anwendungskonsistente Momentaufnahmen erstellt werden.
 
        ![Erstellen einer Replikationsrichtlinie](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Die Richtlinie wird dem Konfigurationsserver automatisch zugeordnet. 

    ![Zuordnen der Replikationsrichtlinie](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Aktivieren der Replikation

Contoso-Administratoren können nun mit der Replikation von WebVM beginnen.

1. Unter **Anwendung replizieren** > **Quelle** > **+ Replizieren** wählt das Unternehmen die Quelleneinstellungen aus.
2. Das Unternehmen wählt aus, dass VMs aktiviert werden sollen. Darüber hinaus wählt es den Server von vCenter Server und den Konfigurationsserver aus.

    ![Aktivieren der Replikation](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Contoso gibt die Zieleinstellungen, einschließlich der Ressourcengruppe und des VNet, und das Speicherkonto an, in dem die replizierten Daten gespeichert werden.

     ![Aktivieren der Replikation](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Sie wählen den WebVM-Computer für die Replikation aus, überprüfen die Replikationsrichtlinie und aktivieren die Replikation. Site Recovery installiert Mobility Service auf der VM, wenn die Replikation aktiviert ist.
 
    ![Aktivieren der Replikation](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Der Replikationsfortschritt wird unter **Aufträge** nachverfolgt. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der Computer bereit für das Failover.
5. Unter **Zusammenfassung** im Azure-Portal können sie die Struktur für die virtuellen Computer anzeigen, die in Azure repliziert werden.

    ![Infrastrukturansicht](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Benötigen Sie weitere Hilfe?**

- Unter [Einrichten der Notfallwiederherstellung für lokale VMware-VMs](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial) finden Sie eine vollständige exemplarische Vorgehensweise all dieser Schritte.
- Ausführliche Anweisungen dienen Ihnen beim [Einrichten der Quellumgebung](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [Bereitstellen des Konfigurationsservers](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) und beim [Konfigurieren von Replikationseinstellungen](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication) als Unterstützung.
- Erfahren Sie mehr über das [Aktivieren der Replikation](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-7-install-the-database-migration-assistant-dma"></a>Schritt 7: Installieren des Datenmigrations-Assistenten (DMA)

Contoso-Administratoren migrieren die SmartHotel360-Datenbank mithilfe des DMA zum virtuellen Azure-Computer **SQLAOG1**. Das Unternehmen geht bei der Einrichtung des DMA wie folgt vor:

1. Es lädt das Tool aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) auf die lokale SQL Server-VM (**SQLVM**) herunter.
2. Es führt das Setup (DownloadMigrationAssistant.msi) auf der VM aus.
3. Auf der Seite **Fertigstellen** wählen sie **Launch Microsoft Data Migration Assistant** (Microsoft-Datenmigrations-Assistenten starten) aus, bevor sie den Assistenten beenden.

## <a name="step-8-migrate-the-database-with-dma"></a>Schritt 8: Migrieren der Datenbank mit dem DMA

1. Im DMA führt das Unternehmen eine neue Migration aus: **SmartHotel**.
2. Es wählt **SQL Server auf virtuellen Azure-Computern**als **Typ des Zielservers** aus. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. In den Migrationsdetails fügt das Unternehmen **SQLVM** als Quellserver und **SQLAOG1** als Ziel hinzu. Es gibt für jeden Computer Anmeldeinformationen an.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Das Unternehmen erstellt für die Datenbank- und Konfigurationsinformationen eine lokale Freigabe. Das SQL-Dienstkonto muss auf der VM SQLVM und der VM SQLAOG1 mit Schreibzugriff darauf zugreifen können.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso wählt die zu migrierenden Anmeldedaten aus und beginnt mit der Migration. Nach Abschluss der Migration zeigt der DMA an, dass die Migration erfolgreich ausgeführt wurde.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Das Unternehmen überprüft, ob die Datenbank auf der VM **SQLAOG1** ausgeführt wird.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS verbindet sich über eine Site-to-Site-VPN-Verbindung zwischen dem Contoso-Rechenzentrum und Azure mit der lokalen SQL Server-VM und migriert anschließend die Datenbank.

## <a name="step-7-protect-the-database-with-alwayson"></a>Schritt 7: Schützen der Datenbank mit Always On

Wenn die App-Datenbank auf **SQLAOG1** ausgeführt wird, können Contoso-Administratoren diese jetzt mithilfe von AlwaysOn-Verfügbarkeitsgruppen schützen. Das Unternehmen konfiguriert Always On mit SQL Server Management Studio und weist anschließend über das Windows-Clustering einen Listener zu. 

### <a name="create-an-alwayson-availability-group"></a>Erstellen einer Always On-Verfügbarkeitsgruppe

1. Contoso klickt in SQL Server Management Studio mit der rechten Maustaste auf **Hochverfügbarkeit mit Always On**, um den **Assistenten für neue Verfügbarkeitsgruppen** zu starten.
2. Unter **Optionen angeben** gibt das Unternehmen der Verfügbarkeitsgruppe den Namen **SHAOG**. Unter **Datenbanken auswählen** wählt das Unternehmen die SmartHotel360-Datenbank aus.

    ![Always On-Verfügbarkeitsgruppe](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. Unter **Specify Replicas** (Replikate angeben) fügt das Unternehmen die zwei SQL-Knoten als Verfügbarkeitsreplikate hinzu und konfiguriert diese so, dass ein automatisches Failover mit synchronem Commit bereitgestellt wird.

     ![Always On-Verfügbarkeitsgruppe](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Contoso konfiguriert einen Listener für die Gruppe (**SHAOG**) und den Port. Die IP-Adresse des internen Lastenausgleichs wird als statische IP-Adresse (10.245.40.100) hinzugefügt.

    ![Always On-Verfügbarkeitsgruppe](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. Unter **Datensynchronisierung auswählen** aktiviert das Unternehmen das automatische Seeding. Mit dieser Option erstellt SQL Server für jede Datenbank in der Gruppe automatisch die sekundären Replikate, sodass Contoso diese nicht manuell sichern und wiederherstellen muss. Nach der Überprüfung wird die Verfügbarkeitsgruppe erstellt.

    ![Always On-Verfügbarkeitsgruppe](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Bei der Erstellung der Gruppe ist ein Problem aufgetreten. Contoso verwendet in Active Directory nicht die integrierte Sicherheit von Windows. Folglich müssen Berechtigungen für die SQL-Anmeldung erteilt werden, damit die Rollen für den Windows-Failovercluster erstellt werden können.

    ![Always On-Verfügbarkeitsgruppe](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Nach der Erstellung der Gruppe kann Contoso diese in SQL Server Management Studio anzeigen.

### <a name="configure-a-listener-on-the-cluster"></a>Konfigurieren eines Listeners für den Cluster

Im letzten Schritt der Einrichtung der SQL-Bereitstellung konfigurieren Contoso-Administratoren den internen Lastenausgleich als Listener für den Cluster und schalten den Listener online. Hierzu verwendet das Unternehmen ein Skript.

![Listener für Cluster](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Überprüfen der Konfiguration

Nachdem Contoso die Einrichtung abgeschlossen hat, verfügt das Unternehmen nun über eine funktionale Verfügbarkeitsgruppe in Azure, die die migrierte Datenbank verwendet. Administratoren überprüfen dies, indem sie eine Verbindung in SQL Server Management Studio mit dem internen Lastenausgleich herstellen.

![Verbinden mit dem internen Lastenausgleich](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Benötigen Sie weitere Hilfe?**
- Weitere Informationen zum Erstellen einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) und eines [Listeners](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Sie können den Vorgang [Einrichten des Clusters für die Verwendung der IP-Adresse des Lastenausgleichs](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address) manuell durchführen.
- [Weitere Informationen](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) zum Erstellen und Verwenden von SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Schritt 8: Migrieren der VM mit Site Recovery

Die Contoso-Administratoren führen ein schnelles Testfailover aus und migrieren anschließend die virtuellen Computer.

### <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

Das Ausführen eines Testfailovers dient zur Sicherstellung, dass vor der Migration alles wie erwartet funktioniert. 

1. Sie führen ein Testfailover auf den letzten verfügbaren Zeitpunkt aus (**Zuletzt verarbeitet**).
2. **Computer vor Starten des Failovers herunterfahren** wird ausgewählt, damit Site Recovery versucht, die Quell-VM vor dem Auslösen des Failovers herunterzufahren. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. 
3. Testfailover durchführen: 

    - Eine Überprüfung der erforderlichen Komponenten wird ausgeführt, um sicherzustellen, dass alle Bedingungen für eine Migration erfüllt sind.
    - Durch das Failover werden die Daten verarbeitet, sodass eine Azure-VM erstellt werden kann. Wenn Sie den letzten Wiederherstellungspunkt auswählen, wird ein Wiederherstellungspunkt auf der Grundlage der Daten erstellt.
    - Eine Azure-VM wird anhand der im vorherigen Schritt verarbeiteten Daten erstellt.

3. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal angezeigt. Das Unternehmen überprüft, ob die VM die richtige Größe hat, mit dem richtigen Netzwerk verbunden ist und ausgeführt wird. 
4. Nach der Überprüfung bereinigt es das Failover. Darüber hinaus werden Beobachtungen aufgezeichnet und gespeichert. 

### <a name="run-a-failover"></a>Ausführen eines Failovers

1. Nachdem Contoso-Administratoren überprüft haben, ob das Testfailover wie erwartet funktioniert, erstellen sie einen Wiederherstellungsplan für die Migration und fügen WEBVM dem Plan hinzu.

     ![Wiederherstellungsplan](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Das Unternehmen führt ein Failover für den Plan aus. Es entscheidet, ein Failover für den letzten Wiederherstellungspunkt auszuführen. Zudem gibt das Unternehmen an, dass Site Recovery versuchen sollte, die lokale VM vor dem Auslösen des Failovers herunterzufahren.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Nach dem Failover überprüft Contoso, ob die Azure-VM wie erwartet im Azure-Portal angezeigt wird.

    ![Wiederherstellungsplan](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Nach dieser Überprüfung führt das Unternehmen die Migration durch, um den Migrationsprozess zu beenden. Darüber hinaus beendet es die Replikation für die VM und die Site Recovery-Abrechnung für die VM.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Der letzte Schritt im Migrationsprozess besteht in der Aktualisierung der Verbindungszeichenfolge der Anwendung, um auf die migrierte Datenbank zu verweisen, die auf dem SHAOG-Listener ausgeführt wird. Diese Konfiguration wird auf der WEBVM geändert, die jetzt in Azure ausgeführt wird.  Diese Konfiguration befindet sich in der Datei „web.config“ der ASP-Anwendung. 

1. Suchen Sie die Datei unter C:\inetpub\SmartHotelWeb\web.config.  Ändern Sie den Namen des Servers, damit der FQDN der Always On-Verfügbarkeitsgruppe widergespiegelt wird: shaog.contoso.com.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Nachdem Contoso die Datei aktualisiert und gespeichert hat, startet das Unternehmen IIS auf WEBVM neu. Hierzu verwendet es IISRESET/RESTART aus einer Eingabeaufforderung.
2. Nachdem IIS neu gestartet wurde, verwendet die Anwendung nun die Datenbank, die auf der verwalteten SQL-Instanz ausgeführt wird.


**Benötigen Sie weitere Hilfe?**

- [Informationen](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) zum Ausführen eines Testfailovers. 
- [Informationen](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) zum Erstellen eines Wiederherstellungsplans.
- [Informationen](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) zum Ausführen eines Failovers für Azure.

## <a name="clean-up-after-migration"></a>Bereinigung nach der Migration

Nach der Migration wird die SmartHotel360-App auf einem Azure-VM ausgeführt, und die SmartHotel360-Datenbank befindet sich im Azure SQL-Cluster.

Contoso muss jetzt folgende Schritte für die Bereinigung durchführen:  

- Entfernen der lokalen VMs aus dem vCenter-Bestand.
- Entfernen der VMs aus lokalen Sicherungsaufträgen.
- Aktualisieren der internen Dokumentation und Anzeigen der neuen Speicherorte und IP-Adressen für VMs.
- Überprüfen sämtlicher Ressourcen, die mit den außer Betrieb genommenen VMs interagieren, und Aktualisieren sämtlicher relevanter Einstellungen oder Dokumentationen zur Widerspiegelung der neuen Konfiguration.
- Hinzufügen der beiden neuen VMs (SQLAOG1 und SQLAOG2) zu Systemen für die Produktionsüberwachung.

## <a name="review-the-deployment"></a>Überprüfen der Bereitstellung

Da die migrierten Ressourcen in Azure enthalten sind, muss Contoso die neue Infrastruktur vollständig operationalisieren und sichern.

### <a name="security"></a>Sicherheit

Das Sicherheitsteam von Contoso überprüft die Azure-VMs WEBVM, SQLAOG1 und SQLAOG2 auf eventuell vorhandene Sicherheitsprobleme. 

- Das Team überprüft die Netzwerksicherheitsgruppen (NSGs) der des virtuellen Computers zur Steuerung des Zugriffs. Mithilfe von NSGs wird sichergestellt, dass nur für die App zulässiger Datenverkehr übergeben werden kann.
- Das Team zieht darüber hinaus in Erwägung, die Daten über Azure Disk Encryption und KeyVault zu sichern.
- Das Team sollte die transparente Datenverschlüsselung (Transparent Data Encryption, TDE) auswerten und dann in der SmartHotel360-Datenbank aktivieren, die auf dem neuen SQL AOG ausgeführt wird. [Weitere Informationen](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)

[Erfahren Sie mehr](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) über Sicherheitsverfahren für VMs.


## <a name="bcdr"></a>BCDR

 Für die Geschäftskontinuität und Notfallwiederherstellung (Business Continuity & Disaster Recovery, BCDR) führt Contoso die folgenden Aktionen durch:
- Schützen von Daten: Contoso sichert die Daten mithilfe des Azure Backup-Diensts auf den virtuellen Computern WEBVM, SQLAOG1 und SQLAOG2. [Weitere Informationen](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  - Contoso erfährt auch, wie Azure Storage zum Sichern von SQL Server direkt im Blobspeicher verwendet wird. [Weitere Informationen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore)
  - Sicherstellen eines unterbrechungsfreien Betriebs der Apps: Contoso repliziert die App-VMs in Azure mithilfe von Site Recovery in einer sekundären Region. [Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)


### <a name="licensing-and-cost-optimization"></a>Lizenzierung und Kostenoptimierung

1. Contoso verfügt über eine Lizenzierung für die WEBVM und nutzt den Azure-Hybridvorteil.  Contoso wird die vorhandenen virtuellen Azure-Computer konvertieren, um von diesen Preisen zu profitieren.
2. Contoso aktiviert Azure Cost Management. Es ist durch Cloudyn lizenziert, ein Tochterunternehmen von Microsoft. Dabei handelt es sich um eine Kostenverwaltungslösung mit mehreren Clouds, die Ihnen das Verwenden und Verwalten von Azure und anderen Cloudressourcen erleichtert.  [Erfahren Sie mehr](https://docs.microsoft.com/azure/cost-management/overview) über die Azure Cost Management. 

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel hat Contoso der SmartHotel360-App in Azure einen neuen Host zugewiesen, indem der Front-End-VM der App mithilfe des Site Recovery-Diensts zu Azure migriert wurde. Contoso hat die App-Datenbank zu einem in Azure bereitgestellten SQL Server-Cluster migriert und diesen in einer SQL Server AlwaysOn-Verfügbarkeitsgruppe geschützt.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel dieser Reihe wird erläutert, wie Contoso für die Service Desk-App osTicket einen neuen Host zuweist, die unter Linux ausgeführt wird und mit einer MySQL-Datenbank bereitgestellt wurde.


