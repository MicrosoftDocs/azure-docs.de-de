---
title: 'Hochverfügbarkeit virtueller Azure-Computer für SAP NetWeaver unter SLES: Multi-SID-Leitfaden | Microsoft-Dokumentation'
description: Multi-SID-Hochverfügbarkeitsleitfaden für SAP NetWeaver unter SUSE Linux Enterprise Server für SAP-Anwendungen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 2ce57911434aa0fdf1a5e624090633e75d98a5ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484242"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Multi-SID-Hochverfügbarkeitsleitfaden für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In diesem Artikel erfahren Sie, wie Sie mehrere hochverfügbare SAP NetWeaver- oder S4HANA-Systeme (d. h. Multi-SID) in einem Cluster mit zwei Knoten auf virtuellen Azure-Computern mit SUSE Linux Enterprise Server für SAP-Anwendungen bereitstellen.  

In den Beispielkonfigurationen, Installationsbefehlen usw. werden drei SAP NetWeaver 7.50-Systeme in einem einzelnen hochverfügbaren Cluster mit zwei Knoten bereitgestellt. Die SIDs der SAP-Systeme lauten:
* **NW1:** ASCS-Instanznummer **00** und virtueller Hostname **msnw1ascs**; ERS-Instanznummer **02** und virtueller Hostname **msnw1ers**  
* **NW2:** ASCS-Instanznummer **10** und virtueller Hostname **msnw2ascs**; ERS-Instanznummer **12** und virtueller Hostname **msnw2ers**  
* **NW3:** ASCS-Instanznummer **20** und virtueller Hostname **msnw3ascs**; ERS-Instanznummer **22** und virtueller Hostname **msnw3ers**  

Die Datenbankebene und die Bereitstellung der SAP-NFS-Freigaben werden in diesem Artikel nicht behandelt. In den Beispielen in diesem Artikel verwenden wir die virtuellen Namen „nw2-nfs“ für die NW2-NFS-Freigaben und „nw3-nfs“ für die NW3-NFS-Freigaben. Es wird davon ausgegangen, dass der NFS-Cluster bereitgestellt wurde.  

Machen Sie sich zunächst mit den folgenden SAP-Hinweisen und Dokumenten vertraut:

