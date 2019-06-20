---
title: SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SAP NetWeaver-HA auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz installieren.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1229b7f9e2a430a663a3e78bb457c03cf4a4a590
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60714427"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

In diesem Artikel wird beschrieben, wie Sie ein SAP-Hochverfügbarkeitssystem in Azure für das Clustering einer SAP ASCS/SCS-Instanz unter Verwendung eines Windows Server-Failoverclusters und eines freigegebenen Datenträgers für den Cluster installieren und konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die folgenden Dokumente, bevor Sie mit der Installation beginnen:

* [Architekturleitfaden: Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure][sap-high-availability-guide-wsfc-shared-disk]

* [Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-infrastructure-wsfc-shared-disk]

Das DBMS-Setup wird in diesem Artikel nicht beschrieben, da die Einrichtung vom verwendeten DBMS abhängt. Es wird davon ausgegangen, dass auf Hochverfügbarkeit bezogene Aspekte des DBMS mit den Funktionen verwaltet werden, die verschiedene DBMS-Hersteller für Azure unterstützen. Beispiele hierfür sind AlwaysOn oder Datenbankspiegelung für SQL Server und Oracle Data Guard für Oracle-Datenbanken. In dem Szenario in diesem Artikel haben wir keinen zusätzlichen Schutz für das DBMS hinzugefügt.

Es sind keine Besonderheiten zu berücksichtigen, wenn unterschiedliche DBMS-Dienste mit einer SAP ASCS/SCS-Clusterkonfiguration in Azure interagieren.

> [!NOTE]
> Der Installationsvorgang von SAP NetWeaver ABAP-, Java- und ABAP+Java-Systemen ist nahezu identisch. Der wichtigste Unterschied ist, dass ein SAP ABAP-System eine ASCS-Instanz hat. Das SAP Java-System hat eine SCS-Instanz. Das SAP ABAP+Java-System hat eine ASCS- und eine SCS-Instanz, die in der gleichen Microsoft-Failoverclustergruppe ausgeführt werden. Unterschiede bei der Installation der einzelnen SAP NetWeaver-Installationsstapel werden explizit angegeben. Sie können davon ausgehen, dass alle anderen Teile gleich sind.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installieren von SAP mit einer ASCS/SCS-Hochverfügbarkeitsinstanz

> [!IMPORTANT]
> Achten Sie darauf, nicht die Auslagerungsdatei auf von SIOS DataKeeper gespiegelten Volumes zu platzieren. DataKeeper unterstützt keine gespiegelten Volumes. Belassen Sie die Auslagerungsdatei auf dem temporären Laufwerk D eines virtuellen Azure-Computers (dies ist auch die Standardeinstellung). Wenn sie sich noch nicht dort befindet, verschieben Sie die Windows-Auslagerungsdatei auf Laufwerk D des virtuellen Azure-Computers.
>
>

Das Installieren von SAP mit einer ASCS/SCS-Instanz mit hoher Verfügbarkeit umfasst die folgenden Aufgaben:

* Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz
* Installieren des ersten SAP-Clusterknotens
* Ändern des SAP-Profils der ASCS/SCS-Instanz
* Hinzufügen eines Testports
* Öffnen des Windows-Firewall-Testports

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz

1. Erstellen Sie im Windows-DNS-Manager einen DNS-Eintrag für den virtuellen Hostnamen der ASCS/SCS-Instanz.

   > [!IMPORTANT]
   > Die IP-Adresse, die Sie dem virtuellen Hostnamen der ASCS/SCS-Instanz zuweisen, muss mit der IP-Adresse identisch sein, die Sie dem Azure Load Balancer zugeordnet haben (\<SID\>-lb-ascs).  
   >
   >

   Die IP-Adresse des virtuellen Hostnamens der SAP ASCS/SCS-Instanz (pr1-ascs-sap) ist mit der IP-Adresse des Azure Load Balancers (pr1-lb-ascs) identisch.

   ![Abbildung 1: Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse][sap-ha-guide-figure-3046]

   _**Abbildung 1:** Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse_

2. Wählen Sie zum Definieren der IP-Adresse, die dem virtuellen Hostnamen zugewiesen ist, die Option **DNS-Manager** > **Domäne**.

   ![Abbildung 2: Neuer virtueller Name und TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration][sap-ha-guide-figure-3047]

   _**Abbildung 2:** Neuer virtueller Name und TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installieren des ersten SAP-Clusterknotens

1. Führen Sie die Option für den ersten Clusterknoten auf Clusterknoten A aus, z.B. auf dem Host „pr1-ascs-0“.
2. Wählen Sie Folgendes aus, um für den internen Azure Load Balancer die Standardports beizubehalten:

   * **ABAP-System**: **ASCS**-Instanznummer **00**
   * **Java-System**: **SCS**-Instanznummer **01**
   * **ABAP + Java-System**: **ASCS**-Instanznummer **00** und **SCS**-Instanznummer **01**

   Um andere Instanznummern als „00“ für die ABAP ASCS-Instanz und „01“ für die Java SCS-Instanz zu verwenden, ändern Sie zunächst die internen standardmäßigen Lastenausgleichsregeln von Azure Load Balancer. Weitere Informationen finden Sie unter [Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer][sap-ha-guide-8.9].

