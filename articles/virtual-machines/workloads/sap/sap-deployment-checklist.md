---
title: Prüfliste für die Planung und Bereitstellung von SAP-Workloads | Microsoft-Dokumentation
description: Prüfliste für die Planung der Bereitstellung von SAP-Workloads in Azure und Bereitstellen der Workloads
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06d20dd47ceb71b51e226d662892eab06b072685
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500936"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure

Diese Prüfliste ist für Kunden vorgesehen, die SAP NetWeaver-, S/4HANA- und Hybris-Anwendungen nach Azure-Infrastructure-as-a-Service verschieben. Während der gesamten Projektdauer sollte diese Prüfliste von einem Partner des Kunden und/oder SAP-Partner überprüft werden. Es ist wichtig festzuhalten, dass viele der Überprüfungen am Anfang des Projekts und in der Planungsphase durchgeführt werden. Nach Abschluss der Bereitstellung können eigentlich geradlinige Änderungen an bereitgestellter Azure-Infrastruktur oder SAP-Softwarereleases äußerst komplex werden.

Überprüfen Sie die Prüfliste während Ihres Projekts bei Erreichen wichtiger Meilensteine. Auf diese Weise können Sie kleine Probleme erkennen, bevor sie zu großen Problemen werden. Außerdem gewinnen Sie dadurch die erforderliche Zeit, um alle notwendigen Änderungen neu zu entwickeln und zu testen. Betrachten Sie diese Prüfliste nicht als abgeschlossen. Je nach Ihrer Situation müssen Sie möglicherweise noch viele weitere Überprüfungen durchführen.

Die Prüfliste umfasst keine Aufgaben, die nicht mit Azure zusammenhängen. Beispielsweise ändern sich die Schnittstellen von SAP-Anwendungen während des Umzugs auf die Azure-Plattform oder zu einem Hostinganbieter.

Diese Prüfliste kann auch auf Systeme angewandt werden, die bereits bereitgestellt wurden. Möglicherweise wurden seit Ihrer Bereitstellung neue Funktionen wie die Schreibbeschleunigung, Verfügbarkeitszonen und neue VM-Typen hinzugefügt. Daher ist es sinnvoll, die Prüfliste in regelmäßigen Abständen durchzugehen, um neue Funktionen der Azure-Plattform umsetzen zu können.

## <a name="project-preparation-and-planning-phase"></a>Projektvorbereitungs- und -planungsphase
In dieser Phase planen Sie die Migration Ihrer SAP-Workload zur Azure-Plattform. In dieser Phase müssen Sie zumindest die folgenden Dokumente erstellen und folgende Elemente der Migration definieren und erörtern:

1. Allgemeines Entwurfsdokument. Dieses Dokument sollte Folgendes enthalten:
    - Den aktuellen Bestand an SAP-Komponenten und -Anwendungen und einen Zielbestand an Anwendungen für Azure
    - Eine Matrix der Zuständigkeiten (RACI), in der die Zuständigkeiten und Aufgaben der verschiedenen Beteiligten definiert sind. Beginnen Sie auf der allgemeinen Ebene, und arbeiten Sie sich bei der Planung und den ersten Bereitstellungen nach und nach zu den Detailebenen vor.
    - Eine allgemeine Lösungsarchitektur
    - Eine Entscheidung zu den Azure-Regionen, in denen die Bereitstellung erfolgen soll. Weitere Informationen finden Sie in der [Liste der Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/). Unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) erfahren Sie, welche Dienste in welcher Region verfügbar sind.
    - Eine Netzwerkarchitektur für Verbindungen von lokalen Standorten zu Azure. Beginnen Sie damit, sich mit der [Blaupause virtueller Rechenzentren für Azure](/azure/architecture/vdc/) vertraut zu machen.
    - Sicherheitsprinzipien für die Ausführung geschäftskritischer Daten in Azure. Wenn Sie mehr über die Datensicherheit erfahren möchten, beginnen Sie mit der [Azure-Sicherheitsdokumentation](../../../security/index.yml).