* SAP-Hinweis [1928533][1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure

* In SAP-Hinweis [2015553][2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2205917][2205917] enthält empfohlene Betriebssystemeinstellungen für den SUSE Linux Enterprise Server for SAP Applications.
* SAP-Hinweis [1944799][1944799] enthält SAP HANA-Richtlinien für den SUSE Linux Enterprise Server for SAP Applications.
* SAP-Hinweis [2178632][2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498][2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692][2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1984787][1984787] enthält allgemeine Informationen zu SUSE Linux Enterprise Server 12.
* SAP-Hinweis [1999351][1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [Best Practices für SUSE SAP HA][suse-ha-guide] Die Leitfäden enthalten alle erforderlichen Informationen, um NetWeaver HA und SAP HANA System Replication vor Ort einzurichten. Verwenden Sie diese Leitfäden als allgemeine Basis. Sie bieten wesentlich mehr Informationen.
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes] (Versionshinweise zur SUSE-Hochverfügbarkeitserweiterung 12 SP3, in englischer Sprache)
* [SUSE-Multi-SID-Cluster-Handbuch für SLES 12 und SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]
## <a name="overview"></a>Übersicht

Die virtuellen Computer des Clusters müssen groß genug sein, um im Falle eines Failovers alle Ressourcen ausführen zu können. Von jeder SAP-SID kann im Multi-SID-Hochverfügbarkeitscluster unabhängig ein Failover durchgeführt werden.  Bei Verwendung der SBD-Umgrenzung können die SBD-Geräte von mehreren Clustern gemeinsam genutzt werden.  

Für Hochverfügbarkeit benötigt SAP NetWeaver hochverfügbare NFS-Freigaben. In diesem Beispiel wird davon ausgegangen, dass die SAP-NFS-Freigaben entweder auf einem hoch verfügbaren [NFS-Dateiserver](./high-availability-guide-suse-nfs.md) gehostet werden, der von mehreren SAP-Systemen verwendet werden kann, oder dass die Freigaben unter [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereitgestellt werden.  

![Für Pacemaker-Cluster werden ausführliche Informationen zu zwei Multi-SID-Clustern (msidcl1 und msidcl2) angezeigt.](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Die Unterstützung von Multi-SID-Clustering von SAP ASCS/ERS mit SUSE Linux als Gastbetriebssystem auf virtuellen Azure-Computern ist auf **fünf** SAP-SIDs pro Cluster beschränkt. Durch jede neue SID erhöht sich die Komplexität. Eine Kombination aus SAP Enqueue Replication Server 1 und Enqueue Replication Server 2 im gleichen Cluster wird **nicht** unterstützt. Als Multi-SID-Clustering wird die Installation mehrerer SAP ASCS/ERS-Instanzen mit verschiedenen SIDs in einem Pacemaker-Cluster beschrieben. Aktuell wird Multi-SID-Clustering nur für ASCS/ERS unterstützt.  

> [!TIP]
> Das Multi-SID-Clustering von SAP ASCS/ERS ist eine Lösung mit höherer Komplexität. Dadurch erhöht sich die Komplexität bei der Implementierung. Wartungsaktivitäten wie etwa das Patchen des Betriebssystems sind außerdem mit einem höheren Verwaltungsaufwand verbunden. Bevor Sie mit der eigentlichen Implementierung beginnen, empfiehlt es sich, die Bereitstellung und alle beteiligten Komponenten wie virtuelle Computer, NFS-Einbindungen, VIPs, Lastenausgleichskonfigurationen und Ähnliches sorgfältig zu planen.  

Der NFS-Server, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS und die SAP HANA-Datenbank verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden.  

Die folgende Liste enthält die Konfiguration des (A)SCS- und ERS-Lastenausgleichs für diesen exemplarischen Multi-SID-Cluster mit drei SAP-Systemen. Für die einzelnen SIDs werden jeweils separate Front-End-IP-Adressen, Integritätstests und Lastenausgleichsregeln für jede ASCS- und ERS-Instanz benötigt. Weisen Sie alle virtuellen Computer, die Teil des ASCS-/ERS-Clusters sind, einem einzelnen Back-End-Pool zu.  

### <a name="ascs"></a>(A)SCS

* Frontendkonfiguration
  * IP-Adresse für NW1:  10.3.1.14
  * IP-Adresse für NW2:  10.3.1.16
  * IP-Adresse für NW3:  10.3.1.13
* Testports
  * Port 620 <strong>&lt;Nr.&gt;</strong> (für NW1, NW2 und NW3 also die Testports 620 **00**, 620 **10** und 620 **20**)
* Lastenausgleichsregeln: 
* Erstellen Sie jeweils eine pro Instanz (NW1/ASCS, NW2/ASCS und NW3/ASCS).
  * Wenn Sie Load Balancer Standard verwenden, wählen Sie **HA-Ports** aus.
  * Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
    * 32<strong>&lt;Nr.&gt;</strong> TCP
    * 36<strong>&lt;Nr.&gt;</strong> TCP
    * 39<strong>&lt;Nr.&gt;</strong> TCP
    * 81<strong>&lt;Nr.&gt;</strong> TCP
    * 5<strong>&lt;Nr.&gt;</strong>13 TCP
    * 5<strong>&lt;Nr.&gt;</strong>14 TCP
    * 5<strong>&lt;Nr.&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Frontendkonfiguration
  * IP-Adresse für NW1: 10.3.1.15
  * IP-Adresse für NW2: 10.3.1.17
  * IP-Adresse für NW3: 10.3.1.19
* Testport
  * Port 621 <strong>&lt;Nr.&gt;</strong> (für NW1, NW2 und NW3 also die Testports 621 **02**, 621 **12** und 621 **22**)
* Lastenausgleichsregeln: Erstellen Sie jeweils eine pro Instanz (NW1/ERS, NW2/ERS und NW3/ERS).
  * Wenn Sie Load Balancer Standard verwenden, wählen Sie **HA-Ports** aus.
  * Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
    * 32<strong>&lt;Nr.&gt;</strong> TCP
    * 33<strong>&lt;Nr.&gt;</strong> TCP
    * 5<strong>&lt;Nr.&gt;</strong>13 TCP
    * 5<strong>&lt;Nr.&gt;</strong>14 TCP
    * 5<strong>&lt;Nr.&gt;</strong>16 TCP

* Backendkonfiguration
  * Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des (A)SCS/ERS-Clusters sein sollen

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

> [!Note]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

> [!IMPORTANT]
> Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-nfs-shares"></a>SAP-NFS-Freigaben

SAP NetWeaver benötigt freigegebenen Speicher für den Transport, das Profilverzeichnis und Ähnliches. Ein hoch verfügbares SAP-System setzt hoch verfügbare NFS-Freigaben voraus. Sie müssen sich für eine Architektur für Ihre SAP-NFS-Freigaben entscheiden. Eine Möglichkeit besteht darin, [hoch verfügbare NFS-Cluster auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server][nfs-ha] zu erstellen, die von mehreren SAP-Systemen gemeinsam genutzt werden können. 

Die Freigaben können aber auch unter [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereitgestellt werden.  Azure NetApp Files bietet integrierte Hochverfügbarkeit für die SAP-NFS-Freigaben.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Bereitstellen des ersten SAP-Systems im Cluster

Nachdem Sie sich für die Architektur für die SAP-NFS-Freigaben entschieden haben, können Sie gemäß der entsprechenden Dokumentation das erste SAP-System im Cluster bereitstellen.

* Dokumentation bei Verwendung eines hoch verfügbaren NFS-Servers: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs auf dem SUSE Linux Enterprise Server for SAP Applications](./high-availability-guide-suse.md)  
* Dokumentation bei Verwendung von Azure NetApp Files-NFS-Volumes: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-suse-netapp-files.md)

In den obigen Dokumenten erfahren Sie Schritt für Schritt, wie Sie die erforderlichen Infrastrukturen vorbereiten, den Cluster erstellen und das Betriebssystem für die Ausführung der SAP-Anwendung vorbereiten.  

> [!TIP]
> Testen Sie nach dem Bereitstellen des ersten Systems immer die Failoverfunktion des Clusters, bevor Sie dem Cluster weitere SAP-SIDs hinzufügen. Dadurch wissen Sie, ob die Clusterfunktion funktioniert, bevor die Komplexität des Clusters durch zusätzliche SAP-Systeme erhöht wird.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Bereitstellen zusätzlicher SAP-Systeme im Cluster

In diesem Beispiel wird davon ausgegangen, dass das System **NW1** bereits im Cluster bereitgestellt wurde. Hier wird gezeigt, wie die SAP-Systeme **NW2** und **NW3** im Cluster bereitgestellt werden. 

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

### <a name="prerequisites"></a>Voraussetzungen 

> [!IMPORTANT]
> Bevor Sie die Schritte zum Bereitstellen zusätzlicher SAP-Systeme im Cluster ausführen, müssen die Schritte zum Bereitstellen des ersten SAP-Systems im Cluster ausgeführt werden, da bestimmte Schritte nur bei der ersten Systembereitstellung erforderlich sind.  

In dieser Dokumentation wird Folgendes vorausgesetzt:
* Der Pacemaker-Cluster ist bereits konfiguriert und wird ausgeführt.  
* Mindestens ein SAP-System (ASCS/ERS-Instanz) wurde bereits bereitgestellt und wird im Cluster ausgeführt.  
* Die Failoverfunktion des Clusters wurde getestet.  
* Die NFS-Freigaben für alle SAP-Systeme wurden bereitgestellt.  

### <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

1. Fügen Sie der vorhandenen Azure Load Balancer-Instanz die Konfiguration für das neu bereitgestellte System (**NW2**, **NW3**) hinzu. Eine entsprechende Anleitung finden Sie unter [Manuelles Bereitstellen von Azure Load Balancer über das Azure-Portal](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal). Passen Sie die IP-Adressen, Integritätstestports und Lastenausgleichsregeln für Ihre Konfiguration an.  

2. **[A]** Richten Sie die Namensauflösung für die zusätzlichen SAP-Systeme ein. Sie können entweder einen DNS-Server verwenden oder `/etc/hosts` auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der Datei `/etc/hosts` gezeigt.  Passen Sie die IP-Adressen und die Hostnamen an Ihre Umgebung an. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** Erstellen Sie die freigegebenen Verzeichnisse für die zusätzlichen SAP-Systeme **NW2** und **NW3**, die Sie im Cluster bereitstellen. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Konfigurieren Sie `autofs`, um die Dateisysteme „/sapmnt/SID“ und „/usr/sap/SID/SYS“ für die zusätzlichen SAP-Systeme einzubinden, die Sie im Cluster bereitstellen. In diesem Beispiel sind das **NW2** und **NW3**.  

   Aktualisieren Sie die Datei `/etc/auto.direct` mit den Dateisystemen für die zusätzlichen SAP-Systeme, die Sie im Cluster bereitstellen.  

   * Gehen Sie bei Verwendung eines NFS-Dateiservers wie [hier](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation) beschrieben vor.
   * Gehen Sie bei Verwendung von Azure NetApp Files wie [hier](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) beschrieben vor. 

   Der Dienst `autofs` muss zum Einbinden der neu hinzugefügten Freigaben neu gestartet werden.  

### <a name="install-ascs--ers"></a>Installieren von ASCS/ERS

1. Erstellen Sie die virtuelle IP-Adresse sowie Integritätstest-Clusterressourcen für die ASCS-Instanz des zusätzlichen SAP-Systems, das Sie im Cluster bereitstellen. Das hier gezeigte Beispiel gilt für die ASCS-Instanzen **NW2** und **NW3** mit hoch verfügbarem NFS-Server.  

   > [!IMPORTANT]
   > Kürzlich durchgeführte Tests haben Situationen aufgezeigt, in denen netcat aufgrund von Backlog und der Einschränkung, nur eine Verbindung zu verarbeiten, nicht mehr auf Anforderungen reagiert. Die netcat-Ressource lauscht dann nicht mehr auf Azure Load Balancer-Anforderungen, und die Floating IP-Adresse ist nicht mehr verfügbar.  
   > Für vorhandene Pacemaker-Cluster wurde zuvor empfohlen, netcat durch socat zu ersetzen. Zurzeit wird empfohlen, den Ressourcen-Agent azure-lb zu verwenden, der Teil des Pakets resource-agents ist. Dabei gelten die folgenden Versionsanforderungen für das Paket:
   > - Für SLES 12 SP4/SP5 muss die Version mindestens resource-agents-4.3.018.a7fb5035-3.30.1 sein.  
   > - Für SLES 15/15 SP1 muss die Version mindestens resource-agents-4.3.0184.6ee15eb2-4.13.1 sein.  
   >
   > Beachten Sie, dass für die Änderung eine kurze Ausfallzeit erforderlich ist.  
   > Wenn die Konfiguration bei vorhandenen Pacemaker-Clustern bereits für die Verwendung von socat geändert wurde, wie unter [Azure Load Balancer-Erkennungshärtung](https://www.suse.com/support/kb/doc/?id=7024128) beschrieben, müssen Sie nicht sofort zum Ressourcen-Agent azure-lb wechseln.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Die erstellten Ressourcen werden unter Umständen verschiedenen Clusterressourcen zugewiesen. Wenn Sie sie gruppieren, werden sie zu einem der Clusterknoten migriert. Vergewissern Sie sich, dass der Cluster ordnungsgemäß funktioniert und alle Ressourcen gestartet wurden. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

2. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS als Root-Benutzer. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die ASCS-Instanz zugeordnet ist. Für das System **NW2** setzt sich der virtuelle Hostname beispielsweise aus <b>msnw2ascs</b>, <b>10.3.1.16</b> und der für den Lastenausgleichstest verwendeten Instanznummer (beispielsweise <b>10</b>) zusammen. Für das System **NW3** setzt sich der virtuelle Hostname aus <b>msnw3ascs</b>, <b>10.3.1.13</b> und der für den Lastenausgleichstest verwendeten Instanznummer (beispielsweise <b>20</b>) zusammen.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter „SAPINST_USE_HOSTNAME“ verwenden, um SAP unter Verwendung eines virtuellen Hostnamens zu installieren.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Falls bei der Installation kein Unterordner unter „/usr/sap/**SID**/ASCS **Instanznr.** “ erstellt werden kann, legen Sie den Besitzer auf „**sid** adm“ und die Gruppe auf „sapsys“ des Ordners „ASCS **Instanznr.** “ fest, und versuchen Sie es noch mal.

3. **[1]** Erstellen Sie eine virtuelle IP-Adresse sowie Integritätstest-Clusterressourcen für die ERS-Instanz des zusätzlichen SAP-Systems, das Sie im Cluster bereitstellen. Das hier gezeigte Beispiel gilt für die ERS-Instanzen **NW2** und **NW3** mit hoch verfügbarem NFS-Server. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Die erstellten Ressourcen werden unter Umständen verschiedenen Clusterknoten zugewiesen. Wenn Sie sie gruppieren, werden sie zu einem der Clusterknoten migriert. Vergewissern Sie sich, dass der Cluster ordnungsgemäß funktioniert und alle Ressourcen gestartet wurden.  

   Stellen Sie als Nächstes sicher, dass die Ressourcen der neu erstellten ERS-Gruppe auf dem Clusterknoten ausgeführt werden, und zwar gegenüber dem Clusterknoten, auf dem die ASCS-Instanz für das gleiche SAP-System installiert wurde.  Wenn also beispielsweise die NW2-ASCS-Instanz auf `slesmsscl1` installiert wurde, muss die NW2-ERS-Gruppe auf `slesmsscl2` ausgeführt werden.  Die NW2-ERS-Gruppe kann mithilfe des folgenden Befehls zu `slesmsscl2` migriert werden: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** Installieren Sie SAP NetWeaver ERS.

   Installieren Sie SAP NetWeaver ERS als Root-Benutzer auf dem anderen Knoten. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die ERS-Instanz zugeordnet ist. Für das System **NW2** setzt sich der virtuelle Hostname beispielsweise aus <b>msnw2ers</b>, <b>10.3.1.17</b> und der für den Lastenausgleichstest verwendeten Instanznummer (beispielsweise <b>12</b>) zusammen. Für das System **NW3** setzt sich der virtuelle Hostname aus <b>msnw3ers</b>, <b>10.3.1.19</b> und der für den Lastenausgleichstest verwendeten Instanznummer (beispielsweise <b>22</b>) zusammen. 

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter „SAPINST_USE_HOSTNAME“ verwenden, um SAP unter Verwendung eines virtuellen Hostnamens zu installieren.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Verwenden Sie SWPM SP 20 PL 05 oder höher. Bei niedrigeren Versionen werden die Berechtigungen nicht ordnungsgemäß festgelegt, sodass bei der Installation ein Fehler auftritt.

   Falls bei der Installation kein Unterordner unter „/usr/sap/**NW2**/ERS **Instanznr.** “ erstellt werden kann, legen Sie den Besitzer auf „**sid** adm“ und die Gruppe auf „sapsys“ des Ordners „ERS **Instanznr.** “ fest, und versuchen Sie es noch mal.

   Wenn die ERS-Gruppe des neu bereitgestellten SAP-Systems zu einem anderen Clusterknoten migriert werden musste, vergessen Sie nicht, die Ortseinschränkung für die ERS-Gruppe zu entfernen. Die Einschränkung kann mithilfe des folgenden Befehls entfernt werden. (Das Beispiel gilt für die SAP-Systeme **NW2** und **NW3**.)  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile für die neu installierten SAP-Systeme an. Das im Anschluss gezeigte Beispiel gilt für NW2. Die ASCS/SCS- und ERS-Profile müssen für alle dem Cluster hinzugefügten SAP-Instanzen angepasst werden.  
 
 * ASCS/SCS-Profil

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Stellen Sie sowohl für ENSA1 als auch für ENSA2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.  

 * ERS-Profil

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** Konfigurieren Sie die SAP-Benutzer für das neu bereitgestellte SAP-System (in diesem Beispiel: **NW2** und **NW3**). 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Fügen Sie die ASCS- und ERS-SAP-Dienste für das neu installierte SAP-System der Datei `sapservice` hinzu. Das im Anschluss gezeigte Beispiel gilt für die SAP-Systeme **NW2** und **NW3**.  

   Fügen Sie den ASCS-Diensteintrag zum zweiten Knoten hinzu. Kopieren Sie dann den ERS-Diensteintrag, und fügen Sie ihn auf dem ersten Knoten ein. Führen Sie die Befehle für jedes SAP-System auf dem Knoten aus, auf dem die ASCS-Instanz für das SAP-System installiert wurde.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** Erstellen Sie die SAP-Clusterressourcen für das neu installierte SAP-System. 

   Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen für die SAP-Systeme **NW2** und **NW3** wie folgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
   Wenn Sie mit [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html) (Enqueue-Server 2-Architektur) arbeiten, definieren Sie die Ressourcen für die SAP-Systeme **NW2** und **NW3** wie folgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.
   Das folgende Beispiel zeigt den Clusterressourcenstatus, nachdem die SAP-Systeme **NW2** und **NW3** dem Cluster hinzugefügt wurden: 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Die folgende Abbildung zeigt die Darstellung der Ressourcen in der HA-Webkonsole (Hawk) mit erweiterten Ressourcen für das SAP-System **NW2**:  

   [![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Fortsetzen der SAP-Installation 

Führen Sie die folgenden Schritte aus, um die SAP-Installation abzuschließen:

* [Vorbereiten Ihrer SAP NetWeaver-Anwendungsserver](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Installieren einer DBMS-Instanz](./high-availability-guide-suse.md#install-database)
* [Installieren eines primären SAP-Anwendungsservers](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Installieren mindestens einer zusätzlichen SAP-Anwendungsinstanz

## <a name="test-the-multi-sid-cluster-setup"></a>Testen der Einrichtung des Multi-SID-Clusters

Bei den folgenden Tests handelt es sich um eine Auswahl von Testfällen aus den Best Practices-Leitfäden von SUSE. Sie werden hier der Einfachheit halber bereitgestellt. Die vollständige Liste mit Clustertests finden Sie hier:

* Dokumentation bei Verwendung eines hoch verfügbaren NFS-Servers: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs auf dem SUSE Linux Enterprise Server for SAP Applications](./high-availability-guide-suse.md)  
* Dokumentation bei Verwendung von Azure NetApp Files-NFS-Volumes: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-suse-netapp-files.md)

Lesen Sie immer die Best Practices-Leitfäden von SUSE, und führen Sie alle zusätzlichen Tests aus, die ggf. hinzugefügt wurden.  
Bei den bereitgestellten Tests handelt es sich um Tests in einem Multi-SID-Cluster mit zwei Knoten und drei installierten SAP-Systemen.  

1. Testen Sie „HAGetFailoverConfig“ und „HACheckFailoverConfig“.

   Führen Sie die folgenden Befehle als „<sapsid>adm“ auf dem Knoten aus, auf dem aktuell die ASCS-Instanz ausgeführt wird. Wenn die Befehle mit dem Fehler „Nicht genügend Arbeitsspeicher“ beendet werden, sind die Ursache möglicherweise Bindestriche im Hostnamen. Dies ist ein bekanntes Problem, das von SUSE im Paket sap-suse-cluster-connector behoben wird.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Migrieren Sie die ASCS-Instanz manuell. Das Beispiel zeigt die Migration der ASCS-Instanz für das SAP-System „NW2“.  
   Ressourcenzustand vor dem Test:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Führen Sie die folgenden Befehle als Root-Benutzer aus, um die NW2-ASCS-Instanz zu migrieren:

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Zustand der Ressource nach dem Test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Testen Sie „HAFailoverToNode“. Der hier bereitgestellte Test zeigt die Migration der ASCS-Instanz für das SAP-System „NW2“.  

   Zustand der Ressource vor dem Starten des Tests:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Führen Sie die folgenden Befehle als „**nw2** adm“ aus, um die NW2-ASCS-Instanz zu migrieren:

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Zustand der Ressource nach dem Test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Simulieren eines Knotenabsturzes

   Zustand der Ressource vor dem Starten des Tests:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Führen Sie den folgenden Befehl als Root-Benutzer auf dem Knoten aus, auf dem mindestens eine ASCS-Instanz ausgeführt wird. In diesem Beispiel haben wir den Befehl auf dem Knoten `slesmsscl2` ausgeführt, auf dem die ASCS-Instanzen für „NW1“ und „NW3“ ausgeführt werden.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Bei Verwendung von SBD sollte Pacemaker nicht automatisch auf dem beendeten Knoten gestartet werden. Der Status nach dem Neustart des Knotens sollte wie folgt aussehen.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Verwenden Sie die folgenden Befehle, um Pacemaker auf dem beendeten Knoten zu starten und um die SBD-Nachrichten und die fehlerhaften Ressourcen zu bereinigen.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Zustand der Ressource nach dem Test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].