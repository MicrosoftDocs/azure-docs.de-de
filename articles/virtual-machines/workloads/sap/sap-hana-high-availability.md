---
title: Hochverfügbarkeit von SAP HANA auf Azure-VMs unter SLES | Microsoft-Dokumentation
description: Hochverfügbarkeit von SAP HANA auf Azure-VMs unter SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 5af2c40dd1efa542ac13bd4cf96ba3017810bf00
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608672"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hochverfügbarkeit von SAP HANA auf Azure-VMs unter SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Für die lokale Entwicklung können Sie entweder die HANA-Systemreplikation oder freigegebenen Speicher verwenden, um Hochverfügbarkeit für SAP HANA einzurichten.
Die HANA-Systemreplikation in Azure ist derzeit die einzige auf virtuellen Azure-Computern (VMs) unterstützte Hochverfügbarkeitsfunktion. Die SAP HANA-Replikation umfasst primären Knoten und mindestens einen sekundären Knoten. Änderungen an den Daten auf dem primären Knoten werden synchron oder asynchron an den sekundären Knoten repliziert.

Dieser Artikel beschreibt das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks sowie das Installieren und Konfigurieren der SAP HANA-Systemreplikation.
In den Beispielkonfigurationen und Installationsbefehlen werden die Instanznummer **03** und HANA-System-ID **HN1** verwendet.

Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Azure-VM-Größen
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
* SAP-Hinweis [401162] enthält Informationen zum Vermeiden der Meldung „Adresse bereits verwendet“ beim Einrichten der HANA-Systemreplikation.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* Leitfaden [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP unter Linux][deployment-guide] (dieser Artikel)
* Leitfaden [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [Leitfäden für bewährte Methoden zu SUSE Linux Enterprise Server für SAP-Anwendungen 12 SP3][sles-for-sap-bp]
  * Einrichten einer leistungsoptimierten Infrastruktur für die SAP HANA-Systemreplikation (SLES for SAP Applications 12 SP1). Das Handbuch enthält alle erforderlichen Informationen zum Einrichten der SAP HANA-Systemreplikation für die lokale Entwicklung. Verwenden Sie dieses Handbuch als Grundlage.
  * Einrichten einer kostenoptimierten Infrastruktur für die SAP HANA-Systemreplikation (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Übersicht

Um hohe Verfügbarkeit zu erreichen, ist SAP HANA auf zwei virtuellen Computern installiert. Die Daten werden mit der HANA-Systemreplikation repliziert.

![Übersicht zur Hochverfügbarkeit von SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Das Setup der SAP HANA-Systemreplikation verwendet einen dedizierten virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Die folgende Liste zeigt die Konfiguration des Lastenausgleichs:

* Front-End-Konfiguration: IP-Adresse 10.0.0.13 für „hn1-db“
* Back-End-Konfiguration: Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil der HANA-Systemreplikation sein sollen.
* Testport: Port 62503
* Lastenausgleichsregeln: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Bereitstellen für Linux

Der Ressourcen-Agent für SAP HANA ist im SUSE Linux Enterprise Server für SAP-Anwendungen enthalten.
Der Azure Marketplace enthält ein Image für den SUSE Linux Enterprise Server for SAP Applications 12, das Sie zum Bereitstellen neuer virtueller Computer verwenden können.

### <a name="deploy-with-a-template"></a>Bereitstellen mit einer Vorlage

Sie können eine der Schnellstartvorlagen auf GitHub verwenden, um alle erforderlichen Ressourcen bereitzustellen. Die Vorlage stellt die virtuellen Computer, den Lastenausgleich, die Verfügbarkeitsgruppe usw. bereit.
Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Öffnen Sie die [Datenbankvorlage][template-multisid-db] oder die [konvergierte Vorlage][template-converged] im Azure-Portal. 
    Die Datenbankvorlage erstellt nur die Lastenausgleichsregeln für eine Datenbank. Die konvergierte Vorlage erstellt auch die Lastenausgleichsregeln für eine ASCS/SCS- und ERS-Instanz (nur Linux). Wenn Sie ein SAP NetWeaver-basiertes System installieren und die ASCS/SCS-Instanz auf denselben Computern installieren möchten, verwenden Sie die [konvergierte Vorlage][template-converged].

1. Legen Sie die folgenden Parameter fest:
    - **SAP-System-ID**: Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
    - **Stapeltyp:** (Dieser Parameter gilt nur, wenn Sie die konvergierte Vorlage verwenden.) Wählen Sie den SAP NetWeaver-Stapeltyp aus.
    - **Betriebssystemtyp**: Wählen Sie eine der Linux-Distributionen aus. Wählen Sie für dieses Beispiel **SLES 12** aus.
    - **DB-Typ**: Wählen Sie **HANA** aus.
    - **SAP-Systemgröße**: Geben Sie die SAPS-Anzahl an, die das neue System bereitstellen soll. Wenn Sie nicht sicher sind, welche SAPS-Anzahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
    - **Systemverfügbarkeit**: Wählen Sie **HA** (Hohe Verfügbarkeit).
    - **Administratorbenutzername und Administratorkennwort:** Ein neuer Benutzer wird erstellt, der für die Anmeldung beim Computer verwendet werden kann.
    - **Neues oder vorhandenes Subnetz:** Legt fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein bestehendes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **Vorhanden** aus.
    - **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID sieht in der Regel wie folgt aus: **/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>** .

### <a name="manual-deployment"></a>Manuelle Bereitstellung

> [!IMPORTANT]
> Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem SAP-zertifiziert ist für SAP HANA auf den spezifischen VM-Typen, die Sie verwenden. Die Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese kann unter [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) nachgeschlagen werden. Stellen Sie sicher, dass Sie in die Details des jeweils aufgeführten VM-Typs klicken, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für den spezifischen VM-Typ anzuzeigen.
>  

1. Erstellen Sie eine Ressourcengruppe.
1. Erstellen Sie ein virtuelles Netzwerk.
1. Erstellen Sie eine Verfügbarkeitsgruppe.
   - Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie einen Lastenausgleich (intern). Sie sollten [Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) verwenden.
   - Wählen Sie das virtuelle Netzwerk aus, das Sie in Schritt 2 erstellt haben.
1. Erstellen Sie den virtuellen Computer 1.
   - Verwenden Sie ein SLES4SAP-Abbild im Azure-Katalog, das für SAP HANA auf dem von Ihnen ausgewählten VM-Typ unterstützt wird.
   - Wählen Sie die Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben.
1. Erstellen Sie den virtuellen Computer 2.
   - Verwenden Sie ein SLES4SAP-Abbild im Azure-Katalog, das für SAP HANA auf dem von Ihnen ausgewählten VM-Typ unterstützt wird.
   - Wählen Sie die Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben. 
1. Fügen Sie Datenträger hinzu.

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.   

> [!Note]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

1. Führen Sie bei Verwendung von Load Balancer Standard die folgenden Konfigurationsschritte aus:
   1. Erstellen Sie zunächst einen Front-End-IP-Pool:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
      1. Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.13**).
      1. Klicken Sie auf **OK**.
      1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.
   
   1. Erstellen Sie als Nächstes einen Back-End-Pool:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
      1. Wählen Sie **Virtuelles Netzwerk** aus.
      1. Wählen Sie **Virtuellen Computer hinzufügen** aus.
      1. Wählen Sie **Virtueller Computer** aus.
      1. Wählen Sie die virtuellen Computer des SAP HANA-Clusters und deren IP-Adressen aus.
      1. Wählen Sie **Hinzufügen**.
   
   1. Erstellen Sie als Nächstes einen Integritätstest:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **hana-hp**).
      1. Wählen Sie als Protokoll **TCP** und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
      1. Klicken Sie auf **OK**.
   
   1. Erstellen Sie als Nächstes die Lastenausgleichsregeln:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **hana-lb**).
      1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **hana-frontend**, **hana-backend** und **hana-hp**).
      1. Wählen Sie **HA-Ports** aus.
      1. Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
      1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
      1. Klicken Sie auf **OK**.

