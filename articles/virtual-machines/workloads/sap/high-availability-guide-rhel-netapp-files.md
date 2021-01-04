---
title: Hochverfügbarkeit von Azure VMs für SAP NW unter RHEL mit Azure NetApp Files | Microsoft-Dokumentation
description: Richten Sie Hochverfügbarkeit für SAP NW auf virtuellen Azure-Computern (VMs) unter RHEL mit Azure NetApp Files ein.
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
ms.openlocfilehash: 3b33351f840cb590d0adea42f9404917b2f30ca8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484259"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen

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

In diesem Artikel werden das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren SAP NetWeaver 7.50-Systems mithilfe von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) beschrieben.
In den Beispielkonfigurationen, Installationsbefehlen usw. entspricht die ASCS-Instanz Nummer 00, die ERS-Instanz Nummer 01, die primäre Anwendungsinstanz (PAS) Nummer 02 und die Anwendungsinstanz (AAS) Nummer 03. Es wird SAP-System-ID-QAS verwendet. 

Die Datenbankschicht wird in diesem Artikel nicht ausführlich behandelt.  

Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* [Azure NetApp Files-Dokumentation][anf-azure-doc] 
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
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Übersicht

Hochverfügbarkeit (HA) für zentrale Dienste von SAP NetWeaver erfordert freigegebenen Speicher.
Um dies unter Red Hat Linux zu erreichen, mussten bisher separate hochverfügbare GlusterFS-Cluster erstellt werden. 

Jetzt ist es möglich, Hochverfügbarkeit für SAP NetWeaver mithilfe von freigegebenem Speicher zu erreichen, der auf Azure NetApp Files bereitgestellt wird. Durch die Verwendung von Azure NetApp Files für den freigegebenen Speicher entfällt die Notwendigkeit zusätzlicher [GlusterFS-Cluster](./high-availability-guide-rhel-glusterfs.md). Pacemaker ist weiterhin für die Hochverfügbarkeit der zentralen Dienste (ASCS/SCS) von SAP NetWeaver erforderlich.

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS und die SAP HANA-Datenbank verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. Die folgende Liste zeigt die Konfiguration des Lastenausgleichs mit getrennten Front-End-IP-Adressen für (A)SCS und ERS.

### <a name="ascs"></a>(A)SCS

* Frontendkonfiguration
  * IP-Adresse 192.168.14.9
* Testport
  * Port 620<strong>&lt;Nr.&gt;</strong>
* Lastenausgleichsregeln
  * Wenn Sie Load Balancer Standard verwenden, wählen Sie **HA-Ports** aus.
  * 32<strong>&lt;Nr.&gt;</strong> TCP
  * 36<strong>&lt;Nr.&gt;</strong> TCP
  * 39<strong>&lt;Nr.&gt;</strong> TCP
  * 81<strong>&lt;Nr.&gt;</strong> TCP
  * 5<strong>&lt;Nr.&gt;</strong>13 TCP
  * 5<strong>&lt;Nr.&gt;</strong>14 TCP
  * 5<strong>&lt;Nr.&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Frontendkonfiguration
  * IP-Adresse 192.168.14.10
* Testport
  * Port 621<strong>&lt;nr&gt;</strong>
* Lastenausgleichsregeln
  * Wenn Sie Load Balancer Standard verwenden, wählen Sie **HA-Ports** aus.
  * 32<strong>&lt;Nr.&gt;</strong> TCP
  * 33<strong>&lt;Nr.&gt;</strong> TCP
  * 5<strong>&lt;Nr.&gt;</strong>13 TCP
  * 5<strong>&lt;Nr.&gt;</strong>14 TCP
  * 5<strong>&lt;Nr.&gt;</strong>16 TCP

* Backendkonfiguration
  * Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil des (A)SCS/ERS-Clusters sein sollen

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Einrichten der Infrastruktur für Azure NetApp Files 

SAP NetWeaver erfordert einen freigegebenen Speicher für den Transport und das Profilverzeichnis.  Bevor Sie mit der Einrichtung der Azure NetApp Files-Infrastruktur beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation][anf-azure-doc] vertraut machen. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird. Unter dem folgenden Link sehen Sie die Verfügbarkeit von Azure NetApp Files nach Azure-Region: [Verfügbarkeit von Azure NetApp Files nach Azure-Region][anf-avail-matrix].

