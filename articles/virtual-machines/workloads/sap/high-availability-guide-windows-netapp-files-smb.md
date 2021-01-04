---
title: Hochverfügbarkeit mit Azure-VMs für SAP NetWeaver unter Windows mit Azure NetApp Files (SMB) | Microsoft-Dokumentation
description: Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Windows mit Azure NetApp Files (SMB) für SAP-Anwendungen
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
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: 90b0ab4fdabd40e803d1f85a640e4cb387e40c44
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958947"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Windows mit Azure NetApp Files (SMB) für SAP-Anwendungen

[dbms-guide]:dbms-guide.md
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

In diesem Artikel werden das Bereitstellen und Konfigurieren der virtuellen Computer, das Installieren des Clusterframeworks und das Installieren eines hochverfügbaren SAP NetWeaver 7.50-Systems auf virtuellen Windows-Computern mithilfe von [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) auf [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) beschrieben.  

Die Datenbankschicht wird in diesem Artikel nicht ausführlich behandelt. Bei den folgenden Schritten wird davon ausgegangen, dass bereits ein [virtuelles Azure-Netzwerk](../../../virtual-network/virtual-networks-overview.md) erstellt wurde.  

Lesen Sie zuerst die folgenden SAP-Hinweise und -Dokumente:

* [Azure NetApp Files-Dokumentation][anf-azure-doc] 
* SAP-Hinweis [1928533][1928533] mit folgendem Inhalt:  
  * Liste der für die Bereitstellung von SAP-Software unterstützten Azure-VM-Größen
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * erforderliche SAP-Kernelversion für Windows in Microsoft Azure
* In SAP-Hinweis [2015553][2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2178632][2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [1999351][1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* SAP-Hinweis [2287140](https://launchpad.support.sap.com/#/notes/2287140) mit den Voraussetzungen für die von SAP unterstützten CA-Funktionen des SMB 3.x-Protokolls
* SAP-Hinweis [2802770](https://launchpad.support.sap.com/#/notes/2802770) mit Informationen zur Problembehandlung bei der langsamen Ausführung von SAP-Transaktion AL11 unter Windows 2012 und 2016
* SAP-Hinweis [1911507](https://launchpad.support.sap.com/#/notes/1911507) mit Informationen zur Funktion für transparente Failover für eine Dateifreigabe unter Windows Server mit dem SMB 3.0-Protokoll
* SAP-Hinweis [662452](https://launchpad.support.sap.com/#/notes/662452) mit Empfehlungen (Deaktivierung der Namensgenerierung aus Version 8.3) für das Beheben von Problemen im Zusammenhang mit einer schlechten Dateisystemleistung und das Beheben von Fehlern während des Datenzugriffs
* [Installieren von SAP NetWeaver-Hochverfügbarkeit in einem Windows-Failovercluster und auf einer Windows-Dateifreigabe für SAP ASCS-/SCS-Instanzen in Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines – Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Hinzufügen des Testports in der ASCS-Clusterkonfiguration](sap-high-availability-installation-wsfc-file-share.md)
* [Installieren einer (A)SCS-Instanz auf einem Failovercluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Erstellen eines SMB-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Übersicht

SAP hat neue Ansätze und eine Alternative zum Gruppieren von freigegebenen Datenträgern entwickelt, die eine Gruppierung von SAP ASCS/SCS-Instanzen in einem Windows-Failovercluster ermöglichen. Anstatt freigegebene Datenträger in einem Cluster zu verwenden, können Sie mit einer SMB-Dateifreigabe globale SAP-Hostdateien bereitstellen. Azure NetApp Files unterstützt SMBv3 (zusammen mit NFS) mit der NTFS-Zugriffssteuerungsliste mithilfe von Active Directory. Azure NetApp Files ist automatisch hochverfügbar, da es sich hierbei um einen PaaS-Dienst handelt. Diese Funktionen machen Azure NetApp Files zur optimalen Lösung für das globale Hosten der SMB-Dateifreigabe für SAP.  
Sowohl [Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) als auch [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) wird unterstützt. Bereits vorhandene Active Directory-Domänencontroller können mit Azure NetApp Files verwendet werden. Domänencontroller können als virtuelle Computer in Azure oder lokal über ExpressRoute oder S2S-VPN betrieben werden. In diesem Artikel wird ein Domänencontroller auf einem virtuellen Azure-Computer verwendet.  
Hochverfügbarkeit (HA) für zentrale Dienste von SAP NetWeaver erfordert freigegebenen Speicher. Bisher musste entweder ein SOFS-Cluster erstellt oder freigegebene Clusterdatenträgersoftware wie SIOS verwendet werden, um dies unter Windows zu erreichen. Jetzt ist es möglich, Hochverfügbarkeit für SAP NetWeaver mithilfe von freigegebenem Speicher zu erreichen, der auf Azure NetApp Files bereitgestellt wird. Wenn Sie Azure NetApp Files für den freigegebenen Speicher nutzen, entfällt die Notwendigkeit der Verwendung von SOFS und SIOS.  

> [!NOTE]
> Das Gruppieren von SAP ASCS/SCS-Instanzen über eine Dateifreigabe wird für SAP NetWeaver 7.40-Produkte (und höher) mit SAP Kernel 7.49 (und höher) unterstützt.  

![SAP ASCS-/SCS-Hochverfügbarkeitsarchitektur mit SMB-Dateifreigabe](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Voraussetzungen für SMB-Dateifreigaben:
* SMB 3.0-Protokoll (oder höher).
* Möglichkeit zum Festlegen von Active Directory-Zugriffssteuerungslisten (ACLs) für Active Directory-Benutzergruppen und das computer$-Computerobjekt.
* aktivierte Hochverfügbarkeit für die Dateifreigabe

Die Dateifreigabe für SAP Central Services in dieser Referenzarchitektur wird von Azure NetApp Files angeboten:

![SAP ASCS-/SCS-Hochverfügbarkeitsarchitektur mit SMB-Freigabedetails](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Erstellen und Bereitstellen von SMB-Volumes für Azure NetApp Files

Führen Sie die folgenden Schritte aus, um die Verwendung von Azure NetApp Files vorzubereiten.  

1. Führen Sie die Schritte für die [Registrierung bei Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) aus.  
2. Erstellen Sie ein Azure NetApp Files-Konto, indem Sie die im Artikel [Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) beschriebenen Schritte ausführen.  
3. Befolgen Sie die Anweisungen im Artikel [Einrichten eines Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md), um einen Kapazitätspool einzurichten.
4. Azure NetApp Files-Ressourcen müssen sich im delegierten Subnetz befinden. Befolgen Sie die Anweisungen im Artikel [Delegieren eines Subnetzes an Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md), um das delegierte Subnetz zu erstellen.  

   > [!IMPORTANT]
   > Bevor Sie ein SMB-Volume erstellen, müssen Sie zunächst Active Directory-Verbindungen erstellen. Überprüfen Sie die [Anforderungen für Active Directory-Verbindungen](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections).  

5. Stellen Sie wie unter [Herstellen einer Active Directory-Verbindung](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) beschrieben eine Active Directory-Verbindung her.  
6. Befolgen Sie die im Artikel [Hinzufügen eines SMB-Volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) beschriebenen Anweisungen, um das Azure NetApp Files-SMB-Volume zu erstellen.  
7. Stellen Sie das SMB-Volume auf Ihrem virtuellen Windows-Computer bereit.

> [!TIP]
> Sie finden die Anweisungen zum Bereitstellen des Azure NetApp Files-Volumes, indem Sie im [Azure-Portal](https://portal.azure.com/#home) zum Objekt „Azure NetApp Files“ navigieren und zunächst auf das Blatt **Volumes** und dann auf **Mount Instructions** (Anweisungen bereitstellen) klicken.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Vorbereiten der Infrastruktur für SAP HA mit einem Windows-Failovercluster 

1. [Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Hinzufügen virtueller Windows-Computer zur Domäne](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)
3. [Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten für eine SAP ASCS-/SCS-Instanz](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Einrichten eines Windows Server-Failoverclusters für eine SAP ASCS-/SCS-Instanz](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Wenn Sie Windows Server 2016 verwenden, wird empfohlen, einen [Azure-Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) zu konfigurieren.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installieren der SAP ASCS-Instanz auf beiden Knoten

Sie benötigen die folgende SAP-Software:
   * Das Installationstool SAP Software Provisioning Manager (SWPM) Version SPS25 oder höher.
   * SAP Kernel 7.49 oder höher
   * Erstellen Sie wie unter [Erstellen eines virtuellen Hostnamens für die SAP ASCS-/SCS-Clusterinstanz](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097) beschrieben einen virtuellen Hostnamen (Clusternetzwerknamen) für die SAP ASCS-/SCS-Clusterinstanz.

> [!NOTE]
> Das Gruppieren von SAP ASCS/SCS-Instanzen über eine Dateifreigabe wird für SAP NetWeaver 7.40-Produkte (und höher) mit SAP Kernel 7.49 (und höher) unterstützt.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installieren einer ASCS/SCS-Instanz auf dem ersten ASCS/SCS-Clusterknoten

1. Installieren Sie eine ASCS/SCS-Instanz auf dem ersten ASCS/SCS-Clusterknoten. Starten Sie das SAP SWPM-Installationstool, und navigieren Sie zu **Produkt** > **DBMS** > Installation > Application Server ABAP (or Java) > High-Availability System > ASCS/SCS instance > First cluster node (Anwendungsserver ABAP (oder Java) > Hochverfügbarkeitssystem > ASCS-/SCS-Instanz > Erster Clusterknoten).  

2. Wählen Sie **File Share Cluster** (Dateifreigabecluster) als Clusterfreigabekonfiguration in SWPM aus.  
3. Geben Sie, wenn Sie beim Schritt **SAP System Cluster Parameters** (SAP-Systemclusterparameter) dazu aufgefordert werden, den Hostnamen für die Azure NetApp Files-SMB-Dateifreigabe ein, die Sie bereits als **File Share Host Name** (Hostname für die Dateifreigabe) erstellt haben.  In diesem Beispiel lautet der Hostname für die SMB-Dateifreigabe **anfsmb-9562**. 

   > [!IMPORTANT]
   > Wenn in den Ergebnissen der Voraussetzungsprüfung in SWPM angezeigt wird, dass die Bedingungen für die Funktion für die fortlaufende Verfügbarkeit nicht erfüllt werden, können Sie die im Artikel [Verzögerte Fehlermeldung beim versuchten Zugriff auf einen nicht länger in Windows verfügbaren freigegebenen Ordner](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l) beschriebenen Anweisungen befolgen, um dieses Problem zu beheben.  

   > [!TIP]
   > Wenn in den Ergebnissen der Voraussetzungsprüfung in SWPM angezeigt wird, dass die Bedingung für die Tauschgröße nicht erfüllt werden, können Sie die Tauschgröße anpassen, indem Sie zu „Arbeitsplatz > Systemeigenschaften > Leistungseinstellungen > Erweitert > Virtueller Arbeitsspeicher > Ändern“ navigieren.  

4. Konfigurieren Sie eine SAP-Clusterressource und den `SAP-SID-IP`-Testport mithilfe von PowerShell. Führen Sie diese Konfiguration wie im Artikel [Konfigurieren des Testports](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761) beschrieben auf einem der SAP ASCS-/SCS-Clusterknoten aus.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installieren einer ASCS/SCS-Instanz auf dem zweiten ASCS/SCS-Clusterknoten

1. Installieren Sie eine ASCS/SCS-Instanz auf dem zweiten ASCS/SCS-Clusterknoten. Starten Sie das SAP SWPM-Installationstool, und navigieren Sie zu **Produkt** > **DBMS** > Installation > Application Server ABAP (or Java) > High-Availability System > ASCS/SCS instance > Additional cluster node (Anwendungsserver ABAP (oder Java) > Hochverfügbarkeitssystem > ASCS-/SCS-Instanz > Zusätzlicher Clusterknoten).  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installieren einer DBMS-Instanz und von SAP-Anwendungsservern

Schließen Sie die SAP-Installation ab, indem Sie Folgendes installieren:

   * eine DBMS-Instanz  
   * einen primären SAP-Anwendungsserver  
   * einen zusätzlichen SAP-Anwendungsserver  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testen des SAP ASCS-/SCS-Instanzfailovers 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Failover von Clusterknoten A auf Clusterknoten B und zurück
In diesem Testszenario verweisen wir auf den Clusterknoten „sapascs1“ als Knoten A und auf Clusterknoten „sapascs2“ als Clusterknoten B.

1. Vergewissern Sie sich, dass die Clusterressourcen auf Knoten A ausgeführt werden. ![Abbildung 1: Auf Knoten A ausgeführte Windows Server-Failoverclusterressourcen vor dem Failovertest](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Starten Sie den Clusterknoten A. Die SAP-Clusterressourcen werden auf Clusterknoten B verschoben. ![Abbildung 2: Auf Knoten B ausgeführte Windows Server-Failoverclusterressourcen nach dem Failovertest](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Sperreintragstest

1. Überprüfen Sie, ob der Enqueue Replication Server-Server (Warteschlangenreplikationsserver, ERS) aktiv ist.  
2. Melden Sie sich beim SAP-System an, führen Sie Transaktion SU01 aus, und öffnen Sie eine Benutzer-ID im Änderungsmodus. Dadurch wird ein SAP-Sperreintrag generiert.  
3. Wenn Sie beim SAP-System angemeldet sind, können Sie den Sperreintrag anzeigen, indem Sie zu Transaktion ST12 navigieren.  
4. Führen Sie einen Failover von den ASCS-Ressourcen auf Clusterknoten A auf Clusterknoten B aus.  
5. Vergewissern Sie sich, dass der vor dem Failover für die SAP ASCS-/SCS-Clusterressourcen generierte Sperreintrag beibehalten wird.  

![Abbildung 3: Sperreintrag wird nach dem Failovertest beibehalten](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Weitere Informationen finden Sie unter [Problembehandlung für das Enqueue-Failover in ASCS mit ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS).
## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zum Erreichen von Hochverfügbarkeit und zum Planen der Notfallwiederherstellung für SAP 
* HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
