---
title: Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure | Microsoft-Dokumentation
description: Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b204aa508370c62aaf33688aeb7ec63d3f8f1b0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019360"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-file-share-on-azure"></a>Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure

> ![Windows-Betriebssystem][Logo_Windows] Windows
>

Sie können mehrere virtuelle IP-Adressen mithilfe eines [internen Azure-Lastenausgleichs][load-balancer-multivip-overview] verwalten. 

Falls Sie über eine SAP-Bereitstellung verfügen, können Sie einen internen Lastenausgleich vornehmen, um eine Windows-Clusterkonfiguration für SAP Central Services-Instanzen (ASCS/SCS) zu erstellen.

Dieser Artikel behandelt den Übergang von einer einzelnen (A)SCS-Installation zu einer SAP-Multi-SID-Konfiguration durch Installation zusätzlicher SAP (A)SCS-Clusterinstanzen auf einem vorhandenen WSFC-Cluster (Windows Server-Failoverclustering) mithilfe der **Dateifreigabe**. Wenn dieser Vorgang abgeschlossen ist, haben Sie einen SAP Multi-SID-Cluster konfiguriert.

> [!NOTE]
>
> Diese Funktion ist nur im Azure Resource Manager-Bereitstellungsmodell verfügbar.
>
>Es besteht eine Höchstgrenze für die Anzahl privater Front-End-IPs für jeden internen Azure-Lastenausgleich.
>
>Dies bedeutet, dass die maximale Anzahl von SAP ASCS/SCS-Instanzen auf einem WSFC-Cluster gleich der maximalen Anzahl privater Front-End-IPs pro internem Azure-Lastenausgleichsmodul ist.
>
> Die Verwendung der in dieser Dokumentation eingeführten Konfiguration wird noch nicht für [Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md) unterstützt.
> 

Weitere Informationen zu den Grenzwerten für Lastenausgleichsmodule finden Sie im Abschnitt „Private Front-End-IP pro Lastenausgleichsmodul“ unter [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager]. Erwägen Sie außerdem die Verwendung der [Azure Load Balancer Standard-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) anstelle der einfachen SKU des Azure Load Balancers.

## <a name="prerequisites"></a>Voraussetzungen

Sie haben bereits einen WSFC-Cluster für die Verwendung einer SAP ASCS/SCS-Instanz unter Verwendung einer **Dateifreigabe** konfiguriert, wie in diesem Diagramm dargestellt.

![Abbildung 1: Bereitstellung einer SAP ASCS/SCS-Instanz und von SOFS in zwei Clustern][sap-ha-guide-figure-8007]

_**Abbildung 1:** Bereitstellung einer SAP ASCS/SCS-Instanz und von SOFS in zwei Clustern_

> [!IMPORTANT]
> Das Setup muss die folgenden Bedingungen erfüllen:
> * Die SAP ASCS/SCS-Instanzen müssen sich den gleichen WSFC-Cluster teilen.
> * Unterschiedliche Dateifreigaben von globalen SAP-Hosts, die verschiedenen SAP-SIDs zugeordnet sind, müssen denselben SOFS-Cluster verwenden.
> * Jede SID eines Datenbank-Verwaltungssystems (DBMS) muss über ihren eigenen dedizierten WSFC-Cluster verfügen.
> * SAP-Anwendungsserver, die zur gleichen SAP-System-SID gehören, müssen eigene dedizierte VMs aufweisen.
> * Eine Kombination von Enqueue Replication Server 1 und Enqueue Replication Server 2 im gleichen Cluster wird nicht unterstützt.  

## <a name="sap-ascsscs-multi-sid-architecture-with-file-share"></a>SAP ASCS/SCS-Instanzen mit Multi-SID-Architektur und Dateifreigabe

Das Ziel besteht darin, mehrere SAP Advanced Business Application Programming- (ASCS) oder SAP Java-Clusterinstanzen (SCS) im selben WSFC-Cluster zu installieren, wie hier dargestellt: 

![Abbildung 2: SAP-Multi-SID-Konfiguration in zwei Clustern][sap-ha-guide-figure-8008]

_**Abbildung 2:** SAP-Multi-SID-Konfiguration in zwei Clustern_

Die Vorgehensweise für die Installation eines zusätzlichen **SAP-\<SID2>** -Systems entspricht der für ein einzelnes \<SID>-System. Für ASCS/SCS-Cluster sowie im SOFS-Dateifreigabecluster sind zwei zusätzliche Vorbereitungsschritte erforderlich.

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a>Vorbereiten der Infrastruktur für ein SAP-Multi-SID-Szenario

### <a name="prepare-the-infrastructure-on-the-domain-controller"></a>Vorbereiten der Infrastruktur auf dem Domänencontroller

