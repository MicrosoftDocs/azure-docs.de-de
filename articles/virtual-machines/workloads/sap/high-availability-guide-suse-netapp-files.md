---
title: Hochverfügbarkeit von Azure VMs für SAP NW unter SLES mit Azure NetApp Files | Microsoft-Dokumentation
description: Hochverfügbarkeitsleitfaden für SAP NetWeaver unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen
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
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: b8f2054296c5b4e567699924305b7f3385f30835
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486214"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

In diesem Artikel werden das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren SAP NetWeaver 7.50-Systems mithilfe von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) beschrieben.
In den Beispielkonfigurationen, Installationsbefehlen usw. trägt die ASCS-Instanz die Nummer 00, die ERS-Instanz die Nummer 01, die primäre Anwendungsinstanz (PAS) die Nummer 02 und die Anwendungsinstanz (AAS) die Nummer 03. Es wird SAP-System-ID-QAS verwendet. 

In diesem Artikel wird erläutert, wie Sie Hochverfügbarkeit für SAP NetWeaver-Anwendung mit Azure NetApp Files erreichen. Die Datenbankschicht wird in diesem Artikel nicht ausführlich behandelt.

Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* [Azure NetApp Files-Dokumentation][anf-azure-doc] 
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
* Das WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [Best Practices für SUSE SAP HA][suse-ha-guide] Die Leitfäden enthalten alle erforderlichen Informationen, um NetWeaver HA und SAP HANA System Replication vor Ort einzurichten. Verwenden Sie diese Leitfäden als allgemeine Basis. Sie bieten wesentlich mehr Informationen.
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes] (Versionshinweise zur SUSE-Hochverfügbarkeitserweiterung 12 SP3, in englischer Sprache)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Übersicht

Hochverfügbarkeit (HA) für zentrale Dienste von SAP NetWeaver erfordert freigegebenen Speicher.
Um dies unter SUSE Linux zu erreichen, mussten bisher separate hochverfügbare NFS-Cluster erstellt werden. 

Jetzt ist es möglich, Hochverfügbarkeit für SAP NetWeaver mithilfe von freigegebenem Speicher zu erreichen, der auf Azure NetApp Files bereitgestellt wird. Durch die Verwendung von Azure NetApp Files für den freigegebenen Speicher entfällt die Notwendigkeit zusätzlicher [NFS-Cluster](./high-availability-guide-suse-nfs.md). Pacemaker ist weiterhin für die Hochverfügbarkeit der zentralen Dienste (ASCS/SCS) von SAP NetWeaver erforderlich.


![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS und die SAP HANA-Datenbank verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein [Lastenausgleich](../../../load-balancer/load-balancer-overview.md) erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. Die folgende Liste zeigt die Konfiguration des A(SCS)- und ERS-Lastenausgleichs.

### <a name="ascs"></a>(A)SCS

* Frontendkonfiguration
  * IP-Adresse 10.1.1.20
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
  * IP-Adresse 10.1.1.21
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


## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Einrichten der Infrastruktur für Azure NetApp Files 

SAP NetWeaver erfordert einen freigegebenen Speicher für den Transport und das Profilverzeichnis.  Bevor Sie mit der Einrichtung der Azure NetApp Files-Infrastruktur beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation][anf-azure-doc] vertraut machen. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird. Unter dem folgenden Link sehen Sie die Verfügbarkeit von Azure NetApp Files nach Azure-Region: [Verfügbarkeit von Azure NetApp Files nach Azure-Region][anf-avail-matrix].

