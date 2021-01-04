---
title: 'SAP in Azure: Unterstützte Szenarien mit Azure-VMs'
description: SAP-Workload in Szenarien mit Unterstützung virtueller Azure-Computer
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54f3f0e1b57525a3bd425575ff03a9f3c91b0044
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951212"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-Workload in Szenarien mit Unterstützung von virtuellen Azure-Computern
Der Entwurf einer SAP NetWeaver-, Business One-, `Hybris`- oder S/4HANA-Systemarchitektur in Azure eröffnet eine Vielzahl von Möglichkeiten für verschiedene Architekturen und Werkzeuge, um zu einer skalierbaren, effizienten und hochverfügbaren Bereitstellung zu gelangen. Abhängig vom verwendeten Betriebssystem oder DBMS gelten jedoch Einschränkungen. Darüber hinaus werden nicht alle Szenarien, die lokal unterstützt werden, in gleicher Weise auch in Azure unterstützt. Das vorliegende Dokument stellt die unterstützten Nicht-Hochverfügbarkeitskonfigurationen und Hochverfügbarkeitskonfigurationen sowie Architekturen vor, die ausschließlich Azure-VMs verwenden. Informationen zu unterstützten Szenarien mit [großen HANA-Instanzen](./hana-overview-architecture.md) finden Sie im Artikel [Unterstützte Szenarien für große HANA-Instanzen](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>Konfiguration mit 2 Ebenen
Eine SAP-Konfiguration mit 2 Ebenen baut im Allgemeinen auf einer kombinierten Ebene des SAP-DBMS und der Anwendungsschicht auf, die auf demselben Server oder derselben VM-Einheit ausgeführt werden. Die zweite Ebene ist die Benutzeroberflächenebene. Im Fall einer 2-Ebenen-Konfiguration verwenden die DBMS-Ebene und die SAP-Anwendungsschicht die Ressourcen der Azure-VM gemeinsam. Als Ergebnis müssen Sie die verschiedenen Komponenten so konfigurieren, dass sie nicht um Ressourcen konkurrieren. Darüber hinaus muss darauf geachtet werden, dass die Ressourcen der VM nicht überbelegt werden. Eine solche Konfiguration bietet keine Hochverfügbarkeit, die über die [Azure-Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/) der verschiedenen beteiligten Azure-Komponenten hinausgeht.

Eine grafische Darstellung einer solchen Konfiguration könnte so aussehen:

![Einfache Konfiguration mit 2 Ebenen](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Derartige Konfigurationen werden mit Windows, Red Hat, SUSE und Oracle Linux für die DBMS-Systeme von SQL Server, Oracle, Db2, maxDB und SAP ASE für Produktions- und Nicht-Produktionsumgebungen unterstützt. Für SAP HANA als DBMS wird diese Art von Konfiguration nur für nicht produktionsbezogene Implementierungen unterstützt. Dies schließt auch die Bereitstellung [großer Azure HANA-Instanzen](./hana-overview-architecture.md) ein.
Für alle in Azure unterstützten OS/DBMS-Kombinationen wird diese Art der Konfiguration unterstützt. Es ist jedoch zwingend erforderlich, dass Sie die Konfiguration des DBMS und der SAP-Komponenten so wählen, dass DBMS und SAP-Komponenten nicht um Speicher- und CPU-Ressourcen konkurrieren und dadurch die physisch verfügbaren Ressourcen übersteigen. Dies muss durch eine Beschränkung des Arbeitsspeichers geschehen, den das DBMS zuweisen darf. Darüber hinaus müssen Sie den SAP-Erweiterungsspeicher für Anwendungsinstanzen begrenzen. Sie müssen außerdem den CPU-Verbrauch der VM insgesamt überwachen, um sicherzustellen, dass die Komponenten nicht die CPU-Ressourcen maximieren. 

> [!NOTE]
> Für SAP-Produktionssysteme werden zusätzlich Hochverfügbarkeitskonfigurationen und Konfigurationen für die Notfallwiederherstellung empfohlen, wie später in diesem Dokument beschrieben wird.


## <a name="3-tier-configuration"></a>Konfiguration mit 3 Ebenen
In diesen Konfiguration werden die SAP-Anwendungsschicht und die DBMS-Ebene auf verschiedene VMs aufgeteilt. Dies geschieht in der Regel bei größeren Systemen und aus Gründen der Flexibilisierung der Ressourcen der SAP-Anwendungsschicht. Im einfachsten Fall bietet eine solche Konfiguration keine Hochverfügbarkeit, die über die [Azure-Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/) der verschiedenen beteiligten Azure-Komponenten hinausgeht. 

Grafisch dargestellt sieht dies so aus:

![Dienstebene einer einfachen Konfiguration auf 3 Ebenen.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Dieser Konfigurationstyp wird unter Windows, Red Hat, SUSE und Oracle Linux für die DBMS-Systeme von SQL Server, Oracle, Db2, SAP HANA, maxDB und SAP ASE für Produktions- und Nicht-Produktionsumgebungen unterstützt. Dies ist die Standardkonfiguration bei der Bereitstellung von [großen Azure HANA-Instanzen](./hana-overview-architecture.md). Zur Vereinfachung haben wir in der SAP-Anwendungsschicht nicht zwischen SAP Central Services und SAP-Dialoginstanzen unterschieden. In dieser einfachen Konfiguration mit 3 Ebenen wäre kein Hochverfügbarkeitsschutz für SAP Central Services gegeben.

> [!NOTE]
> Für SAP-Produktionssysteme werden zusätzlich Hochverfügbarkeitskonfigurationen und Konfigurationen für die Notfallwiederherstellung empfohlen, wie später in diesem Dokument beschrieben wird.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Mehrere DBMS-Instanzen pro VM oder großer HANA-Instanz
Bei diesem Konfigurationstyp hosten Sie mehrere DBMS-Instanzen pro Azure-VM oder großer HANA-Instanz. Der Grund hierfür kann darin bestehen, weniger Betriebssysteme warten zu müssen und so die Kosten zu senken. Eine andere Motivationen könnte sein, mehr Flexibilität und mehr Effizienz zu erzielen, indem die Ressourcen einer größeren VM oder großen HANA-Instanz auf mehrere DBMS-Instanzen aufgeteilt werden. Bislang wurden diese Konfigurationen vor allem bei Nicht-Produktionssystemen eingesetzt.

Eine solche Konfiguration könnte so aussehen:

![Mehrere DBMS-Instanzen in einer Einheit](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Diese Art von DBMS-Bereitstellung wird unterstützt für:

- SQL Server unter Windows
- IBM Db2. Ausführliche Informationen finden Sie im Artikel [Mehrere Instanzen (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html).
- Für Oracle. Ausführliche Informationen finden Sie im [SAP-Supporthinweis Nr. 1778431](https://launchpad.support.sap.com/#/notes/1778431) und in zugehörigen SAP-Hinweisen.
- Für SAP HANA werden mehrere Instanzen auf einer VM – SAP nennt diese Bereitstellungsmethode MCOS – unterstützt. Ausführliche Informationen finden Sie im SAP-Artikel [Mehrere SAP HANA-Systeme auf einem Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Bei der Ausführung mehrerer Datenbankinstanzen auf einem Host müssen Sie sicherstellen, dass die verschiedenen Instanzen nicht um Ressourcen konkurrieren und dadurch die physischen Ressourcengrenzen der VM überschreiten. Dies gilt insbesondere für den Arbeitsspeicher. Legen Sie eine Obergrenze für die Menge an Arbeitsspeicher fest, den jede dieser Instanzen mit gemeinsam verwendeter VM zuweisen kann. Das kann auch für die CPU-Ressourcen gelten, die die verschiedenen Datenbankinstanzen nutzen können. Alle genannten DBMS besitzen Konfigurationen, die eine Begrenzung der Speicherzuweisung und der CPU-Ressourcen auf Instanzebene ermöglichen.
Um eine solche Konfiguration für Azure-VMs zu unterstützen, wird erwartet, dass die Datenträger oder Volumes für die Daten und Protokoll-/Wiederherstellungsprotokolldateien der von den verschiedenen Instanzen verwalteten Datenbanken voneinander getrennt sind. Anders ausgedrückt: Daten oder Protokoll-/Wiederherstellungsprotokolldateien von Datenbanken, die von verschiedenen DBMS-Instanzen verwaltet werden, dürfen nicht auf denselben Datenträgern oder Volumes gespeichert werden. 

Die Datenträger für große HANA-Instanzen werden konfiguriert bereitgestellt. Ausführliche Informationen finden Sie in [Unterstützte Szenarien für große HANA-Instanzen](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Für SAP-Produktionssysteme werden zusätzlich Hochverfügbarkeitskonfigurationen und Konfigurationen für die Notfallwiederherstellung empfohlen, wie später in diesem Dokument beschrieben wird. Die später in diesem Dokument beschriebenen Hochverfügbarkeitskonfigurationen werden für VMs mit mehreren DBMS-Instanzen nicht unterstützt.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Mehrere SAP-Dialoginstanzen in einer VM
In vielen Fällen werden mehrere Dialoginstanzen auf Bare-Metal-Servern oder sogar in VMs bereitgestellt, die in privaten Clouds ausgeführt werden. Eine solche Konfigurationen wird gewählt, um SAP-Dialoginstanzen an eine bestimmte Workload, Geschäftsfunktionalität oder an einen Workloadtyp anzupassen. Der Grund dafür, diese Instanzen nicht in separaten VMs zu isolieren, liegt im Aufwand für die Wartung und den Betrieb des Betriebssystems. Häufig spielen auch die Kosten eine Rolle, falls der Hostinganbieter oder Betreiber der VM eine monatliche Gebühr pro VM verlangt, die betrieben und verwaltet wird. In Azure wird ein Szenario mit Hosting mehrerer SAP-Dialoginstanzen innerhalb einer einzigen VM für Produktions- und Nicht-Produktionszwecke auf den Betriebssystemen Windows, Red Hat, SUSE und Oracle Linux unterstützt. Wenn mehrere SAP-Anwendungsserverinstanzen auf einer einzigen VM ausgeführt werden, sollte der SAP-Kernelparameter PHYS_MEMSIZE festgelegt werden, der für Windows- und moderne Linux-Kernel verfügbar ist. Außerdem wird empfohlen, die Erweiterung des SAP-Erweiterungsspeichers auf Betriebssystemen wie Windows zu begrenzen, bei denen eine automatische Vergrößerung des SAP-Erweiterungsspeichers implementiert ist. Dies ist mit dem SAP-Profilparameter `em/max_size_MB` möglich.

Eine 3-Ebenen-Konfiguration mit Ausführung mehrerer SAP-Dialoginstanzen innerhalb von Azure-VMs könnte so aussehen:

![Diagramm einer Konfiguration auf 3 Ebenen, bei der mehrere SAP-Dialoginstanzen in Azure VMs ausgeführt werden.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Zur Vereinfachung haben wir in der SAP-Anwendungsschicht nicht zwischen SAP Central Services und SAP-Dialoginstanzen unterschieden. In dieser einfachen Konfiguration mit 3 Ebenen wäre kein Hochverfügbarkeitsschutz für SAP Central Services gegeben. Für Produktionssysteme ist es nicht empfehlenswert, SAP Central Services ungeschützt zu lassen. Einzelheiten zu sogenannten „Multi-SID-Konfigurationen“ mit SAP Central Services-Instanzen und zur Hochverfügbarkeit solcher Multi-SID-Konfigurationen finden Sie in späteren Abschnitten dieses Dokuments.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Hochverfügbarkeitsschutz für die SAP-DBMS-Ebene
Bei der Bereitstellung von SAP-Produktionssystemen sollten Sie den unmittelbar betriebsbereiten Standbytyp für Hochverfügbarkeitskonfigurationen in Betracht ziehen. Insbesondere bei SAP HANA-Systemen, bei denen Daten in den Arbeitsspeicher geladen werden müssen, bevor erneut die volle Leistung und Skalierbarkeit erreicht werden kann, ist die Azure-Dienstreparatur zur Sicherstellung von Hochverfügbarkeit nicht ideal. 

Im Allgemeinen unterstützt Microsoft nur Hochverfügbarkeitskonfigurationen und Softwarepakete, die im Abschnitt zur SAP-Workload auf docs.microsoft.com beschrieben sind. Sie können diese Informationen auch im SAP-Hinweis [Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) nachlesen. Microsoft bietet keine Unterstützung für Hochverfügbarkeits-Softwareframeworks von Drittanbietern, die von Microsoft in Verbindung mit der SAP-Workload nicht dokumentiert sind. In diesen Fällen muss die Unterstützung der Hochverfügbarkeitskonfiguration durch den Drittanbieter des Hochverfügbarkeitsframeworks erfolgen, der von Ihnen als Kunde in den Prozess eingebunden werden muss. Ausnahmen werden in diesem Artikel genannt. 

Im Allgemeinen unterstützt Microsoft eine begrenzte Anzahl von Hochverfügbarkeitskonfigurationen für Azure-VMs oder große HANA-Instanzen. Informationen zu den unterstützten Szenarien mit großen HANA-Instanzen finden Sie im Artikel [Unterstützte Szenarien für große HANA-Instanzen](./hana-supported-scenario.md).

Für Azure-VMs werden auf DBMS-Ebene die folgenden Hochverfügbarkeitskonfigurationen unterstützt:

- SAP HANA-Systemreplikation basierend auf Linux Pacemaker unter SUSE und Red Hat. Ausführliche Informationen finden Sie in diesen Artikeln:
    - [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- Konfigurationen (n+m) zum Aufskalieren von SAP HANA unter Verwendung von [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) unter SUSE und Red Hat. Detaillierte Informationen erhalten Sie in diesen Artikeln:
    - [Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server-Failovercluster basierend auf Windows-Dateidiensten für das Aufskalieren. Für Produktionssysteme wird allerdings empfohlen, SQL Server Always On anstelle des Clustering zu verwenden. SQL Server Always On bietet eine bessere Verfügbarkeit durch separaten Speicher. Ausführliche Informationen finden Sie in diesem Artikel: 
    - [Konfigurieren der SQL Server-Failoverclusterinstanz auf virtuellen Azure-Computern](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On wird mit den Windows-Betriebssystemen für SQL Server in Azure unterstützt. Dies ist die Standardempfehlung für SQL Server-Produktionsinstanzen in Azure. Detaillierte Informationen erhalten Sie in diesen Artikeln:
    - [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)
    - [Konfigurieren einer Always On-Verfügbarkeitsgruppe auf virtuellen Azure-Computern in verschiedenen Regionen](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
    - [Konfigurieren einer Load Balancer-Instanz für eine Always On-Verfügbarkeitsgruppe in Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)
- Oracle Data Guard für Windows und Oracle Linux. Ausführliche Informationen zu Oracle Linux finden Sie in diesem Artikel:
    - [Implementieren von Oracle Data Guard auf einem virtuellen Azure-Linux-Computer](../oracle/configure-oracle-dataguard.md)
- IBM Db2 HADR unter SUSE und RHEL. Eine detaillierte Dokumentation für SUSE und RHEL unter Verwendung von Pacemaker finden Sie hier:
    - [Hochverfügbarkeit von IBM Db2 LUW auf Azure-VMs unter SUSE Linux Enterprise Server mit Pacemaker](./dbms-guide-ha-ibm.md)
    - [Hochverfügbarkeit von IBM Db2 LUW auf Azure-VMs unter Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP ASE- und SAP maxDB-Konfigurationen, wie in diesen Dokumenten beschrieben:
    - [Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload](./dbms_guide_sapase.md)
    - [Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server auf Azure-VMs](./dbms_guide_maxdb.md)
- Ausführliche Informationen zu Hochverfügbarkeitsszenarien mit großen HANA-Instanzen finden Sie hier:
    - [Unterstützte Szenarien für große HANA-Instanzen – HSR mit STONITH für Hochverfügbarkeit](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Unterstützte Szenarien für große HANA-Instanzen – Automatisches Hostfailover (1+1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Für keines der oben beschriebenen Szenarien werden Konfigurationen von mehreren DBMS-Instanzen in einer VM unterstützt. Dies bedeutet, dass jeweils nur eine Datenbankinstanz pro VM bereitgestellt und mit den beschriebenen Hochverfügbarkeitsmethoden geschützt werden kann. Der Schutz mehrerer DBMS-Instanzen über denselben Windows- oder Pacemaker-Failovercluster wird aktuell **NICHT** unterstützt. Auch Oracle Data Guard wird nur für Bereitstellungen mit einer einzelnen Instanz pro VM unterstützt. 

Verschiedene Datenbanksysteme ermöglichen das Hosten mehrerer Datenbanken in einer DBMS-Instanz. Wie bei SAP HANA können mehrere Datenbanken in mehreren Datenbankcontainern (MDC) gehostet werden. In Fällen, bei denen diese Konfigurationen mit mehreren Datenbanken innerhalb einer Failoverclusterressource funktionieren, werden diese Konfigurationen unterstützt. Bei nicht unterstützten Konfigurationen sind mehrere Clusterressourcen erforderlich. Dies ist beispielsweise bei Konfigurationen der Fall, in denen Sie mehrere SQL Server-Verfügbarkeitsgruppen in einer SQL Server-Instanz definieren würden.


![DBMS-Hochverfügbarkeitskonfiguration](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Je nach DBMS und/oder Betriebssystem können Komponenten wie z. B. der Azure-Lastenausgleich als Teil der Lösungsarchitektur erforderlich sein oder auch nicht. 

Insbesondere für maxDB muss eine andere Speicherkonfiguration verwendet werden. In maxDB müssen die Daten und Protokolldateien für Hochverfügbarkeitskonfigurationen in einem gemeinsam verwendeten Speicher abgelegt werden. Nur im Fall von maxDB wird der gemeinsame Speicher für Hochverfügbarkeit unterstützt. Für alle anderen DBMS werden ausschließlich separate Speicherstapel pro Knoten als Datenträgerkonfigurationen unterstützt.

Es sind weitere Hochverfügbarkeitsframeworks vorhanden, die ebenfalls in Microsoft Azure ausgeführt werden können. Microsoft hat diese Frameworks jedoch nicht getestet. Wenn Sie Ihre Hochverfügbarkeitskonfiguration mit einem dieser Frameworks erstellen möchten, müssen Sie im Hinblick auf die folgenden Punkte mit dem Anbieter dieser Software zusammenarbeiten:

- Entwicklung einer Bereitstellungsarchitektur
- Bereitstellung der Architektur
- Unterstützung der Architektur

> [!IMPORTANT]
> Im Microsoft Azure Marketplace stehen zahlreiche Softwareappliances bereit, die Speicherlösungen zusätzlich zu den nativen Azure-Speichertypen bieten. Diese Softwareappliances können auch zum Erstellen von NFS-Freigaben verwendet werden, die theoretisch in Bereitstellungen zum Aufskalieren von SAP HANA verwendet werden könnten, wenn ein Standbyknoten erforderlich ist. Aus verschiedenen Gründen wird keine dieser Speichersoftwareappliances für eine der DBMS-Bereitstellungen von Microsoft und SAP in Azure unterstützt. Bereitstellungen von DBMS in SMB-Freigaben werden zum jetzigen Zeitpunkt gar nicht unterstützt. Die Bereitstellung von DBMS in NFS-Freigaben ist auf NFS 4.1-Freigaben in [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) beschränkt.


## <a name="high-availability-for-sap-central-service"></a>Hochverfügbarkeit für SAP Central Services
SAP Central Services ist ein zweiter Single Point of Failure (SPOF) Ihrer SAP-Konfiguration. Als Ergebnis müssen Sie diese Central Services-Prozesse ebenfalls schützen. Für die SAP-Workload werden die folgenden Angebote unterstützt und dokumentiert:

- Windows Server-Failovercluster unter Verwendung von Windows-Diensten für das Aufskalieren für das sapmnt- und das globale Transportverzeichnis. Ausführliche Informationen finden Sie in diesem Artikel:
    - [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows Server-Failovercluster unter Verwendung einer SMB-Freigabe in [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) für das sapmnt- und das globale Transportverzeichnis. Ausführliche Informationen finden Sie in diesem Artikel:
    - [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Windows mit Azure NetApp Files (SMB) für SAP-Anwendungen](./high-availability-guide-windows-netapp-files-smb.md)
- Windows Server-Failovercluster basierend auf SIOS `Datakeeper`. Trotz Dokumentation durch Microsoft benötigen Sie eine Supportbeziehung mit SIOS, damit Sie bei Verwendung dieser Lösung den SIOS-Support in Anspruch nehmen können. Ausführliche Informationen finden Sie in diesem Artikel:
    - [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker unter einem SUSE-Betriebssystem mit Erstellung einer hochverfügbaren NFS-Freigabe unter Verwendung von zwei SUSE-VMs und `drdb` für die Dateireplikation. Ausführliche Informationen werden in den folgenden Artikeln dokumentiert:
    - [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse.md)
    - [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Pacemaker SUSE-Betriebssystem mit Nutzung von NFS-Freigaben, bereitgestellt durch [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Ausführliche Informationen sind hier dokumentiert:
    - [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-suse-netapp-files.md)
- Pacemaker unter einem Red Hat-Betriebssystem mit NFS-Freigabe, gehostet auf einem `glusterfs`-Cluster. Weitere Informationen finden Sie in den folgenden Artikeln:
    - [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker unter einem Red Hat-Betriebssystem mit NFS-Freigabe, gehostet in [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Ausführliche Informationen finden Sie in diesem Artikel:
    - [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-rhel-netapp-files.md)

Für die aufgeführten Lösungen benötigen Sie eine Supportbeziehung mit SIOS, um das `Datakeeper`-Produkt zu unterstützen und bei Problemen direkt mit SIOS in Kontakt zu treten. Abhängig von der verwendeten Lizenzierung für das Windows-, Red Hat- und/oder SUSE-Betriebssystem müssen Sie möglicherweise auch einen Supportvertrag mit Ihrem Betriebssystemanbieter abschließen, um volle Unterstützung für die aufgeführten Hochverfügbarkeitskonfigurationen zu erhalten.

Die Konfiguration kann folgendermaßen aussehen:

![Hochverfügbarkeitskonfiguration für DBMS und ASCS](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Die rechte Seite der Grafik zeigt die hochverfügbaren SAP Central Services-Instanzen. Neben dem Schutz der SAP Central Services-Instanzen durch ein Failoverclusterframework, das im Falle eines Problems übernehmen kann, ist eine hochverfügbare NFS- oder SMB-Freigabe oder ein freigegebener Windows-Datenträger erforderlich, um sicherzustellen, dass das sapmnt- und das globale Transportverzeichnis unabhängig vom Vorhandensein einer einzelnen VM verfügbar sind. Zusätzlich ist für einige der Lösungen, beispielsweise für den Windows Server-Failovercluster und für Pacemaker, ein Azure-Lastenausgleich erforderlich, um den Datenverkehr an einen fehlerfreien Knotenpunkt zu leiten oder umzuleiten.

In der gezeigten Liste wird das Betriebssystem Oracle Linux nicht erwähnt. Oracle Linux bietet keine Unterstützung für Pacemaker als Clusterframework. Wenn Sie Ihr SAP-System unter Oracle Linux bereitstellen möchten und ein Hochverfügbarkeitsframework für Oracle Linux benötigen, müssen Sie mit Drittanbietern zusammenarbeiten. Ein Anbieter ist SIOS mit seiner Protection Suite für Linux, die von SAP in Azure unterstützt wird. Weitere Informationen finden Sie im SAP-Hinweis [Nr. 1662610: Details zur Unterstützung der SIOS Protection Suite für Linux](https://launchpad.support.sap.com/#/notes/1662610).



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Unterstützter Speicher für die oben aufgeführten Szenarien mit SAP Central Services
Da nur eine Teilmenge der Azure-Speichertypen hochverfügbare NFS- oder SMB-Freigaben bieten, die für den Einsatz in unseren SAP Central Services Cluster-Szenarien geeignet sind, finden Sie nachfolgend eine Liste der unterstützten Speichertypen.

- Ein Windows Server-Failovercluster mit Windows-Dateiserver für das Aufskalieren kann – mit Ausnahme von Azure NetApp Files – für alle nativen Azure-Speichertypen bereitgestellt werden. Es wird jedoch empfohlen, aufgrund von überlegenen Service-Level-Vereinbarungen in Bezug auf Durchsatz und IOPS Storage Premium zu nutzen.
- Ein Windows Server-Failovercluster mit SMB in Azure NetApp Files wird für Azure NetApp Files unterstützt. SMB-Freigaben werden in Azure-Dateidiensten aktuell **NICHT** unterstützt.
- Ein Windows Server-Failovercluster mit einem freigegebenen Windows-Datenträger basierend auf SIOS `Datakeeper` kann – mit Ausnahme von Azure NetApp Files – für alle nativen Azure-Speichertypen bereitgestellt werden. Es wird jedoch empfohlen, aufgrund von überlegenen Service-Level-Vereinbarungen in Bezug auf Durchsatz und IOPS Storage Premium zu nutzen.
- SUSE oder Red Hat Pacemaker mit Verwendung von NFS-Freigaben in Azure NetApp Files werden für Azure NetApp Files unterstützt. 
- SUSE Pacemaker mit einer `drdb`-Konfiguration zwischen zwei VMs wird unter Verwendung nativer Azure-Speichertypen (mit Ausnahme von Azure NetApp Files) unterstützt. Es wird jedoch empfohlen, aufgrund von überlegenen Service-Level-Vereinbarungen in Bezug auf Durchsatz und IOPS Storage Premium zu nutzen.
- Red Hat Pacemaker mit Verwendung von `glusterfs` zur Bereitstellung einer NFS-Freigabe wird unter Verwendung nativer Azure-Speichertypen (mit Ausnahme von Azure NetApp Files) unterstützt. Es wird jedoch empfohlen, aufgrund von überlegenen Service-Level-Vereinbarungen in Bezug auf Durchsatz und IOPS Storage Premium zu nutzen.

> [!IMPORTANT]
> Im Microsoft Azure Marketplace stehen zahlreiche Softwareappliances bereit, die Speicherlösungen zusätzlich zu den nativen Azure-Speichertypen bieten. Diese Softwareappliances können auch zum Erstellen von NFS- oder SMB-Freigaben verwendet und theoretisch im Failovercluster für SAP Central Services genutzt werden. Diese Lösungen werden von Microsoft nicht direkt für die SAP-Workload unterstützt. Wenn Sie sich für eine solche Lösung entscheiden, um Ihre NFS- oder SMB-Freigabe zu erstellen, muss die Unterstützung für die SAP Central Services-Konfiguration durch den Drittanbieter bereitgestellt werden, der die Rechte an der Software in der Speichersoftwareappliance besitzt.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>SAP Central Services-Failovercluster mit Multi-SID-Konfiguration
Um die Anzahl der in großen SAP-Landschaften benötigten Azure-VMs zu verringern, ermöglicht SAP die Ausführung von SAP Central Services-Instanzen mehrerer verschiedener SAP-Systeme in einer Failoverclusterkonfiguration. Stellen Sie sich Fälle vor, in denen Sie über 30 oder mehr NetWeaver- oder S/4HANA-Produktionssysteme verfügen. Ohne Multi-SID-Clustering werden für diese Konfigurationen 60 oder mehr VMs in 30 oder mehr Windows- oder Pacemaker-Failoverclusterkonfigurationen benötigt. Darüber hinaus werden DBMS-Failovercluster benötigt. Durch eine Bereitstellung mehrerer SAP Central Services-Instanzen auf zwei Knoten in einer Failoverclusterkonfiguration kann die Anzahl von VMs erheblich reduziert werden. Die Bereitstellung mehrerer SAP Central Services-Instanzen in einer einzigen Clusterkonfiguration mit zwei Knoten hat jedoch auch einige Nachteile. Probleme mit einer einzelne VM in der Clusterkonfiguration wirken sich auf mehrere SAP-Systeme aus. Die Wartung des Gastbetriebssystems, das in der Clusterkonfiguration ausgeführt wird, erfordert mehr Koordination, da mehrere SAP-Produktionssysteme betroffen sind. Tools wie SAP LaMa unterstützen kein Multi-SID-Clustering im Prozess zum Klonen des Systems.

In Azure wird eine Multi-SID-Clusterkonfiguration für das Windows-Betriebssystem mit ENSA1 und ENSA2 unterstützt. Es wird empfohlen, die ältere Enqueue Replication Service-Architektur (ENSA1) nicht mit der neuen Architektur (ENSA2) auf einem Multi-SID-Cluster zu kombinieren. Ausführliche Informationen zu einer solchen Architektur finden Sie in den folgenden Artikeln:

- [Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

Für SUSE wird auch ein Multi-SID-Cluster basierend auf Pacemaker unterstützt. Bisher wird die Konfiguration unterstützt für:

- Maximal 5 SAP ASCS/SCS-Instanzen
- Die alte Enqueue Replication Service-Architektur (ENSA1)
- Pacemaker-Clusterkonfigurationen mit 2 Knoten

Die Konfiguration wird dokumentiert in [Multi-SID-Hochverfügbarkeitsleitfaden für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse-multi-sid.md).

Ein Multi-SID-Cluster mit Enqueue Replication-Server sieht schematisch wie folgt aus:

![Diagramm eines Multi-SID-Clusters mit Enqueue Replication-Server.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Szenarien zum Aufskalieren von SAP HANA
Szenarien zum Aufskalieren von SAP HANA werden für eine Teilmenge der HANA-zertifizierten Azure-VMs unterstützt, siehe hierzu die Liste im [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Alle VMs, die in der Spalte „Clustering“ mit „Ja“ markiert sind, können entweder für das Aufskalieren von OLAP oder S/4HANA verwendet werden. Konfigurationen ohne Standby werden für die folgenden Azure Storage-Typen unterstützt: 

- Azure Storage Premium, einschließlich Azure-Schreibbeschleunigung für das /hana/log-Volume
- [Ultra-Datenträger](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA-Konfiguration für das Aufskalieren für OLAP oder S/4HANA mit Standbyknoten werden ausschließlich mit NFS-Freigaben unterstützt, die in Azure NetApp Files gehostet werden.

Weitere Informationen zu den genauen Speicherkonfigurationen mit oder ohne Standbyknoten finden Sie in diesen Artikeln:

- [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md) 
- [Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP-Supporthinweis Nr. 2080991](https://launchpad.support.sap.com/#/notes/2080991)

Einzelheiten zu den von großen HANA-Instanzen unterstützten Konfigurationen für das Aufskalieren von HANA finden Sie in der folgenden Dokumentation:

- [Unterstützte Szenarien für große HANA-Instanzen – Horizontales Hochskalieren mit Standby](./hana-supported-scenario.md#scale-out-with-standby)
- [Unterstützte Szenarien für große HANA-Instanzen – Horizontales Hochskalieren ohne Standby](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Szenarien für die Notfallwiederherstellung
Es gibt eine Vielzahl von Notfallwiederherstellungsszenarien, die unterstützt werden. Notfallarchitekturen sind als Architekturen definiert, die den Ausfall einer vollständigen Azure-Region kompensieren sollen. Dies bedeutet, dass wir als Ziel für die Notfallwiederherstellung eine andere Azure-Region benötigen als diejenige, die für den Betrieb Ihrer SAP-Landschaft verwendet wird. Wir trennen Methoden und Konfigurationen in DBMS-Ebene und Nicht-DBMS-Ebene. 

### <a name="dbms-layer"></a>DBMS-Ebene
Für die DBMS-Ebene werden Konfigurationen mit den DBMS-nativen Replikationsmechanismen unterstützt, beispielsweise Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On oder HANA-Systemreplikation. Es ist zwingend erforderlich, dass der Replikationsstream in solchen Fällen asynchron ist, statt synchron wie in typischen Hochverfügbarkeitsszenarien mit Bereitstellung innerhalb einer einzigen Azure-Region. Ein typisches Beispiel für eine solche unterstützte DBMS-Konfiguration für die Notfallwiederherstellung wird im Artikel [Verfügbarkeit von SAP HANA in Azure in verschiedenen Azure-Regionen](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions) beschrieben. Die zweite Abbildung in diesem Abschnitt beschreibt ein Szenario mit HANA als Beispiel. Die wichtigsten Datenbanken, die für SAP-Anwendungen unterstützt werden, können alle in einem solchen Szenario eingesetzt werden.

In der Region für die Notfallwiederherstellung wird die Verwendung einer kleineren VM als Zielinstanz unterstützt, weil diese VM nicht den gesamten Workloaddatenverkehr verarbeiten muss. Hierbei müssen Sie die folgenden Punkte berücksichtigen:

- An kleinere VM-Typen können weniger Datenträger angefügt werden.
- Kleinere VMs bieten einen geringeren Netzwerk- und Speicherdurchsatz.
- Eine Größenänderung zwischen VM-Familien kann ein Problem darstellen, wenn die verschiedenen VMs in einer Verfügbarkeitsgruppe zusammengefasst sind oder wenn die Größenänderung zwischen VMs der M-Serie und der Mv2-Familie erfolgen soll.
- Es muss ausreichend CPU-Kapazität und Arbeitsspeicher vorhanden sein, damit die Datenbankinstanz den Änderungsstream mit minimaler Verzögerung empfangen und diese Änderungen mit minimaler Verzögerung auf die Daten anwenden kann.  

Weitere Details zu den Beschränkungen für verschiedene VM-Größen finden Sie [hier](../../sizes.md). 

Eine weitere unterstützte Methode zur Bereitstellung eines Notfallwiederherstellungsziels ist die Installation einer zweiten DBMS-Instanz auf einer VM, die eine nicht in der Produktion eingesetzte DBMS-Instanz einer nicht in der Produktion eingesetzten SAP-Instanz ausführt. Dies kann eine Herausforderung darstellen, da Sie herausfinden müssen, welche Mengen an Arbeitsspeicher, CPU-Ressourcen, Netzwerkbandbreite und Speicherbandbreite für die jeweiligen Zielinstanzen benötigt werden, die im Notfallwiederherstellungsszenario als Hauptinstanz fungieren sollen. Insbesondere in HANA wird dringend empfohlen, die als Notfallwiederherstellungsziel verwendete Instanz auf einem gemeinsam genutzten Host zu konfigurieren, damit die Daten nicht vorab in die Zielinstanz für die Notfallwiederherstellung geladen werden.

Informationen zu Notfallwiederherstellungsszenarien für große HANA-Instanzen finden Sie in diesen Dokumenten:

- [Einzelner Knoten mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Einzelner Knoten mit DR (Mehrzweck) mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Einzelner Knoten mit DR (Mehrzweck) mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Hochverfügbarkeit mit HSR und Notfallwiederherstellung mit Speicherreplikation](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Horizontale Skalierung mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Einzelner Knoten mit DR über HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR in einem einzelnen Knoten für Notfallwiederherstellung (kostenoptimiert)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Hochverfügbarkeit und Notfallwiederherstellung mit HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Hochverfügbarkeit und Notfallwiederherstellung mit HSR (kostenoptimiert)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Horizontale Skalierung mit DR über HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> Die Verwendung von [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) wurde nicht für DBMS-Bereitstellungen mit einer SAP-Workload getestet. Deshalb wird zum aktuellen Zeitpunkt keine Unterstützung für die DBMS-Ebene von SAP-Systemen bereitgestellt. Andere, nicht aufgeführte Replikationsmethoden von Microsoft und SAP werden nicht unterstützt. Die Verwendung von Drittanbietersoftware für die Replikation der DBMS-Ebene von SAP-Systemen zwischen verschiedenen Azure-Regionen muss vom Herausgeber der Software unterstützt werden und wird nicht über die Supportkanäle von Microsoft und SAP unterstützt. 
 
## <a name="non-dbms-layer"></a>Nicht-DBMS-Ebene
Für die SAP-Anwendungsschicht und eventuell benötigte Freigaben oder Speicherorte nutzen Kunden hauptsächlich diese beiden Szenarien:

- Die Notfallwiederherstellungsziele in der zweiten Azure-Region werden weder für Produktions- noch für Nicht-Produktionszwecke genutzt. In diesem Szenario werden die VMs, die als Notfallwiederherstellungsziel fungieren, idealerweise nicht bereitgestellt, und das Image sowie Änderungen an den Images der SAP-Anwendungsschicht aus der Produktion werden in die Region für die Notfallwiederherstellung repliziert. Diese Aufgabe kann beispielsweise durch [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md) erfüllt werden. Azure Site Recovery bietet Unterstützung für Azure-zu-Azure-Replikationsszenarien wie diese. 
- Die Notfallwiederherstellungsziele sind VMs, die tatsächlich von Nicht-Produktionssystemen verwendet werden. Die gesamte SAP-Landschaft verteilt sich auf zwei verschiedene Azure-Regionen, von denen die eine Region in der Regel Produktionssysteme und die andere Region Nicht-Produktionssysteme enthält. Bei vielen Kundenbereitstellungen verfügt der Kunde über ein Nicht-Produktionssystem, das einem Produktionssystem entspricht. Der Kunde hat Produktionsanwendungsinstanzen in der Anwendungsschicht der Nicht-Produktionssysteme vorinstalliert. Im Falle eines Failovers werden in diesem Szenario die nicht produktionsbezogenen Instanzen heruntergefahren, die virtuellen Namen der Produktions-VMs in die Nicht-Produktions-VMs verschoben (nach Zuweisung neuer IP-Adressen in DNS) und anschließend die vorinstallierten Produktionsinstanzen in Betrieb genommen.

### <a name="sap-central-services-clusters"></a>SAP Central Services-Cluster
SAP Central Services-Cluster, die gemeinsam genutzte Datenträger (Windows), SMB-Freigaben (Windows) oder NFS-Freigaben verwenden, sind etwas schwieriger zu replizieren. Für Windows kann die Windows-Speicherreplikation verwendet werden. Unter Linux ist rsync eine brauchbare Lösung.



## <a name="non-supported-scenario"></a>Nicht unterstützte Szenarien
Nachfolgend finden Sie eine Liste der Szenarien, die für die SAP-Workload in Azure-Architekturen nicht unterstützt werden. **Nicht unterstützt** bedeutet, dass SAP und Microsoft nicht in der Lage sind, diese Konfigurationen zu unterstützen und auf einen eventuell beteiligten Drittanbieter zurückgreifen müssen, der die Software zur Einrichtung solcher Architekturen bereitstellt. Zwei der Kategorien sind:

- Speichersoftwareappliances: Im Azure Marketplace werden verschiedene Speichersoftwareappliances angeboten. Einige Anbieter stellen eine eigene Dokumentation zur Verwendung dieser Speichersoftwareappliances in Azure im Zusammenhang mit SAP-Software bereit. Die Unterstützung von Konfigurationen oder Bereitstellungen, die solche Speichersoftwareappliances beinhalten, muss vom Anbieter dieser Speichersoftwareappliances bereitgestellt werden. Dies wird auch im [SAP-Supporthinweis Nr. 2015553](https://launchpad.support.sap.com/#/notes/2015553) erläutert.
- Hochverfügbarkeitsframeworks: Nur Pacemaker und Windows Server-Failovercluster werden als Hochverfügbarkeitsframeworks für die SAP-Workload in Azure unterstützt. Wie bereits erwähnt, wird die SIOS-Lösung `Datakeeper` von Microsoft beschrieben und dokumentiert. Dennoch müssen die Komponenten von SIOS `Datakeeper` durch SIOS als Anbieter dieser Komponenten unterstützt werden. SAP hat in verschiedenen SAP-Hinweisen auch andere zertifizierte Hochverfügbarkeitsframeworks aufgeführt. Einige dieser Frameworks wurden vom jeweiligen Drittanbieter auch für Azure zertifiziert. Trotzdem muss die Unterstützung für Konfigurationen, die diese Produkte verwenden, durch den Produktanbieter bereitgestellt werden. Verschiedene Anbieter integrieren ihre Produkte auf unterschiedliche Weise in die SAP-Unterstützungsprozesse. Sie sollten klären, welcher Unterstützungsprozess für den jeweiligen Anbieter am besten funktioniert, bevor Sie sich für die Verwendung des Produkts in SAP-Konfigurationen entscheiden, die in Azure eingesetzt werden.
- Cluster mit gemeinsam genutzten Datenträgern, bei denen sich die Datenbankdateien auf den gemeinsam genutzten Datenträgern befinden, werden mit Ausnahme von maxDB nicht unterstützt. Für alle weiteren Datenbanken besteht die unterstützte Lösung darin, anstelle einer SMB- oder NFS-Freigabe oder eines freigegebenen Datenträgers separate Speicherorte zu verwenden, um ein Hochverfügbarkeitsszenario zu konfigurieren.

Weitere Szenarien, die nicht unterstützt werden, sind folgende:

- Bereitstellungsszenarien, die zu größerer Netzwerklatenz zwischen der SAP-Anwendungsschicht und der SAP-DBMS-Ebene in der gemeinsamen Architektur von SAP führen, wie beispielsweise in NetWeaver, S/4HANA und `Hybris`. Dies schließt Folgendes ein:
    - Bereitstellung einer der Ebenen als lokale Ebene, während die andere Ebene in Azure bereitgestellt wird.
    - Bereitstellung der SAP-Anwendungsebene eines Systems in einer anderen Azure-Region als derjenigen mit der DBMS-Ebene.
    - Bereitstellung einer Ebene in Rechenzentren, die parallel zu Azure ausgeführt werden, und der anderen Ebene in Azure – es sei denn, ein solches Architekturmuster wird durch einen nativen Azure-Dienst bereitgestellt.
    - Bereitstellung virtueller Netzwerkappliances zwischen der SAP-Anwendungsebene und der DBMS-Ebene.
    - Nutzung von Speicher, der in parallel zu Azure verwendeten Rechenzentren gehostet wird, für die SAP DBMS-Ebene oder das globale SAP-Transportverzeichnis.
    - Bereitstellung der beiden Ebenen mit zwei verschiedenen Cloudanbietern. Dies wäre z. B. der Fall, wenn die DBMS-Ebene in einer Oracle-Cloudinfrastruktur und die Anwendungsschicht in Azure bereitgestellt würde.
- HANA-Pacemaker-Clusterkonfigurationen mit mehreren Instanzen
- Windows-Clusterkonfigurationen mit gemeinsam genutzten Datenträgern über SOFS oder SMB in Azure NetApp Files für unter Windows unterstützte SAP-Datenbanken. Stattdessen wird die Verwendung einer nativen Hochverfügbarkeitsreplikation der jeweiligen Datenbanken und die Verwendung separater Speicherstapel empfohlen.
- Bereitstellung von unter Linux unterstützten SAP-Datenbanken mit Datenbankdateien in NFS-Freigaben zusätzlich zu Azure NetApp Files, ausgenommen ist SAP HANA.
- Bereitstellung von Oracle DBMS auf einem anderen Gastbetriebssystem als Windows und Oracle Linux. Siehe auch [SAP-Supporthinweis Nr. 2039619](https://launchpad.support.sap.com/#/notes/2039619)

Szenarien, die nicht getestet wurden und für die deshalb keine Erfahrungswerte vorliegen:

- Azure Site Recovery zur Replikation von VMs der DBMS-Ebene. Daher wird die Nutzung der nativen asynchronen Replikationsfunktionalität der Datenbank für eine Notfallwiederherstellungskonfiguration empfohlen.
 

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Informationen zu den nächsten Schritten unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](./planning-guide.md).




  