Azure NetApp Files sind in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Fordern Sie Onboarding für Azure NetApp Files an, bevor Sie Azure NetApp Files bereitstellen. Befolgen Sie dazu die [Registrierungsanweisungen für Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen  

In diesen Schritten wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.  

1. Fordern Sie [Onboarding für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) an, sofern noch nicht geschehen.  
2. Erstellen Sie das NetApp-Konto entsprechend den [Anweisungen zum Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) in der ausgewählten Azure-Region.  
3. Richten Sie entsprechend den [Anweisungen zum Einrichten eines Azure NetApp Files-Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) einen Azure NetApp Files-Kapazitätspool ein.  
Die in diesem Artikel vorgestellte SAP NetWeaver-Architektur verwendet einen einzigen Azure NetApp Files-Kapazitätspool mit der Premium-SKU. Wir empfehlen die Premium-SKU von Azure NetApp Files für SAP NetWeaver-Anwendungsworkloads in Azure.  
4. Delegieren Sie ein Subnetz für Azure NetApp Files, wie in den [Anweisungen zum Delegieren eines Subnetzes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) beschrieben.  

5. Stellen Sie Azure NetApp Files-Volumes entsprechend den [Anweisungen zum Erstellen eines Azure NetApp Files-Volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit. Stellen Sie die Volumes im festgelegten [Subnetz](/rest/api/virtualnetwork/subnets) für Azure NetApp Files bereit. Die IP-Adressen der Azure NetApp-Volumes werden automatisch zugewiesen. Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. In diesem Beispiel werden die beiden Azure NetApp Files-Volumes sap<b>QAS</b> und transSAP verwendet. Die an den entsprechenden Bereitstellungspunkten bereitgestellten Dateipfade lauten: /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys usw.  

   1. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. transSAP-Volume (nfs://192.168.24.4/transSAP)
   6. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. sap<b>QAS</b>-Volume (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
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

## <a name="setting-up-ascs"></a>Einrichten von (A)SCS

In diesem Beispiel wurden die Ressourcen manuell über das [Azure-Portal](https://portal.azure.com/#home) bereitgestellt.

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuelles Bereitstellen von Linux über das Azure-Portal

Zuerst müssen Sie die Azure NetApp Files-Volumes erstellen. Stellen Sie die VMs bereit. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer im Back-End-Pool.

1. Erstellen Sie einen Lastenausgleich (intern, Standard):  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 192.168.14.9 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.QAS.ASCS**).
         1. Legen Sie „Zuweisung“ auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **192.168.14.9**).
         1. OK klicken
      1. IP-Adresse 192.168.14.10 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **192.168.14.10** und **frontend.QAS.ERS**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **backend.QAS**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie einen virtuellen Computer aus. 
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
      1. Lastenausgleichsregeln für ASCS
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
      1. IP-Adresse 192.168.14.9 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.QAS.ASCS**).
         1. Legen Sie „Zuweisung“ auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **192.168.14.9**).
         1. OK klicken
      1. IP-Adresse 192.168.14.10 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **192.168.14.10** und **frontend.QAS.ERS**).
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

## <a name="disable-id-mapping-if-using-nfsv41"></a>Deaktivieren der ID-Zuordnung (bei Verwendung von NFSv4.1)

Die Anweisungen in diesem Abschnitt gelten nur, wenn Azure NetApp Files-Volumes mit dem NFSv4.1-Protokoll verwendet werden. Führen Sie die Konfiguration auf allen VMs aus, auf denen Azure NetApp Files-Volumes mit NFSv4.1 bereitgestellt werden.  

1. Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **`defaultv4iddomain.com`** ) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass die NFS-Domäne in `/etc/idmapd.conf` auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **`defaultv4iddomain.com`** . Wenn es einen Konflikt zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) gibt, werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als `nobody` angezeigt.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Weitere Informationen zum Ändern des Parameters `nfs4_disable_idmapping` finden Sie unter https://access.redhat.com/solutions/1749883.

### <a name="create-pacemaker-cluster"></a>Erstellen des Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) aus, um ein grundlegendes Pacemaker-Cluster für diesen (A)SCS-Server zu erstellen.