Azure NetApp Files ist in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Fordern Sie Onboarding für Azure NetApp Files an, bevor Sie Azure NetApp Files bereitstellen. Befolgen Sie dazu die [Registrierungsanweisungen für Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen  

In diesen Schritten wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.  

1. Fordern Sie [Onboarding für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) an, sofern noch nicht geschehen.  

2. Erstellen Sie das NetApp-Konto entsprechend den [Anweisungen zum Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) in der ausgewählten Azure-Region.  
3. Richten Sie entsprechend den [Anweisungen zum Einrichten eines Azure NetApp Files-Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) einen Azure NetApp Files-Kapazitätspool ein.  
Die in diesem Artikel vorgestellte SAP NetWeaver-Architektur verwendet einen einzigen Azure NetApp Files-Kapazitätspool mit der Premium-SKU. Wir empfehlen die Premium-SKU von Azure NetApp Files für SAP NetWeaver-Anwendungsworkloads in Azure.  

4. Delegieren Sie ein Subnetz für Azure NetApp Files, wie in den [Anweisungen zum Delegieren eines Subnetzes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) beschrieben.  

5. Stellen Sie Azure NetApp Files-Volumes entsprechend den [Anweisungen zum Erstellen eines Azure NetApp Files-Volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit. Stellen Sie die Volumes im festgelegten [Subnetz](/rest/api/virtualnetwork/subnets) für Azure NetApp Files bereit. Die IP-Adressen der Azure NetApp-Volumes werden automatisch zugewiesen. Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. In diesem Beispiel werden die beiden Azure NetApp Files-Volumes sap<b>QAS</b> und trans verwendet. Die an den entsprechenden Bereitstellungspunkten bereitgestellten Dateipfade lauten: /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys usw.  

   1. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. Volume trans (nfs://10.1.0.4/trans)
   6. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)

   
In diesem Beispiel haben wir Azure NetApp Files für alle SAP NetWeaver-Dateisysteme verwendet, um die Verwendung von Azure NetApp Files zu veranschaulichen. Die SAP-Dateisysteme, die nicht über NFS eingebunden werden müssen, können auch als [Azure Disk Storage](../../disks-types.md#premium-ssd) bereitgestellt werden. In diesem Beispiel müssen <b>a-e</b> als Azure NetApp Files bereitgestellt werden, und <b>f-g</b> (d. h. „/usr/sap/<b>QAS</b>/D<b>02</b>“ und „/usr/sap/<b>QAS</b>/D<b>03</b>“) können als Azure Disk Storage bereitgestellt werden. 

### <a name="important-considerations"></a>Wichtige Hinweise

Wenn Sie Azure NetApp Files für die Hochverfügbarkeitsarchitektur von SAP NetWeaver unter SUSE in Betracht ziehen, beziehen Sie die folgenden wichtigen Überlegungen mit ein:

- Die Mindestgröße eines Kapazitätspools beträgt 4 TiB. Die Größe des Kapazitätspools kann in 1-TiB-Inkrementen erhöht werden.
- Das kleinste Volume ist 100 GiB groß.
- Azure NetApp Files und alle virtuellen Computer, auf denen Azure NetApp Files-Volumes eingebunden werden sollen, müssen sich im selben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden. Azure NetApp Files-Zugriff über VNET-Peering in derselben Region wird jetzt unterstützt. Azure NetApp-Zugriff über globales Peering wird noch nicht unterstützt.
- Das ausgewählte virtuelle Netzwerk muss über ein an Azure NetApp Files delegiertes Subnetz verfügen.
- Azure NetApp Files bietet [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): Sie können die zulässigen Clients und den Zugriffstyp (Lesen und Schreiben, schreibgeschützt usw.) steuern. 
- Azure NetApp Files wertet derzeit noch keine Zonen aus. Das Azure NetApp Files-Feature wird bisher nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen. 
- Azure NetApp Files-Volumes können als NFSv3- oder NFSv4.1-Volumes bereitgestellt werden. Beide Protokolle werden für die SAP-Anwendungsschicht (ASCS/ERS, SAP-Anwendungsserver) unterstützt. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Manuelles Bereitstellen von Linux-VMs über das Azure-Portal

Zuerst müssen Sie die Azure NetApp Files-Volumes erstellen. Stellen Sie die VMs bereit. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer in den Back-End-Pools.

1. Erstellen einer Ressourcengruppe
1. Erstellen eines virtuellen Netzwerks
1. Erstellen Sie eine Verfügbarkeitsgruppe für ASCS.  
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie den virtuellen Computer 1.  
   Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das Image SLES4SAP 12 SP3 verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für ASCS erstellt haben.  
1. Erstellen Sie den virtuellen Computer 2.  
   Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das Image SLES4SAP 12 SP3 verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für ASCS erstellt haben.  
1. Erstellen Sie eine Verfügbarkeitsgruppe für die SAP-Anwendungsinstanzen (PAS, AAS).    
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie den virtuellen Computer 3.  
   Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das Image SLES4SAP 12 SP3 verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für PAS/AAS erstellt haben.   
1. Erstellen Sie den virtuellen Computer 4.  
   Verwenden Sie mindestens SLES4SAP 12 SP3. In diesem Beispiel wird das Image SLES4SAP 12 SP3 verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für PAS/AAS erstellt haben.  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Deaktivieren der ID-Zuordnung (bei Verwendung von NFSv4.1)

Die Anweisungen in diesem Abschnitt gelten nur, wenn Azure NetApp Files-Volumes mit dem NFSv4.1-Protokoll verwendet werden. Führen Sie die Konfiguration auf allen VMs aus, auf denen Azure NetApp Files-Volumes mit NFSv4.1 bereitgestellt werden.  

1. Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **`defaultv4iddomain.com`** ) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass die NFS-Domäne in `/etc/idmapd.conf` auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **`defaultv4iddomain.com`** . Wenn es einen Konflikt zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) gibt, werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als `nobody` angezeigt.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Überprüfen Sie `nfs4_disable_idmapping`. Diese Angabe sollte auf **Y** (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Einrichten von (A)SCS

In diesem Beispiel wurden die Ressourcen manuell über das [Azure-Portal](https://portal.azure.com/#home) bereitgestellt.

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Manuelles Bereitstellen von Azure Load Balancer über das Azure-Portal

Zuerst müssen Sie die Azure NetApp Files-Volumes erstellen. Stellen Sie die VMs bereit. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer im Back-End-Pool.

1. Erstellen Sie einen Lastenausgleich (intern, Standard):  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.1.1.20 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.QAS.ASCS**).
         1. Legen Sie „Zuweisung“ auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **10.1.1.20**).
         1. OK klicken
      1. IP-Adresse 10.1.1.21 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.1.1.21** und **frontend.QAS.ERS**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **backend.QAS**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie „Virtueller Computer“ aus.
      1. Wählen Sie die virtuellen Computer des (A)SCS-Clusters mit ihren IP-Adressen aus.
      1. Klicken Sie auf "Hinzufügen".
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z. B. **health.QAS.ASCS**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **01** für ASCS ERS
            * Wiederholen Sie die oben unter „c“ angegebenen Schritte, um einen Integritätstest für ERS zu erstellen (z. B. 621 **01** und **health.QAS.ERS**).
   1. Lastenausgleichsregeln
      1. Erstellen eines Back-End-Pools für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **lb.QAS.ASCS**).
         1. Wählen Sie die Front-End-IP-Adresse für ASCS, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **frontend.QAS.ASCS**, **backend.QAS** und **health.QAS.ASCS**).
         1. Wählen Sie **HA-Ports** aus.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
         * Wiederholen Sie die oben angegebenen Schritte, um Lastenausgleichsregeln für ERS zu erstellen (z. B. **lb.QAS.ERS**).