Erstellen Sie die Domänengruppe **\<Domain>\SAP_\<SID2>_GlobalAdmin**, und legen Sie für \<SID2> beispielsweise „PR2“ fest. Der Name der Domänengruppe lautet „\<Domain>\SAP_PR2_GlobalAdmin“.

### <a name="prepare-the-infrastructure-on-the-ascsscs-cluster"></a>Vorbereiten der Infrastruktur im ASCS/SCS-Cluster

Sie müssen die Infrastruktur auf dem vorhandenen ASCS/SCS-Cluster für eine zweite SAP-\<SID> vorbereiten. Hierfür sind folgende Schritte erforderlich:

* Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz auf dem DNS-Server.
* Hinzufügen einer IP-Adresse zu einem vorhandenen internen Azure-Lastenausgleich mit PowerShell.

Diese Schritte werden unter [Vorbereiten der Infrastruktur auf ein SAP-Multi-SID-Szenario][sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare] beschrieben.


### <a name="prepare-the-infrastructure-on-an-sofs-cluster-by-using-the-existing-sap-global-host"></a>Vorbereiten der Infrastruktur in einem SOFS-Cluster unter Verwendung des vorhandenen globalen SAP-Hosts

Sie können den vorhandenen \<SAPGlobalHost> und Volume1 des ersten SAP-\<SID1>-Systems wiederverwenden.

![Abbildung 3: Multi-SID-SOFS ist identisch mit dem Namen des globalen SAP-Hosts][sap-ha-guide-figure-8014]

_**Abbildung 3:** Multi-SID-SOFS ist identisch mit dem Namen des globalen SAP-Hosts_

> [!IMPORTANT]
>Für das zweite **SAP-\<SID2>** -System werden ebenfalls Volume1 und derselbe Netzwerkname für **\<SAPGlobalHost>** verwendet.
>Da Sie **SAPMNT** bereits als Freigabename für verschiedene SAP-Systeme festgelegt haben, müssen Sie zum Wiederverwenden des Netzwerknamens **\<SAPGlobalHost>** dasselbe **Volume1** verwenden.
>
>Der Dateipfad für den globalen \<SID2>-Host lautet „C:\ClusterStorage\\**Volume1**\usr\sap\<SID2>\SYS\\.“.
>

Für das \<SID2>-System müssen Sie den Ordner „..\SYS\.“ des globalen SAP-Hosts im SOFS-Cluster vorbereiten.

Um den globalen SAP-Host für die \<SID2>-Instanz vorzubereiten, führen Sie das folgende PowerShell-Skript aus:


```powershell
##################
# SAP multi-SID
##################

$SAPSID2 = "PR2"
$DomainName2 = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName2 = "$DomainName2\SAP_" + $SAPSID2 + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSCluster2Node1 = "ja1-ascs-0"
$ASCSCluster2Node2 = "ja1-ascs-1"

# Define the SAP ASCS/SCS cluster node computer objects
$ASCSCluster2ObjectNode1 = "$DomainName2\$ASCSCluster2Node1$"
$ASCSCluster2ObjectNode2 = "$DomainName2\$ASCSCluster2Node2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder2 = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID2\SYS"
New-Item -Path $SAPGlobalFolder2 -ItemType Directory

# Add permissions for the SAP SID2 system
Grant-SmbShareAccess -Name sapmnt -AccountName $SAPSIDGlobalAdminGroupName2, $ASCSCluster2ObjectNode1, $ASCSCluster2ObjectNode2 -AccessRight Full -Force


$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName2,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

### <a name="prepare-the-infrastructure-on-the-sofs-cluster-by-using-a-different-sap-global-host"></a>Vorbereiten der Infrastruktur im SOFS-Cluster unter Verwendung eines anderen globalen SAP-Hosts

Sie können den zweiten SOFS-Cluster konfigurieren (z. B. für die zweite SOFS-Clusterrolle mit **\<SAPGlobalHost2>** und einem anderen **Volume2** für die zweite **\<SID2>** ).

![Abbildung 4: Multi-SID-SOFS ist identisch mit dem Namen des globalen SAP-Hosts 2][sap-ha-guide-figure-8015]

_**Abbildung 4:** Multi-SID-SOFS ist identisch mit dem Namen des globalen SAP-Hosts 2_

Um die zweite SOFS-Rolle mit \<SAPGlobalHost2> zu erstellen, führen Sie dieses PowerShell-Skript aus:

```powershell
# Create SOFS with SAP Global Host Name 2
$SAPGlobalHostName = "sapglobal2"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

Erstellen Sie als zweites Volume **Volume2**. Führen Sie das folgende PowerShell-Skript aus:

```powershell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR2 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```

