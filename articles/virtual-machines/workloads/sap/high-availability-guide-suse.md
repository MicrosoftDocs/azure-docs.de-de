---
title: Hochverfügbarkeit von Azure VMs für SAP NetWeaver unter SLES | Microsoft-Dokumentation
description: Hochverfügbarkeitsleitfaden für SAP NetWeaver auf dem SUSE Linux Enterprise Server for SAP Applications
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
ms.openlocfilehash: ac70833d10c7cbcf5c4dc0fe709e2a6a58a30f26
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484208"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs auf dem SUSE Linux Enterprise Server for SAP Applications

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In diesem Artikel wird das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren SAP NetWeaver 7.50-Systems beschrieben.
In den Beispielkonfigurationen, Installationsbefehlen usw. werden die ASCS-Instanznummer „00“, die ERS-Instanznummer „02“ und die SAP-System-ID „NW1“ verwendet. Bezüglich der Namen der Ressourcen (z. B. der virtuellen Computer und virtuellen Netzwerke) im Beispiel wird davon ausgegangen, dass Sie für die Erstellung der Ressourcen die [zusammengeführte Vorlage][template-converged] mit der SAP-System-ID „NW1“ verwendet haben.

Lesen Sie zuerst die folgenden SAP Notes und Dokumente:

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

## <a name="overview"></a>Übersicht

Zum Erreichen von Hochverfügbarkeit erfordert SAP NetWeaver einen NFS-Server. Der NFS-Server ist in einem separaten Cluster konfiguriert und kann von mehreren SAP-Systemen verwendet werden.

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/high-availability-guide-suse/ha-suse.png)

Der NFS-Server, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS und die SAP HANA-Datenbank verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Es wird empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. Die folgende Liste zeigt die Konfiguration des A(SCS)- und ERS-Lastenausgleichs.

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


## <a name="setting-up-a-highly-available-nfs-server"></a>Einrichten eines hoch verfügbaren NFS-Servers

SAP NetWeaver erfordert einen freigegebenen Speicher für den Transport und das Profilverzeichnis. Weitere Informationen zum Einrichten eines NFS-Servers für SAP NetWeaver erhalten Sie unter [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server][nfs-ha].

## <a name="setting-up-ascs"></a>Einrichten von (A)SCS

Sie können entweder eine Azure-Vorlage aus GitHub verwenden, um alle erforderlichen Azure-Ressourcen, einschließlich der virtuellen Computer, der Verfügbarkeitsgruppe und des Lastenausgleichs, bereitzustellen, oder Sie können die Ressourcen manuell bereitstellen.

### <a name="deploy-linux-via-azure-template"></a>Bereitstellen von Linux über die Azure-Vorlage

Der Azure Marketplace enthält ein Image für den SUSE Linux Enterprise Server for SAP Applications 12, das Sie zum Bereitstellen neuer virtueller Computer verwenden können. Das Marketplace-Image enthält den Ressourcen-Agent für SAP NetWeaver.

Sie können eine der Schnellstartvorlagen auf GitHub verwenden, um alle erforderlichen Ressourcen bereitzustellen. Die Vorlage stellt die virtuellen Computer, den Load Balancer, die Verfügbarkeitsgruppe etc. bereit. Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Öffnen Sie die [ASCS/SCS-Multi-SID-Vorlage][template-multisid-xscs] oder die [konvergierte Vorlage][template-converged] im Azure-Portal. 
   Die ASCS/SCS-Vorlage erstellt nur die Regeln des Lastenausgleichs für die SAP NetWeaver ASCS/SCS- und ERS-Instanz (nur Linux), während die konvergierte Vorlage auch die Regeln des Lastenausgleichs für eine Datenbank (z.B. Microsoft SQL Server oder SAP HANA) erstellt. Wenn Sie ein SAP NetWeaver-basiertes System installieren und auch die Datenbank auf denselben Computern installieren möchten, verwenden Sie die [konvergierte Vorlage][template-converged].