1. Wenn Ihr Szenario die Verwendung von Load Balancer Basic vorschreibt, führen Sie stattdessen die folgenden Konfigurationsschritte aus:
   1. Erstellen Sie zunächst einen Front-End-IP-Pool:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
      1. Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse ein (z.B. **10.0.0.13**).
      1. Klicken Sie auf **OK**.
      1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.
   
   1. Erstellen Sie als Nächstes einen Back-End-Pool:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
      1. Wählen Sie **Virtuellen Computer hinzufügen** aus.
      1. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben.
      1. Wählen Sie die virtuellen Computer des SAP HANA-Clusters aus.
      1. Klicken Sie auf **OK**.
   
   1. Erstellen Sie als Nächstes einen Integritätstest:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **hana-hp**).
      1. Wählen Sie als Protokoll **TCP** und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
      1. Klicken Sie auf **OK**.
   
   1. Erstellen Sie die Lastenausgleichsregeln für SAP HANA 1.0:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 15“).
      1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**), aus.
      1. Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 15 ein.
      1. Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
      1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
      1. Klicken Sie auf **OK**.
      1. Wiederholen Sie diese Schritte für den Port 3 **03** 17.
   
   1. Erstellen Sie für SAP HANA 2.0 Lastenausgleichsregeln für die Systemdatenbank:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 13“).
      1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**), aus.
      1. Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 13 ein.
      1. Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
      1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
      1. Klicken Sie auf **OK**.
      1. Wiederholen Sie diese Schritte für den Port 3 **03** 14.
   
   1. Erstellen Sie für SAP HANA 2.0 Lastenausgleichsregeln für die Mandantendatenbank:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 40“).
      1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**) aus.
      1. Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 40 ein.
      1. Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
      1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
      1. Klicken Sie auf **OK**.
      1. Wiederholen Sie diese Schritte für die Ports 3 **03** 41 und 3 **03** 42.

   Weitere Informationen zu den erforderlichen Ports für SAP HANA finden Sie im Kapitel zu [Verbindungen mit Mandantendatenbanken](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) im Handbuch zu [SAP HANA-Mandantendatenbanken](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) oder im [SAP-Hinweis 2388694][2388694].

