---
title: Horizontale SAP HANA-Skalierung mit Standby mit Azure NetApp Files unter RHEL | Microsoft-Dokumentation
description: Hochverfügbarkeitsleitfaden für SAP NetWeaver unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen
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
ms.date: 06/15/2020
ms.author: radeltch
ms.openlocfilehash: d2cc8487f9864a27c1a2b02ef6e846bc43727e27
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608536"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files auf Red Hat Enterprise Linux 

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


Dieser Artikel beschreibt die Bereitstellung eines hochverfügbaren SAP HANA-Systems in einer Konfiguration mit horizontaler Skalierung mit Standby auf virtuellen Azure Red Hat Enterprise Linux-Computern (VMs) mithilfe von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) für die freigegebenen Speichervolumes.  

In den Beispielkonfigurationen, Installationsbefehlen usw. werden die HANA-Instanz **03** und HANA-System-ID **HN1** verwendet. Die Beispiele basieren auf HANA 2.0 SP4 und Red Hat Enterprise Linux für SAP 7.6. 

> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.


Bevor Sie beginnen, lesen Sie die folgenden SAP-Hinweise und Dokumente:

* [Azure NetApp Files-Dokumentation][anf-azure-doc] 
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
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux -Netzwerkleitfaden](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure-spezifische RHEL-Dokumentation:
  * [Installieren von SAP HANA unter Red Hat Enterprise Linux für die Verwendung in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]


## <a name="overview"></a>Übersicht

Eine Methode zum Erreichen von Hochverfügbarkeit mit HANA ist das Konfigurieren des automatischen Hostfailovers. Zum Konfigurieren des automatischen Hostfailovers fügen Sie dem HANA-System einen oder mehrere virtuelle Computer hinzu und konfigurieren sie als Standbyknoten. Wenn der aktive Knoten ausfällt, übernimmt automatisch ein Standbyknoten. In der dargestellten Konfiguration mit virtuellen Azure-Computern wird das automatische Failover durch [NFS in Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) erreicht.  

> [!NOTE]
> Der Standbyknoten erfordert Zugriff auf alle Datenbankvolumes. Die HANA-Volumes müssen als NFSv4-Volumes bereitgestellt werden. Der verbesserte, auf Dateilease basierende Sperrmechanismus im NFSv4-Protokoll wird zum `I/O`-Fencing verwendet. 

> [!IMPORTANT]
> Zum Erstellen der unterstützten Konfiguration müssen Sie die HANA-Datenvolumes und -Protokollvolumes als NFSv4.1-Volumes bereitstellen und unter Verwendung des NFSv4.1-Protokolls einbinden. Die Konfiguration zum automatischen Failover des HANA-Hosts mit dem Standbyknoten wird bei NFSv3 nicht unterstützt.

![Hochverfügbarkeit von SAP NetWeaver – Übersicht](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Im vorangehenden Diagramm, das den Empfehlungen für SAP HANA-Netzwerke entspricht, werden drei Subnetze in einem virtuellen Azure-Netzwerk dargestellt: 
* Für die Clientkommunikation
* Für die Kommunikation mit dem Speichersystem
* Für die interne Kommunikation zwischen SAP HANA-Knoten

Die Azure NetApp-Volumes befinden sich in einem separaten Subnetz, das an [Azure NetApp Files delegiert wurde](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Die Subnetze in dieser Beispielkonfiguration sind:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (an Azure NetApp Files delegiertes Subnetz)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Einrichten der Infrastruktur für Azure NetApp Files 

Bevor Sie mit der Einrichtung der Azure NetApp Files-Infrastruktur beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation][anf-azure-doc] vertraut machen. 

Azure NetApp Files ist in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird.  

Informationen zur Verfügbarkeit von Azure NetApp Files in den einzelnen Azure-Regionen finden Sie unter [Verfügbarkeit von Azure NetApp Files nach Azure-Region][anf-avail-matrix].  

Fordern Sie Onboarding für Azure NetApp Files an, bevor Sie Azure NetApp Files bereitstellen. Wechseln Sie dazu zu den [Registrierungsanweisungen für Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen  

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.  

1. Wenn Sie die Ressourcen noch nicht bereitgestellt haben, fordern Sie [Onboarding für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) an.  

2. Erstellen Sie entsprechend den Anweisungen in [Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) ein NetApp-Konto in der ausgewählten Azure-Region.  

3. Richten Sie entsprechend den Anweisungen in [Einrichten eines Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) einen Azure NetApp Files-Kapazitätspool ein.  

   Die in diesem Artikel vorgestellte HANA-Architektur verwendet einen einzigen Azure NetApp Files-Kapazitätspool mit der Dienstebene *Ultra*. Für HANA-Workloads in Azure empfehlen wir die [Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) *Ultra* oder *Premium* für Azure NetApp Files.  

4. Delegieren Sie ein Subnetz an Azure NetApp Files, wie in den Anweisungen in [Delegieren eines Subnetzes an Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) beschrieben.  

5. Stellen Sie Azure NetApp Files-Volumes entsprechend den Anweisungen in [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit.  

   Stellen Sie beim Bereitstellen der Volumes sicher, dass Sie die Version **NFSv4.1** auswählen. Stellen Sie die Volumes im festgelegten [Subnetz](/rest/api/virtualnetwork/subnets) für Azure NetApp Files bereit. Die IP-Adressen der Azure NetApp-Volumes werden automatisch zugewiesen. 
   
   Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. Beispielsweise sind „**HN1**-data-mnt00001“, „**HN1**-log-mnt00001“ usw. die Namen der Volumes, und „nfs://10.9.0.4/**HN1**-data-mnt00001“, „nfs://10.9.0.4/**HN1**-log-mnt00001“ usw. sind die Dateipfade für die Azure NetApp Files-Volumes.  

   * Volume „**HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)“
   * Volume „**HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)“
   * Volume „**HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)“
   * Volume „**HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)“
   * Volume „**HN1**-shared (nfs://10.9.0.4/**HN1**-shared)“
   
   In diesem Beispiel haben wir ein separates Azure NetApp Files-Volume für die einzelnen HANA-Daten- und -Protokollvolumes verwendet. Für eine kostenoptimierte Konfiguration in kleineren oder nicht produktiven Systemen können alle Datenbereitstellungen auf einem einzelnen Volume und alle Protokollbereitstellungen auf einem anderen einzelnen Volume platziert werden.  

### <a name="important-considerations"></a>Wichtige Hinweise

Berücksichtigen Sie die folgenden wichtigen Überlegungen, wenn Sie Azure NetApp Files für das Szenario mit horizontaler Skalierung für SAP HANA mit Standbyknoten erstellen:

- Die Mindestgröße eines Kapazitätspools beträgt 4 TiB (Tebibyte).  
- Die Mindestvolumegröße ist 100 GiB (Gibibyte).
- Azure NetApp Files und alle virtuellen Computer, auf denen die Azure NetApp Files-Volumes eingebunden werden sollen, müssen sich in demselben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden.  
- Das ausgewählte virtuelle Netzwerk muss über ein an Azure NetApp Files delegiertes Subnetz verfügen.
- Der Durchsatz eines Azure NetApp-Volumes ist eine Funktion des Volumekontingents und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben. Stellen Sie bei der Größenanpassung der HANA Azure NetApp-Volumes sicher, dass der sich ergebende Durchsatz die HANA-Systemanforderungen erfüllt.  
- Mit den [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) von Azure NetApp Files können Sie die zulässigen Clients und den Zugriffstyp (Lese- und Schreibzugriff, schreibgeschützt usw.) steuern. 
- Das Azure NetApp Files-Feature wertet derzeit noch keine Zonen aus. Das Feature wird bisher nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen.  

> [!IMPORTANT]
> Bei SAP HANA-Workloads ist eine niedrige Latenz sehr wichtig. Arbeiten Sie mit Ihrem Microsoft-Vertreter zusammen, um sicherzustellen, dass die virtuellen Computer und die Azure NetApp Files-Volumes in unmittelbarer Nähe zueinander bereitgestellt werden.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Größenanpassung für eine HANA-Datenbank in Azure NetApp Files

Der Durchsatz eines Azure NetApp Files-Volumes ist eine Funktion der Volumegröße und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben. 

Beachten Sie beim Entwerfen der Infrastruktur für SAP in Azure einige Mindestanforderungen von SAP an den Speicher, aus denen sich die Mindestdurchsatzeigenschaften ergeben:

- Lese-/Schreibzugriff auf „/hana/log“ für 250 Megabyte pro Sekunde (MB/s) mit einer E/A-Größe von 1 MB.  
- Leseaktivität mit mindestens 400 MB/s in „/hana/data“ für E/A-Größen von 16 MB und 64 MB.  
- Schreibaktivität mit mindestens 250 MB/s in „/hana/data“ für E/A-Größen von 16 MB und 64 MB. 

Die [Azure NetApp Files-Durchsatzlimits](../../../azure-netapp-files/azure-netapp-files-service-levels.md) pro 1 TiB an Volumekontingent lauten:
- Storage Premium-Tarif: 64 MiB/s  
- Storage Ultra-Tarif: 128 MiB/s  

Um die SAP-Mindestanforderungen für den Durchsatz für Daten und Protokolle und die Richtlinien für „hana/shared“ zu erfüllen, werden folgende Größen empfohlen:

| Volume | Größe<br>Storage Premium-Tarif | Größe<br>Storage Ultra-Tarif | Unterstütztes NFS-Protokoll |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared | 1 x RAM pro 4 Workerknoten | 1 x RAM pro 4 Workerknoten | v3 oder v4.1 |

Die SAP HANA-Konfiguration für das in diesem Artikel vorgestellte Layout mit Azure NetApp Files-Tarif „Storage Ultra“ lautet:

| Volume | Größe<br>Storage Ultra-Tarif | Unterstütztes NFS-Protokoll |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 oder v4.1 |

> [!NOTE]
> Die hier angegebenen Empfehlungen für die Azure NetApp Files-Größenanpassung zielen darauf ab, die Mindestanforderungen zu erfüllen, die SAP seinen Infrastrukturanbietern empfiehlt. In realen Kundenbereitstellungen und Workloadszenarien sind diese Größen möglicherweise nicht ausreichend. Verwenden Sie diese Empfehlungen also als Ausgangspunkt, und nehmen Sie Anpassungen auf Grundlage der Anforderungen der spezifischen Workload vor.  

> [!TIP]
> Sie können die Größe der Azure NetApp Files-Volumes dynamisch anpassen, ohne *die Bereitstellung der Volumes aufheben* oder die virtuellen Computer oder SAP HANA beenden zu müssen. Mit diesem Ansatz können Sie sowohl den erwarteten als auch unvorhergesehenen Durchsatzanforderungen flexibel gerecht werden.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal

Zuerst müssen Sie die Azure NetApp Files-Volumes erstellen. Führen Sie dann die folgenden Schritte aus:
1. Erstellen Sie im [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) die [Azure Virtual Network-Subnetze](../../../virtual-network/virtual-network-manage-subnet.md). 
1. Stellen Sie die VMs bereit. 
1. Erstellen Sie die zusätzlichen Netzwerkschnittstellen, und fügen Sie diese an die entsprechenden virtuellen Computer an.  

   Jeder virtuelle Computer verfügt über drei Netzwerkschnittstellen, die den drei Azure Virtual Network-Subnetzen (`client`, `storage` und `hana`) entsprechen. 

   Weitere Informationen finden Sie in [Erstellen eines virtuellen Linux-Computers in Azure mit mehreren Netzwerkschnittstellenkarten](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> Bei SAP HANA-Workloads ist eine niedrige Latenz sehr wichtig. Arbeiten Sie mit Ihrem Microsoft-Vertreter zusammen, um sicherzustellen, dass die virtuellen Computer und die Azure NetApp Files-Volumes in unmittelbarer Nähe zueinander bereitgestellt werden, damit eine niedrige Latenz erzielt wird. Übermitteln Sie beim [Onboarding eines neuen SAP HANA-Systems](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), das SAP HANA Azure NetApp Files verwendet, die erforderlichen Informationen. 
 
In den nächsten Anweisungen wird davon ausgegangen, dass Sie bereits die Ressourcengruppe, das Azure Virtual Network und die drei Azure Virtual Network-Subnetze erstellt haben: `client`, `storage` und `hana`. Wählen Sie beim Bereitstellen der VMs das Clientsubnetz aus, sodass die Clientnetzwerkschnittstelle die primäre Schnittstelle auf den VMs ist. Sie müssen außerdem eine explizite Route zum delegierten Subnetz von Azure NetApp Files über das Gateway des Speichersubnetzes konfigurieren. 

> [!IMPORTANT]
> Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem SAP-zertifiziert ist für SAP HANA auf den spezifischen VM-Typen, die Sie verwenden. Eine Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese finden Sie auf der Website [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klicken Sie in die Details des jeweils aufgeführten VM-Typs, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für diesen Typ anzuzeigen.  

1. Erstellen Sie eine Verfügbarkeitsgruppe für SAP HANA. Stellen Sie sicher, dass Sie die maximale Updatedomäne einrichten.  

2. Erstellen Sie anhand der folgenden Schritte drei virtuelle Computer (**hanadb1**, **hanadb2**, **hanadb3**):  

   a. Verwenden Sie ein Red Hat Enterprise Linux-Image im Azure-Katalog, das für SAP HANA unterstützt wird. In diesem Beispiel wurde ein RHEL-SAP-HA 7.6-Image verwendet.  

   b. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für SAP HANA erstellt haben.  

   c. Wählen Sie das Azure Virtual Network-Clientsubnetz aus. Wählen Sie [Beschleunigter Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md) aus.  

   Der Name der Netzwerkschnittstelle wird automatisch beim Bereitstellen der virtuellen Computer generiert. In diesen Anweisungen werden die automatisch generierten Netzwerkschnittstellen, die mit dem Subnetz des virtuellen Azure-Clientnetzwerks verbunden sind, der Einfachheit halber als **hanadb1-client**, **hanadb2-client** und **hanadb3-client** bezeichnet. 

3. Erstellen Sie für das Subnetz des virtuellen `storage`-Netzwerks drei Netzwerkschnittstellen – eine für jeden virtuellen Computer (in diesem Beispiel **hanadb1-storage**, **hanadb2-storage** und **hanadb3-storage**).  

4. Erstellen Sie für das Subnetz des virtuellen `hana`-Netzwerks drei Netzwerkschnittstellen – eine für jeden virtuellen Computer (in diesem Beispiel **hanadb1-hana**, **hanadb2-hana** und **hanadb3-hana**).  

5. Fügen Sie die neu erstellten virtuellen Netzwerkschnittstellen mit den folgenden Schritten an die entsprechenden virtuellen Computer an:  

    a. Navigieren Sie im [Azure-Portal](https://portal.azure.com/#home) zum virtuellen Computer.  

    b. Wählen Sie im linken Bereich **Virtuelle Computer** aus. Filtern Sie nach dem Namen des virtuellen Computers (z. B. **hanadb1**), und wählen Sie dann den virtuellen Computer aus.  

    c. Klicken Sie im Bereich **Übersicht** auf **Beenden**, um die Zuordnung des virtuellen Computers aufzuheben.  

    d. Wählen Sie **Netzwerk** aus, und fügen Sie dann die Netzwerkschnittstelle an. Wählen Sie in der Dropdownliste **Netzwerkschnittstelle anfügen** die bereits erstellten Netzwerkschnittstellen für die Subnetze `storage` und `hana` aus.  
    
    e. Wählen Sie **Speichern** aus. 
 
    f. Wiederholen Sie die Schritte b bis e für die restlichen virtuellen Computer (in unserem Beispiel **hanadb2** und **hanadb3**).
 
    g. Belassen Sie die virtuelle Computer für den Moment im Status „Beendet“. Als Nächstes aktivieren wir den [beschleunigten Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md) für alle neu angefügten Netzwerkschnittstellen.  

6. Aktivieren Sie den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen für die Subnetze `storage` und `hana` mit den folgenden Schritten:  

    a. Öffnen Sie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) im [Azure-Portal](https://portal.azure.com/#home).  

    b. Führen Sie die folgenden Befehle aus, um den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen zu aktivieren, die wir an die Subnetze `storage` und `hana` angefügt haben.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Starten Sie die virtuellen Computer, indem Sie folgendermaßen vorgehen:  

    a. Wählen Sie im linken Bereich **Virtuelle Computer** aus. Filtern Sie nach dem Namen des virtuellen Computers (z. B. **hanadb1**), und wählen Sie ihn dann aus.  

    b. Wählen Sie im Bereich **Übersicht** die Option **Starten** aus.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguration und Vorbereitung des Betriebssystems

Die Anweisungen in den nächsten Abschnitten weisen eines der folgenden Präfixe auf:
* **[A]** : Gilt für alle Knoten
* **[1]** : Gilt nur für Knoten 1
* **[2]** : Gilt nur für Knoten 2
* **[3]** : Gilt nur für Knoten 3

Führen Sie die folgenden Schritte aus, um das Betriebssystem zu konfigurieren und vorzubereiten:

1. **[A]** Verwalten Sie die Hostdateien auf den virtuellen Computern. Schließen Sie Einträge für alle Subnetze ein. Die folgenden Einträge wurden für dieses Beispiel zu `/etc/hosts` hinzugefügt.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Fügen Sie eine Netzwerkroute hinzu, sodass die Kommunikation mit Azure NetApp Files über die Speichernetzwerkschnittstelle erfolgt.  

   In diesem Beispiel wird `Networkmanager` verwendet, um die zusätzliche Netzwerkroute zu konfigurieren. In den folgenden Anweisungen wird davon ausgegangen, dass die Speichernetzwerkschnittstelle `eth1` ist.  
   Bestimmen Sie zuerst den Verbindungsnamen für das Gerät `eth1`. In diesem Beispiel ist der Verbindungsname für das Gerät `eth1` `Wired connection 1`.  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Konfigurieren Sie dann eine weitere Route über `eth1` zum delegierten Azure NetApp Files-Netzwerk.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Starten Sie die VM neu, um die Änderungen zu aktivieren.  

3. **[A]** Installieren Sie das NFS-Clientpaket.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Bereiten Sie das Betriebssystem für die Ausführung von SAP HANA auf Azure NetApp mit NFS vor, wie unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure] beschrieben. Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/netapp-hana.conf* für die NetApp-Konfigurationseinstellungen.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/ms-az.conf* mit weiteren Optimierungseinstellungen.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** Passen Sie die sunrpc-Einstellungen entsprechend den Empfehlungen unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure] an.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat für HANA-Konfiguration.

    Konfigurieren Sie RHEL wie in den SAP-Hinweisen [2292690], [2455582], [2593824] und unter <https://access.redhat.com/solutions/2447641> beschrieben.

    > [!NOTE]
    > Wenn Sie HANA 2.0 SP04 installieren, müssen Sie das Paket `compat-sap-c++-7` wie im SAP-Hinweis [2593824] beschrieben installieren, damit Sie SAP HANA installieren können. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Einbinden der Azure NetApp Files-Volumes

1. **[A]** Erstellen Sie Bereitstellungspunkte für die HANA-Datenbankvolumes.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Erstellen Sie knotenspezifische Verzeichnisse für „/usr/sap“ auf „**HN1**-shared“.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **`defaultv4iddomain.com`** ) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.  

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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Weitere Informationen zum Ändern des Parameters `nfs4_disable_idmapping` finden Sie unter https://access.redhat.com/solutions/1749883.

6. **[A]** Binden Sie die freigegebenen Azure NetApp Files-Volumes ein.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Binden Sie die knotenspezifischen Volumes auf **hanadb1** ein.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Binden Sie die knotenspezifischen Volumes auf **hanadb2** ein.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Binden Sie die knotenspezifischen Volumes auf **hanadb3** ein.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Überprüfen Sie, ob alle HANA-Volumes mit der NFS-Protokollversion **NFSv4** eingebunden wurden.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Installation  

In diesem Beispiel für die Bereitstellung von SAP HANA in einer Konfiguration mit horizontaler Skalierung mit Standbyknoten mit Azure haben wir HANA 2.0 SP4 verwendet.  

### <a name="prepare-for-hana-installation"></a>Vorbereiten der HANA-Installation

1. **[A]** Legen Sie vor der HANA-Installation das Stammkennwort fest. Nachdem die Installation abgeschlossen wurde, können Sie das Stammkennwort deaktivieren. Führen Sie den Befehl `passwd` als `root` aus.  

2. **[1]** Vergewissern Sie sich, dass Sie sich bei **hanadb2** und **hanadb3** per SSH anmelden können, ohne zur Eingabe eines Kennworts aufgefordert zu werden.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Installieren Sie zusätzliche Pakete, die für HANA 2.0 SP4 erforderlich sind. Weitere Informationen finden Sie im SAP-Hinweis [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Übertragen Sie den Besitz der SAP HANA-Verzeichnisse `data` und `log` an „**hn1** adm“.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Deaktivieren Sie die Firewall vorübergehend, damit sie die HANA-Installation nicht stört. Sie können sie nach der HANA-Installation wieder aktivieren. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA-Installation

1. **[1]** Installieren Sie SAP HANA gemäß den Anweisungen im [SAP HANA 2.0 Installation and Update Guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) (Installations- und Updateleitfaden für SAP HANA 2.0). In diesem Beispiel installieren wir eine SAP HANA-Konfiguration mit horizontaler Skalierung mit einem Master-, einem Worker- und einem Standbyknoten.  

   a. Starten Sie das Programm **hdblcm** über das HANA-Installationssoftwareverzeichnis. Verwenden Sie den `internal_network`-Parameter, und übergeben Sie den Adressraum für das Subnetz, das für die interne Kommunikation zwischen HANA-Knoten verwendet wird.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Aktion auswählen**: Geben Sie **1** ein (für „installieren“).
     * Für **Additional components for installation** (Zusätzliche Komponenten für die Installation): Geben Sie **2, 3** ein.
     * Für den Installationspfad: Drücken Sie die EINGABETASTE (Standardwert „/hana/shared“).
     * Für **Local Host Name** (Name des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie dem System Hosts hinzufügen?** : Geben Sie **y** ein.
     * Für **Hostnamen, die durch Kommas getrennt hinzugefügt werden sollen**: Geben Sie **hanadb2, hanadb3** ein.
     * Für **Root User Name** (Root-Benutzername) [root]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für Rollen für den Host „hanadb2“: Geben Sie **1** ein (für Workerrolle).
     * Für **Host Failover Group** (Hostfailovergruppe) für den Host „hanadb2“ [Standard]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Storage Partition Number** (Speicherpartitionsnummer) für den Host „hanadb2“ [<<assign automatically>>]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Worker Group** (Workergruppe) für den Host „hanadb2“ [Standard]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select Roles** (Rollen auswählen) für den Host „hanadb3“: Geben Sie **2** (für Standby) ein.
     * Für **Host Failover Group** (Hostfailovergruppe) für den Host „hanadb3“ [Standard]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Worker Group** (Workergruppe) für den Host „hanadb3“ [Standard]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **SAP HANA System ID** (SAP HANA-System-ID): Geben Sie **HN1** ein.
     * Für **Instance number** (Instanznummer) [00]: Geben Sie **03** ein.
     * Für **Local Host Worker Group** (Workergruppe des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select System Usage / Enter index [4]** (Systemnutzung auswählen/Index eingeben): Geben Sie **4** (für benutzerdefiniert) ein.
     * Für **Location of Data Volumes** (Speicherort der Datenvolumes) [/Hana/Data/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Location of Log Volumes** (Speicherort der Protokollvolumes) [/Hana/log/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie die maximale Speicherbelegung beschränken?** [n] Geben Sie **n** ein.
     * Für **Certificate Host Name For Host hanadb1** (Zertifikathostname für Host „hanadb1“ [hanadb1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Certificate Host Name For Host hanadb2** (Zertifikathostname für Host „hanadb2“ [hanadb1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Certificate Host Name For Host hanadb3** (Zertifikathostname für Host „hanadb1“ [hanadb3]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator (hn1adm) Password** (Kennwort für den Systemadministrator (hn1adm)): Geben Sie das Kennwort ein.
     * For **System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system)): Geben Sie das Kennwort für das System ein.
     * For **Confirm System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system) bestätigen): Geben Sie das Kennwort für das System ein.
     * Für **Soll das System nach dem Neustart des Computers neu starten?** [n] Geben Sie **n** ein. 
     * Für **Möchten Sie fortfahren (y/n)?** : Überprüfen Sie die Zusammenfassung. Wenn alle Werte korrekt sind, geben Sie **y** ein.


2. **[1]** Überprüfen Sie die „global.ini“.  

   Zeigen Sie die „global. ini“ an, und stellen Sie sicher, dass die Konfiguration für die interne SAP HANA-Kommunikation zwischen Knoten eingerichtet ist. Überprüfen Sie den Abschnitt **communication**. Dieser sollte den Adressraum für das `hana`-Subnetz enthalten, und `listeninterface` sollte auf `.internal` festgelegt sein. Überprüfen Sie den Abschnitt **internal_hostname_resolution**. Er sollte die IP-Adressen für die virtuellen HANA-Computer enthalten, die zum `hana`-Subnetz gehören.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Fügen Sie die Hostzuordnung hinzu, um sicherzustellen, dass die Client-IP-Adressen für die Clientkommunikation verwendet werden. Fügen Sie den Abschnitt `public_host_resolution` hinzu, und fügen Sie die entsprechenden IP-Adressen aus dem Clientsubnetz hinzu.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Starten Sie SAP HANA neu, um die Änderungen zu aktivieren.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Stellen Sie sicher, dass die Clientschnittstelle zur Kommunikation die IP-Adressen aus dem `client`-Subnetz verwendet.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Weitere Informationen zum Überprüfen der Konfiguration finden Sie im SAP-Hinweis [2183363 – Konfiguration des internen SAP HANA-Netzwerks](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Aktivieren Sie die Firewall wieder.  
   - HANA beenden
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Firewall erneut aktivieren
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Erforderliche Firewallports öffnen

       > [!IMPORTANT]
       > Erstellen Sie Firewallregeln, um die HANA-Kommunikation zwischen Knoten und Clientdatenverkehr zuzulassen. Die erforderlichen Ports finden Sie unter [TCP/IP-Ports aller SAP-Produkte](https://help.sap.com/viewer/ports). Die folgenden Befehle dienen lediglich als Beispiel. In diesem Szenario wurde die Systemnummer 03 verwendet.

       <pre><code>
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
       </code></pre>

   - HANA starten
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Um SAP HANA für den zugrunde liegenden Azure NetApp Files-Speicher zu optimieren, legen Sie die folgenden SAP HANA-Parameter fest:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks` **all**

   Weitere Informationen finden Sie unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]. 

   Ab SAP HANA 2.0 können Sie die Parameter in `global.ini` festlegen. Weitere Informationen finden Sie im SAP-Hinweis [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Für SAP HANA 1.0-Systeme, Versionen bis SPS12, können diese Parameter bei der Installation festgelegt werden, wie im SAP-Hinweis [2267798](https://launchpad.support.sap.com/#/notes/2267798) beschrieben.  

7. Die Größe des von Azure NetApp Files verwendeten Speichers ist auf 16 Terabyte (TB) begrenzt. SAP HANA erkennt die Speicherbegrenzung nicht implizit und erstellt nicht automatisch eine neue Datendatei, wenn die Dateigröße von 16 TB erreicht ist. Wenn SAP HANA versucht, die Datei über 16 TB hinaus zu vergrößern, führt dies zu Fehlern und schließlich zum Absturz des Indexservers. 

   > [!IMPORTANT]
   > Um zu verhindern, dass SAP HANA versucht, Datendateien über die [16-TB-Begrenzung](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) des Speichersubsystems hinaus zu vergrößern, legen Sie die folgenden Parameter in `global.ini` fest.  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000. Weitere Informationen finden Sie im SAP-Hinweis [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Beachten Sie den SAP-Hinweis [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testen des SAP HANA-Failovers 

1. Simulieren Sie einen Knotenabsturz auf einem SAP HANA-Workerknoten. Gehen Sie folgendermaßen vor: 

   a. Führen Sie die folgenden Befehle als „**hn1** adm“ aus, um den Status der Umgebung zu erfassen, bevor Sie den Knotenabsturz simulieren:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Um einen Knotenabsturz zu simulieren, führen Sie auf dem Workerknoten, in diesem Fall **hanadb2**, den folgenden Befehl als Root-Benutzer aus:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Überwachen Sie das System, bis das Failover abgeschlossen ist. Erfassen Sie den Status nach Abschluss des Failovers – dieser sollte wie folgt aussehen:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Legen Sie `kernel.panic` auf *allen* virtuellen HANA-Computern auf 20 Sekunden fest, um bei einem Kernel-Panic-Status eines Knotens Verzögerungen beim SAP HANA-Failover zu vermeiden. Die Konfiguration wird in `/etc/sysctl` ausgeführt. Starten Sie die virtuellen Computer neu, um die Änderung zu aktivieren. Wenn diese Änderung nicht vorgenommen wird, kann im Falle des Kernel-Panic-Status eines Knotens ein Failover 10 Minuten oder länger dauern.  

2. Beenden Sie den Namenserver, indem Sie die folgenden Schritte ausführen:

   a. Führen Sie die folgenden Befehle als „**hn1** adm“ aus, um den Status der Umgebung vor dem Test zu überprüfen:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Führen Sie die folgenden Befehle als „**hn1** adm“ auf dem aktiven Masterknoten aus (in diesem Fall **hanadb1**):  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Der Standbyknoten **hanadb3** wird zum Masterknoten. Dies ist der Ressourcenstatus nach Abschluss des Failovertests:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Starten Sie die HANA-Instanz auf **hanadb1** (d. h. auf demselben virtuellen Computer, auf dem der Namenserver beendet wurde) neu. Der Knoten **hanadb1** tritt wieder der Umgebung bei und behält seine Standbyrolle.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Nach dem Starten von SAP HANA auf **hanadb1** wird der folgende Status erwartet:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Beenden Sie den Namenserver erneut auf dem derzeit aktiven Masterknoten (auf dem Knoten **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Der Knoten **hanadb1** übernimmt wieder die Rolle des Masterknotens. Nachdem der Failovertest abgeschlossen wurde, sieht der Status wie folgt aus:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Starten Sie SAP HANA auf **hanadb3** – dieser Knoten kann jetzt als Standbyknoten verwendet werden.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Nach dem Starten von SAP HANA auf **hanadb3** sieht der Status wie folgt aus:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