### <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

    ```
    sudo vi /etc/hosts
    ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Erstellen Sie im Azure NetApp Files-Volume SAP-Verzeichnisse.  
   Stellen Sie das Azure NetApp Files-Volume vorübergehend auf einem der virtuellen Computer bereit, und erstellen Sie die SAP-Verzeichnisse (Dateipfade).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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

1. **[A]** Erstellen Sie die freigegebenen Verzeichnisse.

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Installieren Sie den NFS-Client und andere Anforderungen.

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Überprüfen Sie die Version des resource-agents-sap-Pakets.

   Stellen Sie sicher, dass mindestens Version 3.9.5-124.el7 des resource-agents-sap-Pakets installiert ist.
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.

   Bei Verwendung von NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Bei Verwendung von NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Stellen Sie sicher, dass Sie beim Einbinden der Volumes die NFS-Protokollversion der Azure NetApp Files-Volumes verwenden. Wenn die Azure NetApp Files-Volumes als NFSv3-Volumes erstellt werden, verwenden Sie die entsprechende NFSv3-Konfiguration. Wenn die Azure NetApp Files-Volumes als NFSv4.1-Volumes erstellt werden, befolgen Sie die Anweisungen zum Deaktivieren der ID-Zuordnung, und stellen Sie sicher, dass Sie die entsprechende NFSv4.1-Konfiguration verwenden. In diesem Beispiel wurden die Azure NetApp Files-Volumes als NFSv3-Volumes erstellt.  

   Stellen Sie die neuen Freigaben bereit.

   ```
   sudo mount -a  
   ```

1. **[A]** Konfigurieren Sie die Auslagerungsdatei.

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

   ```
   sudo service waagent restart
   ```

1. **[A]** Konfigurieren Sie RHEL.

   Konfigurieren Sie RHEL gemäß dem SAP-Hinweis [2002167].

### <a name="installing-sap-netweaver-ascsers"></a>Installieren von SAP NetWeaver ASCS/ERS

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ASCS-Instanz.

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS auf dem ersten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für ASCS zugeordnet ist (z. B. <b>anftstsapvh</b>, <b>192.168.14.9</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. <b>00</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Wenn bei der Installation kein Unterordner in „/usr/sap/**QAS**/ASCS **00**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ASCS **00**“ fest, und versuchen Sie es noch einmal.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ERS-Instanz.

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Installieren Sie SAP NetWeaver ERS.  

   Installieren Sie SAP NetWeaver ERS auf dem zweiten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ERS zugeordnet ist (z. B. <b>anftstsapers</b>, <b>192.168.14.10</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. <b>01</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Wenn bei der Installation kein Unterordner in „/usr/sap/**QAS**/ERS **01**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ERS **01**“ fest, und versuchen Sie es noch einmal.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile an.

   * ASCS/SCS-Profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Stellen Sie sowohl für ENSA1 als auch für ENSA2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.  

   * ERS-Profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Konfigurieren Sie Keep-Alive.

   Die Kommunikation zwischen dem SAP NetWeaver-Anwendungsserver und ASCS/SCS wird durch einen Softwarelastenausgleich weitergeleitet. Der Lastenausgleich trennt nach einem konfigurierbaren Timeout inaktive Verbindungen. Um dies zu verhindern, müssen Sie bei Verwendung von ENSA1 einen Parameter im ASCS-/SCS-Profil von SAP NetWeaver festlegen. Bei ENSA1 und ENSA2 müssen Sie außerdem die Linux-Systemeinstellungen für `keepalive` auf allen SAP-Servern ändern. Weitere Informationen finden Sie im [SAP-Hinweis 1410736][1410736].

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]**  Aktualisieren Sie die Datei „/usr/sap/sapservices“.

   Um den Start der Instanzen durch das „sapinit“-Startskript zu verhindern, müssen alle von Pacemaker verwalteten Instanzen aus der Datei „/usr/sap/sapservices“ auskommentiert werden. Kommentieren Sie nicht die SAP HANA-Instanz aus, wenn sie mit HANA SR verwendet wird.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Erstellen Sie die SAP-Clusterressourcen.

   Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
   Installieren Sie bei Verwendung der Enqueue-Server 2-Architektur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) den Ressourcen-Agent resource-agents-sap-4.1.1-12.el7.el7.x86_64 oder höher, und definieren Sie die Ressourcen wie folgt:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische SAP-Setup angepasst werden. 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]**  Fügen Sie Firewallregeln für ASCS und ERS auf beiden Knoten hinzu. Fügen Sie die Firewallregeln für ASCS und ERS auf beiden Knoten hinzu.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Vorbereitung des SAP NetWeaver-Anwendungsservers

   Für einige Datenbanken ist es erforderlich, dass die Installation der Datenbankinstanz auf einem Anwendungsserver ausgeführt wird. Bereiten Sie die virtuellen Computer für den Anwendungsserver vor, damit Sie diese in diesen Fällen verwenden können.  

   Bezüglich der nachstehenden Schritten wird davon ausgegangen, dass Sie den Anwendungsserver auf einem anderen Server als den ASCS/SCS- und HANA-Server installiert haben. Anderenfalls sind einige der nachfolgenden Schritte (wie die Konfiguration der Hostnamensauflösung) nicht erforderlich.  

   Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** (gilt für alle PAS und AAS), **[P]** (gilt nur für PAS) oder **[S]** (gilt nur für AAS).  

1. **[A]** Richten Sie die Hostnamensauflösung ein. Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen:  

   ```
   sudo vi /etc/hosts
   ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Erstellen Sie das Verzeichnis „sapmnt“.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Installieren Sie den NFS-Client und andere Anforderungen.  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.  
   Bei Verwendung von NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Bei Verwendung von NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Stellen Sie die neuen Freigaben bereit.

   ```
   sudo mount -a
   ```