> [!IMPORTANT]
> Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Siehe auch SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker auf SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) zum Erstellen eines grundlegenden Pacemaker-Clusters für diesen HANA-Server aus. Sie können denselben Pacemaker-Cluster für SAP HANA und SAP NetWeaver (A)SCS verwenden.

## <a name="install-sap-hana"></a>Installieren von SAP HANA

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:
- **[A]** : Der Schritt gilt für alle Knoten.
- **[1]** : Der Schritt gilt nur für den Knoten 1.
- **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

1. **[A]** Richten Sie das Datenträgerlayout **Logical Volume Management (LVM)** (Logische Volumeverwaltung) ein.

   Es wird empfohlen, LVM für Volumes zu verwenden, die Daten- und Protokolldateien speichern. Im folgenden Beispiel wird davon ausgegangen, dass die virtuellen Computer über vier Datenträger verfügen, die zum Erstellen von zwei Volumes verwendet werden.

   Auflisten aller verfügbaren Datenträger:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Beispielausgabe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Erstellen Sie physische Volumes für alle Datenträger, die Sie verwenden möchten:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Erstellen Sie eine Volumegruppe für die Datendateien. Erstellen Sie eine Volumegruppe für Protokolldateien und eine für das freigegebene Verzeichnis von SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Erstellen Sie die logischen Volumes. Ein lineares Volume wird erstellt, wenn Sie `lvcreate` ohne den Schalter `-i` verwenden. Es wird empfohlen, ein Stripesetvolume für eine bessere E/A-Leistung zu erstellen und die Stripegrößen an die in [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md) dokumentierten Werte anzupassen. Das `-i`-Argument sollte die Anzahl der zugrunde liegenden physischen Volumes und das `-I`-Argument die Stripegröße sein. In diesem Dokument werden zwei physische Volumes für das Datenvolume verwendet, daher wird das Argument für den Schalter `-i` auf **2** festgelegt. Die Stripegröße für das Datenvolume beträgt **256 KiB**. Für das Protokollvolume wird ein physisches Volume verwendet, sodass keine `-i`- oder `-I`-Schalter explizit für die Protokollvolumebefehle verwendet werden.  

   > [!IMPORTANT]
   > Verwenden Sie den Schalter `-i`, und ändern Sie die Zahl in die Anzahl der zugrunde liegenden physischen Volumes, wenn Sie für die einzelnen Daten-, Protokoll- oder freigegebenen Volumes mehrere physische Datenträger verwenden. Verwenden Sie den Schalter `-I`, um die Stripegröße festzulegen, wenn Sie ein Stripesetvolume erstellen.  
   > Informationen zu empfohlenen Speicherkonfigurationen, einschließlich Stripegrößen und Anzahl der Datenträger, finden Sie unter [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md).  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Erstellen Sie die Bereitstellungsverzeichnisse, und kopieren Sie die UUID aller logischen Volumes:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Erstellen Sie `fstab`-Einträge für die drei logischen Volumes:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Fügen Sie die folgende Zeile in die Datei `/etc/fstab` ein:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Stellen Sie die neuen Volumes bereit:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Richten Sie das Datenträgerlayout **Einfache Datenträger** ein.

   Für Demosysteme können Sie Ihre HANA-Daten- und Protokolldateien auf einem Datenträger platzieren. Erstellen Sie auf „/dev/disk/azure/scsi1/lun0“ eine Partition, und formatieren Sie sie mit XFS:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Fügen Sie diese Zeile in die Datei „/etc/fstab“ ein:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Erstellen Sie das Zielverzeichnis, und stellen Sie den Datenträger bereit:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Richten Sie die Hostnamensauflösung für alle Hosts ein.

   Sie können entweder einen DNS-Server verwenden oder die Datei „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der Datei „/etc/hosts“ veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie in der Datei „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Installieren Sie die Hochverfügbarkeitspakete für SAP HANA.

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Installieren Sie die SAP HANA-Systemreplikation gemäß Kapitel 4 des [SAP HANA SR Performance Optimized Scenario guide](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/) \(Handbuch zum Szenario der leistungsoptimierten SAP HANA-Systemreplikation).