1. Legen Sie die folgenden Parameter fest:
   1. Ressourcenpräfix (nur ASCS/SCS-Multi-SID-Vorlage)  
      Geben Sie das Präfix ein, das verwendet werden soll. Der Wert wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
   3. SAP-System-ID (nur konvergierte Vorlage)  
      Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
   4. Stapeltyp  
      Wählen Sie den SAP NetWeaver-Stapeltyp aus.
   5. Betriebssystemtyp  
      Wählen Sie eine der Linux-Distributionen aus. Wählen Sie für dieses Beispiel die Option „SLES 12 BYOS“.
   6. DB-Typ  
      Wählen Sie „HANA“.
   7. SAP-Systemgröße.  
      Die Anzahl der vom neuen System bereitgestellten SAPS. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
   8. Systemverfügbarkeit  
      Wählen Sie „HA“ (hohe Verfügbarkeit).
   9. Administratorbenutzername und Administratorkennwort  
      Es wird ein neuer Benutzer erstellt, der sich am Computer anmelden kann.
   10. Subnetz-ID  
   Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/ **&lt;Abonnement-ID&gt;** /resourceGroups/ **&lt;Name der Ressourcengruppe&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;Name des virtuellen Netzwerks&gt;** /subnets/ **&lt;Name des Subnetzes&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuelles Bereitstellen von Linux über das Azure-Portal

Sie müssen zunächst die virtuellen Computer für diesen NFS-Cluster erstellen. Anschließend erstellen Sie einen Lastenausgleich und verwenden die virtuellen Computer im Back-End-Pool.

1. Erstellen einer Ressourcengruppe
1. Erstellen eines virtuellen Netzwerks
1. Erstellen Sie eine Verfügbarkeitsgruppe.  
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie den virtuellen Computer 1.  
   Verwenden Sie mindestens SLES4SAP 12 SP1 – in diesem Beispiel das SLES4SAP 12 SP1-Image https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM.  
   SLES for SAP Applications 12 SP1 wird verwendet.  
   Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor erstellt haben.  