2.  Technisches Entwurfsdokument. Dieses Dokument sollte Folgendes enthalten:
    - Ein Blockdiagramm für die Lösung.
    - Die Dimensionierung von Compute-, Speicher- und Netzwerkkomponenten in Azure. Informationen von SAP zur Dimensionierung von Azure-VMs finden Sie im [SAP- 
    -  Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - BCDR-Architektur (Business Continuity & Disaster Recovery, Geschäftskontinuität und Notfallwiederherstellung)
    - Ausführliche Informationen zu den Supportpaketversionen für Betriebssystem, Datenbank, Kernel und SAP. Es kann nicht unbedingt davon ausgegangen werden, dass jede von SAP NetWeaver oder S/4HANA unterstützte Betriebssystemversion auch von Azure-VMs unterstützt wird. Gleiches gilt auch für DBMS-Versionen. Überprüfen Sie die folgenden Quellen, um SAP-Versionen, DMBS-Versionen und Betriebssystemversionen aneinander auszurichten und ggf. zu aktualisieren, um Unterstützung von SAP und in Azure sicherzustellen. Sie benötigen Versionskombinationen, die von SAP und Azure unterstützt werden, um vollständige Unterstützung von SAP und Microsoft zu erhalten. Ggf. müssen Sie ein Upgrade einiger Softwarekomponenten einplanen. Weitere Einzelheiten zur unterstützten SAP-, Betriebssystem- und DBMS-Software sind hier dokumentiert:
        - [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533). In diesem Hinweis wird die minimale Betriebssystemversion definiert, die auf Azure-VMs unterstützt wird. Außerdem sind die Mindestanforderungen für die Datenbankversionen definiert, die für die meisten Nicht-HANA-Datenbanken erforderlich sind. Schließlich sind die SAP-Angaben zur Dimensionierung für die von SAP unterstützten Azure-VM-Typen angegeben.
        - [SAP-Supporthinweis Nr. 2015553](https://launchpad.support.sap.com/#/notes/2015553). In diesem Hinweis werden die Richtlinien zur Unterstützung von Azure Storage und die für Microsoft benötigte Supportbeziehung definiert.
        - [SAP-Supporthinweis Nr. 2039619](https://launchpad.support.sap.com/#/notes/2039619). In diesem Hinweis ist die Oracle-Unterstützungsmatrix für Azure definiert. Oracle unterstützt nur Windows und Oracle Linux als Gastbetriebssysteme auf Azure für SAP-Workloads. Dieser Supporthinweis gilt ebenfalls für die SAP-Anwendungsschicht, in der die SAP-Instanzen ausgeführt werden. Oracle unterstützt jedoch keine Hochverfügbarkeit für SAP Central Services in Oracle Linux über Pacemaker. Wenn Sie Hochverfügbarkeit für ASCS in Oracle Linux benötigen, müssen Sie die SIOS Protection Suite für Linux verwenden. Ausführliche SAP-Zertifizierungsdaten finden Sie im SAP-Supporthinweis [#1662610 - Support details for SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610) (Nr. 1662610: Details zur Unterstützung der SIOS Protection Suite für Linux). Für Windows wird die von SAP unterstützte Windows Server-Failoverclusterlösung für SAP Central Services in Verbindung mit Oracle als DBMS-Ebene unterstützt.
        - [SAP-Supporthinweis Nr. 2235581](https://launchpad.support.sap.com/#/notes/2235581). Dieser Hinweis enthält die Unterstützungsmatrix für SAP HANA in verschiedenen Betriebssystemversionen.
        - Von SAP HANA unterstützte Azure-VMs und [HANA (große Instanzen)](./hana-overview-architecture.md) sind auf der [SAP-Website](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) aufgeführt.
        - [SAP-Produktverfügbarkeitsmatrix [Product Availability Matrix, PAM]](https://support.sap.com/en/).
        - [SAP-Supporthinweis Nr. 2555629 – SAP HANA 2.0 Dynamic Tiering – Hypervisor und Cloudunterstützung](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP-Supporthinweis Nr. 1662610 – Supportdetails zu SIOS Protection Suite für Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-Hinweise zu anderen SAP-spezifischen Produkten     
    - Die Verwendung von Clusterkonfigurationen mit mehreren SIDs für SAP Central Services wird auf Azure in Windows-, SLES- und RHEL-Gastbetriebssystemen unterstützt. Beachten Sie, dass der Auswirkungsgrad zunehmen kann, je mehr ASCS/SCS Sie auf einem solchen Multi-SID-Cluster platzieren. In diesen Artikeln finden Sie Dokumentation zum Szenario mit dem jeweiligen Gastbetriebssystem:
        - [Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Multi-SID-Hochverfügbarkeitsleitfaden für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse-multi-sid.md)
        - [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](./high-availability-guide-rhel-multi-sid.md)
    - Hochverfügbarkeits- und Notfallwiederherstellungs-Architektur
        - Definieren Sie auf der Grundlage von RTO und RPO, wie die Architektur für Hochverfügbarkeit und Notfallwiederherstellung aussehen muss.
        - Überprüfen Sie für Hochverfügbarkeit innerhalb einer Zone, welche Funktionen das gewünschte DBMS in Azure bietet. Die meisten DBMS-Pakete bieten synchrone Methoden eines synchronen unmittelbar betriebsbereiten Standbyservers, die für Produktionssysteme empfohlen werden. Lesen Sie auch die SAP-bezogene Dokumentation zu den verschiedenen Datenbanken, angefangen mit [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](./dbms_guide_general.md) und zugehörigen Dokumenten.
           Die Verwendung von Windows Server-Failoverclustern mit freigegebenen Datenträgern als DBMS-Schicht, wie z.B. [für SQL Server beschrieben](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), wird NICHT unterstützt. Verwenden Sie stattdessen Lösungen wie
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [HANA-Systemreplikation](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Um Informationen zur Notfallwiederherstellung über die Grenzen von Azure-Regionen hinweg zu erhalten, prüfen Sie die von den verschiedenen DBMS-Anbietern angebotenen Lösungen. Die meisten unterstützen die asynchrone Replikation oder den Protokollversand.
        - Bestimmen Sie für die SAP-Anwendungsschicht, ob Sie die Regressionstestsysteme für Ihr Unternehmen, die idealerweise Replikate Ihrer Produktionsbereitstellungen sind, in derselben Azure-Region oder in Ihrer Region für die Notfallwiederherstellung ausführen möchten. Im zweiten Fall können Sie dieses Geschäftsregressionssystem als Ziel für die Notfallwiederherstellung für Ihre Produktionsbereitstellungen festlegen.
        - Wenn Sie die Produktionssysteme nicht am Notfallwiederherstellungsstandort platzieren möchten, informieren Sie sich über Azure Site Recovery als Methode für die Replikation der SAP-Anwendungsschicht in die Azure-Region für die Notfallwiederherstellung. Weitere Informationen finden Sie unter [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md).
        - Wenn Sie sich für den Einsatz einer kombinierten HADR-Konfiguration mithilfe von [Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md) entscheiden, machen Sie sich mit den Azure-Regionen vertraut, in denen Verfügbarkeitszonen erhältlich sind. Berücksichtigen Sie außerdem Einschränkungen, die durch erhöhte Netzwerklatenzen zwischen zwei Verfügbarkeitszonen verursacht werden können.  
3.  Einen Bestand aller SAP-Schnittstellen (SAP und nicht SAP)
4.  Den Entwurf der erforderlichen Grunddienste. Dieser Entwurf sollte die folgenden Elemente enthalten:
    - Active Directory- und DNS-Entwurf
    - Netzwerktopologie in Azure und Zuweisung der unterschiedlichen SAP-Systeme
    - Struktur für die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../../role-based-access-control/overview.md) für Teams, die Infrastruktur und SAP-Anwendungen in Azure verwalten.
    - Topologie der Ressourcengruppe
    - [Tagstrategie](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)
    - Benennungskonvention für virtuelle Computer und andere Infrastrukturkomponenten und/oder logische Namen
5.  Microsoft Professional oder Premier Support-Vertrag. Identifizieren Sie Ihren Microsoft Technical Account Manager (TAM), wenn Sie über einen Premier Support-Vertrag mit Microsoft verfügen. Informationen zu den Supportanforderungen von SAP finden Sie im SAP-[Supporthinweis Nr. 2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Die Anzahl der Azure-Abonnements und das Kernkontingent für die Abonnements. [Erstellen Sie ggf. Supportanfragen, um Kontingente von Azure-Abonnements zu erhöhen](../../../azure-portal/supportability/resource-manager-core-quotas-request.md).
7.  Plan zur Datenverringerung und -migration für die Migration von SAP-Daten zu Azure. Für SAP NetWeaver-Systeme stellt SAP Richtlinien zum Einschränken des Volumens bei großen Datenmengen bereit. Lesen Sie [diese SAP-Anleitung](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) zum Datenmanagement in SAP-ERP-Systemen. Ein Teil des Inhalts gilt auch allgemein für NetWeaver- und S/4HANA-Systeme.
8.  Einen Ansatz für automatisierte Bereitstellung. Das Ziel der Automatisierung von Infrastrukturbereitstellungen in Azure ist die Umsetzung in einer deterministischen Weise, die zu vorhersagbaren Ergebnissen führt. Viele Kunden verwenden PowerShell- oder CLI-basierte Skripts. Es gibt aber verschiedene Open-Source-Technologien, die zum Bereitstellen von Azure-Infrastruktur für SAP und sogar zum Installieren von SAP-Software verwendet werden können. Beispiele dazu finden Sie auf GitHub:
    - [Automatisierte SAP-Bereitstellungen in der Azure-Cloud](https://github.com/Azure/sap-hana)
    - [SAP HANA-Installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Festlegen einer regelmäßigen Überprüfung von Entwurf und Bereitstellung zwischen Ihnen als Kunde, dem Systemintegrator, Microsoft und anderen beteiligten Parteien.


## <a name="pilot-phase-strongly-recommended"></a>Pilotphase (dringend empfohlen)
 
Sie können einen Pilotversuch vor oder während der Projektplanung und -vorbereitung ausführen. Sie können die Pilotphase außerdem verwenden, um Ansätze und Entwürfe aus der Planungs- und Vorbereitungsphase zu testen. Und Sie können die Pilotphase erweitern, um sie zu einem echten Proof of Concept zu machen.

Es empfiehlt sich, im Rahmen einer Pilotbereitstellung eine vollständige HADR-Lösung und einen vollständigen Sicherheitsentwurf einzurichten und zu validieren. Einige Kunden führen in dieser Phase auch Tests auf Skalierbarkeit durch. Andere Kunden verwenden in der Pilotphase Bereitstellungen von SAP-Sandboxsystemen. Wir gehen davon aus, dass Sie für den Pilotversuch bereits ein System bestimmt haben, das Sie zu Azure migrieren möchten.

1. Optimieren der Datenübertragung zu Azure. Die optimale Wahl hängt stark vom spezifischen Szenario ab. Die Übertragung vom lokalen Standort mithilfe von [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) stellt die schnellste Möglichkeit dar, falls der ExpressRoute-Kreis über ausreichend Bandbreite verfügt. In anderen Szenarien ist die Übertragung über das Internet schneller.
2. Im Fall der Migration einer heterogenen SAP-Plattform, die mit einem Export und Import von Daten einhergeht, sollten Sie die Export- und Importphasen testen und ggf. optimieren. Für große Migrationen, in denen SQL Server die Zielplattform bildet, stehen [Empfehlungen](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) zur Verfügung. Sie können Migration Monitor/SWPM verwenden, wenn Sie kein kombiniertes Versionsupgrade benötigen. Wenn Sie die Migration mit einem Upgrade der SAP-Version kombinieren möchten, können Sie den [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)-Prozess verwenden. In diesem Fall müssen bestimmte Anforderungen an die Kombination der DBMS-Quell- und Zielplattform erfüllt sein. Dieser Prozess ist in der [Database Migration Option (DMO) of SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152) (Option zur Datenbankmigration (DMO) von SUM 2.0 SP03) dokumentiert.
   1.  Leistung beim Exportieren in die Quelle, beim Hochladen von Exportdateien zu Azure und beim Importieren. Maximieren Sie die Überschneidung zwischen Export und Import.
   2.  Bewerten Sie das Volumen der Datenbank auf den Quell- und Zielplattformen zwecks Dimensionierung der Infrastruktur.
   3.  Überprüfen und Optimieren der zeitlichen Steuerung
1. Technische Validierung
   1. Arten von virtuellen Computern
        - Arbeiten Sie die Ressourcen in den SAP-Supporthinweisen, im SAP HANA-Hardwareverzeichnis und im SAP PAM erneut durch. Vergewissern Sie sich, dass es keine Änderungen an unterstützten VMs für Azure, unterstützten Betriebssystemversionen für diese VM-Arten und unterstützten SAP- und DBMS-Versionen gegeben hat.
        - Überprüfen Sie erneut die Größe Ihrer Anwendung und die Infrastruktur, die Sie in Azure bereitstellen. Falls Sie vorhandene Anwendungen verschieben, können Sie die erforderlichen SAPS häufig über die verwendete Infrastruktur und die [Benchmark-Webseite von SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) ableiten und sie mit der Anzahl von SAPS vergleichen, die im [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) aufgeführt wird. Berücksichtigen Sie außerdem diesen [Artikel zu SAPS-Bewertungen](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208).
        - Bewerten und Testen der Größe Ihrer Azure-VMs im Hinblick auf den maximalen Speicher- und Netzwerkdurchsatz der verschiedenen VM-Typen, die Sie während der Planungsphase ausgewählt haben. Sie finden die Daten hier:
           -  [Größen für virtuelle Windows-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Für die Dimensionierung müssen Sie den *maximalen Durchsatz des Datenträgers ohne Cache* berücksichtigen.
           -  [Größen für virtuelle Linux-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Für die Dimensionierung müssen Sie den *maximalen Durchsatz des Datenträgers ohne Cache* berücksichtigen.
   2. Speicher
        - Lesen Sie das Dokument [Azure-Speichertypen für SAP-Workloads](./planning-guide-storage.md).
        - Verwenden Sie mindestens [Azure SSD-Standardspeicher](../../disks-types.md#standard-ssd) für VMs, die SAP-Anwendungsschichten darstellen, und für nicht leistungskritische DBMS-Bereitstellungen
        - Im Allgemeinen empfehlen wir die Verwendung von [Azure HDD-Standarddatenträgern](../../disks-types.md#standard-hdd) nicht.
        - Verwenden Sie [Azure Storage Premium](../../disks-types.md#premium-ssd) für alle DBMS-VMs, die auch nur entfernt leistungskritisch sind
        - Verwenden Sie [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)
        - Verwenden Sie Azure-Schreibbeschleunigung für die DBMS-Protokolllaufwerke mit M-Serie. Beachten Sie die Grenzwerte für die Schreibbeschleunigung und die Nutzung. Eine Beschreibung finden Sie unter [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md).
        - Überprüfen Sie für die verschiedenen DBMS-Typen die [allgemeine SAP-bezogene DBMS-Dokumentation](./dbms_guide_general.md) und die DBMS-spezifische Dokumentation, auf die das allgemeine Dokument verweist.
        - Weitere Informationen zu SAP HANA finden Sie unter [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](./hana-vm-operations.md).
        - Binden Sie Azure-Datenträger niemals mithilfe der Geräte-ID auf einem virtuellen Azure-Linux-Computer ein. Verwenden Sie stattdessen den Universally Unique Identifier (UUID). Seien Sie vorsichtig, wenn Sie grafische Tools z. B. für das Einbinden von Azure-Datenträgern verwenden. Überprüfen Sie die Einträge in „/etc/fstab“ erneut, um sicherzustellen, dass die Datenträger mithilfe des UUID bereitgestellt sind. Weitere Informationen finden Sie in [diesem Artikel](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Netzwerk
        - Testen und evaluieren Sie Ihre virtuelle Netzwerkinfrastruktur und die Verteilung Ihrer SAP-Anwendungen auf die oder innerhalb der unterschiedlichen virtuellen Azure-Netzwerke.
        -  Bewerten Sie den Ansatz einer Hub-and-Spoke-Architektur für Ihr virtuelles Netzwerk im Vergleich zu einer Mikrosegmentierung innerhalb eines einzelnen virtuellen Azure-Netzwerks Legen Sie der Bewertung diese Aspekte zugrunde:
               1. Kosten für den Datenaustausch zwischen [virtuellen Azure-Netzwerken mit Peering](../../../virtual-network/virtual-network-peering-overview.md). Informationen zu den Kosten können Sie [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/) entnehmen.
               2. Vorteile einer schnellen Trennung des Peerings zwischen virtuellen Azure-Netzwerken im Vergleich mit dem Wechsel der Netzwerksicherheitsgruppe zum Isolieren eins Subnetzes innerhalb eines virtuellen Netzwerks. Diese Bewertung ist für Fälle vorgesehen, in denen Anwendungen oder VMs, die in einem Subnetz des virtuellen Netzwerks gehostet sind, zu einem Sicherheitsrisiko werden.
                3. Zentrale Protokollierung und Überwachung des Netzwerkdatenverkehrs zwischen dem lokalen Standort, externen Systemen und dem virtuellen Rechenzentrum, das Sie in Azure erstellt haben.
        - Bewerten und testen Sie den Datenpfad zwischen der SAP-Anwendungsschicht und der SAP-DBMS-Schicht.
            -  Die Platzierung von [virtuellen Azure-Netzwerkgeräten](https://azure.microsoft.com/solutions/network-appliances/) im Kommunikationspfad zwischen der SAP-Anwendung und der DBMS-Schicht von SAP-Systemen, die auf NetWeaver, Hybris oder S/4HANA basieren, wird nicht unterstützt.
            -  Die Platzierung der SAP-Anwendungsschicht und von SAP-DBMS in verschiedenen virtuellen Azure-Netzwerken, die nicht mittels Peering miteinander verknüpft sind, wird nicht unterstützt.
            -  Sie können [Regeln für Anwendungssicherheitsgruppen und Netzwerksicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md) verwenden, um Routen zwischen der SAP-Anwendungsschicht und der SAP DBMS-Schicht zu definieren.
        - Stellen Sie sicher, dass auf den virtuellen Computern, die in der SAP-Anwendungsschicht und der SAP-DBMS-Schicht verwendet werden, der [beschleunigte Azure-Netzwerkbetrieb](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiviert ist. Beachten Sie, dass unterschiedliche Betriebssystemebenen benötigt werden, um den beschleunigten Netzwerkbetrieb in Azure zu unterstützen:
            - Windows Server 2012 R2 oder höher.
            - SUSE Linux 12 SP3 oder höher
            - RHEL 7.4 oder höher
            - Oracle Linux 7.5. Wenn Sie den RHCKL-Kernel verwenden, ist Release 3.10.0-862.13.1.el7 erforderlich. Wenn Sie den Oracle UEK-Kernel verwenden, ist Release 5 erforderlich.
        - Testen und evaluieren Sie die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs gemäß SAP-Supporthinweis [Nr. 500235](https://launchpad.support.sap.com/#/notes/500235) und SAP Supporthinweis [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren oder guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen, wie in [diesem Artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance) beschrieben.
        - Vergewissern Sie sich, dass ILB-Bereitstellungen für die Verwendung von Direct Server Return eingerichtet wurden. Diese Einstellung verringert die Latenz, wenn Azure-ILBs für Konfigurationen mit Hochverfügbarkeit in der DBMS-Schicht verwendet werden
        - Wenn Sie Azure Load Balancer in Verbindung mit Linux-Gastbetriebssystemen verwenden, überprüfen Sie, ob der Linux-Netzwerkparameter **net.ipv4.tcp_timestamps** auf **0** festgelegt ist. Diese Empfehlung steht im Widerspruch zu Empfehlungen in älteren Versionen von [SAP-Hinweis Nr. 2382421](https://launchpad.support.sap.com/#/notes/2382421). Der SAP-Hinweis wurde jetzt aktualisiert und gibt an, dass dieser Parameter auf **0** festgelegt werden muss, damit er mit Azure Load Balancern funktioniert.
        - Erwägen Sie den Einsatz von [Azure-Näherungsplatzierungsgruppen](../../linux/co-location.md) zum Erreichen einer optimalen Netzwerklatenz. Weitere Informationen finden Sie unter [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz bei SAP-Anwendungen](sap-proximity-placement-scenarios.md)
   4. Bereitstellungen mit Hochverfügbarkeit und Notfallwiederherstellung
        - Wenn Sie die SAP-Anwendungsschicht bereitstellen, ohne eine bestimmte Azure-Verfügbarkeitszone zu definieren, stellen Sie sicher, dass alle virtuellen Computer, auf denen SAP-Dialoginstanzen oder Middlewareinstanzen eines einzelnen SAP-System ausgeführt werden, in einer [Verfügbarkeitsgruppe](../../manage-availability.md) bereitgestellt werden.
        - Falls Sie für die SAP Central Services und das DBMS keine Hochverfügbarkeit benötigen, können Sie diese virtuellen Computer in derselben Verfügbarkeitsgruppe wie die SAP-Anwendungsschicht bereitstellen.
        - Wenn Sie die SAP Central Services- und die DBMS-Schicht für Hochverfügbarkeit mithilfe von passiver Replikation schützen, müssen Sie die beiden Knoten für SAP Central Services in einer separaten Verfügbarkeitsgruppe und die beiden DBMS-Knoten in einer anderen Verfügbarkeitsgruppe platzieren.
        - Wenn die Bereitstellung in Azure-Verfügbarkeitszonen erfolgt, können Sie keine Verfügbarkeitsgruppen verwenden. Sie müssen aber sicherstellen, dass der aktive und der passive Central Services-Knoten in zwei verschiedenen Verfügbarkeitszonen bereitgestellt werden. Verwenden Sie Verfügbarkeitsknoten, zwischen denen die niedrigste Latenz besteht.
          Denken Sie daran, dass Sie den [Azure Load Balancer Standard](../../../load-balancer/load-balancer-standard-availability-zones.md) für den Anwendungsfall verwenden müssen, dass Windows- oder Pacemaker-Failovercluster für die DBMS- und SAP Central Services-Schicht über Verfügbarkeitszonen hinweg erstellt werden. [Load Balancer Basic](../../../load-balancer/load-balancer-overview.md) kann nicht für zonale Bereitstellungen verwendet werden.
   5. Timeouteinstellungen
        - Überprüfen Sie die SAP NetWeaver-Entwicklernachverfolgung der SAP-Instanzen, um sicherzustellen, dass keine Verbindungsunterbrechungen zwischen dem Warteschlangenserver und den SAP-Arbeitsprozessen auftreten. Sie können diese Verbindungsunterbrechungen durch Festlegen dieser beiden Registrierungsparameter verhindern:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Weitere Informationen finden Sie unter [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Weitere Informationen finden Sie unter [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Um GUI-Timeouts zwischen lokalen grafischen SAP-Benutzeroberflächen und in Azure bereitgestellten SAP-Anwendungsschichten zu vermeiden, überprüfen Sie, ob diese Parameter in der Datei „default.pfl“ oder im Profil für die Instanz festgelegt wurden:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Um eine Unterbrechung der bestehenden Verbindungen zwischen dem SAP-Warteschlangeneinreihungs-Prozess und dem SAP-Arbeitsprozess zu vermeiden, muss der Parameter **enque/encni/set_so_keepalive** auf **true** festgelegt werden. Weitere Informationen finden Sie im [SAP-Hinweis Nr. 2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Wenn Sie eine Konfiguration mit Windows-Failovercluster verwenden, stellen Sie sicher, dass die Zeit zum Reagieren auf nicht reagierende Knoten für Azure ordnungsgemäß festgelegt ist. Im -Artikel [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) (Optimieren der Netzwerkschwellenwerte für Failovercluster) werden die Parameter und ihre Auswirkungen auf Failover aufgeführt. Unter der Annahme, dass sich die Clusterknoten im selben Subnetz befinden, sollten Sie diese Parameter ändern:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Betriebssystemeinstellungen oder Patches
        - Lesen Sie zur Ausführung von HANA auf SAP diese Hinweise und Dokumentationen:
            -   [SAP-Supporthinweis Nr. 2814271 – SAP HANA Backup fails on Azure with Checksum Error](https://launchpad.support.sap.com/#/notes/2814271) (SAP HANA-Sicherung in Azure schlägt mit Prüfsummenfehler fehl)
            -   [SAP-Supporthinweis Nr. 2753418 – Potential Performance Degradation Due to Timer Fallback](https://launchpad.support.sap.com/#/notes/2753418) (Potenzielle Leistungsbeeinträchtigung aufgrund eines Zeitgeberfallbacks)
            -   [SAP-Supporthinweis Nr. 2791572 – Performance Degradation Because of Missing VDSO Support For Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572) (Leistungsbeeinträchtigung aufgrund von fehlender VDSO-Unterstützung für Hyper-V in Azure)
            -   [SAP-Supporthinweis Nr. 2382421 – Optimieren der Netzwerkkonfiguration auf HANA- und Betriebssystemebene](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP-Supporthinweis Nr. 2694118 – Hochverfügbarkeits-Add-On für Red Hat Enterprise Linux in Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP-Supporthinweis Nr. 1984787 – SUSE LINUX Enterprise Server 12: Installationshinweise](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation und Upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support note #2292690 - SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (2292690 – SAP HANA DB: empfohlene Betriebssystemeinstellungen für RHEL 7)
            -   [SAP-Supporthinweis Nr. 2772999 – Red Hat Enterprise Linux 8.x: Installation und Konfiguration](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP-Supporthinweis Nr. 2777782 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP-Supporthinweis Nr. 2578899 – SUSE Linux Enterprise Server 15: Installationshinweis](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP-Supporthinweis Nr. 2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
            -    [SAP-Supporthinweis Nr. 2729475 – HWCCT Failed with Error "Hypervisor is not supported" on Azure VMs certified for SAP HANA](https://launchpad.support.sap.com/#/notes/2729475) (HWCCT ist mit der Meldung „Hypervisor wird nicht unterstützt“ auf für SAP HANA zertifizierten Azure-VMs fehlgeschlagen)
1. Testen Ihrer Verfahren für Hochverfügbarkeit und Notfallwiederherstellung
   1. Simulieren Sie Failoversituationen durch Herunterfahren von VMs (Windows-Gastbetriebssysteme) oder das Versetzen von Betriebssystemen in den Panikmodus (Linux-Gastbetriebssysteme). Mithilfe dieses Schritts können Sie herausfinden, ob Ihre Failoverkonfigurationen wie beabsichtigt funktionieren.
   1. Messen Sie, wie viel Zeit für die Ausführung eines Failovers benötigt wird. Wenn Sie die Zeiten zu lang sind, erwägen Sie Folgendes:
        - Verwenden Sie für SUSE Linux SBD-Geräte anstelle von Azure Fence-Agent, um das Failover zu beschleunigen.
        - Wenn bei SAP HANA das erneute Laden von Daten zu lange dauert, erwägen Sie die Bereitstellung zusätzlicher Speicherbandbreite.
   3. Testen Sie Abfolge und Timing Ihrer Sicherungs-/Wiederherstellungsprozesse, und nehmen Sie ggf. Korrekturen vor. Achten Sie auf ausreichende Sicherungszeiten. Außerdem müssen Sie die Wiederherstellungs- und Zeitwiederherstellungsaktivitäten testen. Achten Sie darauf, dass die Wiederherstellungszeiten innerhalb Ihrer RTO-SLAs liegen, wenn Ihr RTO den Wiederherstellungsprozess für einen virtuellen Computer oder eine Datenbank voraussetzt.
   4. Testen Sie Funktion und Architektur der regionsübergreifenden Notfallwiederherstellung.
1. Sicherheitsüberprüfungen
   1. Testen Sie die Gültigkeit Ihrer Architektur der rollenbasierten Zugriffssteuerung von Azure (RBAC). Das Ziel besteht in der Trennung und Einschränkung von Zugriff und Berechtigungen verschiedener Teams. Beispielweise sollten SAP-Basisteammitglieder in der Lage sein, virtuelle Computer bereitzustellen und Datenträger aus Azure Storage in einem angegebenen virtuellen Azure-Netzwerk zuzuweisen. Jedoch sollte das SAP-Basisteam keine eigenen virtuellen Netzwerke erstellen oder die Einstellungen vorhandener virtueller Netzwerke ändern können. Mitglieder des Netzwerkteams sollten nicht berechtigt sein, virtuelle Computer in virtuellen Netzwerken bereitzustellen, in denen VMs für die SAP-Anwendung und das DBMS ausgeführt werden. Auch sollten Mitglieder dieses Teams nicht berechtigt sein, Attribute von virtuellen Computern zu ändern oder VMs oder Datenträger zu löschen.  
   1.  Überprüfen Sie, ob die Regeln für [Netzwerksicherheitsgruppen und ASC](../../../virtual-network/network-security-groups-overview.md) erwartungsgemäß funktionieren und die geschützten Ressourcen abschirmen.
   1.  Stellen Sie sicher, dass alle Ressourcen, die verschlüsselt werden müssen, auch verschlüsselt werden. Definieren und implementieren Sie Prozesse zum Sichern von Zertifikaten, zum Speichern und Zugreifen auf diese Zertifikate und zum Wiederherstellen der verschlüsselten Entitäten.
   1.  Verwenden Sie [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) für Betriebssystem-Datenträger, sofern dies im Rahmen der Betriebssystemunterstützung möglich ist.
   1.  Achten Sie darauf, nicht zu viele Verschlüsselungsebenen zu verwenden. Es ist in manchen Fällen sinnvoll, Azure Disk Encryption zusammen mit einer der TDE-Methoden (Transparent Data Encryption) für das DBMS einzusetzen, um verschiedene Datenträger oder Komponenten auf demselben Server zu schützen.  Beispielsweise kann auf einem SAP DBMS-Server Azure Disk Encryption (ADE) auf dem Startdatenträger des Betriebssystems (wenn das Betriebssystem ADE unterstützt) und den Datenträgern aktiviert werden, die von den DBMS-Datenpersistenzdateien nicht verwendet werden.  Ein Beispiel ist die Verwendung von ADE auf dem Datenträger, auf dem sich die DBMS TDE-Verschlüsselungsschlüssel befinden.
1. Leistungstests. Nehmen Sie in SAP auf der Grundlage von SAP-Ablaufverfolgung und Messungen diese Vergleiche vor:
   - Vergleichen Sie ggf. die 10 wichtigsten Onlineberichte mit Ihrer aktuellen Implementierung.
   - Vergleichen Sie ggf. die 10 wichtigsten Batchaufträge mit Ihrer aktuellen Implementierung.
   - Vergleichen Sie Datenübertragungen über Schnittstellen mit dem SAP-System. Konzentrieren Sie sich auf Schnittstellen, von denen Sie wissen, dass die Übertragung zwischen verschiedenen Standorten erfolgt, etwa von lokal zu Azure.


## <a name="non-production-phase"></a>Nicht-Produktionsphase 
In dieser Phase wird davon ausgegangen, dass Sie nach einem erfolgreichen Pilotprojekt oder einem Proof of Concept (POC) damit beginnen, SAP-Systeme, die nicht für die Produktion vorgesehen sind, in Azure bereitzustellen. Integrieren Sie alle Erkenntnisse aus der POC-Phase in diese Bereitstellung. Alle Kriterien und Schritte, die für POCs aufgeführt sind, gelten auch für diese Bereitstellung.

In dieser Phase stellen Sie in der Regel Entwicklungssysteme, Komponententestsysteme und Regressionstestsysteme in Azure bereit. Es ist empfehlenswert, für mindestens ein Nicht-Produktionssystem einer SAP-Anwendungsreihe die gesamte Hochverfügbarkeitskonfiguration einzurichten, die auch für das zukünftige Produktionssystem verwendet werden soll. Hier finden Sie einige zusätzliche Schritte, die Sie in dieser Phase ausführen müssen:  

1.  Sammeln Sie Ressourcenverbrauchsdaten, wie CPU-Auslastung, Speicherdurchsatz und IOPS-Daten, bevor Sie Systeme von der alten Plattform zu Azure verschieben. Erfassen Sie diese Daten insbesondere für Einheiten der DBMS-Schicht, aber auch für Einheiten der Anwendungsschicht. Messen Sie außerdem die Latenz von Netzwerk und Speicher.
2.  Zeichnen Sie die Nutzungszeitmuster Ihrer Systeme für Verfügbarkeit auf. Das Ziel besteht darin, zu bestimmen, ob Nicht-Produktionssysteme täglich rund um die Uhr verfügbar sein müssen oder während bestimmter Phasen einer Woche oder eines Monats heruntergefahren werden können
3.  Testen und bestimmen Sie, ob Sie eigene Betriebssystemimages für Ihre VMs in Azure erstellen oder ein Image aus der Azure Shared Image Gallery verwenden möchten. Wenn Sie ein Image aus der Shared Image Gallery verwenden, achten Sie darauf, ein Image zu verwenden, das dem Supportvertrag mit Ihrem Betriebssystemanbieter entspricht. Bei manchen Betriebssystemanbietern können Sie in der Shared Image Gallery BYOL-Images (Bring Your Own License) verwenden. Bei anderen Betriebssystemimages ist er Support bereits im für Azure angegebenen Preis enthalten. Wenn Sie eigene Betriebssystemimages erstellen möchten, lesen Sie die Dokumentation in den folgenden Artikeln:
    -   [Build a generalized image of a Windows VM deployed in Azure](../../windows/capture-image-resource.md) (Erstellen eines generalisierten Images einer in Azure bereitgestellten Windows-VM)
    -   [Build a generalized image of a Linux VM deployed in Azure](../../linux/capture-image.md) (Erstellen eines generalisierten Images einer in Azure bereitgestellten Linux-VM)
3.  Wenn Sie SUSE- und Red Hat Linux-Images aus der Azure Shared Image Gallery verwenden, müssen Sie die für SAP bestimmten Images verwenden, die von den Linux-Anbietern in der Shared Image Gallery angeboten werden.
4.  Achten Sie darauf, dass Sie den Anforderungen an SAP-Unterstützung für Microsoft-Supportverträge gerecht werden. Mehr dazu finden Sie im [SAP-Supporthinweis Nr. 2015553](https://launchpad.support.sap.com/#/notes/2015553). Für HANA (große Instanzen) lesen Sie das Dokument [Onboardinganforderungen](./hana-onboarding-requirements.md).
4.  Achten Sie darauf, dass die richtigen Personen [Benachrichtigungen zu geplanten Wartungen](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) erhalten, sodass Sie die besten Ausfallzeiten auswählen können.
5.  Suchen Sie regelmäßig in Kanälen wie [Channel9](https://channel9.msdn.com/) nach Azure-Präsentationen zu neuen Funktionen, die möglicherweise für Ihre Bereitstellungen anwendbar sind.
6.  Lesen Sie die SAP-Hinweise im Zusammenhang mit Azure, z.B. den [Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) zu neuen VM-SKUs und neuen unterstützten Betriebssystem- und DBMS-Releases. Vergleichen Sie die Preise für neue VM-Typen mit denen für ältere, damit Sie virtuelle Computer mit dem besten Preis-Leistungs-Verhältnis bereitstellen können.
7.  Überprüfen Sie regelmäßig die SAP-Supporthinweise, das SAP HANA-Hardwareverzeichnis und das SAP PAM. Vergewissern Sie sich, dass es keine Änderungen an unterstützten VMs für Azure, unterstützten Betriebssystemversionen für diese VMs und unterstützten SAP- und DBMS-Versionen gegeben hat.
8.  Prüfen Sie auf neue HANA-zertifizierte SKUs in Azure auf [der SAP-Website](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Vergleichen Sie die Preise neuer SKUs mit denen, deren Einsatz Sie geplant haben. Nehmen Sie ggf. die erforderlichen Änderungen vor, um die SKUs mit dem besten Preis-Leistungsverhältnis zu verwenden.
9.  Passen Sie die Bereitstellungsskripts für die Verwendung neuer VM-Typen und die Einbeziehung neuer Azure-Funktionen an, die Sie nutzen möchten.
10. Testen und evaluieren Sie nach der Bereitstellung der Infrastruktur die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs gemäß den SAP-Supporthinweisen [Nr. 500235](https://launchpad.support.sap.com/#/notes/500235) und [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren oder guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen, wie in [diesem Artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance) beschrieben. Stellen Sie sicher, dass für Ihre Bereitstellung keine der Einschränkungen in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workloads](./dbms_guide_general.md#azure-network-considerations) und [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](./hana-vm-operations.md) gelten.
11. Stellen Sie sicher, dass Ihre VMs in der richtigen [Azure-Näherungsplatzierungsgruppe](../../linux/co-location.md) bereitgestellt werden, wie im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) beschrieben.
11. Führen Sie vor dem Anwenden der Workload alle anderen Überprüfungen durch, die für die Pilotphase (Proof of Concept) aufgeführt werden.
12. Zeichnen Sie beim Anwenden der Workload den Ressourcenverbrauch der Systeme in Azure auf. Vergleichen Sie diesen Verbrauch mit Aufzeichnungen aus Ihrer alten Plattform. Passen Sie die VM-Dimensionierung künftiger Bereitstellungen an, wenn Sie große Unterschiede verzeichnen. Beachten Sie, dass beim Downsizing auch die Speicher- und Netzwerkbandbreiten von virtuellen Computern reduziert werden.
    - [Größen für virtuelle Windows-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Größen für virtuelle Linux-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimentieren Sie mit Systemfunktionen und -prozessen für das Kopieren. Das Ziel besteht darin, Ihnen das Kopieren eines Bereitstellungssystems oder Testsystems zu erleichtern, damit Projektteams schnell an neue Systeme gelangen können. 
14. Optimieren und verfeinern Sie den rollenbasierten Zugriff, die Berechtigungen und die Prozesse Ihres Teams in Azure, um eine sichere Trennung der Aufgaben zu erreichen. Vergewissern Sie sich im gleichen Zug, dass alle Teams ihre Aufgaben in der Azure-Infrastruktur verrichten können.
15. Überprüfen, testen und dokumentieren Sie die Verfahren für Hochverfügbarkeit und Notfallwiederherstellung, damit Ihre Mitarbeiter diese Aufgaben ausführen können. Identifizieren Sie Mängel, und übernehmen Sie neue Azure-Funktionen in Ihre Bereitstellungen


## <a name="production-preparation-phase"></a>Vorbereitungsphase für die Produktion 
Sammeln Sie in dieser Phase alle Ihre Erkenntnisse aus den Nicht-Produktionsbereitstellungen, und wenden Sie sie auf künftige Produktionsbereitstellungen an. Außerdem müssen Sie den Prozess der Datenübertragung zwischen dem aktuellen Hostingstandort und Azure vorbereiten.

1.  Schließen Sie vor dem Verschieben nach Azure die erforderlichen SAP-Versionsupgrades Ihrer Produktionssysteme ab.
1.  Stimmen Sie sich mit den Geschäftsinhabern zu den Funktions- und Geschäftstests ab, die nach der Migration des Produktionssystems durchgeführt werden müssen.
1.  Stellen Sie sicher, dass alle diese Tests mit den Quellsystemen am aktuellen Hostingstandort abgeschlossen werden. Vermeiden Sie es, Tests erstmalig nach dem Verschieben des Systems nach Azure durchzuführen.
1.  Testen Sie den Prozess der Migration von Produktionssystemen zu Azure. Falls Sie nicht alle Produktionssysteme in einem Durchgang nach Azure verschieben, erstellen Sie Gruppen von Produktionssystemen, die am gleichen Standort gehostet werden müssen. Testen Sie die Datenmigration. Hier folgen einige gebräuchliche Methoden:
    - Verwenden Sie DBMS-Methoden wie Sicherung/Wiederherstellung in Verbindung mit SQL Server Always On, HANA-Systemreplikation oder Protokollversand für das Seeding und die Synchronisierung von Datenbankinhalten in Azure.
    - Verwenden Sie Sicherung/Wiederherstellung für kleinere Datenbanken
    - Verwenden Sie SAP Migration Monitor, der in SAP SWPM integriert ist, für heterogene Migrationsvorgänge.
    - Verwenden Sie den [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)-Prozess, wenn Sie zugleich mit Ihrer Migration ein Upgrade der SAP-Version durchführen müssen. Bedenken Sie, dass nicht alle Kombinationen von Quell- und Ziel-DBMS unterstützt werden. Weitere Informationen finden Sie in den spezifischen SAP-Supporthinweisen zu den verschiedenen DMO-Releases. Beispielsweise in [Database Migration Option (DMO) für SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Testen Sie, ob der Durchsatz der Datenübertragung über das Internet oder über ExpressRoute besser ist, für den Fall dass Sie Sicherungen oder SAP-Exportdateien verschieben müssen. Falls Sie Daten über das Internet übertragen, müssen Sie möglicherweise einige Regeln Ihrer Netzwerksicherheitsgruppen/Anwendungssicherheitsgruppen ändern, die Sie für zukünftige Produktionssysteme benötigen.
1.  Bevor Sie Systeme von ihrer alten Plattform zu Azure verschieben, erfassen Sie Daten zum Ressourcenverbrauch. Zu den nützlichen Daten zählen CPU-Auslastung, Speicherdurchsatz und IOPS-Daten. Erfassen Sie diese Daten insbesondere für Einheiten der DBMS-Schicht, aber auch für Einheiten der Anwendungsschicht. Messen Sie außerdem die Latenz von Netzwerk und Speicher.
1.  Überprüfen Sie die SAP-Supporthinweise sowie die erforderlichen Betriebssystemeinstellungen, das SAP HANA-Hardwareverzeichnis und die SAP PAM. Vergewissern Sie sich, dass es keine Änderungen an unterstützten VMs für Azure, unterstützten Betriebssystemversionen für diese VMs und unterstützten SAP- und DBMS-Versionen gegeben hat.
1.  Aktualisieren Sie Bereitstellungsskripts, um die neuesten Entscheidungen zu berücksichtigen, die Sie zu VM-Typen und Azure-Funktionen getroffen haben.
1.  Überprüfen Sie nach der Bereitstellung von Infrastruktur und Anwendungen Folgendes:
    - Es wurden die richtigen VM-Typen mit den korrekten Attributen und Speichergrößen bereitgestellt.
    - Auf den virtuellen Computern sind einheitlich die richtigen und gewünschten Betriebssystemversionen und -patches vorhanden.
    - VMs wurden gemäß den Anforderungen und in einheitlicher Weise gehärtet.
    - Es wurden die richtigen Anwendungsversionen und -patches bereitgestellt.
    - Die virtuellen Computer wurden wie geplant in Azure-Verfügbarkeitsgruppen bereitgestellt.
    - Azure Storage Premium wird für Datenträger mit spezifischen Anforderungen an die Latenz oder mit einer [SLA von 99,9 % für Einzel-VMs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) verwendet.
    - Azure Schreibbeschleunigung wurde ordnungsgemäß bereitgestellt.
        - Vergewissern Sie sich, dass Speicherplätze in den VMs oder Stripesets für Datenträger, die Schreibbeschleunigung benötigen, ordnungsgemäß erstellt wurden.
        - Überprüfen Sie die [Konfiguration von Software-RAID unter Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).
        - Überprüfen Sie die [Konfiguration von LVM unter Linux VMs in Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm).
    - Es werden ausschließlich [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwendet.
    - Die VMs wurden in den richtigen Verfügbarkeitsgruppen und -zonen bereitgestellt.
    - Auf den virtuellen Computern, die in der SAP-Anwendungsschicht und der SAP-DBMS-Schicht verwendet werden, ist der [beschleunigte Azure-Netzwerkbetrieb](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) aktiviert.
    - Es befinden sich keine [virtuellen Azure-Netzwerkgeräte](https://azure.microsoft.com/solutions/network-appliances/) im Kommunikationspfad zwischen der SAP-Anwendung und der DBMS-Schicht von SAP-Systemen, die auf NetWeaver, Hybris oder S/4HANA aufbauen.
    - Die Regeln von Anwendungssicherheitsgruppen und Netzwerksicherheitsgruppen erlauben die Kommunikation wie gewünscht und geplant und blockieren die Kommunikation, wo dies erforderlich ist.
    - Die Timeouteinstellungen wurden ordnungsgemäß festgelegt, wie weiter oben beschrieben.
    - Ihre VMs wurden in der richtigen [Azure-Näherungsplatzierungsgruppe](../../linux/co-location.md) bereitgestellt, wie im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) beschrieben.
    - Die Netzwerklatenz zwischen VMs der SAP-Anwendungsschicht und DBMS-VMs wurde getestet und überprüft, wie in den SAP-Supporthinweisen [Nr. 500235](https://launchpad.support.sap.com/#/notes/500235) und [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E) beschrieben. Werten Sie die Ergebnisse anhand der Hinweise zur Netzwerklatenz im SAP-Supporthinweis [Nr. 1100926](https://launchpad.support.sap.com/#/notes/1100926/E) aus. Die Netzwerklatenz sollte in einem mittleren oder guten Bereich liegen. Ausnahmen gelten für den Datenverkehr zwischen virtuellen Computern und Einheiten großer HANA-Instanzen, wie in [diesem Artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance) beschrieben.
    - Es wurde Verschlüsselung mit den geeigneten Verschlüsselungsmethoden implementiert, wo dies erforderlich ist.
    - Schnittstellen und andere Anwendungen können eine Verbindung mit der neu bereitgestellten Infrastruktur herstellen.
1.  Erstellen Sie ein Playbook für das Reagieren auf die geplante Wartung in Azure. Bestimmen Sie die Reihenfolge, in der Systeme und VMs bei der geplanten Wartung neu gestartet werden sollen.
    

## <a name="go-live-phase"></a>Aktivierungsphase
Während der Aktivierungsphase sollten Sie unbedingt den Playbooks folgen, die Sie in früheren Phasen entwickelt haben. Führen Sie die Schritte aus, die Sie getestet und trainiert haben. Lassen Sie keine kurzfristigen Änderungen an Konfigurationen und Prozessen mehr zu. Führen Sie außerdem diese Schritte aus:

1. Vergewissern Sie sich, dass die Überwachung im Azure-Portal und andere Überwachungstools funktionieren. Wir empfehlen den Windows-Systemmonitor (perfmon) für Windows und SAR für Linux.
    - CPU-Indikatoren
        - Durchschnittliche CPU-Auslastung – gesamt (alle CPUs)
        - Durchschnittliche CPU-Auslastung – jeder einzelne Prozessor (bei M128-VMs also 128 Prozessoren)
        - CPU-Kernelzeit – jeder einzelne Prozessor
        - CPU-Benutzerzeit – jeder einzelne Prozessor
    - Arbeitsspeicher.
        - Freier Arbeitsspeicher
        - Seite eingehend/s
        - Seite ausgehend/s
    - auswählen.
        - Datenträgerlesevorgänge in KB/s – pro Datenträger
        - Datenträgerlesevorgänge/s – pro Datenträger
        - Datenträgerlesevorgänge in µs/Lesevorgang – pro Datenträger
        - Datenträgerschreibvorgänge in KB/s – pro Datenträger
        - Datenträgerschreibvorgänge/s – pro Datenträger
        - Datenträgerschreibvorgänge in µs/Lesevorgang – pro Datenträger
    - Netzwerk
        - Netzwerkpakete eingehend/s
        - Netzwerkpakete ausgehend/s
        - Netzwerk KB eingehend/s
        - Netzwerk KB ausgehend/s
1.  Führen Sie nach der Datenmigration alle Validierungstests durch, die Sie mit den Geschäftsinhabern vereinbart haben. Akzeptieren Sie Ergebnisse von Validierungstests nur, wenn Sie über Ergebnisse für die ursprünglichen Quellsysteme verfügen.
1.  Überprüfen Sie, ob die Schnittstellen funktionieren und ob andere Anwendungen mit den neu bereitgestellten Produktionssystemen kommunizieren können.
1.  Überprüfen Sie das Transport- und Korrektursystem über Transaktions-STMS für SAP.
1.  Führen Sie Datenbanksicherungen aus, nachdem das System für die Produktion freigegeben wurde.
1.  Führen Sie VM-Sicherungen für die virtuellen Computer auf der SAP-Anwendungsschicht aus, nachdem das System für die Produktion freigegeben wurde.
1.  Für SAP-Systeme, die nicht Bestandteil der aktuellen Aktivierungsphase sind, aber mit den SAP-Systemen, die Sie in dieser Aktivierungsphase nach Azure verschoben haben, kommunizieren, müssen Sie den Hostnamenpuffer in SM51 zurücksetzen. Dadurch werden alte zwischengespeicherte IP-Adressen, die den Namen der nach Azure verschobenen Anwendungsinstanzen zugeordnet sind, gelöscht.  


## <a name="post-production"></a>Nachbearbeitung
In dieser Phase geht es um Überwachung, Betrieb und Verwaltung des Systems. Aus SAP-Sicht sind dies die gleichen üblichen Aufgaben, die Sie auch an Ihrem alten Hostingstandort ausführen mussten. Führen Sie außerdem diese Azure-spezifischen Aufgaben aus:

1. Überprüfen von Azure-Rechnungen, um Systeme mit hohen Gebühren zu ermitteln
2. Optimieren des Preis-Leistungs-Verhältnisses auf Seiten der virtuellen Computer und des Speichers
3. Optimieren der Uhrzeiten, zu denen Systeme heruntergefahren werden können.  


## <a name="next-steps"></a>Nächste Schritte
Informationen hierzu finden Sie in diesen Artikeln:

- [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](./deployment-guide.md)
- [Überlegungen zu Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workloads](./dbms_guide_general.md)