1. **[A]** Führen Sie das Programm **hdblcm** von der HANA-DVD aus. Geben Sie an der Eingabeaufforderung folgende Werte ein:
   * Wählen Sie die Installation aus: Geben Sie **1** ein.
   * Wählen Sie weitere Komponenten für die Installation aus: Geben Sie **1** ein.
   * Geben Sie den Installationspfad ein [/hana/shared]: Drücken Sie die EINGABETASTE.
   * Geben Sie den Namen des lokalen Hosts ein [..]: Drücken Sie die EINGABETASTE.
   * Möchten Sie dem System weitere Hosts hinzufügen? (j/n) [n] : Drücken Sie die EINGABETASTE.
   * Geben Sie die SAP HANA-System-ID ein: Geben Sie die HANA-SID ein, z.B.: **HN1**.
   * Geben Sie die Instanznummer ein [00]: Geben Sie die HANA-Instanznummer ein. Verwenden Sie **03**, wenn Sie die Azure-Vorlage verwendet oder die in diesem Artikel beschriebene manuelle Bereitstellung durchgeführt haben.
   * Wählen Sie den Datenbankmodus / geben Sie den Index ein [1]: Drücken Sie die EINGABETASTE.
   * Wählen Sie die Systemnutzung / geben Sie den Index [ein 4]: Wählen Sie den Systemnutzungswert aus.
   * Geben Sie den Speicherort der Datenvolumes ein [/hana/data/HN1]: Drücken Sie die EINGABETASTE.
   * Geben Sie den Speicherort der Protokollvolumes ein [/hana/log/HN1]: Drücken Sie die EINGABETASTE.
   * Möchten Sie die maximale Speicherbelegung beschränken? [n]: Drücken Sie die EINGABETASTE.
   * Geben Sie den Zertifikathostnamen für Host „...“ ein [...]: Drücken Sie die EINGABETASTE.
   * Geben Sie das Kennwort für den SAP-Host-Agent-Benutzer ein (sapadm): Geben Sie das Kennwort für den Host-Agent-Benutzer ein.
   * Bestätigen Sie das Kennwort für den SAP-Host-Agent-Benutzer (sapadm): Geben Sie das Kennwort für den Host-Agent-Benutzer erneut ein, um es zu bestätigen.
   * Geben Sie das Kennwort für den Systemadministrator ein (hdbadm): Geben Sie das Systemadministratorkennwort ein.
   * Bestätigen Sie das Kennwort für den Systemadministrator (hdbadm): Geben Sie das Systemadministratorkennwort erneut ein, um es zu bestätigen.
   * Geben Sie das Basisverzeichnis für den Systemadministrator ein [/usr/sap/HN1/home]: Drücken Sie die EINGABETASTE.
   * Geben Sie die Anmelde-Shell für den Systemadministrator ein [/ bin/sh]: Drücken Sie die EINGABETASTE.
   * Geben Sie die Benutzer-ID für den Systemadministrator ein [1001]: Drücken Sie die EINGABETASTE.
   * Geben Sie die ID der Benutzergruppe ein (sapsys) [79]: Drücken Sie die EINGABETASTE.
   * Geben Sie das Datenbankbenutzer-Kennwort ein (SYSTEM): Geben Sie das Kennwort für Datenbankbenutzer ein.
   * Bestätigen Sie das Datenbankbenutzer-Kennwort (SYSTEM): Geben Sie das Kennwort für Datenbankbenutzer erneut ein, um es zu bestätigen.
   * Soll das System nach dem Neustart des Computers neu starten? [n]: Drücken Sie die EINGABETASTE.
   * Möchten Sie fortfahren? (j/n): Überprüfen Sie die Zusammenfassung. Geben Sie **y** ein, um fortzufahren.

