---
title: Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter RHEL | Microsoft-Dokumentation
description: Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter RHEL
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
ms.openlocfilehash: 23a5ea2d3ffc1511bea66bb8bc3c4282b6d16cc2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489121"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Hochverfügbarkeit der horizontalen SAP HANA-Skalierung unter Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Dieser Artikel beschreibt die Bereitstellung eines hochverfügbaren SAP HANA-Systems in einer Konfiguration mit horizontaler Skalierung mit HSR (HANA System Replication) und Pacemaker auf virtuellen Azure Red Hat Enterprise Linux-Computern (VMs). Die freigegebenen Dateisysteme in der vorgestellten Architektur werden von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) bereitgestellt und über NFS eingebunden.  

In den Beispielkonfigurationen, Installationsbefehlen usw. werden die HANA-Instanz **03** und HANA-System-ID **HN1** verwendet. Die Beispiele basieren auf HANA 2.0 SP4 und Red Hat Enterprise Linux für SAP 7.6. 

Bevor Sie beginnen, lesen Sie die folgenden SAP-Hinweise und Dokumente:

* SAP-Hinweis [1928533] enthält:  
  * Liste der für die Bereitstellung von SAP-Software unterstützten Azure-VM-Größen
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Die erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* SAP-Hinweis [2015553]: Enthält eine Liste der Voraussetzungen für Bereitstellungen von SAP-Software in Azure, die von SAP unterstützt werden
* SAP-Hinweis [2002167] enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux.
* SAP-Hinweis [2009879] enthält SAP HANA-Richtlinien für Red Hat Enterprise Linux.
* SAP-Hinweis [2178632]: Enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden
* SAP-Hinweis [2191498]: Enthält die erforderliche SAP Host Agent-Version für Linux in Azure
* SAP-Hinweis [2243692]: Enthält Informationen zur SAP-Lizenzierung unter Linux in Azure
* SAP-Hinweis [1999351]: Enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP
* SAP-Hinweis [1900823]: Enthält Informationen zu SAP HANA-Speicheranforderungen
* [Wiki der SAP Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [SAP HANA-Netzwerkanforderungen](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux -Netzwerkleitfaden](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Konfigurieren der horizontalen SAP HANA-Skalierungssystemreplikation in einem Pacemaker-Cluster mit HANA-Dateisystemen auf NFS-Freigaben](https://access.redhat.com/solutions/5423971)
* Azure-spezifische RHEL-Dokumentation:
  * [Installieren von SAP HANA unter Red Hat Enterprise Linux für die Verwendung in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat Enterprise Linux-Lösung für horizontale SAP HANA-Skalierung und -Systemreplikation](https://access.redhat.com/solutions/4386601)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]
* [Azure NetApp Files-Dokumentation][anf-azure-doc] 


## <a name="overview"></a>Übersicht

Eine Methode zum Erreichen von HANA-Hochverfügbarkeit für Installationen horizontaler HANA-Skalierungen besteht darin, die HANA-Systemreplikation zu konfigurieren und die Lösung mit einem Pacemaker-Cluster zu schützen, um ein automatisches Failover zu ermöglichen. Wenn ein aktiver Knoten ausfällt, führt der Clusterknoten einen Failover zu den HANA-Ressourcen am anderen Standort aus.  
Die vorgestellte Konfiguration zeigt drei HANA-Knoten an jedem Standort sowie einen Majority Maker-Knoten, um ein Split-Brain-Szenario zu verhindern. Die Anweisungen können angepasst werden, um mehr virtuelle Computer als HANA DB-Knoten einzubeziehen.  

Das freigegebene HANA-Dateisystem `/hana/shared` in der vorgestellten Architektur wird von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) bereitgestellt. Es wird über NFSv4.1 auf jedem HANA-Knoten an demselben Replikationsstandort des HANA-Systems eingebunden. Die Dateisysteme `/hana/data` und `/hana/log` sind lokale Dateisysteme und werden nicht von den HANA DB-Knoten freigegeben. SAP HANA wird im nicht freigegebenen Modus installiert. 

> [!TIP]
> Informationen zu empfohlenen SAP HANA-Speicherkonfigurationen finden Sie unter [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md).   

[![Horizontale SAP HANA-Skalierung mit HSR und Pacemaker-Cluster](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Im vorangehenden Diagramm werden drei Subnetze in einem virtuellen Azure-Netzwerk dargestellt, das den Empfehlungen für SAP HANA-Netzwerke entspricht: 
* Für die Clientkommunikation – `client` 10.23.0.0/24  
* Für die interne Kommunikation zwischen SAP HANA-Knoten – `inter` 10.23.1.128/26  
* Für die HANA-Systemreplikation – `hsr` 10.23.1.192/26  

Da `/hana/data` und `/hana/log` auf lokalen Datenträgern bereitgestellt werden, ist es nicht erforderlich, separate Subnetze und separate virtuelle Netzwerkkarten für die Kommunikation mit dem Speicher bereitzustellen.  

Die Azure NetApp-Volumes werden in einem separaten Subnetz bereitgestellt, das an [Azure NetApp Files delegiert wurde](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Einrichten der Infrastruktur

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits die Ressourcengruppe und das Azure Virtual Network mit drei Azure Virtual Network-Subnetzen erstellt haben: `client`, `inter` und `hsr`.

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal
1. Stellen Sie die virtuellen Azure-Computer bereit.  
Stellen Sie für die in diesem Dokument vorgestellte Konfiguration sieben virtuelle Computer bereit: 
   - Drei virtuelle Computer, die als HANA DB-Knoten für HANA-Replikationsstandort 1 dienen: **hana-s1-db1**, **hana-s1-db2** und **hana-s1-db3**  
   - Drei virtuelle Computer, die als HANA DB-Knoten für HANA-Replikationsstandort 2 dienen: **hana-s2-db1**, **hana-s2-db2** and **hana-s2-db3**  
   - Ein kleiner virtueller Computer, der als *Majority Maker* dient: **hana-s-mm**

   Die virtuellen Computer, die als SAP DB HANA-Knoten bereitgestellt werden, sollten von SAP für HANA zertifiziert sein, wie im [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) veröffentlicht. Stellen Sie bei der Bereitstellung der HANA DB-Knoten sicher, dass [Beschleunigtes Netzwerk](../../../virtual-network/create-vm-accelerated-networking-cli.md) aktiviert ist.  
  
   Für den Majority Maker-Knoten können Sie einen kleinen virtuellen Computer bereitstellen, da auf diesem keine der SAP HANA-Ressourcen ausgeführt wird. Der virtuelle Majority Maker-Computer wird in der Clusterkonfiguration verwendet, um eine ungerade Anzahl von Clusterknoten in einem Split-Brain-Szenario zu erreichen. Der virtuelle Majority Maker-Computer benötigt in diesem Beispiel nur eine virtuelle Netzwerkschnittstelle im Subnetz `client`.        

   Stellen Sie lokal verwaltete Datenträger für `/hana/data` und `/hana/log` bereit. Die empfohlene Mindestspeicherkonfiguration für `/hana/data` und `/hana/log` ist in der [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md) beschrieben.

   Stellen Sie die primäre Netzwerkschnittstelle für die einzelnen virtuellen Computer im `client`-Subnetz des virtuellen Netzwerks bereit.  
   Wenn der virtuelle Computer über das Azure-Portal bereitgestellt wird, erfolgt die automatische Generierung des Namens der Netzwerkschnittstelle. In diesen Anweisungen werden die automatisch generierten primären Netzwerkschnittstellen, die mit dem `client`-Subnetz des virtuellen Azure-Netzwerks verbunden sind, der Einfachheit halber als **hana-s1-db1-client**, **hana-s1-db2-client**, **hana-s1-db3-client** usw. bezeichnet.  


   > [!IMPORTANT]
   > Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem SAP-zertifiziert ist für SAP HANA auf den spezifischen VM-Typen, die Sie verwenden. Eine Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese finden Sie auf der Website [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klicken Sie in die Details des jeweils aufgeführten VM-Typs, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für diesen Typ anzuzeigen.  
  

2. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `inter`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-inter**, **hana-s1-db2-inter**, **hana-s1-db3-inter**, **hana-s2-db1-inter**, **hana-s2-db2-inter** und **hana-s2-db3-inter**).  

3. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `hsr`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-hsr**, **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr** und **hana-s2-db3-hsr**).  

4. Fügen Sie die neu erstellten virtuellen Netzwerkschnittstellen an die entsprechenden virtuellen Computer an:  

    a. Navigieren Sie im [Azure-Portal](https://portal.azure.com/#home) zum virtuellen Computer.  

    b. Wählen Sie im linken Bereich **Virtuelle Computer** aus. Filtern Sie nach dem Namen des virtuellen Computers (z. B. **hana-s1-db1**), und wählen Sie dann den virtuellen Computer aus.  

    c. Klicken Sie im Bereich **Übersicht** auf **Beenden**, um die Zuordnung des virtuellen Computers aufzuheben.  

    d. Wählen Sie **Netzwerk** aus, und fügen Sie dann die Netzwerkschnittstelle an. Wählen Sie in der Dropdownliste **Netzwerkschnittstelle anfügen** die bereits erstellten Netzwerkschnittstellen für die Subnetze `inter` und `hsr` aus.  
    
    e. Wählen Sie **Speichern** aus. 
 
    f. Wiederholen Sie die Schritte b bis e für die übrigen virtuellen Computer (in unserem Beispiel **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2** und **hana-s2-db3**).
 
    g. Belassen Sie die virtuelle Computer für den Moment im Status „Beendet“. Als Nächstes aktivieren wir den [beschleunigten Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md) für alle neu angefügten Netzwerkschnittstellen.  

5. Aktivieren Sie den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen für die Subnetze `inter` und `hsr` mit den folgenden Schritten:  

    a. Öffnen Sie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) im [Azure-Portal](https://portal.azure.com/#home).  

    b. Führen Sie die folgenden Befehle aus, um den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen zu aktivieren, die wir an die Subnetze `inter` und `hsr` angefügt haben.  

    ```
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Starten der virtuellen HANA DB-Computer

### <a name="deploy-azure-load-balancer"></a>Bereitstellen von Azure Load Balancer

1. Es wird empfohlen, Load Balancer Standard zu verwenden. Befolgen Sie die folgenden Konfigurationsschritte zur Bereitstellung von Load Balancer Standard:
   1. Erstellen Sie zunächst einen Front-End-IP-Pool:

      1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
      1. Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse ein (z. B. **10.23.0.18**).
      1. Klicken Sie auf **OK**.
      1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.

   1. Erstellen Sie dann einen Back-End-Pool, und fügen Sie alle virtuellen Clustercomputer zum Back-End-Pool hinzu:

      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
      1. Wählen Sie **Virtuellen Computer hinzufügen** aus.
      1. Klicken Sie auf **Virtuelle Computer**.
      1. Wählen Sie die virtuellen Computer des SAP HANA-Clusters und deren IP-Adressen für das `client`-Subnetz aus.
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

   > [!IMPORTANT]
   > Floating IP-Adressen werden für sekundäre NIC-IP-Konfigurationen in Szenarien mit Lastenausgleich nicht unterstützt. Ausführliche Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.    
   
   > [!Note]
   > Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  


   > [!IMPORTANT]
   > Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Siehe auch SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Bereitstellen der Infrastruktur für Azure NetApp Files 

Stellen Sie die ANF-Volumes für das `/hana/shared`-Dateisystem bereit. Sie benötigen ein separates `/hana/shared`-Volume für jeden Replikationsstandort des HANA-Systems. Weitere Informationen finden Sie unter [Einrichten der Infrastruktur für Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

In diesem Beispiel wurden die folgenden Azure NetApp Files-Volumes verwendet: 

* Volume „**HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)“
* Volume „**HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)“

## <a name="operating-system-configuration-and-preparation"></a>Konfiguration und Vorbereitung des Betriebssystems

Die Anweisungen in den nächsten Abschnitten weisen eine der folgenden Abkürzungen auf:
* **[A]** :      Gilt für alle Knoten
* **[AH]** :     Gilt für alle HANA DB-Knoten
* **[M]** :      Gilt für den Majority Maker-Knoten
* **[AH1]** :    Gilt für alle HANA DB-Knoten an STANDORT 1
* **[AH2]** :    Gilt für alle HANA DB-Knoten an STANDORT 2
* **[1]** :      Gilt nur für HANA DB-Knoten 1, STANDORT 1
* **[2]** :      Gilt nur für HANA DB-Knoten 1, STANDORT 2


Führen Sie die folgenden Schritte aus, um das Betriebssystem zu konfigurieren und vorzubereiten:

1. **[A]** Verwalten Sie die Hostdateien auf den virtuellen Computern. Schließen Sie Einträge für alle Subnetze ein. Die folgenden Einträge wurden für dieses Beispiel zu `/etc/hosts` hinzugefügt.  

    ```
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** Installieren Sie das NFS-Clientpaket.  

    ```yum install nfs-utils ```


3. **[AH]** Red Hat für HANA-Konfiguration.  

    Konfigurieren Sie RHEL wie in <https://access.redhat.com/solutions/2447641> und in den folgenden SAP-Hinweisen beschrieben:  
   - [2292690 – SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 7)
   - [2777782 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
   - [2593824 – Linux: Ausführen von mit GCC 7.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Ausführen von mit GCC 9.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Vorbereiten der Dateisysteme
### <a name="mount-the-shared-file-systems"></a>Einbinden der freigegebenen Dateisysteme

In diesem Beispiel werden die freigegebenen HANA-Dateisysteme auf Azure NetApp Files bereitgestellt und über NFSv4 eingebunden.  

1. **[AH]** Erstellen Sie Bereitstellungspunkte für die HANA-Datenbankvolumes.  

    ```
    mkdir -p /hana/shared
    ```

2. **[AH]** Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **`defaultv4iddomain.com`** ) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.  
   Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFSv4.1 verwenden.  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass die NFS-Domäne in `/etc/idmapd.conf` auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **`defaultv4iddomain.com`** . Wenn es einen Konflikt zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) gibt, werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als `nobody` angezeigt.  

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** Überprüfen Sie `nfs4_disable_idmapping`. Diese Angabe sollte auf **Y** (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.  
   Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFSv4.1 verwenden.  

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Weitere Informationen zum Ändern des Parameters `nfs4_disable_idmapping` finden Sie unter https://access.redhat.com/solutions/1749883.

4. **[AH1]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 1 ein.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 2 ein.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[AH]** Überprüfen Sie, ob die entsprechenden `/hana/shared/`-Dateisysteme auf allen virtuellen HANA DB-Computern mit NFS-Protokollversion **NFSv4** eingebunden sind.  

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Vorbereiten der Daten und Protokollieren lokaler Dateisysteme
In der dargestellten Konfiguration werden die Dateisysteme `/hana/data` und `/hana/log` auf einem verwalteten Datenträger bereitgestellt und lokal an die einzelnen virtuellen HANA DB-Computer angefügt. Sie müssen die Schritte zur Erstellung der lokalen Daten- und Protokollvolumes auf jedem virtuellen HANA DB-Computer ausführen. 

Richten Sie das Datenträgerlayout mit **Logical Volume Manager (LVM)** ein. Im folgenden Beispiel wird davon ausgegangen, dass jeder virtuelle HANA-Computer über drei Datenträger verfügen, die zum Erstellen von zwei Volumes verwendet werden.

1. **[AH]** Auflisten aller verfügbaren Datenträger:
    ```
    ls /dev/disk/azure/scsi1/lun*
    ```

   Beispielausgabe:

    ```
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** Erstellen Sie physische Volumes für alle Datenträger, die Sie verwenden möchten:
    ```
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** Erstellen Sie eine Volumegruppe für die Datendateien. Erstellen Sie eine Volumegruppe für Protokolldateien und eine für das freigegebene Verzeichnis von SAP HANA:
    ```
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** Erstellen Sie die logischen Volumes. 
   Ein lineares Volume wird erstellt, wenn Sie `lvcreate` ohne den Schalter `-i` verwenden. Es wird empfohlen, ein Stripesetvolume für eine bessere E/A-Leistung zu erstellen und die Stripegrößen an die in [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md) dokumentierten Werte anzupassen. Das `-i`-Argument sollte die Anzahl der zugrunde liegenden physischen Volumes und das `-I`-Argument die Stripegröße sein. In diesem Dokument werden zwei physische Volumes für das Datenvolume verwendet, daher wird das Argument für den Schalter `-i` auf **2** festgelegt. Die Stripegröße für das Datenvolume beträgt **256 KiB**. Für das Protokollvolume wird ein physisches Volume verwendet, sodass keine `-i`- oder `-I`-Schalter explizit für die Protokollvolumebefehle verwendet werden.  

   > [!IMPORTANT]
   > Verwenden Sie den Schalter `-i`, und ändern Sie die Zahl in die Anzahl der zugrunde liegenden physischen Volumes, wenn Sie für die einzelnen Daten- oder Protokollvolumes mehrere physische Datenträger verwenden. Verwenden Sie den Schalter `-I`, um die Stripegröße festzulegen, wenn Sie ein Stripesetvolume erstellen.  
   > Informationen zu empfohlenen Speicherkonfigurationen, einschließlich Stripegrößen und Anzahl der Datenträger, finden Sie unter [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md).  

    ```
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** Erstellen Sie die Bereitstellungsverzeichnisse, und kopieren Sie die UUID aller logischen Volumes:
    ```
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** Erstellen Sie `fstab`-Einträge für die logischen Volumes, und binden Sie Folgendes an:
    ```
    sudo vi /etc/fstab
    ```

   Fügen Sie die folgende Zeile in die Datei `/etc/fstab` ein:

    ```
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Stellen Sie die neuen Volumes bereit:

    ```
    sudo mount -a
    ```

## <a name="installation"></a>Installation  

In diesem Beispiel für die Bereitstellung von SAP HANA in einer Konfiguration mit horizontaler Skalierung mit HSR auf virtuellen Azure-Computern haben wir HANA 2.0 SP4 verwendet.  

### <a name="prepare-for-hana-installation"></a>Vorbereiten der HANA-Installation

1. **[AH]** Legen Sie vor der HANA-Installation das Stammkennwort fest. Nachdem die Installation abgeschlossen wurde, können Sie das Stammkennwort deaktivieren. Führen Sie den Befehl `passwd` als `root` aus.  

2. **[1,2]** Ändern der Berechtigungen für `/hana/shared` 
    ```
    chmod 775 /hana/shared
    ```

3. **[1]** Überprüfen Sie, ob Sie sich über SSH bei den virtuellen HANA DB-Computern an diesem Standort, **hana-s1-db2** und **hana-s1-db3**, anmelden können, ohne nach einem Kennwort gefragt zu werden.  
   Wenn das nicht der Fall ist, tauschen Sie SSH-Schlüssel aus, wie in [Verwenden der schlüsselbasierten Authentifizierung](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs) dokumentiert.  
    ```
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Überprüfen Sie, ob Sie sich über SSH bei den virtuellen HANA DB-Computern an diesem Standort, **hana-s2-db2** und **hana-s2-db3**, anmelden können, ohne nach einem Kennwort gefragt zu werden.  
   Wenn das nicht der Fall ist, tauschen Sie SSH-Schlüssel aus, wie in [Verwenden der schlüsselbasierten Authentifizierung](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs) dokumentiert.  
    ```
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** Installieren Sie zusätzliche Pakete, die für HANA 2.0 SP4 erforderlich sind. Weitere Informationen finden Sie im SAP-Hinweis [2593824](https://launchpad.support.sap.com/#/notes/2593824) für RHEL 7. 

    ```
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** Deaktivieren Sie die Firewall vorübergehend, damit sie die HANA-Installation nicht stört. Sie können sie nach der HANA-Installation wieder aktivieren. 
    ```
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-Installation auf dem ersten Knoten an jedem Standort

1. **[1]** Installieren Sie SAP HANA gemäß den Anweisungen im [SAP HANA 2.0 Installation and Update Guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) (Installations- und Updateleitfaden für SAP HANA 2.0). In den folgenden Anweisungen wird die SAP HANA-Installation auf dem ersten Knoten an STANDORT 1 veranschaulicht.   

   a. Starten Sie das Programm **hdblcm** als `root` über das HANA-Installationssoftwareverzeichnis. Verwenden Sie den `internal_network`-Parameter, und übergeben Sie den Adressraum für das Subnetz, das für die interne Kommunikation zwischen HANA-Knoten verwendet wird.  

    ```
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Aktion auswählen**: Geben Sie **1** ein (für „installieren“).
     * Für **Additional components for installation** (Zusätzliche Komponenten für die Installation): Geben Sie **2, 3** ein.
     * Für den Installationspfad: Drücken Sie die EINGABETASTE (Standardwert „/hana/shared“).
     * Für **Local Host Name** (Name des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Do you want to add hosts to the system?** (Möchten Sie dem System Hosts hinzufügen?): Geben Sie **n** ein.
     * Für **SAP HANA System ID** (SAP HANA-System-ID): Geben Sie **HN1** ein.
     * Für **Instance number** (Instanznummer) [00]: Geben Sie **03** ein.
     * Für **Local Host Worker Group** (Workergruppe des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select System Usage / Enter index [4]** (Systemnutzung auswählen/Index eingeben): Geben Sie **4** (für benutzerdefiniert) ein.
     * Für **Location of Data Volumes** (Speicherort der Datenvolumes) [/Hana/Data/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Location of Log Volumes** (Speicherort der Protokollvolumes) [/Hana/log/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie die maximale Speicherbelegung beschränken?** [n] Geben Sie **n** ein.
     * Für **Certificate Host Name For Host hana-s1-db1** [hana-s1-db1]: (Zertifikathostname für Host „hana-s1-db1“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer): Geben Sie das Kennwort ein.
     * Für **Confirm SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer bestätigen): Geben Sie das Kennwort ein.
     * Für **System Administrator (hn1adm) Password** (Kennwort für den Systemadministrator (hn1adm)): Geben Sie das Kennwort ein.
     * Für **System Administrator Home Directory** (Stammverzeichnis des Systemadministrators) [/usr/sap/HN1/home]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator Login Shell** (Anmelde-Shell für den Systemadministrator) [/bin/sh]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator User ID** (Benutzer-ID des Systemadministrators) [1001]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter ID of User Group (sapsys)** (ID der Benutzergruppe (sapsys) eingeben) [79]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * For **System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system)): Geben Sie das Kennwort für das System ein.
     * For **Confirm System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system) bestätigen): Geben Sie das Kennwort für das System ein.
     * Für **Soll das System nach dem Neustart des Computers neu starten?** [n] Geben Sie **n** ein. 
     * Für **Möchten Sie fortfahren (y/n)?** : Überprüfen Sie die Zusammenfassung. Wenn alle Werte korrekt sind, geben Sie **y** ein.

2. **[2]** Wiederholen Sie den vorhergehenden Schritt, um SAP HANA auf dem ersten Knoten an STANDORT 2 zu installieren.   

3. **[1,2]** Überprüfen Sie die „global.ini“.  

   Zeigen Sie die „global. ini“ an, und stellen Sie sicher, dass die Konfiguration für die interne SAP HANA-Kommunikation zwischen Knoten eingerichtet ist. Überprüfen Sie den Abschnitt **communication**. Dieser sollte den Adressraum für das `inter`-Subnetz enthalten, und `listeninterface` sollte auf `.internal` festgelegt sein. Überprüfen Sie den Abschnitt **internal_hostname_resolution**. Er sollte die IP-Adressen für die virtuellen HANA-Computer enthalten, die zum `inter`-Subnetz gehören.  

   ```
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1,2]** Bereiten Sie `global.ini` für die Installation in einer nicht freigegebenen Umgebung vor, wie in SAP-Hinweis [2080991](https://launchpad.support.sap.com/#/notes/0002080991) beschrieben.  

   ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** Starten Sie SAP HANA neu, um die Änderungen zu aktivieren.  

   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** Stellen Sie sicher, dass die Clientschnittstelle zur Kommunikation die IP-Adressen aus dem `client`-Subnetz verwendet.  

    ```
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Weitere Informationen zum Überprüfen der Konfiguration finden Sie im SAP-Hinweis [2183363 – Konfiguration des internen SAP HANA-Netzwerks](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[AH]** Ändern Sie die Berechtigungen für die Daten- und Protokollverzeichnisse, um einen HANA-Installationsfehler zu vermeiden.  

   ```
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** Installieren Sie die sekundären HANA-Knoten. Die Beispielanweisungen in diesem Schritt beziehen sich auf STANDORT 1.  
   a. Starten Sie das Programm **hdblcm** als `root`.    
    ```
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Aktion auswählen**: Geben Sie **2** ein (für „Hosts installieren“).
     * Für **Enter comma separated host names to add** (Durch Komma getrennte hinzuzufügende Hostnamen eingeben): hana-s1-db2, hana-s1-db3
     * Für **Additional components for installation** (Zusätzliche Komponenten für die Installation): Geben Sie **2, 3** ein.
     * Für **Enter Root User Name [root]** (Root-Benutzername [root] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select roles for host 'hana-s1-db2' [1]** (Rollen für Host 'hana-s1-db2' [1] eingeben): 1 (für Worker)
     * Für **Enter Host Failover Group for host 'hana-s1-db2' [default]** (Hostfailovergruppe für Host 'hana-s1-db2' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Storage Partition Number for host 'hana-s1-db2' [<<assign automatically>>]** (Speicherpartitionsnummer für Host 'hana-s1-db2' eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Worker Group for host 'hana-s1-db2' [default]** (Workergruppe für Host 'hana-s1-db2' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select roles for host 'hana-s1-db3' [1]** (Rollen für Host 'hana-s1-db3' [1] eingeben): 1 (für Worker)
     * Für **Enter Host Failover Group for host 'hana-s1-db3' [default]** (Hostfailovergruppe für Host 'hana-s1-db3' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Storage Partition Number for host 'hana-s1-db3' [<<assign automatically>>]** (Speicherpartitionsnummer für Host 'hana-s1-db3' eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Worker Group for host 'hana-s1-db3' [default]** (Workergruppe für Host 'hana-s1-db3' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator (hn1adm) Password** (Kennwort für den Systemadministrator (hn1adm)): Geben Sie das Kennwort ein.
     * Für **Enter SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer (sapadm) eingeben): Geben Sie das Kennwort ein.
     * Für **Confirm SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer bestätigen): Geben Sie das Kennwort ein.
     * Für **Certificate Host Name For Host hana-s1-db2** [hana-s1-db2]: (Zertifikathostname für Host „hana-s1-db2“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Certificate Host Name For Host hana-s1-db3** [hana-s1-db3]: (Zertifikathostname für Host „hana-s1-db3“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie fortfahren (y/n)?** : Überprüfen Sie die Zusammenfassung. Wenn alle Werte korrekt sind, geben Sie **y** ein.

9. **[2]** Wiederholen Sie den vorhergehenden Schritt, um die sekundären SAP HANA-Knoten auf STANDORT 2 zu installieren.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurieren der SAP HANA 2.0-Systemreplikation

1. **[1]**  Konfigurieren Sie die Systemreplikation auf STANDORT 1.

   Sichern Sie die Datenbanken als **hn1** adm:

    ```
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Kopieren Sie die PKI-Systemdateien auf den sekundären Standort:

    ```
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Erstellen Sie den primären Standort:

    ```
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Konfigurieren Sie die Systemreplikation auf STANDORT 2.
    
   Registrieren Sie den zweiten Standort zum Starten der Replikation. Führen Sie den folgenden Befehl als „<hanasid\>adm“ aus:

    ```
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Überprüfen Sie den Replikationsstatus.

   Überprüfen Sie den Replikationsstatus, und warten Sie, bis alle Datenbanken synchronisiert wurden.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** Ändern Sie die HANA-Konfiguration, sodass die Kommunikation für die HANA-Systemreplikation über die virtuellen Netzwerkschnittstellen der HANA-Systemreplikation geleitet wird.   
   - HANA an beiden Standorten beenden
    ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Bearbeiten Sie „global.ini“, um die Hostzuordnung für die HANA-Systemreplikation hinzuzufügen: Verwenden Sie die IP-Adressen aus dem Subnetz `hsr`.  
    ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - HANA an beiden Standorten starten
   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Weitere Informationen finden Sie unter [Hostnamensauflösung für die Systemreplikation](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[AH]** Aktivieren Sie die Firewall wieder.  
   - Firewall erneut aktivieren
       ```
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Öffnen Sie die erforderlichen Firewallports. Sie müssen die Ports für Ihre HANA-Instanznummer anpassen.  

       > [!IMPORTANT]
       > Erstellen Sie Firewallregeln, um die HANA-Kommunikation zwischen Knoten und Clientdatenverkehr zuzulassen. Die erforderlichen Ports finden Sie unter [TCP/IP-Ports aller SAP-Produkte](https://help.sap.com/viewer/ports). Die folgenden Befehle dienen lediglich als Beispiel. In diesem Szenario wurde die Systemnummer 03 verwendet.

       ```
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) aus, um ein grundlegendes Pacemaker-Cluster für diesen HANA-Server zu erstellen.
Beziehen Sie alle virtuellen Computer ein, einschließlich Majority Maker im Cluster.  

> [!IMPORTANT]
> Legen Sie `quorum expected-votes` nicht auf 2 fest, da es sich nicht um einen Cluster mit zwei Knoten handelt.  
> Stellen Sie sicher, dass die Clustereigenschaft `concurrent-fencing` aktiviert ist, damit das Knotenfencing deserialisiert wird.   

## <a name="create-file-system-resources"></a>Erstellen von Dateisystemressourcen

1. **[1,2]** Beenden Sie SAP HANA an beiden Replikationsstandorten. Führen Sie als <sid\>adm aus.  

    ```
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[AH]** Heben Sie die Einbindung des Dateisystems `/hana/shared` auf, das für die Installation auf allen virtuellen HANA DB-Computern temporär eingebunden wurde. Sie müssen alle Prozesse und Sitzungen, die das Dateisystem verwenden, beenden, bevor Sie die Einbindung des Dateisystems aufheben können. 
 
    ```
    umount /hana/shared 
    ```

3. **[1]** Erstellen Sie die Clusterressourcen des Dateisystems für `/hana/shared` im deaktivierten Zustand. Die Ressourcen werden mit der Option `--disabled` erstellt, da Sie die Standorteinschränkungen definieren müssen, bevor die Einbindungen aktiviert werden.  

    ```
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   Das Attribut `OCF_CHECK_LEVEL=20` wird dem Überwachungsvorgang hinzugefügt, sodass Überwachungsvorgänge einen Lese-/Schreibtest auf dem Dateisystem ausführen. Ohne dieses Attribut überprüft der Überwachungsvorgang nur, ob das Dateisystem eingebunden ist. Dies kann ein Problem sein, denn im Fall eines Konnektivitätsverlusts bleibt das Dateisystem möglicherweise eingebunden, es kann aber nicht darauf zugegriffen werden.  

   Das Attribut `on-fail=fence` wird dem Überwachungsvorgang ebenfalls hinzugefügt. Durch diese Option wird ein Knoten sofort eingegrenzt, wenn beim Überwachungsvorgang für diesen Knoten ein Fehler auftritt. Ohne diese Option sieht das Standardverhalten folgendermaßen aus: Alle Ressourcen, die von der fehlerhaften Ressource abhängig sind, werden beendet. Dann wird die fehlerhafte Ressource neu gestartet, und danach werden alle Ressourcen gestartet, die von der fehlerhaften Ressource abhängig sind. Dieses Verhalten kann nicht nur sehr viel Zeit in Anspruch nehmen, wenn eine SAPHana-Ressource von der fehlerhaften Ressource abhängig ist, es kann auch vollständig fehlschlagen. Die SAPHana-Ressource kann nicht erfolgreich beendet werden, wenn die NFS-Freigabe, auf der sich die HANA-Binärdateien befinden, nicht erreichbar ist.  

4. **[1]** Konfigurieren und überprüfen Sie die Knotenattribute. Allen SAP HANA DB-Knoten am Replikationsstandort 1 wird das Attribut `S1` zugewiesen, und allen SAP HANA DB-Knoten am Replikationsstandort 2 wird das Attribut `S2` zugewiesen.  

    ```
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** Konfigurieren Sie die Einschränkungen, die bestimmen, wo die NFS-Dateisysteme eingebunden werden, und aktivieren Sie die Dateisystemressourcen.  
    ```
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Wenn Sie die Dateisystemressourcen aktivieren, wird der Cluster die `/hana/shared`-Dateisysteme einbinden.
 
6. **[AH]** Überprüfen Sie, ob die ANF-Volumes auf allen virtuellen HANA DB-Computern an beiden Standorten unter `/hana/shared` eingebunden sind.

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** Konfigurieren Sie die Attributressourcen. Konfigurieren Sie die Beschränkungen, die die Attribute auf `true` festlegen, wenn die NFS-Einbindungen für `hana/shared` eingebunden sind.  

    ```
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Wenn Ihre Konfiguration außer /`hana/shared` noch andere Dateisysteme enthält, die über NFS eingebunden sind, geben Sie die Option `sequential=false` an, sodass keine Reihenfolgenabhängigkeiten zwischen den Dateisystemen entstehen. Alle über NFS eingebundenen Dateisysteme müssen vor der entsprechenden Attributressource gestartet werden, aber sie müssen nicht in einer bestimmten Reihenfolge relativ zueinander gestartet werden. Weitere Informationen finden Sie unter [Wie konfiguriere ich SAP HANA-HSR mit horizontaler Skalierung in einem Pacemaker-Cluster, wenn die HANA-Dateisysteme NFS-Freigaben sind?](https://access.redhat.com/solutions/5423971).  

8. **[1]** Versetzen Sie Pacemaker in den Wartungsmodus, als Vorbereitung für die Erstellung der HANA-Clusterressourcen.  
    ```
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Erstellen von SAP HANA-Clusterressourcen

1. **[A]** Installieren Sie den HANA-Ressourcen-Agent für die horizontale Skalierung auf allen Clusterknoten, einschließlich Majority Maker.    

    ```
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Informationen zur minimal unterstützten Version des Pakets `resource-agents-sap-hana-scaleout` für Ihr Betriebssystemrelease finden Sie unter [Supportrichtlinien für RHEL HA-Cluster – Verwaltung von SAP HANA in einem Cluster](https://access.redhat.com/articles/3397471).  

2. **[1,2]** Installieren Sie den „Systemreplikationshook“ für HANA. Der Hook muss auf einem HANA DB-Knoten an jedem Systemreplikationsstandort installiert werden. SAP HANA sollte noch nicht verfügbar sein.        

   1. Bereiten Sie den Hook als `root` vor. 
    ```
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Passen Sie `global.ini` an.
    ```
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[AH]** Der Cluster erfordert sudoers-Konfiguration auf dem Clusterknoten für <sid\>adm. In diesem Beispiel wird dies durch das Erstellen einer neuen Datei erreicht. Führen Sie die Befehle als `root` aus.    
    ``` 
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1,2]** Starten Sie SAP HANA an beiden Replikationsstandorten. Führen Sie als <sid\>adm aus.  

    ```
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** Überprüfen Sie die Installation des Hooks. Nehmen Sie die Ausführung als <sid\>adm auf dem aktiven Replikationsstandort des HANA-Systems vor.   

    ```
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** Erstellen Sie die HANA-Clusterressourcen. Führen Sie die folgenden Befehle als `root` aus.    
   1. Stellen Sie sicher, dass sich der Cluster bereits im Wartungsmodus befindet.  
    
   2. Als nächstes erstellen Sie die HANA-Topologieressource.  
      Verwenden Sie beim Erstellen eines RHEL **7.x**-Clusters die folgenden Befehle:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Verwenden Sie beim Erstellen eines RHEL **8.x**-Clusters die folgenden Befehle:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Als nächstes erstellen Sie die HANA-Instanzressource.  
      > [!NOTE]
      > Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.  
 
      Verwenden Sie beim Erstellen eines RHEL **7.x**-Clusters die folgenden Befehle:    
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Verwenden Sie beim Erstellen eines RHEL **8.x**-Clusters die folgenden Befehle:  
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Es wird als bewährte Methode empfohlen, AUTOMATED_REGISTER nur auf **no** festzulegen, während gründliche Failovertests durchgeführt werden, um zu verhindern, dass sich eine fehlerhafte primäre Instanz automatisch als sekundäre Instanz registriert. Nachdem die Failover-Tests erfolgreich abgeschlossen sind, legen Sie AUTOMATED_REGISTER auf **yes** fest, sodass nach der Übernahme die Systemreplikation automatisch wieder aufgenommen werden kann. 

   4. Erstellen Sie die virtuelle IP-Adresse und zugehörige Ressourcen.  
      ```
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Erstellen der Clustereinschränkungen  
      Verwenden Sie beim Erstellen eines RHEL **7.x**-Clusters die folgenden Befehle:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Verwenden Sie beim Erstellen eines RHEL **8.x**-Clusters die folgenden Befehle:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** Beenden Sie für den Cluster den Wartungsmodus. Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind.  
    ```
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische HANA-Setup angepasst werden. Beispielsweise müssen Sie ggf. das Starttimeout erhöhen, wenn es länger dauert, bis die SAP HANA-Datenbank gestartet wird.
  
## <a name="test-sap-hana-failover"></a>Testen des SAP HANA-Failovers 

1. Bevor Sie einen Test starten, überprüfen Sie den Cluster und den Replikationsstatus des SAP HANA-Systems.  

   a. Überprüfen Sie, ob es keine fehlerhaften Clusteraktionen gibt.  
     ```
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Überprüfen Sie, ob die SAP HANA-Systemreplikation synchronisiert ist.

      ```
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Überprüfen Sie die Clusterkonfiguration auf ein Fehlerszenario, bei dem ein Knoten den Zugriff auf die NFS-Freigabe (`/hana/shared`) verliert.  

   Die SAP HANA-Ressourcen-Agents benötigen in `/hana/shared` gespeicherte Binärdateien, um während eines Failovers Vorgänge auszuführen. Das Dateisystem `/hana/shared` ist in der dargestellten Konfiguration über NFS eingebunden. Ein Test könnte darin bestehen, das `/hana/shared`-Dateisystem als *schreibgeschützt* erneut einzubinden. Auf diese Weise lässt sich überprüfen, ob der Cluster ein Failover ausführen wird, wenn der Zugriff auf `/hana/shared` am aktiven Systemreplikationsstandort verloren geht.  

   **Erwartetes Ergebnis**: Wenn Sie `/hana/shared` erneut als *Schreibgeschützt* einbinden, tritt beim Überwachungsvorgang, die Lese-/Schreibvorgänge für das Dateisystem durchführt, ein Fehler auf, da sie nicht in der Lage ist, in das Dateisystem zu schreiben, und ein HANA-Ressourcenfailover auslöst. Dasselbe Ergebnis ist zu erwarten, wenn der HANA-Knoten den Zugriff auf die NFS-Freigabe verliert.  
     
   Sie können den Zustand der Clusterressourcen prüfen, indem Sie `crm_mon` oder `pcs status` ausführen. Zustand der Ressource vor dem Starten des Tests:
      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Führen Sie den folgenden Befehl aus, um einen Fehler für `/hana/shared` auf einem der virtuellen Computer des primären Replikationsstandorts zu simulieren:
      ```
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   Der virtuelle HANA-Computer, der den Zugriff auf `/hana/shared` verloren hat, sollte je nach Clusterkonfiguration neu gestartet oder beendet werden. Die Clusterressourcen werden zum anderen Replikationsstandort des HANA-Systems migriert.  
         
   Wenn der Cluster auf dem neu gestarteten virtuellen Computer noch nicht gestartet ist, starten Sie den Cluster, indem Sie Folgendes ausführen: 

      ```
      # Start the cluster 
      pcs cluster start
      ```
   
   Wenn der Cluster startet, wird das Dateisystem `/hana/shared` automatisch eingebunden.     
   Wenn Sie AUTOMATED_REGISTER="false" festlegen, müssen Sie die SAP HANA-Systemreplikation am sekundären Standort konfigurieren. In diesem Fall können Sie diese Befehle ausführen, um SAP HANA als sekundär neu zu konfigurieren.   

      ```
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Der Zustand der Ressourcen nach dem Test: 

      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Es wird empfohlen, die SAP HANA-Clusterkonfiguration gründlich zu testen, indem auch die in [HA für SAP HANA auf Azure VMs unter RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup) dokumentierten Tests durchgeführt werden.


## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].