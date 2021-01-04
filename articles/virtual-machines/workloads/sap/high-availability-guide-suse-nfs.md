---
title: Hochverfügbarkeit für NFS auf Azure-VMs unter SLES | Microsoft-Dokumentation
description: Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server
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
ms.openlocfilehash: 6b0504f5e4199ee3cd8e86660b866fddf2568485
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608570"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

In diesem Artikel werden das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren NFS-Servers, auf dem die freigegebenen Daten eines hochverfügbaren SAP-Systems gespeichert werden können, beschrieben.
In dieser Anleitung wird beschrieben, wie ein hochverfügbarer NFS-Server eingerichtet wird, der von zwei SAP-Systemen verwendet wird: NW1 und NW2. Bezüglich der Namen der Ressourcen (z. B. virtuelle Computer, virtuelle Netzwerke) im Beispiel wird davon ausgegangen, dass Sie die [SAP-Dateiservervorlage][template-file-server] mit dem Ressourcenpräfix **prod** verwendet haben.


> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Slave* und *Master*, die von Microsoft nicht mehr verwendet werden. Sobald die Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.

Lesen Sie zuerst die folgenden SAP Notes und Dokumente:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure

* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2205917] enthält empfohlene Betriebssystemeinstellungen für den SUSE Linux Enterprise Server for SAP Applications.
* SAP-Hinweis [1944799] enthält SAP HANA-Richtlinien für den SUSE Linux Enterprise Server for SAP Applications.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1984787] enthält allgemeine Informationen zu SUSE Linux Enterprise Server 12.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP unter Linux (dieser Artikel)][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [Leitfäden für bewährte Methoden zu SUSE Linux Enterprise High Availability Extension 12 SP3][sles-hae-guides]
  * Hochverfügbarer NFS-Speicher mit DRBD und Pacemaker
* [Leitfäden für bewährte Methoden zu SUSE Linux Enterprise Server für SAP-Anwendungen 12 SP3][sles-for-sap-bp]
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes] (Versionshinweise zur SUSE-Hochverfügbarkeitserweiterung 12 SP3, in englischer Sprache)

## <a name="overview"></a>Übersicht

Zum Erreichen von Hochverfügbarkeit erfordert SAP NetWeaver einen NFS-Server. Der NFS-Server ist in einem separaten Cluster konfiguriert und kann von mehreren SAP-Systemen verwendet werden.

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Der NFS-Server verwendet einen dedizierten virtuellen Hostnamen und virtuelle IP-Adressen für jedes SAP-System, das diesen NFS-Server verwendet. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Die folgende Liste zeigt die Konfiguration des Lastenausgleichs.        

* Frontendkonfiguration
  * IP-Adresse 10.0.0.4 für NW1
  * IP-Adresse 10.0.0.5 für NW2
* Backendkonfiguration
  * Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des NFS-Clusters sein sollen
* Testport
  * Port 61000 für NW1
  * Port 61001 für NW2
* Lastenausgleichsregeln (bei Verwendung von Load Balancer Basic)
  * 2049 TCP für NW1
  * 2049 UDP für NW1
  * 2049 TCP für NW2
  * 2049 UDP für NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Einrichten eines hochverfügbaren NFS-Servers

Sie können entweder eine Azure-Vorlage aus GitHub verwenden, um alle erforderlichen Azure-Ressourcen, einschließlich der virtuellen Computer, der Verfügbarkeitsgruppe und des Lastenausgleichs, bereitzustellen, oder Sie können die Ressourcen manuell bereitstellen.

### <a name="deploy-linux-via-azure-template"></a>Bereitstellen von Linux über die Azure-Vorlage

Der Azure Marketplace enthält ein Image für den SUSE Linux Enterprise Server for SAP Applications 12, das Sie zum Bereitstellen neuer virtueller Computer verwenden können.
Sie können eine der Schnellstartvorlagen auf GitHub verwenden, um alle erforderlichen Ressourcen bereitzustellen. Die Vorlage stellt die virtuellen Computer, den Load Balancer, die Verfügbarkeitsgruppe etc. bereit. Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Öffnen Sie im Azure-Portal die [SAP-Dateiservervorlage][template-file-server].   
1. Legen Sie die folgenden Parameter fest:
   1. Ressourcenpräfix  
      Geben Sie das Präfix ein, das verwendet werden soll. Der Wert wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
   2. Anzahl der SAP-Systeme  
      Geben Sie die Anzahl der SAP-Systeme ein, die den Dateiserver verwenden. Dadurch wird die erforderliche Menge an Front-End-Konfigurationen, Lastenausgleichsregeln, Testports, Datenträgern usw. bereitgestellt.
   3. Betriebssystemtyp  
      Wählen Sie eine der Linux-Distributionen aus. Wählen Sie für dieses Beispiel die Option „SLES 12“.
   4. Administratorbenutzername und Administratorkennwort  
      Es wird ein neuer Benutzer erstellt, der sich am Computer anmelden kann.
   5. Subnetz-ID  
      Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/ **&lt;Abonnement-ID&gt;** /resourceGroups/ **&lt;Name der Ressourcengruppe&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;Name des virtuellen Netzwerks&gt;** /subnets/ **&lt;Name des Subnetzes&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuelles Bereitstellen von Linux über das Azure-Portal

