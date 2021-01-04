---
title: Hochverfügbarkeit von Azure VMs für SAP NW unter RHEL | Microsoft-Dokumentation
description: Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux
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
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: c275d3fc1bb2372b36a3a29ae3b72f3e5e9b758a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484225"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

In diesem Artikel wird das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren SAP NetWeaver 7.50-Systems beschrieben.
In den Beispielkonfigurationen, Installationsbefehlen usw. werden die ASCS-Instanznummer „00“, die ERS-Instanznummer „02“ und die SAP-System-ID „NW1“ verwendet. Die Namen der Ressourcen (z. B. der virtuellen Computer und virtuellen Netzwerke) im Beispiel lassen vermuten, dass Sie zum Erstellen der Ressourcen die [ASCS/SCS-Vorlage][template-multisid-xscs] mit dem Ressourcenpräfix „NW1“ verwendet haben.

Lesen Sie zuerst die folgenden SAP Notes und Dokumente:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure

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
* [Produktdokumentation für Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP NetWeaver im Pacemaker-Cluster](https://access.redhat.com/articles/3150081)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurieren von ASCS/ERS für SAP NetWeaver mit eigenständigen Ressourcen in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS with Standalone Enqueue Server 2 (ENSA2) in Pacemaker on RHEL](https://access.redhat.com/articles/3974941) (Konfigurieren von SAP S/4HANA ASCS/ERS mit eigenständigen Enqueue-Server 2 (ENSA2) in Pacemaker unter RHEL)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Übersicht

Zum Erreichen von Hochverfügbarkeit erfordert SAP NetWeaver freigegebenen Speicher. GlusterFS ist in einem separaten Cluster konfiguriert und kann von mehreren SAP-Systemen verwendet werden.

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS und die SAP HANA-Datenbank verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. Die folgende Liste zeigt die Konfiguration des A(SCS)- und ERS-Lastenausgleichs.

### <a name="ascs"></a>(A)SCS

* Frontendkonfiguration
  * IP-Adresse 10.0.0.7
* Testport
  * Port 620<strong>&lt;Nr.&gt;</strong>
* Lastenausgleichsregeln
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
  * IP-Adresse 10.0.0.8
* Testport
  * Port 621<strong>&lt;nr&gt;</strong>
* Lastenausgleichsregeln
  * Wenn Sie Load Balancer Standard verwenden, wählen Sie **HA-Ports** aus.
  * Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
    * 32<strong>&lt;Nr.&gt;</strong> TCP
    * 33<strong>&lt;Nr.&gt;</strong> TCP
    * 5<strong>&lt;Nr.&gt;</strong>13 TCP
    * 5<strong>&lt;Nr.&gt;</strong>14 TCP
    * 5<strong>&lt;Nr.&gt;</strong>16 TCP

* Backendkonfiguration
  * Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des (A)SCS/ERS-Clusters sein sollen

## <a name="setting-up-glusterfs"></a>Einrichten von GlusterFS

SAP NetWeaver erfordert einen freigegebenen Speicher für den Transport und das Profilverzeichnis. Informationen zum Einrichten von GlusterFS für SAP NetWeaver finden Sie unter [GlusterFS auf Azure-VMs unter Red Hat Enterprise Linux für SAP NetWeaver][glusterfs-ha].

## <a name="setting-up-ascs"></a>Einrichten von (A)SCS

Sie können entweder eine Azure-Vorlage aus GitHub verwenden, um alle erforderlichen Azure-Ressourcen, einschließlich der virtuellen Computer, der Verfügbarkeitsgruppe und des Lastenausgleichs, bereitzustellen, oder Sie können die Ressourcen manuell bereitstellen.

### <a name="deploy-linux-via-azure-template"></a>Bereitstellen von Linux über die Azure-Vorlage

Der Azure Marketplace enthält ein Image für Red Hat Enterprise Linux, das Sie zum Bereitstellen neuer virtueller Computer verwenden können. Sie können eine der Schnellstartvorlagen auf GitHub verwenden, um alle erforderlichen Ressourcen bereitzustellen. Die Vorlage stellt die virtuellen Computer, den Load Balancer, die Verfügbarkeitsgruppe etc. bereit. Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Öffnen Sie die [ASCS/SCS-Vorlage][template-multisid-xscs] im Azure-Portal.  
1. Legen Sie die folgenden Parameter fest:
   1. Ressourcenpräfix  
      Geben Sie das Präfix ein, das verwendet werden soll. Der Wert wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
   1. Stapeltyp  
      Wählen Sie den SAP NetWeaver-Stapeltyp aus.
   1. Betriebssystemtyp  
      Wählen Sie eine der Linux-Distributionen aus. Wählen Sie für dieses Beispiel die Option „RHEL 7“ aus.
   1. DB-Typ  
      Wählen Sie „HANA“.
   1. Anzahl von SAP-Systemen:  
      Die Anzahl von SAP-Systemen, die in diesem Cluster ausgeführt werden. Wählen Sie „1“ aus.
   1. Systemverfügbarkeit  
      Wählen Sie „HA“ (hohe Verfügbarkeit).
   1. Administratorbenutzername, Administratorkennwort oder SSH-Schlüssel  
      Ein neuer Benutzer wird erstellt, der für die Anmeldung beim Computer verwendet werden kann.
   1. Subnetz-ID  
   Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/ **&lt;Abonnement-ID&gt;** /resourceGroups/ **&lt;Name der Ressourcengruppe&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;Name des virtuellen Netzwerks&gt;** /subnets/ **&lt;Name des Subnetzes&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuelles Bereitstellen von Linux über das Azure-Portal

Zuerst müssen Sie die virtuellen Computer für diesen Cluster erstellen. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer im Back-End-Pool.

1. Erstellen einer Ressourcengruppe
1. Erstellen eines virtuellen Netzwerks
1. Erstellen Sie eine Verfügbarkeitsgruppe.  
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie den virtuellen Computer 1.  
   Verwenden Sie RHEL 7 oder höher, in diesem Beispiel das Red Hat Enterprise Linux 7.4-Image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.  
1. Erstellen Sie den virtuellen Computer 2.  
   Verwenden Sie RHEL 7 oder höher, in diesem Beispiel das Red Hat Enterprise Linux 7.4-Image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.  
1. Fügen Sie beiden virtuellen Computern jeweils mindestens einen Datenträger für Daten hinzu.  
   Die Datenträger werden für das Verzeichnis „/usr/sap/`<SAPSID`>“ benötigt.
1. Erstellen Sie einen Lastenausgleich (intern, Standard):  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.0.0.7 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **nw1-ascs-frontend**).
         1. Legen Sie die Zuweisung als statisch fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.7**).
         1. OK klicken
      1. IP-Adresse 10.0.0.8 für ASCS ERS
         * Wiederholen Sie die obigen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.0.0.8** und **nw1-aers-frontend**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **nw1-backend**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie einen virtuellen Computer aus.
      1. Wählen Sie die virtuellen Computer des (A)SCS-Clusters mit ihren IP-Adressen aus.
      1. Klicken Sie auf "Hinzufügen".
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **nw1-ascs-hp**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **02** für ASCS ERS
         * Wiederholen Sie die oben stehenden Schritte, um einen Integritätstest für ERS zu erstellen (z.B. 621 **02** und **nw1-aers-hp**).
   1. Lastenausgleichsregeln
      1. Lastenausgleichsregeln für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **nw1-lb-ascs**).
         1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **nw1-ascs-frontend**, **nw1-backend** und **nw1-ascs-hp**).
         1. Wählen Sie **HA-Ports** aus.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
         * Wiederholen Sie die oben angegebenen Schritte, um Lastenausgleichsregeln für ERS zu erstellen (z. B. **nw1-lb-ers**).