![Abbildung 5: Zweites Volume2 im Failovercluster-Manager][sap-ha-guide-figure-8016]

_**Abbildung 5:** Zweites Volume2 im Failovercluster-Manager_

Erstellen Sie einen Ordner für den globalen SAP-Host für die zweite \<SID2>, und legen Sie die Dateisicherheit fest.

Führen Sie das folgende PowerShell-Skript aus:

```powershell
# Create a folder for <SID2> on a second Volume2 and set file security
$SAPSID = "PR2"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume2\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume2\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the file security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

Um eine SAPMNT-Dateifreigabe auf „Volume2“ mit dem Hostnamen des *\<SAPGlobalHost2>* für die zweite SAP-\<SID2> zu erstellen, starten Sie den Assistenten **Dateifreigabe hinzufügen** im Failovercluster-Manager.

Klicken Sie mit der rechten Maustaste auf die SOFS-Clustergruppe **sapglobal2**, und wählen Sie dann **Dateifreigabe hinzufügen** aus.

![Abbildung 6: Starten des Assistenten „Dateifreigabe hinzufügen“][sap-ha-guide-figure-8017]

_**Abbildung 6:** Starten des Assistenten „Dateifreigabe hinzufügen“_

<br>

![Abbildung 7: Auswählen von „SMB-Freigabe – Schnell“][sap-ha-guide-figure-8018]

_**Abbildung 7:** Auswählen von „SMB-Freigabe – Schnell“_

<br>

![Abbildung 8: Auswählen von „sapglobalhost2“ und Eingeben des Pfads auf Volume2][sap-ha-guide-figure-8019]

_**Abbildung 8:** Auswählen von „sapglobalhost2“ und Eingeben des Pfads auf Volume2_

<br>

![Abbildung 9: Festlegen des Dateifreigabenamens auf „sapmnt“][sap-ha-guide-figure-8020]

_**Abbildung 9:** Festlegen des Dateifreigabenamens auf „sapmnt“_

<br>

![Abbildung 10: Deaktivieren aller Einstellungen][sap-ha-guide-figure-8021]

_**Abbildung 10:** Deaktivieren aller Einstellungen_

<br>

Weisen Sie folgenden Elementen *Vollzugriff* auf Dateien und die Freigabe „sapmnt“ zu:
* Domänenbenutzergruppe **SAP_\<SID>_GlobalAdmin**
* Computerobjekt der ASCS/SCS-Clusterknoten **ascs-1$** und **ascs-2$**

![Abbildung 11: Zuweisen von Vollzugriffsberechtigungen für Benutzergruppen und Computerkonten][sap-ha-guide-figure-8022]

_**Abbildung 11:** Zuweisen von Vollzugriffsberechtigungen für Benutzergruppen und Computerkonten_

<br>

![Abbildung 12: Auswählen von „Erstellen“][sap-ha-guide-figure-8023]

_**Abbildung 12:** Auswählen von „Erstellen“_

<br>

![Abbildung 13: Die zweite Dateifreigabe „sapmnt“, die an den Host sapglobal2 und Volume2 gebunden ist, wird erstellt][sap-ha-guide-figure-8024]

_**Abbildung 13:** Die zweite Dateifreigabe „sapmnt“, die an den Host sapglobal2 und Volume2 gebunden ist, wird erstellt_

<br>

## <a name="install-sap-netweaver-multi-sid"></a>Installieren von SAP NetWeaver-Multi-SID

### <a name="install-sap-sid2-ascsscs-and-ers-instances"></a>Installieren von SAP-\<SID2>-ASCS/SCS- und -ERS-Instanzen

Führen Sie die gleichen Installations- und Konfigurationsschritte aus, die weiter oben für die Installation einer einzelnen SAP-\<SID> beschrieben wurden.

### <a name="install-dbms-and-sap-application-servers"></a>Installieren von DBMS und SAP-Anwendungsservern
Installieren Sie DBMS und SAP-Anwendungsserver nach der oben beschriebenen Vorgehensweise.

## <a name="next-steps"></a>Nächste Schritte

* [Installieren einer ASCS/SCS-Instanz in einem Failovercluster ohne freigegebene Datenträger][sap-official-ha-file-share-document]: offizielle SAP-Richtlinien für eine Dateifreigabe mit Hochverfügbarkeit

* [Direkte Speicherplätze in Windows Server 2016][s2d-in-win-2016]

* [Übersicht über Dateiserver mit horizontaler Skalierung für Anwendungsdaten][sofs-overview]

* [Neuerungen beim Speicher in Windows Server 2016][new-in-win-2016-storage]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[sap-installation-guides]:http://service.sap.com/instguides
[sap-installation-guides-file-share]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md#25e358f8-92e5-4e8d-a1e5-df7580a39cb0
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