1. Wenn Ihr Szenario einen grundlegenden Lastenausgleich (intern) erfordert, führen Sie stattdessen die folgenden Schritte aus:  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.1.1.20 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.QAS.ASCS**).
         1. Legen Sie „Zuweisung“ auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **10.1.1.20**).
         1. OK klicken
      1. IP-Adresse 10.1.1.21 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.1.1.21** und **frontend.QAS.ERS**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **backend.QAS**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für ASCS erstellt haben. 
      1. Wählen Sie die virtuellen Computer des A(SCS)-Clusters aus.
      1. OK klicken
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z. B. **health.QAS.ASCS**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **01** für ASCS ERS
            * Wiederholen Sie die oben unter „c“ angegebenen Schritte, um einen Integritätstest für ERS zu erstellen (z. B. 621 **01** und **health.QAS.ERS**).
   1. Lastenausgleichsregeln
      1. 32 **00** TCP für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **lb.QAS.ASCS.3200**).
         1. Wählen Sie die Front-End-IP-Adresse für ASCS, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **frontend.QAS.ASCS**).
         1. Behalten Sie **TCP** als Protokoll bei, und geben Sie Port **3200** ein.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
      1. Zusätzliche Ports für ASCS
         * Wiederholen Sie die oben unter „d“ angegebenen Schritte für die Ports 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 und „TCP“ für ASCS.
      1. Zusätzliche Ports für ASCS ERS
         * Wiederholen Sie die oben unter „d“ angegebenen Schritte für die Ports 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 und „TCP“ für ASCS ERS.

      
      > [!IMPORTANT]
      > Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

      > [!Note]
      > Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

      > [!IMPORTANT]
      > Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Erstellen des Pacemaker-Clusters

