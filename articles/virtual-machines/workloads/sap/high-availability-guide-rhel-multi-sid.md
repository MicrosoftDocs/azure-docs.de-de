---
title: 'Hochverfügbarkeit von Azure VMs für SAP NW unter RHEL: Multi-SID-Leitfaden | Microsoft-Dokumentation'
description: Richten Sie die Hochverfügbarkeit von SAP NW auf virtuellen Azure-Computern (VMs) unter RHEL mit Multi-SID ein.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: b944ed37fe8df5fd4964342d8c0f52a040612ee4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486401"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

In diesem Artikel wird beschrieben, wie Sie mehrere hochverfügbare SAP NetWeaver-Systeme (Multi-SID) in einem Cluster mit zwei Knoten auf virtuellen Azure-Computern mit Red Hat Enterprise Linux für SAP-Anwendungen bereitstellen.  

In den Beispielkonfigurationen, Installationsbefehlen usw. werden drei SAP NetWeaver 7.50-Systeme in einem einzelnen hochverfügbaren Cluster mit zwei Knoten bereitgestellt. Die SIDs der SAP-Systeme lauten:
* **NW1:** ASCS-Instanznummer **00** und virtueller Hostname **msnw1ascs**; ERS-Instanznummer **02** und virtueller Hostname **msnw1ers**  
* **NW2:** ASCS-Instanznummer **10** und virtueller Hostname **msnw2ascs**; ERS-Instanznummer **12** und virtueller Hostname **msnw2ers**  
* **NW3:** ASCS-Instanznummer **20** und virtueller Hostname **msnw3ascs**; ERS-Instanznummer **22** und virtueller Hostname **msnw3ers**  