Sie müssen zunächst die virtuellen Computer für diesen NFS-Cluster erstellen. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer in den Back-End-Pools.

1. Erstellen einer Ressourcengruppe
1. Erstellen eines virtuellen Netzwerks
1. Erstellen Sie eine Verfügbarkeitsgruppe.  
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen von VM 1: Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das SLES4SAP 12 SP3 BYOS-Image, „SLES für SAP-Anwendungen 12 SP3 (BYOS)“, verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.  
1. Erstellen von VM 2: Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das SLES4SAP 12 SP3 BYOS-Image,  
   „SLES für SAP-Anwendungen 12 SP3 (BYOS)“, verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.  
1. Fügen Sie einen Datenträger für jedes SAP-System auf beiden virtuellen Computern hinzu.
1. Erstellen Sie einen Lastenausgleich (intern). Sie sollten [Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) verwenden.  
   1. Befolgen Sie diese Anleitung, um eine Load Balancer Standard-Instanz zu erstellen:
      1. Erstellen der Front-End-IP-Adressen
         1. IP-Adresse 10.0.0.4 für NW1
            1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **nw1-frontend**).
            1. Legen Sie die Zuweisung als statisch fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.4**).
            1. OK klicken
         1. IP-Adresse 10.0.0.5 für NW2
            * Wiederholen Sie die oben genannten Schritte für NW2.
      1. Erstellen der Back-End-Pools
         1. Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des NFS-Clusters sein sollen
            1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Back-End-Pools (z. B. **nw-backend**) ein.
            1. Virtuelles Netzwerk auswählen
            1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
            1. Wählen Sie die virtuellen Computer des NFS-Clusters und deren IP-Adressen aus.
            1. Klicken Sie auf Hinzufügen.
      1. Erstellen der Integritätstests
         1. Port 61000 für NW1
            1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **nw1-hp**).
            1. Wählen Sie TCP als Protokoll und Port 610 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
            1. OK klicken
         1. Port 61001 für NW2
            * Wiederholen Sie die oben genannten Schritte, um einen Integritätstest für NW2 zu erstellen.
      1. Lastenausgleichsregeln
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **nw1-lb**).
         1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **nw1-frontend**, **nw-backend** und **nw1-hp**)
         1. Wählen Sie **HA-Ports** aus.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
         * Wiederholen Sie die oben genannten Schritte, um eine Lastenausgleichsregel für NW2 zu erstellen.
   1. Wenn Ihr Szenario einen grundlegenden Lastenausgleich erfordert, befolgen Sie stattdessen die folgenden Anweisungen:
      1. Erstellen der Front-End-IP-Adressen
         1. IP-Adresse 10.0.0.4 für NW1
            1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **nw1-frontend**).
            1. Legen Sie die Zuweisung als statisch fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.4**).
            1. OK klicken
         1. IP-Adresse 10.0.0.5 für NW2
            * Wiederholen Sie die oben genannten Schritte für NW2.
      1. Erstellen der Back-End-Pools
         1. Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des NFS-Clusters sein sollen
            1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Back-End-Pools (z. B. **nw-backend**) ein.
            1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
            1. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.
            1. Wählen Sie die virtuellen Computer des NFS-Clusters aus.
            1. OK klicken
      1. Erstellen der Integritätstests
         1. Port 61000 für NW1
            1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **nw1-hp**).
            1. Wählen Sie TCP als Protokoll und Port 610 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
            1. OK klicken
         1. Port 61001 für NW2
            * Wiederholen Sie die oben genannten Schritte, um einen Integritätstest für NW2 zu erstellen.
      1. Lastenausgleichsregeln
         1. 2049 TCP für NW1
            1. Öffnen Sie den Load Balancer, wählen Sie das Laden von Lastenausgleichsregeln, und klicken Sie auf „Hinzufügen“.
            1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. **nw1-lb-2049**).
            1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z.B. **nw1-frontend**).
            1. Behalten Sie **TCP** als Protokoll bei, und geben Sie Port **2049** ein.
            1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
            1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
            1. OK klicken
         1. 2049 UDP für NW1
            * Wiederholen Sie die oben genannten Schritte für Port 2049 und UDP für NW1.
         1. 2049 TCP für NW2
            * Wiederholen Sie die oben genannten Schritte für Port 2049 und TCP für NW2.
         1. 2049 UDP für NW2
            * Wiederholen Sie die oben genannten Schritte für Port 2049 und UDP für NW2.

> [!IMPORTANT]
> Floating IP-Adressen werden für sekundäre NIC-IP-Konfigurationen in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

> [!Note]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

> [!IMPORTANT]
> Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Erstellen des Pacemaker-Clusters

Führen Sie die Schritte unter [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) zum Erstellen eines grundlegenden Pacemaker-Clusters für den NFS-Server aus.