1. Wenn Ihr Szenario einen grundlegenden Lastenausgleich (intern) erfordert, führen Sie stattdessen die folgenden Schritte aus:  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.0.0.7 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **nw1-ascs-frontend**).
         1. Legen Sie die Zuweisung als statisch fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.7**).
         1. OK klicken
      1. IP-Adresse 10.0.0.8 für ASCS ERS
         * Wiederholen Sie die obigen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.0.0.8** und **nw1-aers-frontend**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **nw1-backend**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.
      1. Wählen Sie die virtuellen Computer des A(SCS)-Clusters aus.
      1. OK klicken
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **nw1-ascs-hp**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **02** für ASCS ERS
         * Wiederholen Sie die oben stehenden Schritte, um einen Integritätstest für ERS zu erstellen (z.B. 621 **02** und **nw1-aers-hp**).
   1. Lastenausgleichsregeln
      1. 32 **00** TCP für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. **nw1-lb-3200**).
         1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z.B. **nw1-ascs-frontend**).
         1. Behalten Sie **TCP** als Protokoll bei, und geben Sie Port **3200** ein.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
      1. Zusätzliche Ports für ASCS
         * Wiederholen Sie die oben stehenden Schritte für die Ports 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 und TCP für ASCS
      1. Zusätzliche Ports für ASCS ERS
         * Wiederholen Sie die oben stehenden Schritte für die Ports 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 und TCP für ASCS ERS

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

