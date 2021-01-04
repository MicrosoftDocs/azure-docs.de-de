---
title: Hochverfügbarkeit von SAP HANA auf Azure-VMs unter RHEL | Microsoft-Dokumentation
description: Richten Sie die Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs) ein.
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
ms.openlocfilehash: 277ed8ad5f9888daa911cb3b5c7dcf00fd285bf4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489155"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Für die lokale Entwicklung können Sie entweder die HANA-Systemreplikation oder freigegebenen Speicher verwenden, um Hochverfügbarkeit für SAP HANA einzurichten.
Die HANA-Systemreplikation in Azure ist derzeit die einzige auf virtuellen Azure-Computern (VMs) unterstützte Hochverfügbarkeitsfunktion.
Die SAP HANA-Replikation umfasst primären Knoten und mindestens einen sekundären Knoten. Änderungen an den Daten auf dem primären Knoten werden synchron oder asynchron an den sekundären Knoten repliziert.

Dieser Artikel beschreibt das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks sowie das Installieren und Konfigurieren der SAP HANA-Systemreplikation.
In den Beispielkonfigurationen und Installationsbefehlen werden die Instanznummer **03** und HANA-System-ID **HN1** verwendet.

Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Azure-VM-Größen
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
* [Azure Virtual Machines – Bereitstellung für SAP unter Linux (dieser Artikel)][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [SAP HANA-Systemreplikation in Pacemaker-Cluster](https://access.redhat.com/articles/3004101)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installieren von SAP HANA unter Red Hat Enterprise Linux für die Verwendung in Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Übersicht

Um hohe Verfügbarkeit zu erreichen, ist SAP HANA auf zwei virtuellen Computern installiert. Die Daten werden mit der HANA-Systemreplikation repliziert.

![Übersicht zur Hochverfügbarkeit von SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

Das Setup der SAP HANA-Systemreplikation verwendet einen dedizierten virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Die folgende Liste zeigt die Konfiguration des Lastenausgleichs:

* Front-End-Konfiguration: IP-Adresse 10.0.0.13 für „hn1-db“
* Back-End-Konfiguration: Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil der HANA-Systemreplikation sein sollen.
* Testport: Port 62503
* Lastenausgleichsregeln: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Bereitstellen für Linux

Der Azure Marketplace enthält ein Image für Red Hat Enterprise Linux 7.4 für SAP HANA, mit dem Sie neue VMs bereitstellen können.

### <a name="deploy-with-a-template"></a>Bereitstellen mit einer Vorlage

Sie können eine der Schnellstartvorlagen auf GitHub verwenden, um alle erforderlichen Ressourcen bereitzustellen. Die Vorlage stellt die virtuellen Computer, den Lastenausgleich, die Verfügbarkeitsgruppe usw. bereit.
Führen Sie diese Schritte aus, um die Vorlage bereitzustellen:

1. Öffnen Sie die [Datenbankvorlage][template-multisid-db] im Azure-Portal.
1. Legen Sie die folgenden Parameter fest:
    * **SAP-System-ID**: Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
    * **Betriebssystemtyp**: Wählen Sie eine der Linux-Distributionen aus. Wählen Sie für dieses Beispiel die Option **RHEL 7** aus.
    * **DB-Typ**: Wählen Sie **HANA** aus.
    * **SAP-Systemgröße**: Geben Sie die SAPS-Anzahl an, die das neue System bereitstellen soll. Wenn Sie nicht sicher sind, welche SAPS-Anzahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
    * **Systemverfügbarkeit**: Wählen Sie **HA** (Hohe Verfügbarkeit).
    * **Administratorbenutzername, Administratorkennwort oder SSH-Schlüssel**: Ein neuer Benutzer wird erstellt, der für die Anmeldung beim Computer verwendet werden kann.
    * **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID sieht in der Regel wie folgt aus: **/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>** . Lassen Sie das Feld leer, wenn Sie ein neues virtuelles Netzwerk erstellen möchten.

### <a name="manual-deployment"></a>Manuelle Bereitstellung

1. Erstellen Sie eine Ressourcengruppe.
1. Erstellen Sie ein virtuelles Netzwerk.
1. Erstellen Sie eine Verfügbarkeitsgruppe.  
   Richten Sie die maximale Updatedomäne ein.
1. Erstellen Sie einen Lastenausgleich (intern). Sie sollten [Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) verwenden.
   * Wählen Sie das virtuelle Netzwerk aus, das Sie in Schritt 2 erstellt haben.
1. Erstellen Sie den virtuellen Computer 1.  
   Verwenden Sie Red Hat Enterprise Linux 7.4 oder höher für SAP HANA. Dieses Beispiel verwendet das Image für Red Hat Enterprise Linux 7.4 für SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM>. Wählen Sie die in Schritt 3 erstellte Verfügbarkeitsgruppe aus.
1. Erstellen Sie den virtuellen Computer 2.  
   Verwenden Sie Red Hat Enterprise Linux 7.4 oder höher für SAP HANA. Dieses Beispiel verwendet das Image für Red Hat Enterprise Linux 7.4 für SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM>. Wählen Sie die in Schritt 3 erstellte Verfügbarkeitsgruppe aus.
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
   1. Konfigurieren Sie den Lastenausgleich. Erstellen Sie zunächst einen Front-End-IP-Pool:

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

## <a name="install-sap-hana"></a>Installieren von SAP HANA

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

* **[A]** : Der Schritt gilt für alle Knoten.
* **[1]** : Der Schritt gilt nur für den Knoten 1.
* **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

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

1. **[A]** Konfigurieren Sie RHEL für HANA.

   Konfigurieren Sie RHEL wie in <https://access.redhat.com/solutions/2447641> und in den folgenden SAP-Hinweisen beschrieben:  
   - [2292690 – SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 7)
   - [2777782 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
   - [2593824 – Linux: Ausführen von mit GCC 7.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Ausführen von mit GCC 9.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** Installieren Sie SAP HANA

   Befolgen Sie die Anweisungen unter <https://access.redhat.com/articles/3004101>, um die SAP HANA-Systemreplikation zu installieren.

   * Führen Sie das Programm **hdblcm** von der HANA-DVD aus. Geben Sie an der Eingabeaufforderung folgende Werte ein:
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

1. **[A]** Konfigurieren Sie die Firewall.

   Erstellen Sie die Firewallregel für den Testport des Azure Load Balancers.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurieren der SAP HANA 2.0-Systemreplikation

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

* **[A]** : Der Schritt gilt für alle Knoten.
* **[1]** : Der Schritt gilt nur für den Knoten 1.
* **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

1. **[A]** Konfigurieren Sie die Firewall.

   Erstellen Sie Firewallregeln, um die HANA-Systemreplikation und Clientdatenverkehr zuzulassen. Die erforderlichen Ports finden Sie unter [TCP/IP-Ports aller SAP-Produkte](https://help.sap.com/viewer/ports). Die folgenden Befehle sind nur ein Beispiel für die Genehmigung der HANA 2.0-Systemreplikation und des Clientdatenverkehrs zu den Datenbanken SYSTEMDB, HN1 und NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Erstellen Sie die Mandantendatenbank.

   Wenn Sie SAP HANA 2.0 oder MDC verwenden, erstellen Sie eine Mandantendatenbank für Ihr SAP NetWeaver-System. Ersetzen Sie **NW1** durch die SID des SAP-Systems.

   Führen Sie als „<hanasid\>adm“ den folgenden Befehl aus:

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

1. **[1]** Überprüfen Sie den Replikationsstatus.

   Überprüfen Sie den Replikationsstatus, und warten Sie, bis alle Datenbanken synchronisiert wurden. Wenn der Status weiterhin „Unbekannt“ lautet, überprüfen Sie die Firewalleinstellungen.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurieren der SAP HANA 1.0-Systemreplikation

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

* **[A]** : Der Schritt gilt für alle Knoten.
* **[1]** : Der Schritt gilt nur für den Knoten 1.
* **[2]** : Der Schritt gilt nur für den Knoten 2 des Pacemaker-Clusters.

1. **[A]** Konfigurieren Sie die Firewall.

   Erstellen Sie Firewallregeln, um die HANA-Systemreplikation und Clientdatenverkehr zuzulassen. Die erforderlichen Ports finden Sie unter [TCP/IP-Ports aller SAP-Produkte](https://help.sap.com/viewer/ports). Die folgenden Befehle sind nur ein Beispiel für die Genehmigung der HANA 2.0-Systemreplikation. Passen Sie es gemäß Ihrer SAP HANA 1.0-Installation an.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) aus, um ein grundlegendes Pacemaker-Cluster für diesen HANA-Server zu erstellen.

## <a name="create-sap-hana-cluster-resources"></a>Erstellen von SAP HANA-Clusterressourcen

Installieren Sie die SAP HANA-Ressourcen-Agents auf **allen Knoten**. Achten Sie darauf, dass Sie ein Repository aktivieren, das das Paket enthält. Sie müssen keine zusätzlichen Repositorys aktivieren, wenn Sie ein RHEL 8.x HA-fähiges Image verwenden.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Erstellen Sie dann die HANA-Topologie. Führen Sie die folgenden Befehle auf einem der Pacemaker-Clusterknoten aus:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Erstellen Sie als Nächstes die HANA-Ressourcen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

Wenn Sie einen Cluster auf **RHEL 7.x** aufbauen, verwenden Sie die folgenden Befehle:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Wenn Sie einen Cluster auf **RHEL 8.x** aufbauen, verwenden Sie die folgenden Befehle:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

> [!NOTE]
> Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische HANA-Setup angepasst werden. Beispielsweise müssen Sie ggf. das Starttimeout erhöhen, wenn es länger dauert, bis die SAP HANA-Datenbank gestartet wird.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

In diesem Abschnitt wird beschrieben, wie Sie Ihre Einrichtung testen können. Bevor Sie den Test starten, stellen Sie sicher, dass Pacemaker keine fehlerhaften Aktionen enthält (mit „pcs status“), dass keine unerwarteten Speicherorteinschränkungen bestehen (z.B. durch zurückgebliebene Elemente vom Migrationstest) und dass HANA synchron ist (z.B. mit „systemReplicationStatus“):

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testen der Migration

Zustand der Ressource vor dem Starten des Tests:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Sie können den SAP HANA-Masterknoten migrieren, indem Sie den folgenden Befehl ausführen:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Wenn Sie `AUTOMATED_REGISTER="false"` festlegen, migriert dieser Befehl den SAP HANA-Masterknoten und die Gruppe, die die virtuelle IP-Adresse für „hn1-db-1“ enthält.

Nachdem die Migration abgeschlossen wurde, sieht die „sudo pcs status“-Ausgabe wie folgt aus:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Die SAP HANA-Ressource auf „hn1-db-0“ wird beendet. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Die Migration erstellt Speicherorteinschränkungen, die erneut gelöscht werden müssen:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Überwachen Sie den Status der HANA-Ressource mithilfe von „pcs status“. Nachdem HANA auf „hn1-db-0“ gestartet wurde, sollte die Ausgabe wie folgt aussehen:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testen des Azure-Umgrenzungs-Agents

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.  

Zustand der Ressource vor dem Starten des Tests:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Sie können die Einrichtung des Azure-Umgrenzungs-Agent testen, indem Sie die Netzwerkschnittstelle auf dem Knoten deaktivieren, auf dem SAP HANA als Master ausgeführt wird.
Im [Red Hat-Knowledgebase-Artikel 79523](https://access.redhat.com/solutions/79523) wird beschrieben, wie ein Netzwerkfehler simuliert wird. In diesem Beispiel verwenden wir das net_breaker-Skript, um den gesamten Zugriff auf das Netzwerk zu blockieren.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Der virtuelle Computer sollte jetzt abhängig von Ihrer Clusterkonfiguration neu gestartet oder beendet werden.
Wenn Sie die `stonith-action`-Einstellung auf „Aus“ festlegen, wird der virtuelle Computer beendet, und die Ressourcen werden zu dem ausgeführten virtuellen Computer migriert.

Wenn Sie den virtuellen Computer erneut starten, wird die SAP HANA-Ressource nicht als sekundär gestartet, wenn Sie `AUTOMATED_REGISTER="false"` festlegen. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Zustand der Ressource nach dem Test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testen eines manuellen Failovers

Zustand der Ressource vor dem Starten des Tests:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Sie können ein manuelles Failover durch Beenden des Clusters auf dem „hn1-db-0“-Knoten testen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Nach dem Failover können Sie den Cluster erneut starten. Wenn Sie `AUTOMATED_REGISTER="false"` festlegen, wird die SAP HANA-Ressource auf dem Knoten „hn1-db-0“ nicht als sekundär gestartet. In diesem Fall konfigurieren Sie die HANA-Instanz als sekundär, indem Sie diesen Befehl ausführen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Zustand der Ressource nach dem Test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Testen eines manuellen Failovers

Zustand der Ressource vor dem Starten des Tests:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Sie können ein manuelles Failover durch Beenden des Clusters auf dem „hn1-db-0“-Knoten testen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md)