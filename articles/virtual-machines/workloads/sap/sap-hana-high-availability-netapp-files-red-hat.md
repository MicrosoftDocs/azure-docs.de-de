---
title: Hochverfügbarkeit bei hochskalierten SAP HANA-Lösungen mit ANF unter RHEL | Microsoft-Dokumentation
description: Richten Sie die Hochverfügbarkeit von SAP HANA mit ANF auf virtuellen Azure-Computern (VMs) ein.
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
ms.openlocfilehash: cfa68bde2462cefd6f690247cfd1e3bd2e3dbc74
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489206"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Hochverfügbarkeit bei hochskalierten SAP HANA-Lösungen mit Azure NetApp Files unter Red Hat Enterprise Linux

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

In diesem Artikel wird beschrieben, wie Sie die SAP HANA-Systemreplikation in einer hochskalierten Bereitstellung mithilfe von Azure NetApp Files (ANF) konfigurieren, wenn die HANA-Dateisysteme über NFS eingebunden sind. In den Beispielkonfigurationen und Installationsbefehlen werden die Instanznummer **03** und die HANA-System-ID **HN1** verwendet. Die SAP HANA-Replikation umfasst primären Knoten und mindestens einen sekundären Knoten.

Einige Schritte in diesem Dokument sind mit Präfixen gekennzeichnet. Diese bedeuten Folgendes:

- **[A]** : Der Schritt gilt für alle Knoten.
- **[1]** : Der Schritt gilt nur für Knoten 1.
- **[2]** : Der Schritt gilt nur für Knoten 2.
 
Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