1. **[A]** Führen Sie ein Upgrade für den SAP-Host-Agent durch.

   Laden Sie das aktuelle SAP-Host-Agent-Archiv vom [SAP Software Center][sap-swcenter] herunter, und führen Sie den folgenden Befehl zum Aktualisieren des Agents aus. Ersetzen Sie den Pfad zum Archiv, um auf die Datei zu verweisen, die Sie heruntergeladen haben:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurieren der SAP HANA 2.0-Systemreplikation

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

* **[A]** : Der Schritt gilt für alle Knoten.
* **[1]** : Der Schritt gilt nur für den Knoten 1.
* **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

1. **[1]** Erstellen Sie die Mandantendatenbank.

   Wenn Sie SAP HANA 2.0 oder MDC verwenden, erstellen Sie eine Mandantendatenbank für Ihr SAP NetWeaver-System. Ersetzen Sie **NW1** durch die SID des SAP-Systems.

   Führen Sie den folgenden Befehl als „<hanasid\>adm“ aus:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurieren Sie die Systemreplikation auf dem ersten Knoten.

   Sichern Sie die Datenbanken als „<hanasid\>adm“:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopieren Sie die PKI-Systemdateien auf den sekundären Standort:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Erstellen Sie den primären Standort:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurieren Sie die Systemreplikation auf dem zweiten Knoten.
    
   Registrieren Sie den zweiten Knoten zum Starten der Replikation. Führen Sie den folgenden Befehl als „<hanasid\>adm“ aus:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurieren der SAP HANA 1.0-Systemreplikation

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

* **[A]** : Der Schritt gilt für alle Knoten.
* **[1]** : Der Schritt gilt nur für den Knoten 1.
* **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

1. **[1]** Erstellen Sie die erforderlichen Benutzer.

   Führen Sie den folgenden Befehl als root aus. Achten Sie darauf, die fett formatierten Zeichenfolgen (HANA-System-ID **HN1** und Instanzenanzahl **03**) durch die Werte Ihrer SAP HANA-Installation zu ersetzen:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Erstellen Sie den Keystoreeintrag.

   Führen Sie den folgenden Befehl als root aus, um einen neuen Keystoreeintrag zu erstellen:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Sichern Sie die Datenbank.

   Sichern Sie die Datenbanken als root:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Wenn Sie eine Installation mit mehreren Mandanten verwenden, sichern Sie auch die Mandantendatenbank:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Konfigurieren Sie die Systemreplikation auf dem ersten Knoten.

   Erstellen Sie den primären Standort als „<hanasid\>adm“:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurieren Sie die Systemreplikation auf dem zweiten Knoten.

   Registrieren Sie den sekundären Standort als „<hanasid\>adm“:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Erstellen von SAP HANA-Clusterressourcen