1. Erstellen Sie den virtuellen Computer 2.  
   Verwenden Sie mindestens SLES4SAP 12 SP1 – in diesem Beispiel das SLES4SAP 12 SP1-Image https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM.  
   SLES for SAP Applications 12 SP1 wird verwendet.  
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
         * Wiederholen Sie die oben stehenden Schritte, um eine IP-Adresse für ERS zu erstellen (z.B. **10.0.0.8** und **nw1-aers-backend**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **nw1-backend**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie „Virtueller Computer“ aus.
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

Führen Sie die Schritte unter [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) aus, um einen grundlegenden Pacemaker-Cluster für den A(SCS)-Server zu erstellen.

### <a name="installation"></a>Installation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Installieren Sie den SUSE-Connector

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Das bekannte Problem bei der Verwendung eines Gedankenstrichs in Hostnamen wird mit Version **3.1.1** des Pakets **sap-suse-cluster-connector** korrigiert. Stellen Sie sicher, dass Sie mindestens Version 3.1.1 des Pakets sap-suse-cluster-connector verwenden, wenn Sie Clusterknoten mit Gedankenstrichen im Hostnamen verwenden. Andernfalls funktioniert der Cluster nicht. 

   Stellen Sie sicher, dass Sie die neue Version des SAP SUSE-Clusterconnectors installiert haben. Der alte Name war „sap_suse_cluster_connector“, und der neue Name ist **sap-suse-cluster-connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** Aktualisieren Sie SAP-Ressourcen-Agents.  
   
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

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

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

1. **[A]** Konfigurieren Sie autofs.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Erstellen Sie eine Datei mit Folgendem:

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Starten Sie autofs neu, um die neuen Freigaben einzubinden.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
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

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS auf dem ersten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ASCS zugeordnet ist (z.B. <b>nw1-ascs</b>, <b>10.0.0.7</b>), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z.B. <b>00</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ASCS **00**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ASCS **00**“ fest, und versuchen Sie es noch mal.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ERS-Instanz.

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Installieren Sie SAP NetWeaver ERS.

   Installieren Sie SAP NetWeaver ERS auf dem zweiten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ERS zugeordnet ist (z.B. <b>nw1-aers</b>, <b>10.0.0.8</b>) und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z.B. <b>02</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Verwenden Sie SWPM SP 20 PL 05 oder höher. Bei niedrigeren Versionen werden die Berechtigungen nicht ordnungsgemäß festgelegt, sodass bei der Installation ein Fehler auftritt.

   Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ERS **02**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ERS **02**“ fest, und versuchen Sie es noch mal.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile an.
 
   * ASCS/SCS-Profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
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

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Konfigurieren Sie Keep-Alive.

   Die Kommunikation zwischen dem SAP NetWeaver-Anwendungsserver und ASCS/SCS wird durch einen Softwarelastenausgleich weitergeleitet. Der Lastenausgleich trennt nach einem konfigurierbaren Timeout inaktive Verbindungen. Um dies zu verhindern, müssen Sie bei Verwendung von ENSA1 einen Parameter im ASCS-/SCS-Profil von SAP NetWeaver festlegen. Bei ENSA1 und ENSA2 müssen Sie außerdem die Linux-Systemeinstellungen für `keepalive` auf allen SAP-Servern ändern. Weitere Informationen finden Sie im [SAP-Hinweis 1410736][1410736].

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** Konfigurieren Sie nach der Installation die SAP-Benutzer.

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Fügen Sie ASCS- und ERS-SAP-Dienste zur Datei „sapservice“ hinzu.

   Fügen Sie den ASCS-Diensteintrag zum zweiten Knoten hinzu. Kopieren Sie dann den ERS-Diensteintrag, und fügen Sie ihn auf dem ersten Knoten ein.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Erstellen Sie die SAP-Clusterressourcen.

Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
Wenn Sie mit [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html) (Enqueue-Server 2-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Vorbereitung des SAP NetWeaver-Anwendungsservers

Für einige Datenbanken ist es erforderlich, dass die Installation der Datenbankinstanz auf einem Anwendungsserver ausgeführt wird. Bereiten Sie die virtuellen Computer für den Anwendungsserver vor, damit Sie diese in diesen Fällen verwenden können.

Bezüglich der nachstehenden Schritten wird davon ausgegangen, dass Sie den Anwendungsserver auf einem anderen Server als den ASCS/SCS- und HANA-Server installiert haben. Anderenfalls sind einige der nachfolgenden Schritte (wie die Konfiguration der Hostnamensauflösung) nicht erforderlich.

1. Konfigurieren des Betriebssystems

   Reduzieren Sie die Größe des Änderungscaches. Weitere Informationen finden Sie unter [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Niedrige Schreibleistung auf SLES 11/12-Servern mit großem Arbeitsspeicher).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Einrichten der Hostnamensauflösung

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

   ```bash
   sudo vi /etc/hosts
   ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Erstellen Sie das sapmnt-Verzeichnis.

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurieren Sie autofs.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Erstellen Sie eine neue Datei mit Folgendem:

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Starten Sie autofs neu, um die neuen Freigaben einzubinden.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurieren Sie die Auslagerungsdatei.

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

## <a name="install-database"></a>Installieren der Datenbank

In diesem Fall ist SAP NetWeaver auf SAP HANA installiert. Für diese Installation können Sie jede unterstützte Datenbank verwenden. Weitere Informationen zum Installieren von SAP HANA in Azure finden Sie unter [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)][sap-hana-ha]. Eine Liste der unterstützten Datenbanken finden Sie im [SAP-Hinweis 1928533][1928533].

1. Ausführen der Installation der SAP-Datenbankinstanz

   Installieren Sie die SAP NetWeaver-Datenbankinstanz als Stamm mit einem virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für die Datenbank zugeordnet ist (z.B. <b>nw1-db</b> und <b>10.0.0.13</b>).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installation des SAP NetWeaver-Anwendungsservers

Führen Sie die folgenden Schritte durch, um einen SAP-Anwendungsserver zu installieren.

1. Vorbereiten des Anwendungsservers

   Führen Sie die Schritte im Kapitel [Vorbereitung des SAP NetWeaver-Anwendungsservers](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) aus, um den Anwendungsserver vorzubereiten.

1. Installieren Sie den SAP NetWeaver-Anwendungsserver.

   Installieren Sie einen primären oder einen zusätzlichen SAP NetWeaver-Anwendungsserver.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualisieren Sie den sicheren SAP HANA-Speicher.

   Ändern Sie den sicheren SAP HANA-Speicher dahingehend, dass er auf den virtuellen Namen der Einrichtung der SAP HANA-Systemreplikation zeigt.

   Führen Sie den folgenden Befehl aus, um die Einträge aufzulisten.
   <pre><code>hdbuserstore List
   </code></pre>

   Damit werden alle Einträge aufgelistet, und die Liste sieht in etwa wie folgt aus.
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Die Ausgabe veranschaulicht, dass die IP-Adresse des Standardeintrags auf den virtuellen Computer zeigt, nicht auf die IP-Adresse des Lastenausgleichs. Dieser Eintrag muss so geändert werden, dass er auf den Namen des virtuellen Hosts des Lastenausgleichs zeigt. Achten Sie darauf, den gleichen Port (**30313** in der obigen Ausgabe) und Datenbanknamen (**HN1** in der obigen Ausgabe) zu verwenden!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

Die folgenden Tests sind eine Kopie der Testfälle in den Best Practices von SUSE. Sie wurden der Einfachheit halber kopiert. Lesen Sie immer auch die Best Practices, und führen Sie alle zusätzlichen Tests aus, die möglicherweise hinzugefügt wurden.

1. Testen von HAGetFailoverConfig, HACheckConfig und HACheckFailoverConfig

   Führen Sie die folgenden Befehle als „\<sapsid>adm“ auf dem Knoten aus, auf dem aktuell die ASCS-Instanz ausgeführt wird. Wenn die Befehle mit dem Fehler „Nicht genügend Arbeitsspeicher“ beendet werden, sind die Ursache möglicherweise Bindestriche im Hostnamen. Dies ist ein bekanntes Problem, das von SUSE im Paket sap-suse-cluster-connector behoben wird.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Manuelles Migrieren der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um die ASCS-Instanz zu migrieren.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testen von HAFailoverToNode

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie die folgenden Befehle als „\<sapsid>adm“ aus, um die ASCS-Instanz zu migrieren.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simulieren eines Knotenabsturzes

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird.

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Bei Verwendung von SBD sollte Pacemaker nicht automatisch auf dem beendeten Knoten gestartet werden. Der Status nach dem Neustart des Knotens sollte wie folgt aussehen.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Verwenden Sie die folgenden Befehle, um Pacemaker auf dem beendeten Knoten zu starten und um die SBD-Nachrichten und die fehlerhaften Ressourcen zu bereinigen.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testen des manuellen Neustarts der ASCS-Instanz

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Erstellen Sie eine Sperre für das Einreihen in die Warteschlange, indem Sie z.B. einen Benutzer in der Transaktion su01 bearbeiten. Führen Sie die folgenden Befehle als „\<sapsid>adm“ auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird. Die Befehle beenden die ASCS-Instanz und starten sie erneut. Wird die Enqueue-Server 1-Architektur verwendet, wird erwartet, dass die Sperre für das Einreihen in die Warteschlange in diesem Test verloren geht. Wird die Enqueue-Server 2-Architektur verwendet, wird das Einreihen in die Warteschlange beibehalten. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Die ASCS-Instanz sollte jetzt in Pacemaker deaktiviert sein.

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Starten Sie die ASCS-Instanz erneut auf demselben Knoten.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Die Sperre für das Einreihen in die Warteschlange der Transaktion su01 sollte aufgehoben und das Back-End sollte zurückgesetzt worden sein. Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Beenden des Prozesses für den Nachrichtenserver

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie die folgenden Befehle als root aus, um Prozess des Nachrichtenservers zu ermitteln und zu beenden.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Wenn Sie den Nachrichtenserver nur einmal beenden, wird er von sapstart neu gestartet. Wenn Sie ihn häufig genug beenden, wird Pacemaker die ASC-Instanz schließlich auf den anderen Knoten verschieben. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Beenden des Prozesses für den Server zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem die ASCS-Instanz ausgeführt wird, um den Server für das Einreihen in die Warteschlange zu beenden.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Für die ASCS-Instanz sollte sofort ein Failover auf den anderen Knoten ausgeführt werden. Für die ERS-Instanz sollte ebenfalls ein Failover ausgeführt werden, nachdem die ASCS-Instanz gestartet wurde. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ASCS- und ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Beenden des Prozesses für den Replikationsserver zum Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie den folgenden Befehl als root auf dem Knoten aus, auf dem die ERS-Instanz ausgeführt wird, um den Prozess für den Replikationsserver zum Einreihen in die Warteschlange zu beenden.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Wenn Sie den Befehl nur einmal ausführen, startet sapstart den Prozess neu. Wenn Sie ihn oft genug ausführen, startet sapstart den Prozess nicht mehr neu, und die Ressource wechselt in den Status „Beendet“. Führen Sie die folgenden Befehle als root aus, um den Ressourcenstatus der ERS-Instanz nach dem Test zu bereinigen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Beenden des sapstartsrv-Prozesses für das Einreihen in die Warteschlange

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten aus, auf dem ASCS ausgeführt wird.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Der Prozess sapstartsrv sollte immer durch den Pacemaker-Ressourcen-Agent neu gestartet werden. Zustand der Ressource nach dem Test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Leitfaden zur Multi-SID-Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].