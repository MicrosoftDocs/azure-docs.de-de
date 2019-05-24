---
title: Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure | Microsoft-Dokumentation
description: Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 9a23f13947c4c7a77460ff389861e1dcc1de3c7f
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992123"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Es gibt zwei Möglichkeiten zum Einrichten eines Pacemaker-Clusters in Azure. Sie können entweder einen Umgrenzungs-Agent verwenden, über den der Neustart eines fehlerhaften Knotens über die Azure-APIs erfolgt, oder Sie können ein SBD-Gerät verwenden.

Für das SBD-Gerät ist mindestens ein zusätzlicher virtueller Computer erforderlich, der als iSCSI-Zielserver dient und ein SBD-Gerät bereitstellt. Diese iSCSI-Zielserver können jedoch für andere Pacemaker-Cluster freigegeben werden. Die Verwendung eines SBD-Geräts bietet den Vorteil einer kürzeren Failoverzeit. Bei lokaler Verwendung von SBD-Geräten sind zudem keine Änderungen am Betrieb des Pacemaker-Clusters erforderlich. Sie können für einen Pacemaker-Cluster bis zu drei SBD-Geräte verwenden, um ein SBD-Gerät verfügbar zu machen (z.B. während des Betriebssystempatchings des iSCSI-Zielservers). Wenn Sie pro Pacemaker mehrere SBD-Geräte verwenden möchten, müssen Sie sicherstellen, dass mehrere iSCSI-Zielserver bereitgestellt werden und ein SBD über die einzelnen iSCSI-Zielserver verbunden wird. Es wird empfohlen, entweder ein SBD-Gerät oder drei SBD-Geräte zu verwenden. Wenn Sie nur zwei SBD-Geräte konfigurieren und eines davon nicht verfügbar ist, kann Pacemaker einen Clusterknoten nicht automatisch umgrenzen. Wenn Sie in der Lage sein möchten, einen Clusterknoten bei einem inaktiven iSCSI-Zielserver zu umgrenzen, müssen Sie drei SBD-Geräte und folglich drei iSCSI-Zielserver verwenden.

Wenn Sie in keinen zusätzlichen virtuellen Computer investieren möchten, können Sie auch den Azure Fence Agent verwenden. Der Nachteil dabei ist, dass ein Failover zwischen 10 und 15 Minuten dauern kann, wenn beim Beenden einer Ressource Fehler auftreten oder keine Kommunikation mehr zwischen den Clusterknoten möglich ist.