### <a name="configure-nfs-server"></a>Konfigurieren des NFS-Servers

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Aktivieren Sie den NFS-Server.

   Erstellen des Eintrags für den Stammeintrag für den NFS-Export

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Installieren Sie DRBD-Komponenten.

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Erstellen Sie eine Partition für die DRBD-Geräte.

   Listen Sie alle verfügbaren Datenträger auf.

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Beispielausgabe
   
   ```
   lun0  lun1
   ```

   Erstellen Sie Partitionen für alle Datenträger.

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Erstellen Sie LVM-Konfigurationen.

   Listen Sie alle verfügbaren Partitionen auf.

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Beispielausgabe
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Erstellen Sie LVM-Volumes für alle Partitionen.

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Konfigurieren Sie DRBD.

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Stellen Sie sicher, dass die Datei „drbd.conf“ die folgenden beiden Zeilen enthält.

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Ändern Sie die globale DRBD-Konfiguration.

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Fügen Sie die folgenden Einträge den Abschnitten „handler“ und „net“ hinzu.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** Erstellen Sie die NFS-DRBD-Geräte.

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Fügen Sie die Konfiguration für das neue DRBD-Gerät ein, und beenden Sie es.

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Fügen Sie die Konfiguration für das neue DRBD-Gerät ein, und beenden Sie es.

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Erstellen Sie das DRBD-Gerät, und starten Sie es.

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Überspringen Sie die Erstsynchronisierung.

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Legen Sie den primären Knoten fest.

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Warten Sie, bis die neuen DRBD-Geräte synchronisiert sind.

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Erstellen Sie Dateisysteme auf den DRBD-Geräten.

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Richten Sie die DRBD-Split Brain-Erkennung ein.

   Bei Verwendung von DRBD zum Synchronisieren von Daten zwischen verschiedenen Hosts kann ein sogenanntes Split Brain-Syndrom auftreten. Ein Split Brain-Syndrom ist ein Szenario, bei dem beide Clusterknoten das DRBD-Gerät als primäres Gerät höher gestuft haben und nicht mehr synchronisiert sind. Dies ist wohl ein selten auftretender Fall, dennoch sollte ein Split Brain-Problem schnellstmöglich behandelt und behoben werden. Es ist daher wichtig, dass bei einem aufgetretenen Split Brain-Problem eine Benachrichtigung erfolgt.

   Informationen zum Einrichten einer Benachrichtigung zu einem Split Brain-Problem finden Sie in der [offiziellen DRBD-Dokumentation](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification).

   Es ist zudem möglich, eine automatische Wiederherstellung nach einem Split Brain-Szenario durchzuführen. Weitere Informationen finden Sie unter [Automatic split brain recovery policies](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration) (Richtlinien zur automatischen Split Brain-Wiederherstellung).
   
### <a name="configure-cluster-framework"></a>Konfigurieren des Clusterframeworks

1. **[1]** Fügen Sie der Clusterkonfiguration die NFS-DRBD-Geräte für das SAP-System NW1 hinzu.

   > [!IMPORTANT]
   > Kürzlich durchgeführte Tests haben Situationen aufgezeigt, in denen netcat aufgrund von Backlog und der Einschränkung, nur eine Verbindung zu verarbeiten, nicht mehr auf Anforderungen reagiert. Die netcat-Ressource lauscht dann nicht mehr auf Azure Load Balancer-Anforderungen, und die Floating IP-Adresse ist nicht mehr verfügbar.  
   > Für vorhandene Pacemaker-Cluster wurde zuvor empfohlen, netcat durch socat zu ersetzen. Zurzeit wird empfohlen, den Ressourcen-Agent azure-lb zu verwenden, der Teil des Pakets resource-agents ist. Dabei gelten die folgenden Versionsanforderungen für das Paket:
   > - Für SLES 12 SP4/SP5 muss die Version mindestens resource-agents-4.3.018.a7fb5035-3.30.1 sein.  
   > - Für SLES 15/15 SP1 muss die Version mindestens resource-agents-4.3.0184.6ee15eb2-4.13.1 sein.  
   >
   > Beachten Sie, dass für die Änderung eine kurze Ausfallzeit erforderlich ist.  
   > Wenn die Konfiguration bei vorhandenen Pacemaker-Clustern bereits für die Verwendung von socat geändert wurde, wie unter [Azure Load Balancer-Erkennungshärtung](https://www.suse.com/support/kb/doc/?id=7024128) beschrieben, müssen Sie nicht sofort zum Ressourcen-Agent azure-lb wechseln.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Fügen Sie der Clusterkonfiguration die NFS-DRBD-Geräte für das SAP-System NW2 hinzu.

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   Die `crossmnt`-Option in den `exportfs`-Clusterressourcen ist in unserer Dokumentation für die Abwärtskompatibilität mit älteren SLES-Versionen enthalten.  

1. **[1]** Deaktivieren Sie den Wartungsmodus.
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Installieren der SAP ASCS-Instanz und der SAP-Datenbank](high-availability-guide-suse.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