Erstellen Sie zuerst die HANA-Topologie. Führen Sie die folgenden Befehle auf einem der Pacemaker-Clusterknoten aus:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Erstellen Sie als Nächstes die HANA-Ressourcen:

> [!IMPORTANT]
> Kürzlich durchgeführte Tests haben Situationen aufgezeigt, in denen netcat aufgrund von Backlog und der Einschränkung, nur eine Verbindung zu verarbeiten, nicht mehr auf Anforderungen reagiert. Die netcat-Ressource lauscht dann nicht mehr auf Azure Load Balancer-Anforderungen, und die Floating IP-Adresse ist nicht mehr verfügbar.  
> Für vorhandene Pacemaker-Cluster wurde zuvor empfohlen, netcat durch socat zu ersetzen. Zurzeit wird empfohlen, den Ressourcen-Agent azure-lb zu verwenden, der Teil des Pakets resource-agents ist. Dabei gelten die folgenden Versionsanforderungen für das Paket:
> - Für SLES 12 SP4/SP5 muss die Version mindestens resource-agents-4.3.018.a7fb5035-3.30.1 sein.  
> - Für SLES 15/15 SP1 muss die Version mindestens resource-agents-4.3.0184.6ee15eb2-4.13.1 sein.  
>
> Beachten Sie, dass für die Änderung eine kurze Ausfallzeit erforderlich ist.  
> Wenn die Konfiguration bei vorhandenen Pacemaker-Clustern bereits für die Verwendung von socat geändert wurde, wie unter [Azure Load Balancer-Erkennungshärtung](https://www.suse.com/support/kb/doc/?id=7024128) beschrieben, müssen Sie nicht sofort zum Ressourcen-Agent azure-lb wechseln.


> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

In diesem Abschnitt wird beschrieben, wie Sie Ihre Einrichtung testen können. Jeder Test setzt voraus, dass Sie der Stammbenutzer sind und der SAP HANA-Master auf dem virtuellen Computer **hn1-db-0** ausgeführt wird.

### <a name="test-the-migration"></a>Testen der Migration

Bevor Sie den Test starten, stellen Sie sicher, dass Pacemaker keine fehlerhaften Aktionen enthält (mit crm_mon -r), dass keine unerwarteten Speicherorteinschränkungen bestehen (z.B. durch zurückgebliebene Elemente vom Migrationstest) und dass HANA synchron ist (z.B. mit SAPHanaSR-showAttr):

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Sie können den SAP HANA-Masterknoten migrieren, indem Sie den folgenden Befehl ausführen:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Wenn Sie `AUTOMATED_REGISTER="false"` festlegen, sollte diese Befehlsfolge den SAP HANA-Masterknoten und die Gruppe, die die virtuelle IP-Adresse enthält, zu „hn1-db-1“ migrieren.

Nachdem die Migration abgeschlossen ist, sieht die Ausgabe von crm_mon -r wie folgt aus:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Die SAP HANA-Ressource auf „hn1-db-0“ wird nicht als sekundär gestartet. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Die Migration erstellt Speicherorteinschränkungen, die erneut gelöscht werden müssen:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Darüber hinaus müssen Sie auch den Status der sekundären Knotenressource bereinigen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Sie überwachen den Status der HANA-Ressource mithilfe von crm_mon -r. Nachdem HANA auf „hn1-db-0“ gestartet wurde, sollte die Ausgabe wie folgt aussehen:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testen des Azure-Umgrenzungs-Agent (nicht SBD)

Sie können die Einrichtung des Azure-Umgrenzungs-Agent testen, indem Sie die Netzwerkschnittstelle auf dem Knoten „hn1-db-0“ deaktivieren:

<pre><code>sudo ifdown eth0
</code></pre>

Der virtuelle Computer sollte jetzt abhängig von Ihrer Clusterkonfiguration neu gestartet oder beendet werden.
Wenn Sie die `stonith-action`-Einstellung auf „Aus“ festlegen, wird der virtuelle Computer beendet, und die Ressourcen werden zu dem ausgeführten virtuellen Computer migriert.

Wenn Sie den virtuellen Computer erneut starten, wird die SAP HANA-Ressource nicht als sekundär gestartet, wenn Sie `AUTOMATED_REGISTER="false"` festlegen. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testen der SBD-Umgrenzung

Sie können das Setup von SBD testen, indem Sie den inquisitor-Prozess beenden.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Der Clusterknoten „hn1-db-0“ sollte neu gestartet werden. Der Pacemaker-Dienst wird anschließend möglicherweise nicht gestartet. Stellen Sie sicher, dass er neu gestartet wird.

### <a name="test-a-manual-failover"></a>Testen eines manuellen Failovers

Sie können ein manuelles Failover durch Beenden des `pacemaker`-Diensts auf Knoten „hn1-db-0“ testen:

<pre><code>service pacemaker stop
</code></pre>

Nach dem Failover können Sie den Dienst erneut starten. Wenn Sie `AUTOMATED_REGISTER="false"` festlegen, wird die SAP HANA-Ressource auf dem Knoten „hn1-db-0“ nicht als sekundär gestartet. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-Tests

> [!IMPORTANT]
> Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem SAP-zertifiziert ist für SAP HANA auf den spezifischen VM-Typen, die Sie verwenden. Die Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese kann unter [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) nachgeschlagen werden. Stellen Sie sicher, dass Sie in die Details des jeweils aufgeführten VM-Typs klicken, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für den spezifischen VM-Typ anzuzeigen.

Führen Sie abhängig von Ihrem Anwendungsfall alle Testfälle aus, die im Szenario zur leistungsoptimierten SAP HANA-Systemreplikation oder zur kostenoptimierten SAP HANA-Systemreplikation aufgeführt werden. Sie finden diese Anleitungen auf der Seite [SLES for SAP – Best Practices][sles-for-sap-bp].

Die folgenden Tests sind eine Kopie der Testbeschreibungen aus der Anleitung zum Szenario für die leistungsoptimierte SAP HANA-Systemreplikation unter SUSE Linux Enterprise Server for SAP Applications 12 SP1. Eine aktuelle Version finden Sie stets in der Anleitung selbst. Stellen Sie immer sicher, dass HANA synchron ist, bevor Sie den Test starten, und dass die Pacemaker-Konfiguration korrekt ist.

In den folgenden Testbeschreibungen wird davon ausgegangen, dass PREFER_SITE_TAKEOVER="true" und AUTOMATED_REGISTER="false" gelten.
HINWEIS:  Die folgenden Tests sind darauf ausgelegt, nacheinander ausgeführt zu werden. Sie hängen vom Endzustand der vorherigen Tests ab.

1. TEST 1: BEENDEN DER PRIMÄREN DATENBANK AUF KNOTEN 1

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-0“ aus:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker sollte die beendete HANA-Instanz erkennen und ein Failover auf den anderen Knoten ausführen. Nach Abschluss des Failovers wird die HANA-Instanz auf Knoten „hn1-db-0“ beendet, da Pacemaker den Knoten nicht automatisch als sekundären HANA-Knoten registriert.

   Führen Sie die folgenden Befehle aus, um den Knoten „hn1-db-0“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: BEENDEN DER PRIMÄREN DATENBANK AUF KNOTEN 2

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-1“ aus:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker sollte die beendete HANA-Instanz erkennen und ein Failover auf den anderen Knoten ausführen. Nach Abschluss des Failovers wird die HANA-Instanz auf dem Knoten „hn1-db-1“ beendet, da Pacemaker den Knoten nicht automatisch als sekundären HANA-Knoten registriert.

   Führen Sie die folgenden Befehle aus, um den Knoten „hn1-db-1“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: ABSTURZ DER PRIMÄREN DATENBANK AUF KNOTEN 1

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-0“ aus:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker sollte die beendete HANA-Instanz erkennen und ein Failover auf den anderen Knoten ausführen. Nach Abschluss des Failovers wird die HANA-Instanz auf Knoten „hn1-db-0“ beendet, da Pacemaker den Knoten nicht automatisch als sekundären HANA-Knoten registriert.

   Führen Sie die folgenden Befehle aus, um den Knoten „hn1-db-0“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: ABSTURZ DER PRIMÄREN DATENBANK AUF KNOTEN 2

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-1“ aus:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker sollte die beendete HANA-Instanz erkennen und ein Failover auf den anderen Knoten ausführen. Nach Abschluss des Failovers wird die HANA-Instanz auf dem Knoten „hn1-db-1“ beendet, da Pacemaker den Knoten nicht automatisch als sekundären HANA-Knoten registriert.

   Führen Sie die folgenden Befehle aus, um den Knoten „hn1-db-1“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: ABSTURZ DES KNOTENS AM PRIMÄREN STANDORT (KNOTEN 1)

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten „hn1-db-0“ aus:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker sollte den beendeten Clusterknoten erkennen und den Knoten umgrenzen. Nachdem der Knoten umgrenzt wurde, löst Pacemaker eine Übernahme der HANA-Instanz aus. Bei einem Neustart des umgrenzten Knotens wird Pacemaker nicht automatisch gestartet.

   Führen Sie die folgenden Befehle aus, um Pacemaker zu starten, die SBD-Nachrichten für den Knoten „hn1-db-0“ zu bereinigen, den Knoten „hn1-db-0“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: ABSTURZ DES KNOTENS AM SEKUNDÄREN STANDORT (KNOTEN 2)

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten „hn1-db-1“ aus:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker sollte den beendeten Clusterknoten erkennen und den Knoten umgrenzen. Nachdem der Knoten umgrenzt wurde, löst Pacemaker eine Übernahme der HANA-Instanz aus. Bei einem Neustart des umgrenzten Knotens wird Pacemaker nicht automatisch gestartet.

   Führen Sie die folgenden Befehle aus, um Pacemaker zu starten, die SBD-Nachrichten für den Knoten „hn1-db-1“ zu bereinigen, den Knoten „hn1-db-1“ als sekundär zu registrieren und die fehlerhafte Ressource zu bereinigen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: BEENDEN DER SEKUNDÄREN DATENBANK AUF KNOTEN 2

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-1“ aus:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker erkennt die beendete HANA-Instanz und kennzeichnet die Ressource auf dem Knoten „hn1-db-1“ als fehlerhaft. Pacemaker sollte die HANA-Instanz automatisch neu starten. Führen Sie zum Bereinigen des fehlerhaften Zustands den folgenden Befehl aus:

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: ABSTURZ DER SEKUNDÄREN DATENBANK AUF KNOTEN 2

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als „<hanasid\>adm on node hn1-db-1“ aus:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker erkennt die beendete HANA-Instanz und kennzeichnet die Ressource auf dem Knoten „hn1-db-1“ als fehlerhaft. Führen Sie zum Bereinigen des fehlerhaften Zustands den folgenden Befehl aus: Pacemaker sollte die HANA-Instanz dann automatisch neu starten.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: ABSTURZ DES KNOTENS AM SEKUNDÄREN STANDORT (KNOTEN 2), AUF DEM DIE SEKUNDÄRE HANA-DATENBANK AUSGEFÜHRT WIRD

   Zustand der Ressource vor dem Starten des Tests:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Führen Sie die folgenden Befehle als root auf dem Knoten „hn1-db-1“ aus:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker sollte den beendeten Clusterknoten erkennen und den Knoten umgrenzen. Bei einem Neustart des umgrenzten Knotens wird Pacemaker nicht automatisch gestartet.

   Führen Sie die folgenden Befehle aus, um Pacemaker zu starten und die SBD-Nachrichten für den Knoten „hn1-db-1“ sowie die fehlerhafte Ressource zu bereinigen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Zustand der Ressource nach dem Test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