> [!Note]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

> [!IMPORTANT]
> Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Erstellen des Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) aus, um ein grundlegendes Pacemaker-Cluster für diesen (A)SCS-Server zu erstellen.

### <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** Erstellen Sie die freigegebenen Verzeichnisse.

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  Installieren Sie den GlusterFS-Client und andere Anforderungen.

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Überprüfen Sie die Version des resource-agents-sap-Pakets.

   Stellen Sie sicher, dass mindestens Version 3.9.5-124.el7 des resource-agents-sap-Pakets installiert ist.
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Stellen Sie die neuen Freigaben bereit.

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurieren Sie die Auslagerungsdatei.

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** Konfigurieren Sie RHEL.

   Konfigurieren Sie RHEL gemäß dem SAP-Hinweis [2002167].

### <a name="installing-sap-netweaver-ascsers"></a>Installieren von SAP NetWeaver ASCS/ERS

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ASCS-Instanz.

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS auf dem ersten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ASCS zugeordnet ist (z.B. <b>nw1-ascs</b>, <b>10.0.0.7</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z.B. <b>00</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ASCS **00**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ASCS **00**“ fest, und versuchen Sie es noch mal.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ERS-Instanz.

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** Installieren Sie SAP NetWeaver ERS.  

   Installieren Sie SAP NetWeaver ERS auf dem zweiten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ERS zugeordnet ist (z.B. <b>nw1-aers</b>, <b>10.0.0.8</b>) und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z.B. <b>02</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ERS **02**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ERS **02**“ fest, und versuchen Sie es noch mal.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile an.

   * ASCS/SCS-Profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Stellen Sie sowohl für ENSA1 als auch für ENSA2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.    

   * ERS-Profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Konfigurieren Sie Keep-Alive.

   Die Kommunikation zwischen dem SAP NetWeaver-Anwendungsserver und ASCS/SCS wird durch einen Softwarelastenausgleich weitergeleitet. Der Lastenausgleich trennt nach einem konfigurierbaren Timeout inaktive Verbindungen. Um dies zu verhindern, müssen Sie bei Verwendung von ENSA1 einen Parameter im ASCS-/SCS-Profil von SAP NetWeaver festlegen. Bei ENSA1 und ENSA2 müssen Sie außerdem die Linux-Systemeinstellungen für `keepalive` auf allen SAP-Servern ändern. Weitere Informationen finden Sie im [SAP-Hinweis 1410736][1410736].

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]**  Aktualisieren Sie die Datei „/usr/sap/sapservices“.

   Um den Start der Instanzen durch das „sapinit“-Startskript zu verhindern, müssen alle von Pacemaker verwalteten Instanzen aus der Datei „/usr/sap/sapservices“ auskommentiert werden. Kommentieren Sie nicht die SAP HANA-Instanz aus, wenn sie mit HANA SR verwendet wird.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** Erstellen Sie die SAP-Clusterressourcen.

  Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
   Installieren Sie bei Verwendung der Enqueue-Server 2-Architektur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) den Ressourcen-Agent resource-agents-sap-4.1.1-12.el7.el7.x86_64 oder höher, und definieren Sie die Ressourcen wie folgt:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische SAP-Setup angepasst werden. 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]**  Fügen Sie Firewallregeln für ASCS und ERS auf beiden Knoten hinzu.

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Vorbereitung des SAP NetWeaver-Anwendungsservers

Für einige Datenbanken ist es erforderlich, dass die Installation der Datenbankinstanz auf einem Anwendungsserver ausgeführt wird. Bereiten Sie die virtuellen Computer für den Anwendungsserver vor, damit Sie diese in diesen Fällen verwenden können.

Bezüglich der nachstehenden Schritten wird davon ausgegangen, dass Sie den Anwendungsserver auf einem anderen Server als den ASCS/SCS- und HANA-Server installiert haben. Anderenfalls sind einige der nachfolgenden Schritte (wie die Konfiguration der Hostnamensauflösung) nicht erforderlich.