1. **[P]** Erstellen Sie das Verzeichnis „PAS“, und binden Sie es ein.  
   Bei Verwendung von NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Bei Verwendung von NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Erstellen Sie das Verzeichnis „AAS“, und binden Sie es ein.  
   Bei Verwendung von NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Bei Verwendung von NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Konfigurieren Sie die Auslagerungsdatei.
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Installieren der Datenbank

In diesem Fall ist SAP NetWeaver auf SAP HANA installiert. Für diese Installation können Sie jede unterstützte Datenbank verwenden. Weitere Informationen zum Installieren von SAP HANA in Azure finden Sie unter [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Ausführen der Installation der SAP-Datenbankinstanz

   Installieren Sie die SAP NetWeaver-Datenbankinstanz als Stamm mit einem virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die Datenbank zugeordnet ist.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installation des SAP NetWeaver-Anwendungsservers

Führen Sie die folgenden Schritte durch, um einen SAP-Anwendungsserver zu installieren.

1. Vorbereiten des Anwendungsservers

   Führen Sie die Schritte im Kapitel [Vorbereitung des SAP NetWeaver-Anwendungsservers](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) aus, um den Anwendungsserver vorzubereiten.

1. Installieren Sie den SAP NetWeaver-Anwendungsserver.

   Installieren Sie einen primären oder einen zusätzlichen SAP NetWeaver-Anwendungsserver.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aktualisieren Sie den sicheren SAP HANA-Speicher.

   Ändern Sie den sicheren SAP HANA-Speicher dahingehend, dass er auf den virtuellen Namen der Einrichtung der SAP HANA-Systemreplikation zeigt.

   Führen Sie den folgenden Befehl aus, um die Einträge als \<sapsid>adm aufzulisten

   ```
   hdbuserstore List
   ```

   Damit werden alle Einträge aufgelistet, und die Liste sieht in etwa wie folgt aus.
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Die Ausgabe veranschaulicht, dass die IP-Adresse des Standardeintrags auf den virtuellen Computer zeigt, nicht auf die IP-Adresse des Lastenausgleichs. Dieser Eintrag muss so geändert werden, dass er auf den Namen des virtuellen Hosts des Lastenausgleichs zeigt. Achten Sie darauf, den gleichen Port (in der obigen Ausgabe **30313**) und den gleichen Datenbanknamen (in der obigen Ausgabe **QAS**) zu verwenden.

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

1. Manuelles Migrieren der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Führen Sie die folgenden Befehle als root aus, um die ASCS-Instanz zu migrieren.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulieren eines Knotenabsturzes

   Zustand der Ressource vor dem Starten des Tests:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird.

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Der Status nach dem Neustart des Knotens sollte wie folgt aussehen.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Führen Sie zum Bereinigen der fehlerhaften Ressourcen den folgenden Befehl aus:

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Beenden des Prozesses für den Nachrichtenserver

   Zustand der Ressource vor dem Starten des Tests:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Führen Sie die folgenden Befehle als root aus, um Prozess des Nachrichtenservers zu ermitteln und zu beenden.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Wenn Sie den Nachrichtenserver nur einmal beenden, wird er von `sapstart` neu gestartet. Wenn Sie ihn häufig genug beenden, wird Pacemaker die ASC-Instanz schließlich auf den anderen Knoten verschieben. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Beenden des Prozesses für den Server zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird, um den Server für das Einreihen in die Warteschlange zu beenden.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Für die ASCS-Instanz sollte sofort ein Failover auf den anderen Knoten ausgeführt werden. Für die ERS-Instanz sollte ebenfalls ein Failover ausgeführt werden, nachdem die ASCS-Instanz gestartet wurde. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Beenden des Prozesses für den Replikationsserver zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ERS-Instanz ausgeführt wird, um den Prozess für den Replikationsserver zum Einreihen in die Warteschlange zu beenden.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Wenn Sie den Befehl nur einmal ausführen, startet `sapstart` den Prozess neu. Wenn Sie ihn oft genug ausführen, startet `sapstart` den Prozess nicht mehr neu, und die Ressource wird beendet. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ERS-Instanz nach dem Test zu bereinigen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Beenden des sapstartsrv-Prozesses für das Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem ASCS ausgeführt wird.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Der Prozess „sapstartsrv“ sollte immer durch den Pacemaker-Ressourcen-Agent im Rahmen der Überwachung neu gestartet werden. Zustand der Ressource nach dem Test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].