![Übersicht über Pacemaker unter SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Bei der Planung und Implementierung von Linux Pacemaker-Clusterknoten und SBD-Geräten ist es für die Gesamtzuverlässigkeit der gesamten Clusterkonfiguration entscheidend, dass das Routing zwischen den beteiligten VMs und den VMs, die die SBD-Geräte hosten, nicht durch andere Geräte wie [NVAs](https://azure.microsoft.com/solutions/network-appliances/) verläuft. Andernfalls können Probleme und Wartungsereignisse mit der NVA negative Auswirkungen auf die Stabilität und Zuverlässigkeit der gesamten Clusterkonfiguration haben. Um derartige Probleme zu vermeiden, definieren Sie keine Routingregeln von NVAs oder [benutzerdefinierte Routingregeln](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview), die den Datenverkehr zwischen Clusterknoten und SBD-Geräten durch NVAs und ähnliche Geräte leiten, wenn Sie Linux Pacemaker-Clusterknoten und SBD-Geräte planen und einsetzen. 
>

## <a name="sbd-fencing"></a>SBD-Umgrenzung

Gehen Sie folgendermaßen vor, wenn Sie ein SBD-Gerät zur Umgrenzung verwenden möchten.

### <a name="set-up-iscsi-target-servers"></a>Einrichten von iSCSI-Zielservern

Sie müssen zunächst die virtuellen Computer für das iSCSI-Ziel erstellen. iSCSI-Zielserver können für mehrere Pacemaker-Cluster freigegeben werden.

1. Stellen Sie neue virtuelle Computer unter SLES 12 SP1 oder höher bereit, und stellen Sie über SSH eine Verbindung mit den Computern her. Die Computer müssen nicht groß sein. Eine VM-Größe wie Standard_E2s_v3 oder Standard_D2s_v3 ist ausreichend. Stellen Sie sicher, dass Sie für den Betriebssystemdatenträger Storage Premium verwenden.

Führen Sie auf allen **virtuellen Computern des iSCSI-Ziels** folgende Befehle aus.

1. Aktualisieren Sie SLES.

   <pre><code>sudo zypper update
   </code></pre>

1. Entfernen Sie Pakete.

   Um ein bekanntes Problem mit targetcli und SLES 12 SP3 zu vermeiden, deinstallieren Sie die folgenden Pakete. Fehlermeldungen bezüglich nicht auffindbarer Pakete können Sie ignorieren.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installieren Sie die iSCSI-Zielpakete.

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Aktivieren Sie den iSCSI-Zieldienst.

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Erstellen des iSCSI-Geräts auf dem iSCSI-Zielserver

Führen Sie auf allen **virtuellen Computern des iSCSI-Ziels** folgende Befehle aus, um die iSCSI-Datenträger für die von Ihren SAP-Systemen verwendeten Cluster zu erstellen. Im folgenden Beispiel werden SBD-Geräte für mehrere Cluster erstellt. Es zeigt, wie Sie einen iSCSI-Zielserver für mehrere Cluster verwenden würden. Die SBD-Geräte werden auf dem Betriebssystemdatenträger platziert. Stellen Sie sicher, dass Sie über ausreichend Speicherplatz verfügen.

**`nfs`** wird zum Identifizieren des NFS-Clusters verwendet, **ascsnw1** zum Identifizieren des ASCS-Clusters von **NW1** und **dbnw1** zum Identifizieren des Datenbankclusters von **NW1**. **nfs-0** und **nfs-1** sind die Hostnamen der NFS-Clusterknoten, **nw1-xscs-0** und **nw1-xscs-1** die Hostnamen des ASCS-Clusterknotens von **NW1** und **nw1-db-0** und **nw1-db-1** die Hostnamen der Clusterknoten der Datenbank. Ersetzen Sie diese durch die Hostnamen Ihrer Clusterknoten und die SID Ihres SAP-Systems.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Sie können überprüfen, ob alles ordnungsgemäß eingerichtet wurde

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Einrichten des SBD-Geräts

Stellen Sie im Cluster eine Verbindung mit dem iSCSI-Gerät her, das im letzten Schritt erstellt wurde.
Führen Sie die folgenden Befehle für die zu erstellenden Knoten des neuen Clusters aus.
Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Stellen Sie eine Verbindung mit den iSCSI-Geräten her.

   Aktivieren Sie zunächst die iSCSI- und SBD-Dienste.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Ändern Sie den Initiatornamen auf dem ersten Knoten.

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändern Sie den Inhalt der Datei so, dass er den ACLs entspricht, die Sie beim Erstellen des iSCSI-Geräts auf dem iSCSI-Zielserver verwendet haben, z.B. bei dem NFS-Server.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Ändern Sie den Initiatornamen auf dem zweiten Knoten.

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändern Sie den Inhalt der Datei so, dass er den ACLs entspricht, die Sie beim Erstellen des iSCSI-Geräts auf dem iSCSI-Zielserver verwendet haben.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Starten Sie den iSCSI-Dienst neu.

   Starten Sie den iSCSI-Dienst neu, um die Änderung zu übernehmen.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Stellen Sie eine Verbindung mit den iSCSI-Geräten her. Im Beispiel unten ist 10.0.0.17 die IP-Adresse des iSCSI-Zielservers und 3260 der Standardport. <b>iqn.2006-04.nfs.local:nfs</b> ist einer der Zielnamen, die aufgeführt werden, wenn Sie den ersten nachfolgenden Befehl ausführen (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Vergewissern Sie sich, dass die iSCSI-Geräte verfügbar sind, und notieren Sie den Gerätenamen („/dev/sde“ im folgenden Beispiel)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Rufen Sie nun die ID der iSCSI-Geräte ab.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Mit dem Befehl werden für jedes SBD-Gerät drei Geräte-IDs aufgeführt. Es wird empfohlen, die ID zu verwenden, die mit „scsi-3“ beginnt, im Beispiel oben ist dies folgende ID:

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Erstellen Sie das SBD-Gerät.

   Verwenden Sie die Geräte-ID der iSCSI-Geräte, um die neuen SBD-Geräte auf dem ersten Clusterknoten zu erstellen.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Passen Sie die SDB-Konfiguration an.

   Öffnen Sie die SBD-Konfigurationsdatei.

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Ändern Sie die Eigenschaft des SBD-Geräts, aktivieren Sie die Pacemaker-Integration, und ändern Sie den Startmodus von SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Erstellen Sie die `softdog`-Konfigurationsdatei.

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Laden Sie nun das Modul.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Clusterinstallation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Aktualisieren Sie den SLES.

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Konfigurieren des Betriebssystems

   In einigen Fällen erstellt Pacemaker viele Prozesse und schöpft dadurch die zulässige Anzahl von Prozessen aus. In einem solchen Fall schlägt ein Heartbeat zwischen den Clusterknoten möglicherweise fehl und führt zu einem Failover Ihrer Ressourcen. Es wird empfohlen, die maximale Anzahl der zulässigen Prozesse zu erhöhen, indem Sie den folgenden Parameter festlegen.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Reduzieren Sie die Größe des Änderungscaches. Weitere Informationen finden Sie unter [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Niedrige Schreibleistung auf SLES 11/12-Servern mit großem Arbeitsspeicher).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Konfigurieren von „cloud-netconfig-azure“ den HA-Cluster

   Ändern Sie die Konfigurationsdatei für die Netzwerkschnittstelle wie unten dargestellt, um das Cloud-Netzwerk-Plug-In daran zu hindern, die virtuelle IP-Adresse zu entfernen (Pacemaker muss die VIP-Zuweisung steuern). Weitere Informationen finden Sie unter [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Aktivieren Sie den SSH-Zugriff.

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Aktivieren Sie den SSH-Zugriff.

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Aktivieren Sie den SSH-Zugriff.

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installieren Sie Fence Agents.
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen. Durch die Verwendung von „/etc/hosts“ wird Ihr Cluster vom DNS (einem weiteren möglichen Single Point of Failure) unabhängig.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installieren Sie den Cluster.

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Fügen Sie dem Cluster Knoten hinzu.

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Ändern Sie das Kennwort „hacluster“ in das gleiche Kennwort.

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Konfigurieren Sie „corosync“ zur Verwendung einer anderen Transportart, und fügen Sie „nodelist“ hinzu. Andernfalls funktioniert der Cluster nicht.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Fügen Sie den folgenden fett formatierten Inhalt in die Datei ein, falls die Werte dort nicht vorhanden sind oder sich von den angegebenen Werten unterscheiden. Stellen Sie sicher, dass Sie das Token in 30000 ändern, um die Wartung mit Speicherbeibehaltung zu ermöglichen. Weitere Informationen finden Sie in [diesem Artikel für Linux][virtual-machines-linux-maintenance] bzw. [diesem Artikel für Windows][virtual-machines-windows-maintenance]. Entfernen Sie außerdem unbedingt den Parameter „mcastaddr“.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Starten Sie den corosync-Dienst dann neu.

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Erstellen des STONITH-Geräts des Azure Fence-Agents

Das STONITH-Gerät verwendet einen Dienstprinzipal zur Autorisierung bei Microsoft Azure. Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen.

1. Gehe zu[https://portal.azure.com](https://portal.azure.com)
1. Öffnen Sie das Blatt „Azure Active Directory“.  
   Wechseln Sie zu „Eigenschaften“, und notieren Sie sich die Verzeichnis-ID. Dies ist die **Mandanten-ID**.
1. Klicken Sie auf „App-Registrierungen“.
1. Klicken Sie auf "Hinzufügen".
1. Geben Sie einen Namen ein, wählen Sie den Anwendungstyp „Web-App/API“, geben Sie eine Anmelde-URL ein (z.B. „http\://localhost“), und klicken Sie auf „Erstellen“.
1. Die Anmelde-URL wird nicht verwendet und kann eine beliebige gültige URL sein.
1. Wählen Sie die neue App aus, und klicken Sie auf der Registerkarte „Einstellungen“ auf „Schlüssel“.
1. Geben Sie eine Beschreibung für einen neuen Schlüssel ein, wählen Sie „Läuft nie ab“, und klicken Sie auf „Speichern“.
1. Notieren Sie sich den Wert. Er dient als **Kennwort** für den Dienstprinzipal.
1. Notieren Sie sich die Anwendungs-ID. Sie wird als Benutzername (**Anmelde-ID** in den folgenden Schritten) des Dienstprinzipals verwendet.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Erstellen einer benutzerdefinierten Rolle für den Fence Agent.

Der Dienstprinzipal hat standardmäßig keine Zugriffsberechtigungen für Ihre Azure-Ressourcen. Sie müssen dem Dienstprinzipal Berechtigungen zum Starten und Beenden (Freigeben) aller virtuellen Computer des Clusters gewähren. Wenn Sie noch keine benutzerdefinierte Rolle erstellt haben, können Sie sie mit [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) oder der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) erstellen.

Verwenden Sie folgenden Inhalt für die Eingabedatei. Sie müssen den Inhalt an Ihre Abonnements anpassen, d.h., Sie müssen „c276fc76-9cd4-44c9-99a7-4fd71546436e“ und „e91d47c4-76f3-4271-a796-21b4ecfe3624“ durch die IDs Ihres Abonnements ersetzen. Wenn Sie nur über ein Abonnement verfügen, entfernen Sie den zweiten Eintrag in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Weisen Sie dem Dienstprinzipal die benutzerdefinierte Rolle zu.

Weisen Sie dem Dienstprinzipal die benutzerdefinierte Rolle „Linux Fence Agent Role“ zu, die im letzten Abschnitt erstellt wurde. Verwenden Sie die Rolle „Owner“ nicht mehr.

1. Gehe zu[https://portal.azure.com](https://portal.azure.com)
1. Öffnen Sie das Blatt „Alle Ressourcen“.
1. Wählen Sie den virtuellen Computer des ersten Clusterknotens aus.
1. Klicken Sie auf „Zugriffssteuerung (IAM)“.
1. Klicken Sie auf „Rollenzuweisung hinzufügen“.
1. Wählen Sie die Rolle „Linux Fence Agent Role“ aus.
1. Geben Sie den Namen der Anwendung ein, die Sie zuvor erstellt haben.
1. Klicken Sie auf Speichern.

Wiederholen Sie die oben genannten Schritte für den zweiten Clusterknoten.

### <a name="1-create-the-stonith-devices"></a>**[1]** Erstellen Sie die STONITH-Geräte.

Nachdem Sie die Berechtigungen für die virtuellen Computer bearbeitet haben, können Sie die STONITH-Geräte im Cluster konfigurieren.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Pacemaker-Standardkonfiguration für SBD

1. **[1]**  Aktivieren Sie die Verwendung eines STONITH-Geräts, und legen Sie die Verzögerung der Umgrenzung fest.

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-Konfiguration für geplante Azure-Ereignisse

Azure verfügt über [geplante Ereignisse](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Geplante Ereignisse werden per Metadatendienst bereitgestellt und sorgen dafür, dass für die Anwendung ausreichend Zeit für die Vorbereitung auf Ereignisse wie das Herunterfahren von VMs, das erneute Bereitstellen von VMs usw. vorhanden ist. Mit dem Ressourcen-Agent **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** wird eine Überwachung auf geplante Azure-Ereignisse durchgeführt. Wenn Ereignisse erkannt werden, versucht der Agent, alle Ressourcen auf der betroffenen VM zu beenden und auf einen anderen Knoten im Cluster zu verschieben. Hierfür müssen zusätzliche Pacemaker-Ressourcen konfiguriert werden. 

1. **[A]** Installieren Sie den **azure-events**-Agent. 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]** Konfigurieren Sie die Ressourcen in Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Nachdem Sie die Pacemaker-Ressourcen für den azure-events-Agent konfiguriert haben, erhalten Sie beim Aktivieren bzw. Deaktivieren des Wartungsmodus für den Cluster ggf. folgende Warnmeldungen vom Typ „Unbekanntes Attribut“:  
     WARNING: cib-bootstrap-options: unknown attribute 'hostName_  <strong>hostname</strong>'  
     WARNING: cib-bootstrap-options: unknown attribute 'azure-events_globalPullState'  
     WARNING: cib-bootstrap-options: unknown attribute 'hostName_ <strong>hostname</strong>'  
   > Diese Warnmeldungen können ignoriert werden.

## <a name="next-steps"></a>Nächste Schritte

* [SAP NetWeaver auf virtuellen Azure-Computern – Planungs- und Implementierungshandbuch][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP][deployment-guide]
* [SAP NetWeaver auf virtuellen Azure-Computern – DBMS-Bereitstellungshandbuch][dbms-guide]
* [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server für SAP-Anwendungen][sles-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