1. Einrichten der Hostnamensauflösung

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Erstellen Sie das sapmnt-Verzeichnis.

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Installieren Sie den GlusterFS-Client und andere Anforderungen.

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Fügen Sie Bereitstellungseinträge hinzu.

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Stellen Sie die neuen Freigaben bereit.

   <pre><code>sudo mount -a
   </code></pre>

1. Konfigurieren Sie die Auslagerungsdatei.
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installieren der Datenbank

In diesem Fall ist SAP NetWeaver auf SAP HANA installiert. Für diese Installation können Sie jede unterstützte Datenbank verwenden. Weitere Informationen zum Installieren von SAP HANA in Azure finden Sie unter [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Ausführen der Installation der SAP-Datenbankinstanz

   Installieren Sie die SAP NetWeaver-Datenbankinstanz als Stamm mit einem virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für die Datenbank zugeordnet ist (z.B. <b>nw1-db</b> und <b>10.0.0.13</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation des SAP NetWeaver-Anwendungsservers

Führen Sie die folgenden Schritte durch, um einen SAP-Anwendungsserver zu installieren.

1. Vorbereiten des Anwendungsservers

   Führen Sie die Schritte im Kapitel [Vorbereitung des SAP NetWeaver-Anwendungsservers](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) aus, um den Anwendungsserver vorzubereiten.

1. Installieren Sie den SAP NetWeaver-Anwendungsserver.

   Installieren Sie einen primären oder einen zusätzlichen SAP NetWeaver-Anwendungsserver.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualisieren Sie den sicheren SAP HANA-Speicher.

   Ändern Sie den sicheren SAP HANA-Speicher dahingehend, dass er auf den virtuellen Namen der Einrichtung der SAP HANA-Systemreplikation zeigt.

   Führen Sie den folgenden Befehl aus, um die Einträge als \<sapsid>adm aufzulisten

   <pre><code>hdbuserstore List
   </code></pre>

   Damit werden alle Einträge aufgelistet, und die Liste sieht in etwa wie folgt aus.
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Die Ausgabe veranschaulicht, dass die IP-Adresse des Standardeintrags auf den virtuellen Computer zeigt, nicht auf die IP-Adresse des Lastenausgleichs. Dieser Eintrag muss so geändert werden, dass er auf den Namen des virtuellen Hosts des Lastenausgleichs zeigt. Achten Sie darauf, den gleichen Port (**30313** in der obigen Ausgabe) und Datenbanknamen (**HN1** in der obigen Ausgabe) zu verwenden!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

1. Manuelles Migrieren der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um die ASCS-Instanz zu migrieren.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Simulieren eines Knotenabsturzes

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird.

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Der Status nach dem Neustart des Knotens sollte wie folgt aussehen.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Führen Sie zum Bereinigen der fehlerhaften Ressourcen den folgenden Befehl aus:

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Beenden des Prozesses für den Nachrichtenserver

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um Prozess des Nachrichtenservers zu ermitteln und zu beenden.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Wenn Sie den Nachrichtenserver nur einmal beenden, wird er von `sapstart` neu gestartet. Wenn Sie ihn häufig genug beenden, wird Pacemaker die ASC-Instanz schließlich auf den anderen Knoten verschieben. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Beenden des Prozesses für den Server zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird, um den Server für das Einreihen in die Warteschlange zu beenden.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Für die ASCS-Instanz sollte sofort ein Failover auf den anderen Knoten ausgeführt werden. Für die ERS-Instanz sollte ebenfalls ein Failover ausgeführt werden, nachdem die ASCS-Instanz gestartet wurde. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Beenden des Prozesses für den Replikationsserver zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ERS-Instanz ausgeführt wird, um den Prozess für den Replikationsserver zum Einreihen in die Warteschlange zu beenden.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Wenn Sie den Befehl nur einmal ausführen, startet `sapstart` den Prozess neu. Wenn Sie ihn oft genug ausführen, startet `sapstart` den Prozess nicht mehr neu, und die Ressource wird beendet. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Beenden des sapstartsrv-Prozesses für das Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem ASCS ausgeführt wird.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Der Prozess „sapstartsrv“ sollte immer durch den Pacemaker-Ressourcen-Agent im Rahmen der Überwachung neu gestartet werden. Zustand der Ressource nach dem Test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].