Führen Sie die Schritte unter [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) aus, um einen grundlegenden Pacemaker-Cluster für den A(SCS)-Server zu erstellen.

### <a name="installation"></a>Installation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Installieren Sie den SUSE-Connector

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Das bekannte Problem bei der Verwendung eines Gedankenstrichs in Hostnamen wird mit Version **3.1.1** des Pakets **sap-suse-cluster-connector** korrigiert. Stellen Sie sicher, dass Sie mindestens Version 3.1.1 des Pakets sap-suse-cluster-connector verwenden, wenn Sie Clusterknoten mit Gedankenstrichen im Hostnamen verwenden. Andernfalls funktioniert der Cluster nicht. 

   Stellen Sie sicher, dass Sie die neue Version des SAP SUSE-Clusterconnectors installiert haben. Der alte Name war „sap_suse_cluster_connector“, und der neue Name ist **sap-suse-cluster-connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** Aktualisieren Sie SAP-Ressourcen-Agents.  
   
   Ein Patch für das Ressourcen-Agents-Paket ist erforderlich, um die in diesem Artikel beschriebene neue Konfiguration zu verwenden. Anhand des folgenden Befehls können Sie überprüfen, ob der Patch bereits installiert ist:

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Die Ausgabe sollte in etwa wie folgt aussehen:

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Wenn der Parameter „IS_ERS“ nicht mit dem Befehl „grep“ gefunden wird, müssen Sie den auf der [SUSE-Downloadseite](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) aufgeführten Patch installieren.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

4. **[1]** Erstellen Sie im Azure NetApp Files-Volume SAP-Verzeichnisse.  
   Stellen Sie das Azure NetApp Files-Volume vorübergehend auf einem der virtuellen Computer bereit, und erstellen Sie die SAP-Verzeichnisse (Dateipfade).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntQAS
    sudo mkdir -p usrsapQASascs
    sudo mkdir -p usrsapQASers
    sudo mkdir -p usrsapQASsys
    sudo mkdir -p usrsapQASpas
    sudo mkdir -p usrsapQASaas
    # unmount the volume and delete the temporary directory
    sudo cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

## <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

1. **[A]** Erstellen Sie die freigegebenen Verzeichnisse.

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** Konfigurieren Sie `autofs`.

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Wenn Sie NFSv3 verwenden, erstellen Sie eine Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Wenn Sie NFSv4.1 verwenden, erstellen Sie eine Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Stellen Sie sicher, dass Sie beim Einbinden der Volumes die NFS-Protokollversion der Azure NetApp Files-Volumes verwenden. Wenn die Azure NetApp Files-Volumes als NFSv3-Volumes erstellt werden, verwenden Sie die entsprechende NFSv3-Konfiguration. Wenn die Azure NetApp Files-Volumes als NFSv4.1-Volumes erstellt werden, befolgen Sie die Anweisungen zum Deaktivieren der ID-Zuordnung, und stellen Sie sicher, dass Sie die entsprechende NFSv4.1-Konfiguration verwenden. In diesem Beispiel wurden die Azure NetApp Files-Volumes als NFSv3-Volumes erstellt.  
   
   Starten Sie `autofs` neu, um die neuen Freigaben einzubinden.
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** Konfigurieren Sie die Auslagerungsdatei.

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

