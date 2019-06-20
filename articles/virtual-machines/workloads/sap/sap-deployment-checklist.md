---
title: Prüfliste für die Planung und Bereitstellung von SAP-Workloads | Microsoft-Dokumentation
description: Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 93fae0babdee5eac87d50679fdd5b2b938c4df2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236898"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure 

Diese Prüfliste ist für Kunden vorgesehen, die ihre SAP NetWeaver-, S/4HANA- und Hybris-Anwendungen nach Azure-Infrastructure-as-a-Service verschieben.  Diese Prüfliste sollte während der Dauer des Projekts von einem Kunden und/oder SAP-Partner überprüft werden. Es ist wichtig, dass viele der Überprüfungen am Anfang des Projekts und in der Planungsphase durchgeführt werden. Nach Abschluss der Bereitstellung können grundlegende Änderungen an bereitgestellter Azure-Infrastruktur oder SAP-Softwarereleases äußerst komplex werden. Überprüfen Sie diese Checkliste an wichtigen Meilensteinen innerhalb eines Projekts.  So können kleine Probleme erkannt werden, bevor sie sich zu großen Problemen entwickeln und solange noch genügend Zeit ist, um Änderungen vorzunehmen und zu testen. Die Prüfliste erhebt keinesfalls einen Anspruch auf Vollständigkeit. Je nach Ihren individuellen Anforderungen müssen möglicherweise viele weitere Überprüfungen durchgeführt werden. 

Die zusammengestellte Prüfliste umfasst ausschließlich Aufgaben mit Bezug zu Azure.  Beispiel: Die Schnittstellen von SAP-Anwendungen ändern sich während des Verschiebens in die öffentliche Azure-Cloud oder zu einem Hostinganbieter.    

Diese Prüfliste kann auch auf bereits bereitgestellte Systeme angewandt werden. Möglicherweise wurden seit Ihrer Bereitstellung neue Funktionen wie die Schreibbeschleunigung, Verfügbarkeitszonen und neue VM-Typen hinzugefügt.  Daher ist es sinnvoll, die Prüfliste in regelmäßigen Abständen durchzugehen, um neue Funktionen der Azure-Plattform umsetzen zu können. 

## <a name="project-preparation-and-planning-phase"></a>Projektvorbereitungs- und -planungsphase
In dieser Phase wird die Migration einer SAP-Workload zu einer öffentlichen Azure-Cloud geplant. Der minimale Satz von Entitäten und Elementen, die beschrieben und definiert sind, wird im Folgenden aufgelistet:

1. Allgemeines Entwurfsdokument – dieses Dokument sollte Folgendes enthalten:
    1. Den aktuellen Bestand an SAP-Komponenten und -Anwendungen und den Zielanwendungsbestand in Azure
    2. Das Erstellen und Durcharbeiten einer RACI-Matrix, welche die Zuständigkeiten und Zuweisungen der verschiedenen Beteiligten definiert. Beginnen Sie auf der allgemeinen Ebene, und fahren Sie bei der Planung und den ersten Bereitstellungen nach und nach mit spezifischeren Ebenen fort.
    2. Eine allgemeine Lösungsarchitektur
    3. Entscheidung zu den Azure-Regionen, in denen die Bereitstellung erfolgen soll. Eine Liste der Azure-Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/). Informationen zu den Diensten, die in den einzelnen Azure-Regionen verfügbar sind, finden Sie im Artikel [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).
    4. Netzwerkarchitektur für Verbindungen von lokalen Standorten zu Azure. Beginnen Sie damit, sich mit der [Blaupause virtueller Rechenzentren für Azure](https://docs.microsoft.com/azure/architecture/vdc/) vertraut zu machen.
    5. Sicherheitsprinzipien für die Ausführung geschäftskritischer Daten in Azure. Lesen Sie von den verfügbaren Materialien zunächst die [Dokumentation zur Azure-Sicherheit](https://docs.microsoft.com/azure/security/).
2.  Technisches Entwurfsdokument – enthält:
    1.  Blockdiagramm der Lösung 
    2.  Größen von Compute-, Speicher- und Netzwerkkomponenten in Azure. Informationen zu den Größen von Azure-VMs für SAP finden Sie im SAP-Supporthinweis [#1928533](https://launchpad.support.sap.com/#/notes/1928533). 
    3.  BCDR-Architektur (Business Continuity & Disaster Recovery)
    4.  Ausführliche Informationen zu den Supportpaketversionen für Betriebssystem, Datenbank, Kernel und SAP. Es kann nicht unbedingt davon ausgegangen werden, dass jedes Betriebssystemrelease, das von SAP NetWeaver oder S/4HANA unterstützt wird, auch von virtuellen Azure-Computern unterstützt wird. Das gilt auch für DBMS-Releases. Es ist zwingend erforderlich, die folgenden Quellen zu überprüfen, um die SAP-, DBMS- oder Betriebssystemreleases anzupassen und ggf. zu aktualisieren, damit sie von SAP und Azure unterstützt werden. Es ist zwingend erforderlich, die von SAP und Azure unterstützten Releasekombinationen einzuhalten, um vollen Support von SAP und Microsoft zu erhalten. Sie müssen ggf. Upgrades einiger Softwarekomponenten einplanen. Weitere Einzelheiten zur unterstützten SAP-, Betriebssystem- und DBMS-Software finden Sie an den folgenden Stellen:
        1.  SAP-Supporthinweis [#1928533](https://launchpad.support.sap.com/#/notes/1928533). In diesem Hinweis wird das minimale Betriebssystemrelease, das für Azure-VMs unterstützt wird, definiert. Außerdem werden die Mindestanforderungen für die Datenbankreleases, die für die meisten Nicht-HANA-Datenbanken erforderlich sind, definiert. Der Hinweis enthält auch Angaben zu den Größen der von SAP unterstützten Azure-VM-Typen.
        2.  SAP-Supporthinweis [#2039619](https://launchpad.support.sap.com/#/notes/2039619). In diesem Hinweis wird die Oracle-Supportmatrix in Azure definiert. Beachten Sie, dass Oracle nur Windows und Oracle Linux als Gastbetriebssysteme für SAP-Workloads in Azure unterstützt. Dieser Supporthinweis gilt auch für die SAP-Anwendungsschicht, auf der die SAP-Instanzen ausgeführt werden. Oracle unterstützt jedoch keine Hochverfügbarkeit für SAP Central Services in Oracle Linux über Pacemaker. Wenn Sie Hochverfügbarkeit für ASCS in Oracle Linux benötigen, müssen Sie die SIOS Protection Suite für Linux verwenden. Ausführliche SAP-Zertifizierungsdaten finden Sie im SAP-Supporthinweis [#1662610 - Support details for SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610) (1662610: Details zur Unterstützung der SIOS Protection Suite für Linux). Für Windows wird die von SAP unterstützte Windows-Failoverclusterlösung für SAP Central Services in Verbindung mit Oracle als DBMS-Ebene unterstützt. 
        3.  SAP-Supporthinweis [#2235581](https://launchpad.support.sap.com/#/notes/2235581) zum Abrufen der Supportmatrix für SAP HANA unter den verschiedenen Betriebssystemreleases
        4.  Von SAP HANA unterstützte Azure-VMs und [Hana (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) werden [hier](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) aufgeführt
        5.  [SAP-Produktverfügbarkeitsmatrix](https://support.sap.com/en/)
        6.  Andere SAP-Hinweise zu anderen speziellen SAP-Produkten  
    5.  Es wird empfohlen, für SAP-Produktionssysteme strenge 3-Tier-Entwürfe zu verwenden. Das Kombinieren von ASCS- und APP-Servern auf demselben virtuellen Computer wird nicht empfohlen.  Die Verwendung von Multi-SID-Clusterkonfigurationen für SAP Central Services wird mit Windows als Gastbetriebssystem in Azure unterstützt. Unter Linux-Betriebssystemen in Azure werden Multi-SID-Clusterkonfigurationen für SAP Central Services jedoch nicht unterstützt. Die Dokumentation zu Anwendungsfällen für Windows als Gastbetriebssystem finden Sie unter:
        1.  [Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Hochverfügbarkeits- und Notfallwiederherstellungs-Architektur
        1.  Definieren Sie basierend auf RTO und RPO, wie die Architektur für Hochverfügbarkeit und Notfallwiederherstellung aussehen muss.
        2.  Überprüfen Sie für Hochverfügbarkeit innerhalb derselben Zone, welche Funktionen das gewünschte DBMS in Azure bietet. Die meisten DBMS bieten synchrone Methoden eines synchronen unmittelbar betriebsbereiten Standbyservers, die für Produktionssysteme empfohlen werden. Lesen Sie auch die SAP-bezogene Dokumentation zu den verschiedenen Datenbanken, angefangen mit [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) und zugehörigen Dokumenten.
            1.  Die Verwendung des Windows-Failoverclusterdiensts mit freigegebenen Datenträgern als DBMS-Ebene, wie z.B. [hier](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) für SQL Server beschrieben, wird **NICHT** unterstützt. Verwenden Sie stattdessen Lösungen wie
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA-Systemreplikation](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Überprüfen Sie für die Notfallwiederherstellung in verschiedenen Azure-Regionen, welche Möglichkeiten durch die verschiedene DBMS-Anbieter angeboten werden. Die meisten unterstützen die asynchrone Replikation oder den Protokollversand.
        4.  Definieren Sie für die SAP-Anwendungsschicht, ob Sie die Regressionstestsysteme für Ihr Unternehmen, die idealerweise Replikate Ihrer Produktionsbereitstellungen sind, in derselben Azure-Region oder in Ihrer Region für die Notfallwiederherstellung ausführen möchten. Im letzteren Fall können Sie dieses Geschäftsregressionssystem als Ziel für die Notfallwiederherstellung für Ihre Produktionssysteme festlegen.
        5.  Wenn Sie die Produktionssysteme nicht am Notfallwiederherstellungsstandort platzieren möchten, informieren Sie sich über Azure Site Recovery als mögliche Methode für die Replikation der SAP-Anwendungsschicht in die Azure-Region für die Notfallwiederherstellung. Lesen Sie auch [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap). 
        6.  Wenn Sie eine kombinierte Konfiguration für Hochverfügbarkeit und Notfallwiederherstellung mithilfe von [Azure-Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview) nutzen möchten, machen Sie sich mit den Azure-Regionen vertraut, in denen Verfügbarkeitszonen verfügbar sind, sowie mit den Einschränkungen, die durch höhere Netzwerklatenzen zwischen zwei Verfügbarkeitszonen entstehen können.  
3.  Kunden und Partner sollten einen Bestand aller SAP-Schnittstellen (SAP und nicht SAP) erstellen. 
4.  Entwurf der Foundation Services – dieser Entwurf enthält beispielsweise
    1.  Active Directory und DNS-Entwurf
    2.  Netzwerktopologie in Azure und Zuweisung der unterschiedlichen SAP-Systeme
    3.  Struktur für den [rollenbasierten Zugriff](https://docs.microsoft.com/azure/role-based-access-control/overview) für die einzelnen Teams, die die Infrastruktur und die SAP-Anwendungen in Azure verwalten
    3.  Topologie der Ressourcengruppe 
    4.  [Tagstrategie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Namenskonvention für virtuelle Computer und andere Infrastrukturkomponenten und/oder logische Namen
5.  Microsoft Premier Support-Vertrag – Identifizieren des MS Technical Account Managers (TAM). Informationen zu den Supportanforderungen von SAP finden Sie im SAP-Supporthinweis [#2015553](https://launchpad.support.sap.com/#/notes/2015553). 
6.  Definieren Sie die Anzahl der Azure-Abonnements und das Kernkontingent für die verschiedenen Abonnements. [Erstellen Sie ggf. Supportanfragen, um Kontingente von Azure-Abonnements zu erhöhen](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). 
7.  Plan zur Datenverringerung und -migration für die Migration von SAP-Daten zu Azure. Für SAP NetWeaver-Systeme stellt SAP Richtlinien zum Verringern des Volumens bei großen Datenmengen bereit. SAP hat diese [tief greifende Anleitung](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) zur Datenverwaltung in SAP-ERP-Systemen veröffentlicht. Ein Teil des Inhalts gilt jedoch auch ganz allgemein für NetWeaver- und S/4HANA-Systeme.
8.  Definieren Sie Ihren Ansatz zur automatisierten Bereitstellung. Ziel der Automatisierung bei Infrastrukturbereitstellungen in Azure ist die Umsetzung in einer deterministischen Weise mit festgelegten Ergebnissen. Viele Kunden verwenden PowerShell- oder CLI-basierte Skripts. Es gibt aber verschiedene Open-Source-Technologien, die zum Bereitstellen von Azure-Infrastruktur für SAP und sogar zum Installieren von SAP-Software verwendet werden können. Beispiele finden Sie auf GitHub:
    1.  [Automatisierte SAP-Bereitstellungen in der Azure-Cloud](https://github.com/Azure/sap-hana)
    2.  [SAP HANA-Installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Festlegen einer regelmäßigen Überprüfung von Entwurf und Bereitstellung zwischen Ihnen als Kunde, dem Systemintegrator, Microsoft und anderen beteiligten Parteien

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotphase (dringend empfohlen)
 
Ein Pilotprojekt kann vor oder während der Projektplanung und -vorbereitung ausgeführt werden. Während dieser Phase können auch verschiedene Ansätze und Entwürfe aus der Planungs- und Vorbereitungsphase getestet werden. Die Pilotphase kann auf ein echtes Proof of Concept erweitert werden. Es wird empfohlen, für die Pilotbereitstellung eine vollständige Lösung für Hochverfügbarkeit und Notfallwiederherstellung sowie den Sicherheitsentwurf einzurichten und zu überprüfen. In einigen Kundenfällen können in dieser Phase auch Skalierbarkeitstests durchgeführt werden. Andere Kunden nutzen die Bereitstellung von SAP-Sandboxsystemen als Pilotphase. Es wird also davon ausgegangen, dass Sie ein System ermittelt haben, das Sie zu Azure migrieren möchten, um ein Pilotsystem auszuführen.

1. Optimieren der Datenübertragung in Azure. Stark abhängig vom jeweiligen Kundenfall ist eine Übertragung über [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) von lokalen Standorten dann am schnellsten, wenn die Express-Verbindung über ausreichende Bandbreite verfügt. Bei anderen Kunden stellte sich die Nutzung des Internets als schneller heraus.
2. Im Fall einer Migration einer heterogenen SAP-Plattform mit einem Export und Import von Datenbankdaten sollten Sie die Export- und Importphasen testen und ggf. optimieren. Empfehlungen zu großen Migrationsvorgängen mit SQL Server als Zielplattform finden Sie [hier](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Sie können Migration Monitor/SWPM nutzen, falls Sie kein kombiniertes Releaseupdate und keinen [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)-Prozess benötigen, wenn Sie die Migration mit einem SAP-Releaseupgrade kombinieren und bestimmte Kombinationen aus Quell- und Ziel-DBMS-Plattform nutzen, die z.B. in [Database Migration Option (DMO) von SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152) dokumentiert sind. 
   1.  Leistung für das Exportieren in die Quelle, das Exportieren von Dateiuploads zu Azure und das Importieren.  Maximieren Sie die Überlappung zwischen Export und Import.
   2.  Bewerten des Volumens der Datenbank zwischen Quell- und Zielplattform, um dies bei der Größe der Infrastruktur zu berücksichtigen    
   3.  Überprüfen und Optimieren der zeitlichen Steuerung 
3. Technische Überprüfung 
   1. Arten von virtuellen Computern
      1.  Überprüfen Sie die Informationen in den SAP-Supporthinweisen, die Ressourcen im SAP HANA-Hardwareverzeichnis und SAP-PAM erneut, um sicherzustellen, dass keine Änderungen an den unterstützten virtuellen Computern für Azure, den unterstützten Betriebssystemreleases für diese VM-Typen und den unterstützten SAP- und DBMS-Releases vorgenommen wurden.
      2.  Überprüfen Sie erneut die Größe Ihrer Anwendung und die Infrastruktur, die Sie in Azure bereitstellen. Falls Sie vorhandene Anwendungen verschieben, leiten Sie die erforderlichen SAPS häufig über die verwendete Infrastruktur und die [Benchmark-Webseite von SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) ab und vergleichen sie mit der Anzahl von SAPS, die im SAP-Supporthinweis [#1928533](https://launchpad.support.sap.com/#/notes/1928533) aufgeführt wird. Beachten Sie auch [diesen Artikel](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208).
      3.  Bewerten und Testen der Größe Ihrer Azure-VMs in Bezug auf den maximalen Speicher- und Netzwerkdurchsatz der verschiedenen VM-Typen, die Sie in der Planungsphase ausgewählt haben. Sie finden diese Daten in:
          1.  [Größen für virtuelle Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Es ist wichtig, für die Größenanpassung den **maximalen Durchsatz des Datenträgers ohne Cache** zu berücksichtigen.
          2.  [Größen für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Es ist wichtig, für die Größenanpassung den **maximalen Durchsatz des Datenträgers ohne Cache** zu berücksichtigen.
   2. Storage
      1.  Verwenden Sie mindestens [Azure SSD-Standardspeicher](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) für VMs, die SAP-Anwendungsschichten darstellen, und für nicht leistungskritische DBMS-Bereitstellungen
      2.  Wir empfehlen allgemein nicht die Verwendung von [Azure HDD-Standarddatenträgern](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)
      2.  Verwenden Sie [Azure Storage Premium](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) für alle DBMS-VMs, die remote und leistungskritisch sind
      2.  Verwenden Sie [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).
      3.  Verwenden Sie Azure-Schreibbeschleunigung für die DBMS-Protokolllaufwerke mit M-Serie. Beachten Sie die Grenzwerte für die Schreibbeschleunigung und die Nutzung. Eine Beschreibung finden Sie unter [Schreibbeschleunigung](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
      4.  Überprüfen Sie für die verschiedenen DBMS-Typen die [allgemeine SAP-bezogene DBMS-Dokumentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) und die DBMS-spezifische Dokumentation, auf die das allgemeine Dokument verweist.
      5.  Weitere Details zu SAP HANA finden Sie unter [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
      6.  Binden Sie Azure-Datenträger niemals mithilfe der Geräte-ID auf einem virtuellen Azure-Linux-Computer ein. Verwenden Sie stattdessen den Universally Unique Identifier (UUID). Seien Sie vorsichtig, wenn Sie grafische Tools z. B. für das Einbinden von Azure-Datenträgern verwenden. Überprüfen Sie die Einträge in „/etc/fstab“ erneut, um sicherzustellen, dass die Datenträger mithilfe des UUID bereitgestellt sind.
          1.  Weitere Informationen finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Netzwerk
      1.  Testen und evaluieren Sie Ihre VNET-Infrastruktur und die Verteilung Ihrer SAP-Anwendungen auf die unterschiedlichen virtuellen Azure-Netzwerke.
          1.  Bewerten Sie den Ansatz einer Hub-Spoke-Architektur für Ihr virtuelles Netzwerk oder einer Mikrosegmentierung innerhalb eines einzelnen virtuellen Azure-Netzwerks basierend auf
              1.  Kosten für den Datenaustausch zwischen [Azure-VNETs mit Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Informationen zu den Kosten finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/).
              2.  Vorteil der schnellen Aufhebung des Peerings zwischen virtuellen Azure-Netzwerken im Vergleich zur Änderung der NSG, um ein Subnetz innerhalb eines virtuellen Netzwerks zu isolieren, falls Anwendungen oder virtuelle Computer, die in einem Subnetz des virtuellen Netzwerks gehostet werden, zu einem Sicherheitsrisiko werden
              3.  Zentrale Protokollierung und Überwachung des Netzwerkdatenverkehrs zwischen dem lokalen Standort und externen Systemen und des virtuellen Rechenzentrums, das Sie in Azure erstellt haben
          2.  Bewerten und testen Sie den Datenpfad zwischen der SAP-Anwendungsschicht und der SAP-DBMS-Schicht. 
              1.  Der Einsatz von [virtuellen Azure-Netzwerkgeräten](https://azure.microsoft.com/solutions/network-appliances/) im Kommunikationspfad zwischen der SAP-Anwendung und der DBMS-Schicht eines SAP NetWeaver-, Hybris- oder S/4HANA-basierten SAP-Systems wird nicht unterstützt.
              2.  Das Platzieren von SAP-Anwendungsschicht und SAP-DBMS in verschiedenen virtuellen Azure-Netzwerken, die nicht mittels Peering miteinander verknüpft sind, wird nicht unterstützt.
              3.  [Azure-ASG- und -NSG-Regeln](https://docs.microsoft.com/azure/virtual-network/security-overview) werden für das Definieren von Routen zwischen der SAP-Anwendungsschicht und der SAP-DBMS-Schicht unterstützt.
          3.  Stellen Sie sicher, dass auf den virtuellen Computern, die in der SAP-Anwendungsschicht und der SAP-DBMS-Schicht verwendet werden, der [beschleunigte Azure-Netzwerkbetrieb](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiviert ist. Beachten Sie, dass unterschiedliche Betriebssystemebenen benötigt werden, um den beschleunigten Netzwerkbetrieb in Azure zu unterstützen:
              1.  Windows Server 2012 R2 oder neuere Releases
              2.  SUSE Linux 12 SP3 oder neuere Releases
              3.  RHEL 7.4 oder neuere Releases
              4.  Oracle Linux 7.5. Bei Verwendung des RHCKL-Kernels muss Release 3.10.0-862.13.1.el7 verwendet werden. Bei Verwendung des Oracle UEK-Kernels ist Release 5 erforderlich.
          4.   Testen und evaluieren Sie die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs gemäß SAP-Supporthinweis [#500235](https://launchpad.support.sap.com/#/notes/500235) und SAP Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren bis guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen, wie [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) beschrieben.
          5.   Stellen Sie sicher, dass ILB-Bereitstellungen so eingerichtet wurden, dass sie Direct Server Return verwenden. Diese Einstellung verringert die Latenz, wenn Azure-ILBs für Konfigurationen mit Hochverfügbarkeit auf der DBMS-Schicht verwendet werden
          6.   Wenn Sie Azure Load Balancer in Verbindung mit Linux-Gastbetriebssystemen verwenden, überprüfen Sie, ob der Linux-Netzwerkparameter **net.ipv4.tcp_timestamps** auf **0** festgelegt ist. Entgegen den Empfehlungen in älteren Versionen von SAP-Hinweis [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Der SAP-Hinweis wurde mittlerweile aktualisiert und trägt jetzt dem Umstand Rechnung, dass der Parameter auf 0 festgelegt sein muss, um in Verbindung mit Azure Load Balancern zu funktionieren.
   4. Bereitstellungen mit Hochverfügbarkeit und Notfallwiederherstellung 
      1. Wenn Sie die SAP-Anwendungsschicht bereitstellen, ohne eine bestimmte Azure-Verfügbarkeitszone zu definieren, stellen Sie sicher, dass alle virtuellen Computer, auf denen SAP-Dialoginstanzen oder Middlewareinstanzen eines einzelnen SAP-System ausgeführt werden, in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) bereitgestellt werden. 
         1.   Für den Fall, dass Sie für die SAP Central Services und das DBMS keine Hochverfügbarkeit benötigen, können diese virtuellen Computer in derselben Verfügbarkeitsgruppe wie die SAP-Anwendungsschicht bereitgestellt werden.
      2. Wenn Sie die SAP Central Services- und die DBMS-Schicht für Hochverfügbarkeit durch passive Replikate schützen, müssen sich die beiden Knoten für SAP Central Services in einer separaten Verfügbarkeitsgruppe und die beiden DBMS-Knoten in einer anderen Verfügbarkeitsgruppe befinden.
      3. Wenn die Bereitstellung in Azure-Verfügbarkeitszonen erfolgt, können Sie keine Verfügbarkeitsgruppen nutzen. Sie müssten jedoch sicherstellen, dass Sie die aktiven und passiven Central Services-Knoten in zwei verschiedenen Verfügbarkeitszonen mit der kleinsten Latenz zwischen Zonen bereitstellen.
         1.   Denken Sie daran, dass Sie den [Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) verwenden müssen, falls Sie Windows- oder Pacemaker-Failovercluster für die DBMS- und SAP Central Services-Schicht über Verfügbarkeitszonen hinweg erstellen. [Load Balancer Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kann nicht für zonale Bereitstellungen verwendet werden. 
   5. Timeouteinstellungen
      1. Überprüfen Sie die SAP NetWeaver-Entwicklernachverfolgung von unterschiedlichen SAP-Instanzen, und stellen Sie sicher, dass keine Verbindungsunterbrechungen zwischen dem Warteschlangenserver und den SAP-Arbeitsprozessen gemeldet wurden. Diese Verbindungsunterbrechungen können vermieden werden, indem Sie diese beiden Registrierungsparameter festlegen:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – siehe [dieser Artikel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – siehe [dieser Artikel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Um GUI-Timeouts zwischen lokal bereitgestellten grafischen SAP-Benutzeroberflächen und in Azure bereitgestellten SAP-Anwendungsschichten zu vermeiden, überprüfen Sie, ob die folgenden Parameter in der Datei „default.pfl“ oder im Profil für die Instanz festgelegt wurden:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Wenn Sie eine Konfiguration mit Windows-Failovercluster verwenden, stellen Sie sicher, dass die Zeit zum Reagieren auf nicht reagierende Knoten für Azure richtig festgelegt ist. Im Microsoft-Artikel [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) (Optimieren der Netzwerkschwellenwerte für Failovercluster) werden die Parameter und ihre Auswirkungen auf Failover aufgeführt. Davon ausgehend, dass sich die Clusterknoten im selben Subnetz befinden, sollten die folgenden Parameter geändert werden:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Testen Ihrer Verfahren für Hochverfügbarkeit und Notfallwiederherstellung
   1. Simulieren Sie Failoversituationen, indem Sie virtuelle Computer (Windows-Gastbetriebssystem) herunterfahren oder Betriebssysteme in den Panikmodus (Linux-Gastbetriebssystem) versetzen, um herauszufinden, ob die Failoverkonfigurationen erwartungsgemäß funktionieren. 
   2. Messen Sie die benötigten Zeiten zum Ausführen eines Failovers. Wenn Sie die Zeiten zu lang sind, erwägen Sie Folgendes:
      1.   Verwenden Sie für SUSE Linux SBD-Geräte anstelle des Azure Fencing-Agents zur Beschleunigung des Failovers.
      2.   Wenn bei SAP HANA das erneute Laden von Daten zu lange dauert, erwägen Sie die Bereitstellung zusätzlicher Speicherbandbreite.
   3. Testen Sie die Sicherungs-/Wiederherstellungssequenz sowie die zeitliche Steuerung, und optimieren Sie sie bei Bedarf. Stellen Sie sicher, dass nicht nur Sicherungszeiten ausreichend sind. Testen Sie auch die Wiederherstellung, und messen Sie die Zeit für Wiederherstellungsaktivitäten. Stellen Sie sicher, dass die Wiederherstellungszeiten innerhalb Ihrer RTO-SLAs liegen, wenn Ihre RTO auf dem Wiederherstellungsprozess für einen virtuellen Computer oder eine Datenbank basiert.
   4. Testen Sie die Funktion und Architektur der regionsübergreifenden Notfallwiederherstellung.
5. Sicherheitsüberprüfungen
   1.  Testen Sie die Gültigkeit der Azure-Architektur für den rollenbasierten Zugriff (RBAC), die Sie implementiert haben. Ziel ist es, den Zugriff und die Berechtigungen der verschiedenen Teams zu trennen und zu beschränken. Beispiel: SAP-Basisteammitglieder sollten in der Lage sein, virtuelle Computer bereitzustellen und Datenträger aus Azure Storage in einem angegebenen virtuellen Azure-Netzwerk zuzuweisen. Jedoch sollte das SAP-Basisteam keine eigenen virtuellen Netzwerke erstellen oder die Einstellungen vorhandener virtueller Netzwerke ändern können. Andererseits sollten Mitglieder des Netzwerkteams nicht berechtigt sein, virtuelle Computer in virtuellen Netzwerken bereitzustellen, in denen VMs für die SAP-Anwendung und das DBMS ausgeführt werden. Auch sollten Mitglieder des Netzwerkteams nicht berechtigt sein, Attribute von virtuellen Computern zu ändern oder VMs oder Datenträger zu löschen.  
   2.  Überprüfen Sie, ob die [NSG- und ASC-Regeln](https://docs.microsoft.com/azure/virtual-network/security-overview) wie erwartet funktionieren, und schirmen Sie die geschützten Ressourcen ab.
   3.  Stellen Sie sicher, dass alle Ressourcen, die verschlüsselt werden müssen, auch verschlüsselt werden. Definieren Sie Prozesse zum Sichern von Zertifikaten, zum Speichern und Zugreifen auf diese Zertifikate und zum Wiederherstellen der verschlüsselten Entitäten, und führen Sie sie aus. 
   4.  Verwenden Sie [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) für Betriebssystem-Datenträger, sofern dies aus Sicht der Betriebssystemunterstützung möglich ist.
   5.  Vergewissern Sie sich, dass nicht zu viele Ebenen der Verschlüsselung verwendet wurden. Es ist teilweise sinnvoll, Azure Disk Encryption zu verwenden und zusätzlich eine der Methoden der Transparent Data Encryption (TDE) für das DBMS einzusetzen.
6. Leistungstests
   1.  Vergleichen Sie in SAP ggf. basierend auf SAP-Ablaufverfolgung und -Messungen die wichtigsten 10 Onlineberichte zur aktuellen Implementierung. 
   2.  Vergleichen Sie in SAP ggf. basierend auf SAP-Ablaufverfolgung und -Messungen die wichtigsten 10 Batchaufträge zur aktuellen Implementierung. 
   3.  Vergleichen Sie in SAP basierend auf SAP-Ablaufverfolgung und -Messungen die Datenübertragungen über Schnittstellen im SAP-System. Konzentrieren Sie sich auf Schnittstellen, von denen Sie wissen, dass die Übertragung zwischen verschiedenen Standorten erfolgt (z.B. von lokal zu Azure). 


## <a name="non-production-phase"></a>Nicht-Produktionsphase 
In dieser Phase wird davon ausgegangen, dass Sie nach einem erfolgreichen Pilotprojekt oder einem PoC damit beginnen, SAP-Systeme, die nicht für die Produktion vorgesehen sind, in Azure bereitzustellen. Alle Erkenntnisse und Erfahrungen aus den Proof of Concepts sollten auf eine solche Bereitstellung angewandt werden. Bei dieser Art von Bereitstellungen gelten außerdem alle Kriterien und Schritte, die im PoC aufgeführt sind. In dieser Phase stellen Sie in der Regel Entwicklungssysteme, Komponententestsysteme und Regressionstestsysteme in Azure bereit. Es wird empfohlen, dass für mindestens ein Nicht-Produktionssystem einer SAP-Anwendungsreihe die gesamte Hochverfügbarkeit konfiguriert wurde, die auch für das zukünftige Produktionssystem gelten wird. Weitere Schritte, die Sie während dieser Phase berücksichtigen sollten:  

1.  Sammeln Sie vor dem Verschieben von Systemen von der alten Plattform zu Azure Ressourcenverbrauchsdaten wie CPU-Auslastung und Speicherdurchsatz sowie IOPS-Daten. Dies gilt insbesondere für Einheiten der DBMS-Schicht, aber auch für Einheiten der Anwendungsschicht. Messen Sie außerdem die Latenz von Netzwerk und Speicher.
2.  Notieren Sie sich Zeitmuster der durch die Systeme verbrauchten Verfügbarkeit. Ziel ist es, herauszufinden, ob Nicht-Produktionssysteme rund um die Uhr verfügbar sein müssen oder während bestimmter Phasen einer Woche oder eines Monats heruntergefahren werden können
3.  Testen und definieren Sie, ob Sie eigene Betriebssystemimages für Ihre VMs in Azure erstellen oder ein Image aus dem Azure-Imagekatalog verwenden möchten. Wenn Sie ein Image aus dem Azure-Katalog verwenden, stellen Sie sicher, dass Sie ein Image auswählen, das dem Supportvertrag mit Ihrem Betriebssystemanbieter entspricht. Für einige Betriebssystemanbieter bietet der Azure-Katalog die Möglichkeit, eigene Lizenzimages zu nutzen. Für andere Betriebssystemimages ist die Unterstützung bereits im Preis für Azure enthalten. Wenn Sie eigene Betriebssystemimages erstellen möchten, lesen Sie die Dokumentation in den folgenden Artikeln:
    1.  Sie können [diese Dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) nutzen, um ein generalisiertes Image einer Windows-VM, die in Azure bereitgestellt wurde, zu erstellen.
    2.  Sie können [diese Dokumentation](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) nutzen, um ein generalisiertes Image einer Linux-VM, die in Azure bereitgestellt wurde, zu erstellen.
3.  Wenn Sie SUSE- und Red Hat Linux-Images aus dem Azure-VM-Katalog verwenden, müssen Sie die von den Linux-Anbietern für SAP bereitgestellten Images im Azure-VM-Katalog verwenden.
4.  Stellen Sie sicher, dass Sie die Supportanforderungen von SAP in Bezug auf Microsoft-Supportverträge erfüllen. Informationen finden Sie im SAP-Supporthinweis [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Für HANA (große Instanzen) lesen Sie das Dokument [Onboardinganforderungen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Stellen Sie sicher, dass die richtigen Personen [Benachrichtigungen zu geplanten Wartungen](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) erhalten, sodass Sie die Ausfallzeit auswählen und einen rechtzeitigen Neustart der virtuellen Computer festlegen können.
5.  Überprüfen Sie regelmäßig die Azure-Dokumentation auf Microsoft-Präsentationen über Kanäle wie [Channel9](https://channel9.msdn.com/) zu neuen Funktionen, die möglicherweise für Ihre Bereitstellungen anwendbar sind.
6.  Lesen Sie die SAP-Hinweise im Zusammenhang mit Azure, z.B. den Supporthinweis [#1928533](https://launchpad.support.sap.com/#/notes/1928533) zu neuen VM-SKUs und neuen unterstützten Betriebssystem- und DBMS-Releases. Vergleichen Sie die Preise für neue VM-Typen mit denen für ältere, damit Sie virtuelle Computer mit dem besten Preis-Leistungs-Verhältnis bereitstellen können.
7.  Überprüfen Sie die Informationen in den SAP-Supporthinweisen, die Ressourcen im SAP HANA-Hardwareverzeichnis und SAP-PAM erneut, um sicherzustellen, dass keine Änderungen an den unterstützten virtuellen Computern für Azure, den unterstützten Betriebssystemreleases für diese VMs und den unterstützten SAP- und DBMS-Releases vorgenommen wurden.
8.  Überprüfen Sie [hier](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), ob es neue für HANA zertifizierte SKUs in Azure gibt, und vergleichen Sie die Preise mit den geplanten Werten. Nehmen Sie ggf. Änderungen vor, um die Einheiten mit besserem Preis-Leistungs-Verhältnis zu nutzen. 
9.  Passen Sie die Bereitstellungsskripts so an, dass neuere VM-Typen und neue Funktionen von Azure, die Sie verwenden möchten, verfügbar sind.
10. Testen und evaluieren Sie nach der Bereitstellung der Infrastruktur die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs gemäß SAP-Supporthinweis [#500235](https://launchpad.support.sap.com/#/notes/500235) und SAP Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren bis guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen – wie [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) beschrieben. Stellen Sie sicher, dass für Ihre Bereitstellung keine der Einschränkungen in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) und [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) gelten.
11. Führen Sie vor dem Anwenden der Workload alle anderen Überprüfungen durch, die für die Pilotphase (Proof of Concept) aufgeführt werden.
12. Zeichnen Sie nach der Anwendung der Workload den Ressourcenverbrauch dieser Systeme in Azure auf, und vergleichen Sie ihn mit den Daten, die Sie von der alten Plattform erhalten haben. Passen Sie die VM-Größe für zukünftige Bereitstellungen an, wenn Sie größere Unterschiede feststellen. Beachten Sie, dass bei einer Verkleinerung auch die Speicher- und Netzwerkbandbreiten eines virtuellen Computers verringert werden:
    1.  [Größen für virtuelle Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Größen für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Passen Sie die Systemfunktionen und -prozesse für das Kopieren an. Ziel ist es, das Kopieren eines Entwicklungssystems oder eines Testsystems so einfach wie möglich zu machen, damit die Projektteams schnell neue Systeme erstellen können. Erwägen Sie [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) als Tool für solche Aufgaben.
14. Optimieren und verfeinern Sie den rollenbasierten Zugriff, die Berechtigungen und die Prozesse Ihres Teams in Azure, um die Aufgaben klar voneinander abzugrenzen. Anderseits sollen alle Teams in der Lage sein, ihre Aufgaben in der Azure-Infrastruktur auszuführen.
15. Überprüfen, testen und dokumentieren Sie die Verfahren für Hochverfügbarkeit und Notfallwiederherstellung, damit Ihre Mitarbeiter diese Aufgaben ausführen können. Identifizieren Sie Mängel, und übernehmen Sie neue Azure-Funktionen in Ihre Bereitstellungen

 
## <a name="production-preparation-phase"></a>Vorbereitungsphase für die Produktion 
In dieser Phase sammeln Sie alle Erfahrungen und Erkenntnisse Ihrer Nichtproduktionsbereitstellungen, um sie auf zukünftige Produktionsbereitstellungen anzuwenden. Zusätzlich zu vorherigen Phasen müssen Sie außerdem auch die Datenübertragung zwischen dem aktuellen Hostingstandort und Azure vorbereiten. 

1.  Überprüfen Sie vor dem Verschieben nach Azure die erforderlichen SAP-Releaseupgrades Ihrer Produktionssysteme.
2.  Stimmen Sie sich mit den Geschäftsinhabern zu den Funktions- und Geschäftstests, die nach der Migration des Produktionssystems durchgeführt werden müssen, ab.
    1.  Stellen Sie sicher, dass alle diese Tests mit den Quellsystemen am aktuellen Hostingstandort ausgeführt werden. Sie sollten vermeiden, dass Tests erst nach dem Verschieben des Systems in Azure zum ersten Mal durchgeführt werden.
2.  Testen Sie die Produktionsmigration zu Azure. Falls Sie nicht alle Produktionssysteme gleichzeitig nach Azure verschieben, erstellen Sie Gruppen von Produktionssystemen, die am gleichen Standort gehostet werden müssen. Überprüfen und testen Sie die Datenmigration. Häufig angewandte Methoden:
    1.  Verwenden Sie DBMS-Methoden wie Sicherung/Wiederherstellung in Verbindung mit SQL Server Always On, HANA-Systemreplikation oder Protokollversand, um Datenbankinhalte in Azure anzulegen und zu synchronisieren.
    2.  Verwenden Sie Sicherung/Wiederherstellung für kleinere Datenbanken.
    3.  Verwenden Sie SAP Migration Monitor für das Implementieren im SAP SWPM-Tool für heterogene Migrationsvorgänge.
    4.  Verwenden Sie den [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)-Prozess, wenn Sie gleichzeitig eine Aktualisierung der SAP-Release durchführen müssen. Bedenken Sie, dass nicht alle Kombinationen von Quell- und Ziel-DBMS unterstützt werden. Weitere Informationen finden Sie in den SAP-Supporthinweisen zu den verschiedenen DMO-Releases, z.B. [Database Migration Option (DMO) für SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    5.  Testen Sie, ob die Datenübertragung über das Internet oder über ExpressRoute einen besseren Durchsatz bietet, falls Sie Sicherungen oder SAP-Exportdateien verschieben müssen. Für die Datenübertragung über das Internet müssen möglicherweise einige Ihrer NSG/ASG-Sicherheitsregeln geändert werden, die Sie für zukünftige Produktionssysteme benötigen.
3.  Sammeln Sie vor dem Verschieben von Systemen von der alten Plattform zu Azure Ressourcenverbrauchsdaten wie CPU-Auslastung und Speicherdurchsatz sowie IOPS-Daten. Dies gilt insbesondere für Einheiten der DBMS-Schicht, aber auch für Einheiten der Anwendungsschicht. Messen Sie außerdem die Latenz von Netzwerk und Speicher.
4.  Überprüfen Sie die Informationen in den SAP-Supporthinweisen, die Ressourcen im SAP HANA-Hardwareverzeichnis und SAP-PAM erneut, um sicherzustellen, dass keine Änderungen an den unterstützten virtuellen Computern für Azure, den unterstützten Betriebssystemreleases für diese VMs und den unterstützten SAP- und DBMS-Releases vorgenommen wurden. 
4.  Passen Sie Bereitstellungsskripts an die neuesten Änderungen bei den VM-Typen und den Azure-Funktionen an.
5.  Führen Sie nach der Bereitstellung von Infrastruktur und Anwendung eine Reihe von Überprüfungen durch:
    1.  Die richtigen VM-Typen wurden mit den korrekten Attributen und Speichergrößen bereitgestellt.
    2.  Auf den virtuellen Computern sind einheitlich die richtigen und gewünschten Betriebssystemreleases und Patches vorhanden.
    3.  Die Härtung der virtuellen Computer entspricht den Anforderungen und ist einheitlich.
    4.  Die richtigen Anwendungsreleases und Patches wurden installiert und bereitgestellt.
    5.  Die virtuellen Computer wurden wie geplant in Azure-Verfügbarkeitsgruppen bereitgestellt.
    6.  Azure Storage Premium wurde für Datenträger mit spezifischen Anforderungen an die Latenz oder mit einer [SLA für Einzelinstanzcomputer von 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) verwendet.
    7.  Die Azure-Schreibbeschleunigung wurde richtig bereitgestellt.
        1.  Die Speicherplätze in den VMs oder die Stripesets für Datenträger, die Unterstützung für die Azure-Schreibbeschleunigung benötigen, wurden korrekt erstellt.
            1.  Lesen Sie [Konfigurieren von Software-RAID unter Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
            2.  Lesen Sie [Konfigurieren von LVM auf einem virtuellen Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    8.  Es wurde ausschließlich [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwendet.
    9.  Die VMs wurden in den richtigen Verfügbarkeitsgruppen und -zonen bereitgestellt.
    10. Auf den virtuellen Computern, die in der SAP-Anwendungsschicht und der SAP-DBMS-Schicht verwendet werden, wurde der [beschleunigte Azure-Netzwerkbetrieb](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiviert.
    11. Es wurden keine [virtuellen Azure-Netzwerkgeräte](https://azure.microsoft.com/solutions/network-appliances/) im Kommunikationspfad zwischen der SAP-Anwendung und der DBMS-Schicht eines SAP NetWeaver-, Hybris- oder S/4HANA-basierten SAP-Systems platziert.
    12. ASG- und NSG-Regeln erlauben die Kommunikation wie gewünscht und geplant und blockieren die Kommunikation, wo dies erforderlich ist.
    13. Die weiter oben beschriebenen Timeouteinstellungen wurden ordnungsgemäß festgelegt.
    14. Testen und evaluieren Sie die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs gemäß SAP-Supporthinweis [#500235](https://launchpad.support.sap.com/#/notes/500235) und SAP Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren bis guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen, wie [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) beschrieben.
    15. Die Verschlüsselung wurde, soweit erforderlich, mit der gewünschten Verschlüsselungsmethode bereitgestellt.
    16. Schnittstellen und andere Anwendungen können eine Verbindung mit der neu bereitgestellten Infrastruktur herstellen.
6.  Erstellen Sie ein Playbook für das Reagieren auf die geplante Wartung in Azure. Definieren Sie die Reihenfolge des Neustarts der Systeme und VMs bei einer geplanten Wartung.
    

## <a name="go-live-phase"></a>Aktivierungsphase
In der Aktivierungsphase sollten Sie unbedingt den Playbooks folgen, die Sie in früheren Phasen entwickelt haben. Führen Sie die Schritte aus, die Sie getestet und trainiert haben. Lassen Sie keine kurzfristigen Änderungen an Konfigurationen und Prozessen mehr zu. Führen Sie darüber hinaus die folgenden Schritte aus:

1. Vergewissern Sie sich, dass die Überwachung im Azure-Portal und andere Überwachungstools funktionieren.  Empfohlene Tools sind Perfmon (Windows) und SAR (Linux): 
    1.  CPU-Indikatoren 
        1.  Durchschnittliche CPU-Auslastung – gesamt (alle CPUs)
        2.  Durchschnittliche CPU-Auslastung – jeder einzelne Prozessor (bei m128-VMs also 128 Prozessoren)
        3.  CPU-Auslastung; Kernelzeit – jeder einzelne Prozessor
        4.  CPU-Auslastung; Benutzerzeit – jeder einzelne Prozessor
    5.  Arbeitsspeicher 
        1.  Freier Arbeitsspeicher
        2.  Seite eingehend/s
        3.  Seite ausgehend/s
    4.  Datenträger 
        1.  Datenträgerlesevorgänge KB/s – pro Datenträger 
        2.  Datenträgerlesevorgänge/s – pro Datenträger
        3.  Datenträgerlesevorgänge ms/Lesevorgang – pro Datenträger
        4.  Datenträgerschreibvorgänge KB/s – pro Datenträger 
        5.  Datenträgerschreibvorgänge/s – pro Datenträger
        6.  Datenträgerschreibvorgänge ms/Schreibvorgang – pro Datenträger
    5.  Netzwerk 
        1.  Netzwerkpakete eingehend/s
        2.  Netzwerkpakete ausgehend/s
        3.  Netzwerk KB eingehend/s
        4.  Netzwerk KB ausgehend/s 
2.  Führen Sie nach der Migration der Daten alle Validierungstests durch, die Sie mit den Geschäftsinhabern vereinbart haben. Akzeptieren Sie nur Validierungstestergebnisse, für die Sie über Ergebnisse für die ursprünglichen Quellsysteme verfügen.
3.  Überprüfen Sie, ob die Schnittstellen funktionieren und ob andere Anwendungen mit den neu bereitgestellten Produktionssystemen kommunizieren können.
4.  Überprüfen Sie das Transport- und Korrektursystem über Transaktions-STMS für SAP.
5.  Führen Sie Datenbanksicherungen aus, nachdem das System für die Produktion freigegeben wurde.
6.  Führen Sie VM-Sicherungen für die virtuellen Computer auf der SAP-Anwendungsschicht aus, nachdem das System für die Produktion freigegeben wurde.
7.  Für SAP-Systeme, die nicht Bestandteil der aktuellen Aktivierungsphase sind, aber mit den SAP-Systemen, die Sie in dieser Aktivierungsphase nach Azure verschoben haben, kommunizieren, müssen Sie den Hostnamenpuffer in SM51 zurücksetzen. Mit diesem Schritt löschen Sie alte zwischengespeicherte IP-Adressen, die den Namen der nach Azure verschobenen Anwendungsinstanzen zugeordnet sind.  


## <a name="post-production"></a>Nachbearbeitung
In dieser Phase geht es um Überwachung, Betrieb und Verwaltung des Systems. Aus SAP-Sicht sind dies die gleichen üblichen Aufgaben, die Sie auch an Ihrem alten Hostingstandort ausführen mussten. Zu den Azure-spezifischen Aufgaben gehören:

1. Analysieren von Azure-Rechnungen, um Systeme mit hohen Gebühren zu ermitteln
2. Optimieren des Preis-Leistungs-Verhältnisses auf Seiten der virtuellen Computer und des Speichers
3. Optimieren der Zeiten, zu denen Systeme heruntergefahren werden können  


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Dokumentation:

- [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