Die Datenbankebene und die Bereitstellung der SAP-NFS-Freigaben werden in diesem Artikel nicht behandelt. In den Beispielen in diesem Artikel wird [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)-Volume **sapMSID** für die NFS-Freigaben verwendet, wobei davon ausgegangen wird, dass das Volume bereits bereitgestellt wurde. Außerdem wird davon ausgegangen, dass das Azure NetApp Files-Volume mit dem NFSv3-Protokoll bereitgestellt wurde und dass die folgenden Dateipfade für die Clusterressourcen für die ASCS- und ERS-Instanzen von den SAP-Systemen „NW1“, „NW2“ und „NW3“ vorhanden sind:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Machen Sie sich zunächst mit den folgenden SAP-Hinweisen und Dokumenten vertraut:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* [Azure NetApp Files-Dokumentation][anf-azure-doc]
* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2002167] enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux.
* SAP-Hinweis [2009879] enthält SAP HANA-Richtlinien für Red Hat Enterprise Linux.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [SAP NetWeaver im Pacemaker-Cluster](https://access.redhat.com/articles/3150081)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurieren von ASCS/ERS für SAP NetWeaver mit eigenständigen Ressourcen in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurieren von SAP S/4HANA ASCS/ERS mit eigenständigem Enqueue-Server 2 (ENSA2) in Pacemaker unter RHEL](https://access.redhat.com/articles/3974941)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Übersicht

Die virtuellen Computer des Clusters müssen groß genug sein, um im Falle eines Failovers alle Ressourcen ausführen zu können. Von jeder SAP-SID kann im Multi-SID-Hochverfügbarkeitscluster unabhängig ein Failover durchgeführt werden.  

Für Hochverfügbarkeit benötigt SAP NetWeaver hochverfügbare Freigaben. In dieser Dokumentation werden die Beispiele für die SAP-Freigaben vorgestellt, die auf [Azure NetApp Files NFS-Volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereitgestellt wurden. Es ist auch möglich, die Freigaben auf dem hoch verfügbaren [GlusterFS-Cluster](./high-availability-guide-rhel-glusterfs.md) zu hosten, der von mehreren SAP-Systemen verwendet werden kann.  

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Die Unterstützung von Multi-SID-Clustering von SAP ASCS/ERS mit Red Hat Linux als Gastbetriebssystem auf virtuellen Azure-Computern ist auf **fünf** SAP-SIDs pro Cluster beschränkt. Durch jede neue SID erhöht sich die Komplexität. Eine Kombination aus SAP Enqueue Replication Server 1 und Enqueue Replication Server 2 im gleichen Cluster wird **nicht** unterstützt. Als Multi-SID-Clustering wird die Installation mehrerer SAP ASCS/ERS-Instanzen mit verschiedenen SIDs in einem Pacemaker-Cluster beschrieben. Aktuell wird Multi-SID-Clustering nur für ASCS/ERS unterstützt.  

> [!TIP]
> Das Multi-SID-Clustering von SAP ASCS/ERS ist eine Lösung mit höherer Komplexität. Dadurch erhöht sich die Komplexität bei der Implementierung. Wartungsaktivitäten wie etwa das Patchen des Betriebssystems sind außerdem mit einem höheren Verwaltungsaufwand verbunden. Bevor Sie mit der eigentlichen Implementierung beginnen, empfiehlt es sich, die Bereitstellung und alle beteiligten Komponenten wie virtuelle Computer, NFS-Einbindungen, VIPs, Lastenausgleichskonfigurationen und Ähnliches sorgfältig zu planen.  

SAP NetWeaver ASCS, SAP NetWeaver SCS und SAP NetWeaver ERS verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden.  

Die folgende Liste enthält die Konfiguration des (A)SCS- und ERS-Lastenausgleichs für diesen exemplarischen Multi-SID-Cluster mit drei SAP-Systemen. Für die einzelnen SIDs werden jeweils separate Front-End-IP-Adressen, Integritätstests und Lastenausgleichsregeln für jede ASCS- und ERS-Instanz benötigt. Weisen Sie alle virtuellen Computer, die Teil des ASCS-/ASCS-Clusters sind, einem einzigen Back-End-Pool eines einzelnen ILB zu.  

### <a name="ascs"></a>(A)SCS

* Frontendkonfiguration
  * IP-Adresse für NW1:  10.3.1.50
  * IP-Adresse für NW2:  10.3.1.52
  * IP-Adresse für NW3:  10.3.1.54

* Testports
  * Port 620 <strong>&lt;Nr.&gt;</strong> (für NW1, NW2 und NW3 also die Testports 620 **00**, 620 **10** und 620 **20**)
* Lastenausgleichsregeln: Erstellen Sie jeweils eine pro Instanz (NW1/ASCS, NW2/ASCS und NW3/ASCS).
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
  * IP-Adresse für NW1: 10.3.1.51
  * IP-Adresse für NW2: 10.3.1.53
  * IP-Adresse für NW3: 10.3.1.55

* Testport
  * Port 621 <strong>&lt;Nr.&gt;</strong> (für NW1, NW2 und N3 – also die Testports 621 **02**, 621 **12** und 621 **22**)
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

## <a name="sap-shares"></a>SAP-Freigaben

SAP NetWeaver benötigt freigegebenen Speicher für den Transport, das Profilverzeichnis und Ähnliches. Ein hoch verfügbares SAP-System setzt hoch verfügbare Freigaben voraus. Sie müssen sich für eine Architektur für Ihre SAP-Freigaben entscheiden. Die Freigaben können in [NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereitgestellt werden.  Azure NetApp Files bietet integrierte Hochverfügbarkeit für die SAP-NFS-Freigaben.

Eine weitere Möglichkeit besteht darin, [GlusterFS auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md) zu erstellen, die zwischen mehreren SAP-Systemen freigegeben werden können. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Bereitstellen des ersten SAP-Systems im Cluster

Nachdem Sie sich für Ihre Architektur für die SAP-Freigaben entschieden haben, stellen Sie das erste SAP-System im Cluster gemäß der entsprechenden Dokumentation bereit.

* Dokumentation bei Verwendung von Azure NetApp Files-NFS-Volumes: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-rhel-netapp-files.md)  
* Wenn Sie den GlusterFS-Cluster verwenden, folgen Sie [GlusterFS auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

In den vorstehend aufgeführten Dokumenten erfahren Sie Schritt für Schritt, wie Sie die erforderliche Infrastruktur vorbereiten, den Cluster erstellen und das Betriebssystem für die Ausführung der SAP-Anwendung vorbereiten.  

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

1. Fügen Sie der vorhandenen Azure Load Balancer-Instanz die Konfiguration für das neu bereitgestellte System (**NW2**, **NW3**) hinzu. Eine entsprechende Anleitung finden Sie unter [Manuelles Bereitstellen von Azure Load Balancer über das Azure-Portal](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Passen Sie die IP-Adressen, Integritätstestports und Lastenausgleichsregeln für Ihre Konfiguration an.  

2. **[A]** Richten Sie die Namensauflösung für die zusätzlichen SAP-Systeme ein. Sie können entweder einen DNS-Server verwenden oder `/etc/hosts` auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der Datei `/etc/hosts` gezeigt.  Passen Sie die IP-Adressen und die Hostnamen an Ihre Umgebung an. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
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

4. **[A**] Fügen Sie die Bereitstellungseinträge für die Dateisysteme „/sapmnt/SID“ und „/usr/sap/SID/SYS“ bei den zusätzlichen SAP-Systemen hinzu, die Sie im Cluster bereitstellen. In diesem Beispiel sind das **NW2** und **NW3**.  

   Aktualisieren Sie die Datei `/etc/fstab` mit den Dateisystemen für die zusätzlichen SAP-Systeme, die Sie im Cluster bereitstellen.  

   * Gehen Sie bei Verwendung von Azure NetApp Files wie [hier](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation) beschrieben vor.  
   * Wenn Sie den GlusterFS-Cluster verwenden, folgen Sie den Anleitungen [hier](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation).  

### <a name="install-ascs--ers"></a>Installieren von ASCS/ERS

1. Erstellen Sie die virtuelle IP-Adresse und die Integritätstest-Clusterressourcen für die ASCS-Instanzen der zusätzlichen SAP-Systeme, die Sie im Cluster bereitstellen. Das hier gezeigte Beispiel gilt für **NW2** und **NW3** ASCS, wobei NFS auf Azure NetApp Files-Volumes mit dem NFSv3-Protokoll verwendet wird.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Vergewissern Sie sich, dass der Cluster ordnungsgemäß funktioniert und alle Ressourcen gestartet wurden. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.  

2. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS als Root-Benutzer. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die ASCS-Instanz zugeordnet ist. Beim System **NW2** setzt sich der virtuelle Hostname beispielsweise aus <b>msnw2ascs</b>, <b>10.3.1.52</b> und der für den Lastenausgleichstest verwendeten Instanznummer (z. B. <b>10</b>) zusammen. Beim System **NW3** setzt sich der virtuelle Hostname aus <b>msnw3ascs</b>, <b>10.3.1.54</b> und der für den Lastenausgleichstest verwendeten Instanznummer (z. B <b>20</b>) zusammen. Notieren Sie sich, auf welchem Clusterknoten Sie ASCS für jede SAP-SID installiert haben.  

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter „SAPINST_USE_HOSTNAME“ verwenden, um SAP unter Verwendung eines virtuellen Hostnamens zu installieren.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Falls bei der Installation kein Unterordner unter „/usr/sap/**SID**/ASCS **Instanznr.** “ erstellt werden kann, legen Sie den Besitzer auf „**sid** adm“ und die Gruppe auf „sapsys“ des Ordners „ASCS **Instanznr.** “ fest, und versuchen Sie es noch mal.

3. **[1]** Erstellen Sie eine virtuelle IP-Adresse sowie Integritätstest-Clusterressourcen für die ERS-Instanz des zusätzlichen SAP-Systems, das Sie im Cluster bereitstellen. Das hier gezeigte Beispiel gilt für **NW2** und **NW3** ERS, wobei NFS auf Azure NetApp Files-Volumes mit dem NFSv3-Protokoll verwendet wird.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Vergewissern Sie sich, dass der Cluster ordnungsgemäß funktioniert und alle Ressourcen gestartet wurden.  

   Stellen Sie als Nächstes sicher, dass die Ressourcen der neu erstellten ERS-Gruppe auf dem Clusterknoten ausgeführt werden, und zwar gegenüber dem Clusterknoten, auf dem die ASCS-Instanz für das gleiche SAP-System installiert wurde.  Wenn also beispielsweise die NW2-ASCS-Instanz auf `rhelmsscl1` installiert wurde, muss die NW2-ERS-Gruppe auf `rhelmsscl2` ausgeführt werden.  Sie können die NW2-ERS-Gruppe zu `rhelmsscl2` migrieren, indem Sie den folgenden Befehl für eine der Clusterressourcen in der Gruppe ausführen: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Installieren Sie SAP NetWeaver ERS.

   Installieren Sie SAP NetWeaver ERS als Root-Benutzer auf dem anderen Knoten. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die ERS-Instanz zugeordnet ist. Beim System **NW2** setzt sich der virtuelle Hostname beispielsweise aus <b>msnw2ers</b>, <b>10.3.1.53</b> und der für den Lastenausgleichstest verwendeten Instanznummer (z. B. <b>12</b>) zusammen. Beim System **NW3** setzt sich der virtuelle Hostname aus <b>msnw3ers</b>, <b>10.3.1.55</b> und der für den Lastenausgleichstest verwendeten Instanznummer (z. B. <b>22</b>) zusammen. 

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter „SAPINST_USE_HOSTNAME“ verwenden, um SAP unter Verwendung eines virtuellen Hostnamens zu installieren.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Verwenden Sie SWPM SP 20 PL 05 oder höher. Bei niedrigeren Versionen werden die Berechtigungen nicht ordnungsgemäß festgelegt, sodass bei der Installation ein Fehler auftritt.

   Falls bei der Installation kein Unterordner unter „/usr/sap/**NW2**/ERS **Instanznr.** “ erstellt werden kann, legen Sie den Besitzer auf „**sid** adm“ und die Gruppe auf „sapsys“ des Ordners „ERS **Instanznr.** “ fest, und versuchen Sie es noch mal.

   Wenn die ERS-Gruppe des neu bereitgestellten SAP-Systems zu einem anderen Clusterknoten migriert werden musste, vergessen Sie nicht, die Ortseinschränkung für die ERS-Gruppe zu entfernen. Die Einschränkung kann mithilfe des folgenden Befehls entfernt werden. (Das Beispiel gilt für die SAP-Systeme **NW2** und **NW3**.) Sorgen Sie dafür, die temporären Einschränkungen für dieselbe Ressource aufzuheben, mit der Sie im Befehl die ERS-Clustergruppe verschoben haben.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile für die neu installierten SAP-Systeme an. Das im Anschluss gezeigte Beispiel gilt für NW2. Die ASCS/SCS- und ERS-Profile müssen für alle dem Cluster hinzugefügten SAP-Instanzen angepasst werden.  
 
   * ASCS/SCS-Profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

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
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]**  Aktualisieren Sie die Datei „/usr/sap/sapservices“.

   Um den Start der Instanzen durch das „sapinit“-Startskript zu verhindern, müssen alle von Pacemaker verwalteten Instanzen aus der Datei `/usr/sap/sapservices` auskommentiert werden.  Das im Anschluss gezeigte Beispiel gilt für die SAP-Systeme **NW2** und **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Erstellen Sie die SAP-Clusterressourcen für das neu installierte SAP-System.  

   Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen für die SAP-Systeme **NW2** und **NW3** wie folgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
   Wenn Sie mit [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html) (Enqueue-Server 2-Architektur) arbeiten, definieren Sie die Ressourcen für die SAP-Systeme **NW2** und **NW3** wie folgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische SAP-Setup angepasst werden. 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.
   Das folgende Beispiel zeigt den Clusterressourcenstatus, nachdem die SAP-Systeme **NW2** und **NW3** dem Cluster hinzugefügt wurden: 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]**  Fügen Sie Firewallregeln für ASCS und ERS auf beiden Knoten hinzu.  Das folgende Beispiel zeigt die Firewallregeln für beide SAP-Systeme – **NW2** und **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Fortsetzen der SAP-Installation 

Führen Sie die folgenden Schritte aus, um die SAP-Installation abzuschließen:

* [Vorbereiten Ihrer SAP NetWeaver-Anwendungsserver](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Installieren einer DBMS-Instanz](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Installieren eines primären SAP-Anwendungsservers](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Installieren mindestens einer zusätzlichen SAP-Anwendungsinstanz

## <a name="test-the-multi-sid-cluster-setup"></a>Testen der Einrichtung des Multi-SID-Clusters

Bei den folgenden Tests handelt es sich um eine Auswahl von Testfällen aus den Best Practices-Leitfäden von Red Hat. Sie werden hier der Einfachheit halber bereitgestellt. Die vollständige Liste mit Clustertests finden Sie hier:

* Dokumentation bei Verwendung von Azure NetApp Files-NFS-Volumes: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter RHEL mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-rhel-netapp-files.md)
* Dokumentation bei Verwendung von hoch verfügbarem `GlusterFS`: [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter RHEL für SAP-Anwendungen](./high-availability-guide-rhel.md).  

Lesen Sie immer die Best Practices-Leitfäden von Red Hat, und führen Sie alle zusätzlichen Tests aus, die möglicherweise hinzugefügt wurden.  
Bei den bereitgestellten Tests handelt es sich um Tests in einem Multi-SID-Cluster mit zwei Knoten und drei installierten SAP-Systemen.  

1. Migrieren Sie die ASCS-Instanz manuell. Das Beispiel zeigt die Migration der ASCS-Instanz für das SAP-System „NW3“.

   Zustand der Ressource vor dem Starten des Tests:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Führen Sie die folgenden Befehle als Root-Benutzer aus, um die NW3-ASCS-Instanz zu migrieren.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Zustand der Ressource nach dem Test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simulieren eines Knotenabsturzes

   Zustand der Ressource vor dem Starten des Tests:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Führen Sie den folgenden Befehl als Root-Benutzer auf einem Knoten aus, auf dem mindestens eine ASCS-Instanz ausgeführt wird. In diesem Beispiel haben wir den Befehl auf dem Knoten `rhelmsscl1` ausgeführt, auf dem die ASCS-Instanzen für „NW1“, „NW2“ und „NW3“ ausgeführt werden.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Der Status nach dem Test und nach dem Neustart des abgestürzten Knotens sollte wie hier aussehen.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Wenn es Meldungen zu fehlerhaften Ressourcen gibt, bereinigen Sie den Status dieser Ressourcen. Beispiel:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].