- SAP-Hinweis [1928533](https://launchpad.support.sap.com/#/notes/1928533) mit folgenden Informationen:
    - Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
    - Wichtige Kapazitätsinformationen für Azure-VM-Größen
    - Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
    - Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
- In SAP-Hinweis [2015553](https://launchpad.support.sap.com/#/notes/2015553) sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
- Im SAP-Hinweis [405827](https://launchpad.support.sap.com/#/notes/405827) sind empfohlene Dateisysteme für die HANA-Umgebung aufgeführt.
- SAP-Hinweis [2002167](https://launchpad.support.sap.com/#/notes/2002167) enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux.
- SAP-Hinweis [2009879](https://launchpad.support.sap.com/#/notes/2009879) enthält SAP HANA-Richtlinien für Red Hat Enterprise Linux.
- SAP-Hinweis [2178632](https://launchpad.support.sap.com/#/notes/2178632) enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
- SAP-Hinweis [2191498](https://launchpad.support.sap.com/#/notes/2191498) enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
- SAP-Hinweis [2243692](https://launchpad.support.sap.com/#/notes/2243692) enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
- SAP-Hinweis [1999351](https://launchpad.support.sap.com/#/notes/1999351) enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
- Das [SAP-Community-Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
- [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
- [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
- [SAP HANA-Systemreplikation im Pacemaker-Cluster](https://access.redhat.com/articles/3004101)
- Allgemeine RHEL-Dokumentation:
    - [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Konfigurieren der SAP HANA-Systemreplikation in einer hochskalierten Lösung in einem Pacemaker-Cluster, wenn sich die HANA-Dateisysteme auf NFS-Freigaben befinden](https://access.redhat.com/solutions/5156571)
- Azure-spezifische RHEL-Dokumentation:
    - [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster: Microsoft Azure Virtual Machines als Clustermitglieder](https://access.redhat.com/articles/3131341)
    - [Installieren und Konfigurieren eines Hochverfügbarkeitsclusters unter Red Hat Enterprise Linux 7.4 (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)
    - [Installieren von SAP HANA unter Red Hat Enterprise Linux für die Verwendung in Microsoft Azure](https://access.redhat.com/solutions/3193782)
    - [Konfigurieren der SAP HANA-Systemreplikation in hochskalierten Umgebungen in einem Pacemaker-Cluster, wenn sich die HANA-Dateisysteme auf NFS-Freigaben befinden](https://access.redhat.com/solutions/5156571)
- [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>Übersicht

In hochskalierten Umgebungen werden traditionellerweise alle Dateisysteme für SAP HANA aus dem lokalen Speicher eingebunden. Informationen zum Einrichten der Hochverfügbarkeit der SAP HANA-Systemreplikation unter Red Hat Enterprise Linux wurden im Leitfaden [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md) veröffentlicht.

Um SAP HANA-Hochverfügbarkeit in hochskalierten Systemen auf [Azure NetApp Files](../../../azure-netapp-files/index.yml)-NFS-Freigaben zu erreichen, sind einige weitere Ressourcenkonfigurationen im Cluster erforderlich, damit HANA-Ressourcen wiederhergestellt werden können, wenn der Zugriff auf die NFS-Freigaben in ANF unterbrochen wurde.  Der Cluster verwaltet die NFS-Bereitstellungen und kann daher die Integrität der Ressourcen überwachen. Zwischen den Dateisystembereitstellungen und den SAP HANA-Ressourcen werden Abhängigkeiten erzwungen.  

![SAP HANA: Hochskalieren für Hochverfügbarkeit in ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA-Dateisysteme werden mithilfe von Azure NetApp Files in NFS-Freigaben auf jedem Knoten eingebunden. Die Dateisystempfade „/hana/data“, „/hana/log“ und „/hana/shared“ sind für jeden Knoten eindeutig. 

Eingebunden auf node1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 in /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 in /hana/log
- 10.32.2.4:/**hanadb1**-shared-mnt00001 in /hana/shared

Eingebunden auf node2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 in /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 in /hana/log
- 10.32.2.4:/**hanadb2**-shared-mnt00001 in /hana/shared

> [!NOTE]
> Die Dateisystempfade „/hana/data“, „/hana/log“ und „/hana/shared“ werden nicht durch die beiden Knoten gemeinsam genutzt. Jeder Clusterknoten besitzt eigene, separate Dateisysteme.   

Die Konfiguration der SAP HANA-Systemreplikation verwendet einen dedizierten virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein Lastenausgleich erforderlich. Die folgende Liste zeigt die Konfiguration des Lastenausgleichs:

- Front-End-Konfiguration: IP-Adresse 10.32.0.10 für „hn1-db“
- Back-End-Konfiguration: Mit primären Netzwerkschnittstellen von allen virtuellen Computern verbunden, die Teil der HANA-Systemreplikation sein sollen.
- Testport: Port 62503
- Lastenausgleichsregeln: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (bei Verwendung von Azure Load Balancer Basic)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Einrichten der Infrastruktur für Azure NetApp Files

Bevor Sie mit der Einrichtung der Azure NetApp Files-Infrastruktur beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation](../../../azure-netapp-files/index.yml) vertraut machen.

Azure NetApp Files ist in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird.

Informationen zur Verfügbarkeit von Azure NetApp Files in den einzelnen Azure-Regionen finden Sie unter [Verfügbarkeit von Azure NetApp Files nach Azure-Region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Fordern Sie Onboarding für Azure NetApp Files an, bevor Sie Azure NetApp Files bereitstellen. Wechseln Sie dazu zu den [Registrierungsanweisungen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.

1. Wenn Sie die Ressourcen noch nicht bereitgestellt haben, fordern Sie [Onboarding für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) an.

2. Erstellen Sie entsprechend den Anweisungen in [Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) ein NetApp-Konto in der ausgewählten Azure-Region.

3.  Richten Sie entsprechend den Anweisungen in [Einrichten eines Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) einen Azure NetApp Files-Kapazitätspool ein.

    Die in diesem Artikel vorgestellte HANA-Architektur verwendet einen einzigen Azure NetApp Files-Kapazitätspool auf der Dienstebene *Ultra*. Für HANA-Workloads in Azure empfehlen wir die [Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) *Ultra* oder *Premium* für Azure NetApp Files.

4.  Delegieren Sie ein Subnetz an Azure NetApp Files, wie in den Anweisungen in [Delegieren eines Subnetzes an Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) beschrieben.

5.  Stellen Sie Azure NetApp Files-Volumes entsprechend den Anweisungen in [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit.

    Wählen Sie beim Bereitstellen der Volumes unbedingt Version NFSv4.1 aus. Stellen Sie die Volumes im festgelegten Subnetz für Azure NetApp Files bereit. Die IP-Adressen der Azure NetApp-Volumes werden automatisch zugewiesen.

    Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. Beispielsweise sind „hanadb1-data-mnt00001“, „hanadb1-log-mnt00001“ usw. die Volumenamen und „nfs://10.32.2.4/hanadb1-data-mnt00001“, „nfs://10.32.2.4/hanadb1-log-mnt00001“ usw. die Dateipfade für die Azure NetApp Files-Volumes.
    
    Auf **hanadb1**

    - Volume „hanadb1-data-mnt00001“ (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume „hanadb1-log-mnt00001“ (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume „hanadb1-shared-mnt00001“ (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Auf **hanadb2**

    - Volume „hanadb2-data-mnt00001“ (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume „hanadb2-log-mnt00001“ (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume „hanadb2-shared-mnt00001“ (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Wichtige Hinweise

Beachten Sie beim Erstellen der Azure NetApp Files für Ihre hochskalierten SAP HANA-Systeme die folgenden Überlegungen:

- Die Mindestgröße eines Kapazitätspools beträgt 4 TiB (Tebibyte).
- Die Mindestvolumegröße ist 100 GiB (Gibibyte).
- Azure NetApp Files und alle virtuellen Computer, auf denen die Azure NetApp Files-Volumes eingebunden werden sollen, müssen sich in demselben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden.
- Das ausgewählte virtuelle Netzwerk muss ein Subnetz besitzen, das an Azure NetApp Files delegiert wurde.
- Der Durchsatz eines Azure NetApp-Volumes ist eine Funktion des Volumekontingents und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben. Stellen Sie bei der Dimensionierung der HANA-Azure NetApp-Volumes sicher, dass der resultierende Durchsatz die HANA-Systemanforderungen erfüllt.
- Mit den [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) von Azure NetApp Files können Sie die zulässigen Clients und den Zugriffstyp (Lese- und Schreibzugriff, schreibgeschützt usw.) steuern.
- Das Azure NetApp Files-Feature wertet derzeit noch keine Zonen aus. Das Feature ist noch nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen.

> [!IMPORTANT]
> Bei SAP HANA-Workloads ist eine niedrige Latenz sehr wichtig. Arbeiten Sie mit Ihrem Microsoft-Vertreter zusammen, um sicherzustellen, dass die virtuellen Computer und die Azure NetApp Files-Volumes nahe beieinander bereitgestellt werden.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Dimensionierung einer HANA-Datenbank in Azure NetApp Files

Der Durchsatz eines Azure NetApp Files-Volumes ist eine Funktion der Volumegröße und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben.

Beachten Sie beim Entwerfen der Infrastruktur für SAP in Azure einige Mindestanforderungen von SAP an den Speicher, aus denen sich die Mindestdurchsatzeigenschaften ergeben:

- Lese-/Schreibzugriff auf „/hana/log“ für 250 Megabyte pro Sekunde (MB/s) mit einer E/A-Größe von 1 MB.
- Leseaktivität mit mindestens 400 MB/s in „/hana/data“ für E/A-Größen von 16 MB und 64 MB.
- Schreibaktivität mit mindestens 250 MB/s in „/hana/data“ für E/A-Größen von 16 MB und 64 MB.

Die [Azure NetApp Files-Durchsatzlimits](../../../azure-netapp-files/azure-netapp-files-service-levels.md) pro 1 TiB an Volumekontingent lauten:

- Storage Premium-Tarif: 64 MiB/s
- Storage Ultra-Tarif: 128 MiB/s

Zur Erfüllung der SAP-Mindestanforderungen an den Durchsatz für „/hana/data“ und „/hana/log“ sowie der Richtlinien für „hana/shared“ werden folgende Größen empfohlen:

|    Volume    | Größe Storage Premium-Tarif | Größe Storage Ultra-Tarif | Unterstütztes NFS-Protokoll |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 × RAM            |          1 × RAM           |          v3 oder v4.1    |


> [!NOTE]
> Die hier angegebenen Empfehlungen für die Azure NetApp Files-Größenanpassung zielen darauf ab, die Mindestanforderungen zu erfüllen, die SAP seinen Infrastrukturanbietern empfiehlt. In realen Kundenbereitstellungen und Workloadszenarien sind diese Größen möglicherweise nicht ausreichend. Verwenden Sie diese Empfehlungen also als Ausgangspunkt, und nehmen Sie Anpassungen auf Grundlage der Anforderungen der spezifischen Workload vor.

> [!TIP]
> Sie können die Größe der Azure NetApp Files-Volumes dynamisch anpassen, ohne *die Bereitstellung der Volumes aufheben* oder die virtuellen Computer oder SAP HANA beenden zu müssen. Mit diesem Ansatz können Sie sowohl den erwarteten als auch unvorhergesehenen Durchsatzanforderungen flexibel gerecht werden.

> [!NOTE]
> Alle Befehle zum Einbinden von „/hana/shared“ in diesem Artikel gelten für /hana/shared-Volumes in NFSv4.1.
> Wenn Sie die /hana/shared-Volumes als NFSv3-Volumes bereitgestellt haben, vergessen Sie nicht, die Einbindebefehle für „/hana/shared“ auf NFSv3 anzupassen.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal 

Zuerst müssen Sie die Azure NetApp Files-Volumes erstellen. Führen Sie dann die folgenden Schritte aus:

1.  Erstellen Sie eine Ressourcengruppe.
2.  Erstellen Sie ein virtuelles Netzwerk.
3.  Erstellen Sie eine Verfügbarkeitsgruppe. Richten Sie die maximale Updatedomäne ein.
4.  Erstellen Sie einen Lastenausgleich (intern). Sie sollten Load Balancer Standard verwenden.
    Wählen Sie das virtuelle Netzwerk aus, das Sie in Schritt 2 erstellt haben.
5.  Erstellen Sie den virtuellen Computer 1 (**hanadb1**). 
6.  Erstellen Sie den virtuellen Computer 2 (**hanadb2**).  
7.  Beim Erstellen des virtuellen Computers werden keine Datenträger hinzugefügt, weil sich sämtliche Bereitstellungspunkt auf NFS-Freigaben von Azure NetApp Files befinden. 

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarios mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.    

> [!NOTE] 
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).

8.  Führen Sie bei Verwendung von Load Balancer Standard die folgenden Konfigurationsschritte aus:
    1.  Erstellen Sie zunächst einen Front-End-IP-Pool:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
        1.  Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse ein (z. B. **10.32.0.10**).
        1.  Klicken Sie auf **OK**.
        1.  Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.
    1.  Erstellen Sie als Nächstes einen Back-End-Pool:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
        1.  Wählen Sie **Virtuellen Computer hinzufügen** aus.
        1.  Wählen Sie **Virtueller Computer** aus.
        1.  Wählen Sie die virtuellen Computer des SAP HANA-Clusters und deren IP-Adressen aus.
        1.  Wählen Sie **Hinzufügen**.
    1.  Erstellen Sie als Nächstes einen Integritätstest:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Integritätstests ein (z.B. **hana-hp**).
        1.  Wählen Sie als Protokoll TCP und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
        1.  Klicken Sie auf **OK**.
    1.  Erstellen Sie als Nächstes die Lastenausgleichsregeln:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **hana-lb**).
        1.  Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **hana-frontend**, **hana-backend** und **hana-hp**).
        1.  Wählen Sie **HA-Ports** aus.
        1.  Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
        1.  Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
        1.  Klicken Sie auf **OK**.


9. Wenn Ihr Szenario die Verwendung von Load Balancer Basic vorschreibt, führen Sie stattdessen die folgenden Konfigurationsschritte aus:
    1.  Konfigurieren Sie den Lastenausgleich. Erstellen Sie zunächst einen Front-End-IP-Pool:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
        1.  Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse ein (z. B. **10.32.0.10**).
        1.  Klicken Sie auf **OK**.
        1.  Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.
    1.  Erstellen Sie als Nächstes einen Back-End-Pool:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
        1.  Wählen Sie **Virtuellen Computer hinzufügen** aus.
        1.  Wählen Sie die Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben.
        1.  Wählen Sie die virtuellen Computer des SAP HANA-Clusters aus.
        1.  Klicken Sie auf **OK**.
    1.  Erstellen Sie als Nächstes einen Integritätstest:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen des neuen Integritätstests ein (z.B. **hana-hp**).
        1.  Wählen Sie als Protokoll **TCP** und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
        1.  Klicken Sie auf **OK**.
    1.  Erstellen Sie die Lastenausgleichsregeln für SAP HANA 1.0:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 15“).
        1.  Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**), aus.
        1.  Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 15 ein.
        1.  Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
        1.  Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
        1.  Klicken Sie auf **OK**.
        1.  Wiederholen Sie diese Schritte für den Port 3 **03** 17.
    1.  Erstellen Sie für SAP HANA 2.0 Lastenausgleichsregeln für die Systemdatenbank:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 13“).
        1.  Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**), aus.
        1.  Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 13 ein.
        1.  Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
        1.  Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
        1.  Klicken Sie auf **OK**.
        1.  Wiederholen Sie diese Schritte für den Port 3 **03** 14.
    1.  Erstellen Sie für SAP HANA 2.0 Lastenausgleichsregeln für die Mandantendatenbank:
        1.  Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
        1.  Geben Sie den Namen der neuen Lastenausgleichsregel ein (z.B. „hana-lb-3 **03** 40“).
        1.  Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest, die Sie zuvor erstellt haben (z.B. **hana-frontend**) aus.
        1.  Behalten Sie als **Protokoll** den Wert **TCP** bei, und geben Sie als Port 3 **03** 40 ein.
        1.  Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.
        1.  Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
        1.  Klicken Sie auf **OK**.
        1.  Wiederholen Sie diese Schritte für die Ports 3 **03** 41 und 3 **03** 42.

Weitere Informationen zu den erforderlichen Ports für SAP HANA finden Sie im Kapitel zu [Verbindungen mit Mandantendatenbanken](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) im Handbuch zu [SAP HANA-Mandantendatenbanken](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) oder im SAP-Hinweis [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md). Siehe auch SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Einbinden des Azure NetApp Files-Volumes

1. **[A]** Erstellen Sie Bereitstellungspunkte für die HANA-Datenbankvolumes. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **defaultv4iddomain.com**) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Stellen Sie sicher, dass die NFS-Domäne in „/etc/idmapd.conf“ auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **defaultv4iddomain.com**. Im Fall eines Konflikts zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als „nobody“ angezeigt.
    

3. **[1]** Binden Sie die knotenspezifischen Volumes auf „node1“ ein (**hanadb1**). 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Binden Sie die knotenspezifischen Volumes auf „node2“ ein (**hanadb2**).
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Überprüfen Sie, ob alle HANA-Volumes mit der NFS-Protokollversion „NFSv4“ eingebunden wurden.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** Überprüfen Sie **nfs4_disable_idmapping**. Diese Angabe sollte auf **Y** (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um im Speicherort von **nfs4_disable_idmapping** die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Weitere Informationen zum Ändern des Parameters **nfs_disable_idmapping** finden Sie unter [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883). 


## <a name="sap-hana-installation"></a>SAP HANA-Installation

1. **[A]** Richten Sie die Hostnamensauflösung für alle Hosts ein.

   Sie können entweder einen DNS-Server verwenden oder die Datei „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der Datei „/etc/hosts“ veranschaulicht. Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** Konfigurieren Sie RHEL für HANA.

   Konfigurieren Sie RHEL basierend auf Ihrer RHEL-Version, wie im unten stehenden SAP-Hinweis beschrieben.

   - [2292690 – SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 7)
   - [2777782 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
   - [2593824 – Linux: Ausführen von mit GCC 7.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Ausführen von mit GCC 9.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Installieren Sie SAP HANA

   Ab HANA 2.0 SPS 01 ist MDC die Standardoption. Wenn Sie das HANA-System installieren, werden SYSTEMDB und ein Mandant mit derselben SID erstellt. In einigen Fällen möchten Sie vielleicht nicht den Standardmandaten verwenden. Wenn Sie den anfänglichen Mandanten nicht zusammen mit der Installation erstellen möchten, befolgen Sie SAP-Hinweis [2629711](https://launchpad.support.sap.com/#/notes/2629711).

    Führen Sie das Programm **hdblcm** von der HANA-DVD aus. Geben Sie an der Eingabeaufforderung folgende Werte ein:  
    Wählen Sie die Installation aus: Geben Sie **1** ein (für die Installation).  
    Wählen Sie weitere Komponenten für die Installation aus: Geben Sie **1** ein.  
    Enter Installation Path [/hana/shared]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Geben Sie den Namen des lokalen Hosts ein [..]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Möchten Sie dem System weitere Hosts hinzufügen? (y/n) [n]: **n**  
    Geben Sie die SAP HANA-System-ID ein: Geben Sie **HN1** ein.  
    Geben Sie die Instanznummer ein [00]: Geben Sie **03** ein.  
    Select Database Mode / Enter Index [1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Select System Usage / Enter Index [4]: Geben Sie **4** (für „custom“) ein.  
    Enter Location of Data Volumes [/hana/data]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Enter Location of Log Volumes [/hana/log]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Möchten Sie die maximale Speicherbelegung beschränken? [n]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Enter Certificate Host Name For Host '...' [...]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Geben Sie das Kennwort für den SAP-Host-Agent-Benutzer ein (sapadm): Geben Sie das Kennwort für den Host-Agent-Benutzer ein.  
    Bestätigen Sie das Kennwort für den SAP-Host-Agent-Benutzer (sapadm): Geben Sie das Kennwort für den Host-Agent-Benutzer erneut ein, um es zu bestätigen.  
    Geben Sie das Kennwort für den Systemadministrator ein (hn1adm): Geben Sie das Systemadministratorkennwort ein.  
    Confirm System Administrator (hn1adm) Password: Geben Sie das Systemadministratorkennwort erneut ein, um es zu bestätigen.  
    Enter System Administrator Home Directory [/usr/sap/HN1/home]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Enter System Administrator Login Shell [/bin/sh]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Enter System Administrator User ID [1001]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Enter ID of User Group (sapsys) [79]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Geben Sie das Datenbankbenutzer-Kennwort ein (SYSTEM): Geben Sie das Kennwort für den Datenbankbenutzer ein.  
    Bestätigen Sie das Datenbankbenutzer-Kennwort (SYSTEM): Geben Sie das Kennwort für den Datenbankbenutzer erneut ein, um es zu bestätigen.  
    Soll das System nach dem Neustart des Computers neu starten? [n]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.  
    Möchten Sie fortfahren? (j/n): Überprüfen Sie die Zusammenfassung. Geben Sie **y** ein, um fortzufahren.  

4. **[A]** Führen Sie ein Upgrade für den SAP-Host-Agent durch.

   Laden Sie das aktuelle SAP-Host-Agent-Archiv vom [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) herunter, und führen Sie den folgenden Befehl zum Aktualisieren des Agents aus. Ersetzen Sie den Pfad zum Archiv, um auf die Datei zu verweisen, die Sie heruntergeladen haben:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** Konfigurieren Sie die Firewall.

   Erstellen Sie die Firewallregel für den Testport des Azure Load Balancers.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurieren der SAP HANA-Systemreplikation

Führen Sie die Schritte zum [Einrichten der SAP HANA-Systemreplikation](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) aus, um die SAP HANA-Systemreplikation zu konfigurieren. 

## <a name="cluster-configuration"></a>Clusterkonfiguration

In diesem Abschnitt werden die Schritte beschrieben, die für einen reibungslosen Clusterbetrieb erforderlich sind, wenn SAP HANA mithilfe von Azure NetApp Files auf NFS-Freigaben installiert ist. 

### <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](./high-availability-guide-rhel-pacemaker.md) aus, um einen einfachen Pacemaker-Cluster für diesen HANA-Server zu erstellen.

### <a name="configure-filesystem-resources"></a>Konfigurieren von Dateisystemressourcen

In diesem Beispiel verfügt jeder Clusterknoten über ein eigenes HANA-NFS-Dateisystem: „/hana/shared“, „/hana/data“ und „/hana/log“.   

1. **[1]** Versetzen Sie den Cluster in den Wartungsmodus.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Erstellen Sie die Dateisystemressourcen für die **hanadb1**-Bereitstellungen.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Erstellen Sie die Dateisystemressourcen für die **hanadb2**-Bereitstellungen.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    Das Attribut `OCF_CHECK_LEVEL=20` wird dem Überwachungsvorgang hinzugefügt, sodass jeder Monitor einen Lese-/Schreibtest auf dem Dateisystem ausführt. Ohne dieses Attribut überprüft der Überwachungsvorgang nur, ob das Dateisystem eingebunden ist. Dies kann ein Problem sein, denn im Fall eines Konnektivitätsverlusts bleibt das Dateisystem möglicherweise eingebunden, es kann aber nicht darauf zugegriffen werden.

    Das Attribut `on-fail=fence` wird dem Überwachungsvorgang ebenfalls hinzugefügt. Durch diese Option wird ein Knoten sofort eingegrenzt, wenn beim Überwachungsvorgang für diesen Knoten ein Fehler auftritt. Ohne diese Option sieht das Standardverhalten folgendermaßen aus: Alle Ressourcen, die von der fehlerhaften Ressource abhängig sind, werden beendet. Dann wird die fehlerhafte Ressource neu gestartet, und danach werden alle Ressourcen gestartet, die von der fehlerhaften Ressource abhängig sind. Dieses Verhalten kann nicht nur sehr viel Zeit in Anspruch nehmen, wenn eine SAPHana-Ressource von der fehlerhaften Ressource abhängig ist, es kann auch vollständig fehlschlagen. Die SAPHana-Ressource kann nicht erfolgreich beendet werden, wenn der NFS-Server, auf dem sich die ausführbaren HANA-Dateien befinden, nicht erreichbar ist.

4. **[1]** Konfigurieren von Speicherorteinschränkungen

   Konfigurieren Sie Speicherorteinschränkungen, um sicherzustellen, dass die Ressourcen, die eindeutige hanadb1-Bereitstellungen verwalten, niemals auf hanadb2 ausgeführt werden können und umgekehrt.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    Die Option `resource-discovery=never` wird festgelegt, weil die eindeutigen Bereitstellungen für jeden Knoten gemeinsam denselben Bereitstellungspunkt nutzen. Beispielsweise verwendet `hana_data1` den Bereitstellungspunkt `/hana/data`, und `hana_data2` verwendet ebenfalls den Bereitstellungspunkt `/hana/data`. Das kann zu falsch positiven Ergebnissen bei Testvorgängen führen, wenn der Ressourcenzustand beim Start des Clusters geprüft wird. Dies wiederum kann unnötiges Wiederherstellungsverhalten verursachen. Dies lässt sich durch Festlegen von `resource-discovery=never` vermeiden.

5. **[1]** Konfigurieren von Attributressourcen

   Konfigurieren Sie Attributressourcen. Diese Attribute werden auf TRUE festgelegt, wenn alle NFS-Bereitstellungen eines Knotens („/hana/data“, „/hana/log“ und „/hana/data“) eingebunden sind. Andernfalls werden sie auf FALSE festgelegt.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Konfigurieren von Speicherorteinschränkungen

   Konfigurieren Sie Speicherorteinschränkungen, um sicherzustellen, dass die Attributressourcen von hanadb1 niemals auf hanadb2 ausgeführt werden und umgekehrt.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** Erstellen von Reihenfolgeneinschränkungen

   Erstellen Sie Reihenfolgeneinschränkungen, damit die Attributressourcen eines Knotens erst dann gestartet werden, wenn alle NFS-Bereitstellungen des Knotens eingebunden sind.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Wenn Ihre Konfiguration außerhalb der Gruppe `hanadb1_nfs` oder der Gruppe `hanadb2_nfs` Dateisysteme enthält, geben Sie die Option `sequential=false` an, sodass keine Reihenfolgenabhängigkeiten zwischen den Dateisystemen entstehen. Alle Dateisysteme müssen vor `hana_nfs1_active` starten, eine bestimmte Reihenfolge untereinander ist aber nicht erforderlich. Weitere Informationen finden Sie unter [Konfigurieren der SAP HANA-Systemreplikation in hochskalierten Umgebungen in einem Pacemaker-Cluster, wenn sich die HANA-Dateisysteme auf NFS-Freigaben befinden](https://access.redhat.com/solutions/5156571).

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurieren von SAP HANA-Clusterressourcen

1. Führen Sie die Schritte unter [Erstellen von SAP HANA-Clusterressourcen](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) aus, um die SAP HANA-Ressourcen im Cluster zu erstellen. Nachdem die SAP HANA-Ressourcen erstellt wurden, müssen Regeln für Speicherorteinschränkungen zwischen den SAP HANA-Ressourcen und den Dateisystemen (NFS-Bereitstellungen) erstellt werden.

2. **[1]** Konfigurieren Sie Einschränkungen zwischen den SAP HANA-Ressourcen und den NFS-Bereitstellungen.

   Regeln für Speicherorteinschränkungen werden so festgelegt, dass die SAP HANA-Ressourcen erst dann auf einem Knoten ausgeführt werden können, wenn alle NFS-Bereitstellungen des Knotens eingebunden wurden.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Überprüfen Sie den Status des Clusters und sämtlicher Ressourcen.
   > [!NOTE]
   > Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

In diesem Abschnitt wird beschrieben, wie Sie Ihre Einrichtung testen können. 

1. Bevor Sie den Test starten, stellen Sie sicher, dass Pacemaker keine fehlerhaften Aktionen enthält (mit „pcs status“), dass keine unerwarteten Speicherorteinschränkungen bestehen (z. B. durch Reste eines Migrationstests) und dass die HANA-Systemreplikation sich in synchronem Zustand befindet (z. B. mit „systemReplicationStatus“):

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Überprüfen Sie die Clusterkonfiguration auf ein Fehlerszenario, bei dem ein Knoten den Zugriff auf die NFS-Freigabe (/hana/shared) verliert.

   Die SAP HANA-Ressourcen-Agents benötigen in `/hana/shared` gespeicherte Binärdateien, um während eines Failovers Vorgänge auszuführen. Das Dateisystem `/hana/shared` ist in diesem Szenario über NFS eingebunden.  
   Es ist schwierig, einen Fehler zu simulieren, bei dem einer der Server den Zugriff auf die NFS-Freigabe verliert. Ein Test könnte darin bestehen, das Dateisystem schreibgeschützt erneut einzubinden.
   Auf diese Weise lässt sich überprüfen, ob der Cluster ein Failover ausführen kann, wenn der Zugriff auf `/hana/shared` auf dem aktiven Knoten verloren geht.     


   **Erwartetes Ergebnis**: Wenn `/hana/shared` als schreibgeschütztes Dateisystem festgelegt wird, tritt beim `OCF_CHECK_LEVEL`-Attribut der Ressource `hana_shared1`, das Lese-/Schreibvorgänge auf dem Dateisystem ausführt, ein Fehler auf: Auf dem Dateisystem kann nicht geschrieben werden, daher wird ein Failover der HANA-Ressource ausgeführt.  Dasselbe Ergebnis ist zu erwarten, wenn der HANA-Knoten den Zugriff auf die NFS-Freigaben verliert. 

   Zustand der Ressource vor dem Starten des Tests:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Sie können „/hana/shared“ mit dem folgenden Befehl auf dem aktiven Clusterknoten in den schreibgeschützten Modus versetzen:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   Der Server „hanadb1“ wird je nach der Aktion, die in „stonith“ (`pcs property show stonith-action`) festgelegt ist, neu gestartet oder ausgeschaltet.  Sobald „hanadb1“ heruntergefahren wurde, wechselt die HANA-Ressource zu „hanadb2“. Sie können den Status des Clusters von „hanadb2“ überprüfen.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Es wird empfohlen, die SAP HANA-Clusterkonfiguration gründlich zu testen, indem Sie auch die unter [Einrichten der SAP HANA-Systemreplikation unter RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup) beschriebenen Tests ausführen.