Die nächsten Aufgaben sind in der SAP-Dokumentation für die Standardinstallation nicht beschrieben.

> [!NOTE]
> Die SAP-Installationsdokumentation beschreibt, wie der erste ASCS/SCS-Clusterknoten installiert wird.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändern des SAP-Profils der ASCS/SCS-Instanz

Fügen Sie zuerst einen neuen Profilparameter hinzu. Dieser Profilparameter verhindert das Schließen von Verbindungen zwischen SAP-Workprozessen und dem Enqueue-Server, wenn diese sich zu lange im Leerlauf befinden. Dieses Problem wurde bereits unter [Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten für eine SAP ASCS/SCS-Instanz][sap-ha-guide-8.11] erwähnt. In diesem Abschnitt haben wir auch zwei Änderungen an grundlegenden TCP/IP-Verbindungsparametern vorgenommen. In einem zweiten Schritt müssen Sie den Enqueue-Server für das Senden eines `keep_alive`-Signals konfigurieren, damit die Verbindungen nicht den Schwellenwert für den Leerlauf des internen Azure Load Balancers erreichen.

So ändern Sie das SAP-Profil der ASCS/SCS-Instanz

1. Fügen Sie diesen Profilparameter dem Profil der SAP ASCS/SCS-Instanz hinzu:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Bei unserem Beispiel lautet der Pfad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Beispiel für das Profil einer SAP SCS-Instanz und den zugehörigen Pfad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Starten Sie die SAP ASCS/SCS-Instanz neu, um die Änderungen zu übernehmen.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Hinzufügen eines Testports

Nutzen Sie die Testfunktionalität des internen Lastenausgleichs, damit die gesamte Clusterkonfiguration mit Azure Load Balancer funktioniert. Der interne Azure Load Balancer verteilt in der Regel die eingehende Workload gleichmäßig auf die beteiligten virtuellen Computer.

 Allerdings funktioniert dies bei einigen Clusterkonfigurationen nicht, da nur eine Instanz aktiv ist. Die andere Instanz ist passiv und kann daher keine Workload annehmen. Eine Testfunktion ist hilfreich, wenn der interne Azure Load Balancer die Workload nur einer aktiven Instanz zuweist. Mit der Testfunktion kann der internen Load Balancer erkennen, welche Instanzen aktiv sind, und dann nur dieser Instanz Workload zuweisen.

So fügen Sie einen Testport hinzu

1. Überprüfen Sie den aktuellen **ProbePort**-Wert, indem Sie den folgenden PowerShell-Befehl ausführen:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Führen Sie den Befehl auf einem der virtuellen Computer in der Clusterkonfiguration aus.

2. Definieren Sie einen Testport. Die Standardnummer für den Testport ist 0. In unserem Beispiel verwenden wir als Testport „62000“.

   ![Abbildung 3: Der Testport der Clusterkonfiguration ist standardmäßig 0.][sap-ha-guide-figure-3048]

   _**Abbildung 3:** Der Standardtestport der Clusterkonfiguration ist 0._

   Die Portnummer ist in Azure Resource Manager-Vorlagen für SAP definiert. Sie können die Portnummer in PowerShell zuweisen.

   Führen Sie das folgende PowerShell-Skript aus, um die PowerShell-Variablen für Ihre Umgebung zu aktualisieren und einen neuen ProbePort-Wert für die Clusterressource SAP \<SID\> IP festzulegen:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Überprüfen Sie, nachdem Sie die Clusterrolle SAP \<SID\> online geschaltet haben, ob **ProbePort** auf den neuen Wert festgelegt wurde.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Nach dem Ausführen des Skripts werden Sie aufgefordert, die SAP-Clustergruppe neu zu starten, um die Änderungen zu aktivieren.

   ![Abbildung 4: Testen des Clusterports nach dem Festlegen des neuen Werts][sap-ha-guide-figure-3049]

   _**Abbildung 4:** Testen des Clusterports nach dem Festlegen des neuen Werts_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öffnen des Windows-Firewall-Testports

Öffnen Sie einen Windows-Firewall-Testport auf beiden Clusterknoten. Verwenden Sie das folgende Skript, um einen Windows-Firewall-Testport zu öffnen. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** wurde auf **62000** festgelegt. Sie können nun auf die Dateifreigabe „\\\ascsha-clsap\sapmnt“ von anderen Hosts zugreifen, z.B. „ascsha-dbas“.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installieren der Datenbankinstanz

Führen Sie für die Installation der Datenbankinstanz die in der SAP-Installationsdokumentation beschriebene Vorgehensweise durch.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installieren des zweiten Clusterknotens

Führen Sie zum Installieren des zweiten Clusters die im SAP-Installationshandbuch beschriebenen Schritte aus.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Ändern des Starttyps der Windows-Dienstinstanz für SAP ERS

Ändern Sie den Starttyp der Windows-Dienste für SAP ERS auf beiden Clusterknoten in **Automatisch (Verzögerter Start)** .