### <a name="installing-sap-netweaver-ascsers"></a>Installieren von SAP NetWeaver ASCS/ERS

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ASCS-Instanz.

   > [!IMPORTANT]
   > Kürzlich durchgeführte Tests haben Situationen aufgezeigt, in denen netcat aufgrund von Backlog und der Einschränkung, nur eine Verbindung zu verarbeiten, nicht mehr auf Anforderungen reagiert. Die netcat-Ressource lauscht dann nicht mehr auf Azure Load Balancer-Anforderungen, und die Floating IP-Adresse ist nicht mehr verfügbar.  
   > Für vorhandene Pacemaker-Cluster wurde zuvor empfohlen, netcat durch socat zu ersetzen. Zurzeit wird empfohlen, den Ressourcen-Agent azure-lb zu verwenden, der Teil des Pakets resource-agents ist. Dabei gelten die folgenden Versionsanforderungen für das Paket:
   > - Für SLES 12 SP4/SP5 muss die Version mindestens resource-agents-4.3.018.a7fb5035-3.30.1 sein.  
   > - Für SLES 15/15 SP1 muss die Version mindestens resource-agents-4.3.0184.6ee15eb2-4.13.1 sein.  
   >
   > Beachten Sie, dass für die Änderung eine kurze Ausfallzeit erforderlich ist.  
   > Wenn die Konfiguration bei vorhandenen Pacemaker-Clustern bereits für die Verwendung von socat geändert wurde, wie unter [Azure Load Balancer-Erkennungshärtung](https://www.suse.com/support/kb/doc/?id=7024128) beschrieben, müssen Sie nicht sofort zum Ressourcen-Agent azure-lb wechseln.

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS auf dem ersten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für ASCS zugeordnet ist (z. B. <b>anftstsapvh</b>, <b>10.1.1.20</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. <b>00</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter SAPINST_USE_HOSTNAME verwenden, um SAP über einen virtuellen Hostnamen zu installieren.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Wenn bei der Installation kein Unterordner in „/usr/sap/**QAS**/ASCS **00**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ASCS **00**“ fest, und versuchen Sie es noch einmal. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ERS-Instanz.

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Installieren Sie SAP NetWeaver ERS.

   Installieren Sie SAP NetWeaver ERS auf dem zweiten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ERS zugeordnet ist (z. B. <b>anftstsapers</b>, <b>10.1.1.21</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. <b>01</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen. Sie können den Parameter SAPINST_USE_HOSTNAME verwenden, um SAP über einen virtuellen Hostnamen zu installieren.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Verwenden Sie SWPM SP 20 PL 05 oder höher. Bei niedrigeren Versionen werden die Berechtigungen nicht ordnungsgemäß festgelegt, sodass bei der Installation ein Fehler auftritt.

   Wenn bei der Installation kein Unterordner in „/usr/sap/**QAS**/ERS **01**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ERS **01**“ fest, und versuchen Sie es noch einmal.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile an.
 
   * ASCS/SCS-Profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Stellen Sie sowohl für ENSA1 als auch für ENSA2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.  

   * ERS-Profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** Konfigurieren Sie Keep-Alive.

   Die Kommunikation zwischen dem SAP NetWeaver-Anwendungsserver und ASCS/SCS wird durch einen Softwarelastenausgleich weitergeleitet. Der Lastenausgleich trennt nach einem konfigurierbaren Timeout inaktive Verbindungen. Um dies zu verhindern, müssen Sie bei Verwendung von ENSA1 einen Parameter im ASCS-/SCS-Profil von SAP NetWeaver festlegen. Bei ENSA1 und ENSA2 müssen Sie außerdem die Linux-Systemeinstellungen für `keepalive` auf allen SAP-Servern ändern. Weitere Informationen finden Sie im [SAP-Hinweis 1410736][1410736].

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** Konfigurieren Sie nach der Installation die SAP-Benutzer.

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Fügen Sie ASCS- und ERS-SAP-Dienste zur Datei `sapservice` hinzu.

   Fügen Sie den ASCS-Diensteintrag zum zweiten Knoten hinzu. Kopieren Sie dann den ERS-Diensteintrag, und fügen Sie ihn auf dem ersten Knoten ein.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** Erstellen Sie die SAP-Clusterressourcen.

Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
Wenn Sie mit [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html) (Enqueue-Server 2-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Vorbereitung des SAP NetWeaver-Anwendungsservers 

Für einige Datenbanken ist es erforderlich, dass die Installation der Datenbankinstanz auf einem Anwendungsserver ausgeführt wird. Bereiten Sie die virtuellen Computer für den Anwendungsserver vor, damit Sie diese in diesen Fällen verwenden können.

Bezüglich der nachstehenden Schritten wird davon ausgegangen, dass Sie den Anwendungsserver auf einem anderen Server als den ASCS/SCS- und HANA-Server installiert haben. Anderenfalls sind einige der nachfolgenden Schritte (wie die Konfiguration der Hostnamensauflösung) nicht erforderlich.

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** (gilt für alle PAS und AAS), **[P]** (gilt nur für PAS) oder **[S]** (gilt nur für AAS).


1. **[A]** Konfigurieren des Betriebssystems

   Reduzieren Sie die Größe des Änderungscaches. Weitere Informationen finden Sie unter [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Niedrige Schreibleistung auf SLES 11/12-Servern mit großem Arbeitsspeicher).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   ```bash
   sudo vi /etc/hosts
   ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** Erstellen des Verzeichnisses „sapmnt“

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** Erstellen des Verzeichnisses „PAS“

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** Erstellen des Verzeichnisses „AAS“

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** Konfigurieren Sie `autofs` für PAS.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Wenn Sie NFSv3 verwenden, erstellen Sie eine neue Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Wenn Sie NFSv4.1 verwenden, erstellen Sie eine neue Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Starten Sie `autofs` neu, um die neuen Freigaben einzubinden.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** Konfigurieren Sie `autofs` für AAS.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Wenn Sie NFSv3 verwenden, erstellen Sie eine neue Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Wenn Sie NFSv4.1 verwenden, erstellen Sie eine neue Datei mit:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Starten Sie `autofs` neu, um die neuen Freigaben einzubinden.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Konfigurieren Sie die Auslagerungsdatei.

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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installieren der Datenbank

In diesem Fall ist SAP NetWeaver auf SAP HANA installiert. Für diese Installation können Sie jede unterstützte Datenbank verwenden. Weitere Informationen zum Installieren von SAP HANA in Azure finden Sie unter [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)][sap-hana-ha]. Eine Liste der unterstützten Datenbanken finden Sie im [SAP-Hinweis 1928533][1928533].

* Ausführen der Installation der SAP-Datenbankinstanz

   Installieren Sie die SAP NetWeaver-Datenbankinstanz als Stamm mit einem virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die Datenbank zugeordnet ist.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation des SAP NetWeaver-Anwendungsservers

Führen Sie die folgenden Schritte durch, um einen SAP-Anwendungsserver zu installieren.

1. **[A]** Vorbereiten des Anwendungsservers. Führen Sie die Schritte im Kapitel [Vorbereitung des SAP NetWeaver-Anwendungsservers](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) aus, um den Anwendungsserver vorzubereiten.

2. **[A]** Installieren des SAP NetWeaver-Anwendungsservers. Installieren Sie einen primären oder einen zusätzlichen SAP NetWeaver-Anwendungsserver.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** Aktualisieren Sie den sicheren SAP HANA-Speicher.

   Ändern Sie den sicheren SAP HANA-Speicher dahingehend, dass er auf den virtuellen Namen der Einrichtung der SAP HANA-Systemreplikation zeigt.

   Führen Sie den folgenden Befehl aus, um die Einträge aufzulisten.
   <pre><code>
   hdbuserstore List
   </code></pre>

   Damit werden alle Einträge aufgelistet, und die Liste sieht in etwa wie folgt aus.
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Die Ausgabe veranschaulicht, dass die IP-Adresse des Standardeintrags auf den virtuellen Computer zeigt, nicht auf die IP-Adresse des Lastenausgleichs. Dieser Eintrag muss so geändert werden, dass er auf den Namen des virtuellen Hosts des Lastenausgleichs zeigt. Achten Sie darauf, den gleichen Port (in der obigen Ausgabe **30313**) und den gleichen Datenbanknamen (in der obigen Ausgabe **QAS**) zu verwenden.

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

Die folgenden Tests sind eine Kopie der Testfälle in den [Best Practices von SUSE][suse-ha-guide]. Sie wurden der Einfachheit halber kopiert. Lesen Sie immer auch die Best Practices, und führen Sie alle zusätzlichen Tests aus, die möglicherweise hinzugefügt wurden.

1. Testen von HAGetFailoverConfig, HACheckConfig und HACheckFailoverConfig

   Führen Sie die folgenden Befehle als „\<sapsid>adm“ auf dem Knoten aus, auf dem aktuell die ASCS-Instanz ausgeführt wird. Wenn die Befehle mit dem Fehler „Nicht genügend Arbeitsspeicher“ beendet werden, sind die Ursache möglicherweise Bindestriche im Hostnamen. Dies ist ein bekanntes Problem, das von SUSE im Paket sap-suse-cluster-connector behoben wird.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Manuelles Migrieren der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um die ASCS-Instanz zu migrieren.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testen von HAFailoverToNode

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Führen Sie die folgenden Befehle als „\<sapsid>adm“ aus, um die ASCS-Instanz zu migrieren.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulieren eines Knotenabsturzes 

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird.

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Bei Verwendung von SBD sollte Pacemaker nicht automatisch auf dem beendeten Knoten gestartet werden. Der Status nach dem Neustart des Knotens sollte wie folgt aussehen.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Verwenden Sie die folgenden Befehle, um Pacemaker auf dem beendeten Knoten zu starten und um die SBD-Nachrichten und die fehlerhaften Ressourcen zu bereinigen.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testen des manuellen Neustarts der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Erstellen Sie eine Sperre für das Einreihen in die Warteschlange, indem Sie z.B. einen Benutzer in der Transaktion su01 bearbeiten. Führen Sie die folgenden Befehle als „<sapsid\>adm“ auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird. Die Befehle beenden die ASCS-Instanz und starten sie erneut. Wird die Enqueue-Server 1-Architektur verwendet, wird erwartet, dass die Sperre für das Einreihen in die Warteschlange in diesem Test verloren geht. Wird die Enqueue-Server 2-Architektur verwendet, wird das Einreihen in die Warteschlange beibehalten. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Die ASCS-Instanz sollte jetzt in Pacemaker deaktiviert sein.

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starten Sie die ASCS-Instanz erneut auf demselben Knoten.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Die Sperre für das Einreihen in die Warteschlange der Transaktion „su01“ sollte, wenn Enqueue-Server-Replikation 1-Architektur verwendet wird, aufgehoben und das Back-End sollte zurückgesetzt worden sein. Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Beenden des Prozesses für den Nachrichtenserver

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um Prozess des Nachrichtenservers zu ermitteln und zu beenden.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Wenn Sie den Nachrichtenserver nur einmal beenden, wird er von `sapstart` neu gestartet. Wenn Sie ihn häufig genug beenden, wird Pacemaker die ASC-Instanz schließlich auf den anderen Knoten verschieben. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Beenden des Prozesses für den Server zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird, um den Server für das Einreihen in die Warteschlange zu beenden.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Für die ASCS-Instanz sollte sofort ein Failover auf den anderen Knoten ausgeführt werden. Für die ERS-Instanz sollte ebenfalls ein Failover ausgeführt werden, nachdem die ASCS-Instanz gestartet wurde. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Beenden des Prozesses für den Replikationsserver zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ERS-Instanz ausgeführt wird, um den Prozess für den Replikationsserver zum Einreihen in die Warteschlange zu beenden.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Wenn Sie den Befehl nur einmal ausführen, startet `sapstart` den Prozess neu. Wenn Sie ihn oft genug ausführen, startet `sapstart` den Prozess nicht mehr neu, und die Ressource wird beendet. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Beenden des sapstartsrv-Prozesses für das Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem ASCS ausgeführt wird.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Der Prozess sapstartsrv sollte immer durch den Pacemaker-Ressourcen-Agent neu gestartet werden. Zustand der Ressource nach dem Test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Leitfaden zur Multi-SID-Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].