![Abbildung 5: Ändern des Starttyps der SAP ERS-Instanz in „Automatisch (Verzögerter Start)“][sap-ha-guide-figure-3050]

_**Abbildung 5:** Ändern des Starttyps der SAP ERS-Instanz in „Automatisch (Verzögerter Start)“_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installieren des primären SAP-Anwendungsservers

Installieren Sie die PAS-Instanz (primärer Anwendungsserver) \<SID\>-di-0 auf dem virtuellen Computer, den Sie als Host für den PAS vorgesehen haben. Es gibt keine Abhängigkeiten in Azure. Es gibt keine DataKeeper-spezifischen Einstellungen.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installieren des zusätzlichen SAP-Anwendungsservers

Installieren Sie einen zusätzlichen SAP-Anwendungsserver (Additional Application Server, AAS) auf allen virtuellen Computern, die Sie als Hosts von SAP-Anwendungsserverinstanzen festgelegt haben. Beispielsweise auf \<SID\>-di-1 bis \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Die Installation eines SAP NetWeaver-Systems mit hoher Verfügbarkeit ist hiermit abgeschlossen. Als Nächstes stehen die Failovertests an.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testen des Failovers der SAP ASCS/SCS-Instanz und der SIOS-Replikation
Mit dem Failovercluster-Manager und dem SIOS DataKeeper-Tool für die Verwaltung und Konfiguration können Sie das Failover für eine SAP ASCS/SCS-Instanz und die SIOS-Datenträgerreplikation ganz einfach testen und überwachen.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Die SAP ASCS/SCS-Instanz wird auf Clusterknoten A ausgeführt

Die Clustergruppe „SAP PR1“ wird auf dem Clusterknoten A ausgeführt, z.B. auf „pr1-ascs-0“. Weisen Sie den freigegebenen Datenträger S, der Teil der Clustergruppe SAP PR1 ist und von der ASCS/SCS-Instanz verwendet wird, dem Clusterknoten A zu. 

![Abbildung 6: Failovercluster-Manager: Die SAP-Clustergruppe \<SID\> wird auf Clusterknoten A ausgeführt.][sap-ha-guide-figure-5000]

_**Abbildung 6:** Failovercluster-Manager: Die SAP-Clustergruppe \<SID\> wird auf Clusterknoten A ausgeführt._

Im SIOS DataKeeper-Tool für die Verwaltung und Konfiguration können Sie erkennen, dass die Daten auf dem freigegebenen Datenträger synchron vom Quellvolume S auf Clusterknoten A auf das Zielvolume S auf Clusterknoten B repliziert werden, also z.B. von „pr1-ascs-0 [10.0.0.40]“ nach „pr1-Asconas-1 [10.0.0.41]“.

![Abbildung 7: Replizieren des lokalen Volumes von Clusterknoten A auf Clusterknoten B in SIOS DataKeeper][sap-ha-guide-figure-5001]

_**Abbildung 7:** Replizieren des lokalen Volumes von Clusterknoten A auf Clusterknoten B in SIOS DataKeeper_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover von Knoten A auf Knoten B

1. Wählen Sie eine dieser Optionen, um ein Failover der SAP-Clustergruppe \<SID\> von Clusterknoten A auf Clusterknoten B auszulösen:
   - Failovercluster-Manager  
   - Failovercluster-PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Starten Sie Clusterknoten A unter dem Windows-Gastbetriebssystem neu. Dadurch wird ein automatisches Failover der SAP-Clustergruppe \<SID\> von Knoten A auf Knoten B ausgelöst.  
3. Starten Sie Clusterknoten A im Azure-Portal neu. Dadurch wird ein automatisches Failover der SAP-Clustergruppe \<SID\> von Knoten A auf Knoten B ausgelöst.  
4. Starten Sie Clusterknoten A mit Azure PowerShell neu. Dadurch wird ein automatisches Failover der SAP-Clustergruppe \<SID\> von Knoten A auf Knoten B ausgelöst.

   Nach dem Failover wird die SAP-Clustergruppe \<SID\> auf dem Clusterknoten B ausgeführt, z.B. auf „pr1-ascs-1“.

   ![Abbildung 8: Failovercluster-Manager: Die SAP-Clustergruppe \<SID\> wird auf Clusterknoten B ausgeführt.][sap-ha-guide-figure-5002]

   _**Abbildung 8**: Failovercluster-Manager: Die SAP-Clustergruppe \<SID\> wird auf Clusterknoten B ausgeführt_.

   Der freigegebene Datenträger wird jetzt auf Clusterknoten B bereitgestellt. SIOS DataKeeper repliziert Daten vom Quellvolume S auf Clusterknoten B auf das Zielvolume S auf Clusterknoten A, also z.B. von „pr1-ascs-1 [10.0.0.41]“ nach „pr1-ascs-0 [10.0.0.40]“.

   ![Abbildung 9: SIOS DataKeeper repliziert das lokale Volume von Clusterknoten B auf Clusterknoten A.][sap-ha-guide-figure-5003]

   _**Abbildung 9:** SIOS DataKeeper repliziert das lokale Volume von Clusterknoten B auf Clusterknoten A._
