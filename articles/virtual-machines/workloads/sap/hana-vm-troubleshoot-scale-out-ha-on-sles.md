---
title: SAP HANA – Horizontale Skalierung von HSR-Pacemaker mit SLES auf Azure VMs – Problembehandlung | Microsoft-Dokumentation
description: Dieser Leitfaden erläutert die Überprüfung und Problembehandlung einer komplexen SAP HANA-Hochverfügbarkeitskonfiguration für die horizontale Skalierung basierend auf der SAP HANA-Systemreplikation (HSR) und Pacemaker unter SLES 12 SP3, die auf virtuellen Azure-Computern ausgeführt wird.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 87758100299eb170a7950a1a7a2c6bd2029b27fb
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621551"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Überprüfen und Problembehandlung beim Setup der SAP HANA-Hochverfügbarkeitskonfiguration zur horizontalen Skalierung unter SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Dieser Artikel unterstützt Sie beim Überprüfen der Pacemaker-Clusterkonfiguration für die horizontale Skalierung mit SAP HANA auf virtuellen Azure-Computern. Das Clustersetup wurde mit einer Kombination aus der SAP HANA-Systemreplikation (HSR) und dem SUSE RPM-Paket „SAPHanaSR-ScaleOut“ durchgeführt. Alle Tests wurden ausschließlich unter SUSE Linux Enterprise Server 12 SP3 (SLES) vorgenommen. Die Abschnitte dieses Artikels decken verschiedene Bereiche ab und bieten Beispielbefehle sowie Auszüge aus Konfigurationsdateien. Es wird empfohlen, diese Beispiele als Methode zum Überprüfen und Verifizieren des gesamten Clustersetups zu verwenden.



## <a name="important-notes"></a>Wichtige Hinweise

Alle Tests für die horizontale Skalierung mit SAP HANA in Kombination mit der SAP HANA-Systemreplikation und Pacemaker wurden ausschließlich mit SAP HANA 2.0 durchgeführt. Als Betriebssystemversion wurde SUSE Linux Enterprise Server 12 SP3 für SAP-Anwendungen verwendet. Zum Einrichten des Pacemaker-Clusters wurde „SAPHanaSR-ScaleOut“ von SUSE verwendet, das neueste RPM-Paket.
SUSE hat eine [ausführliche Beschreibung dieses leistungsoptimierten Setups][sles-hana-scale-out-ha-paper] veröffentlicht.

Sie finden die VM-Typen, die für die horizontale Skalierung mit SAP HANA unterstützt werden, im [SAP HANA-zertifizierten IaaS-Verzeichnis][sap-hana-iaas-list].

> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.

Bei der horizontalen Skalierung mit SAP HANA in Kombination mit mehreren Subnetzen und virtuellen Netzwerkkarten sowie der Einrichtung der HSR gab es ein technisches Problem. Es ist zwingend erforderlich, dass Sie die neuesten Patches von SAP HANA 2.0 verwenden, bei denen dieses Problem behoben wurde. Folgende SAP HANA-Versionen werden unterstützt: 

* rev2.00.024.04 oder höher 
* rev2.00.032 oder höher

Wenn Sie Support von SUSE benötigen, befolgen Sie die Anweisungen in diesem [Leitfaden][suse-pacemaker-support-log-files]. Sammeln Sie alle Informationen über den SAP HANA-Hochverfügbarkeitscluster wie in diesem Artikel beschrieben. Der SUSE-Support benötigt diese Informationen zur weiteren Analyse.

Bei internen Tests trat beim Clustersetup ein Fehler durch ein normales ordnungsgemäßes Herunterfahren von VMs über das Azure-Portal auf. Es wird daher empfohlen, mit anderen Methoden ein Clusterfailover zu testen. Verwenden Sie Methoden wie die Erzwingung einer Kernel Panic, fahren Sie die Netzwerke herunter, oder migrieren Sie die **msl**-Ressource. Weitere Details finden Sie in den folgenden Abschnitten. Es wird davon ausgegangen, dass ein standardmäßiger Vorgang zum Herunterfahren absichtlich durchgeführt wird. Das beste Beispiel für ein absichtliches Herunterfahren ist das Herunterfahren zu Wartungszwecken. Informationen dazu finden Sie unter [Geplante Wartung](#planned-maintenance).

Bei internen Tests trat beim Clustersetup ein Fehler auf, nachdem eine manuelle SAP HANA-Übernahme durchgeführt wurde, während sich der Cluster im Wartungsmodus befand. Es wird empfohlen, manuell zurückzuwechseln, bevor Sie den Clusterwartungsmodus beenden. Eine andere Option besteht darin, ein Failover auszulösen, bevor Sie den Cluster in den Wartungsmodus versetzen. Weitere Informationen finden Sie unter [Geplante Wartung](#planned-maintenance). In der SUSE-Dokumentation wird beschrieben, wie Sie den Cluster auf diese Weise mit dem **crm**-Befehl zurücksetzen können. Allerdings erwies sich der oben genannte Ansatz bei internen Tests als stabil und zeigte keine unerwarteten Nebeneffekte.

Wenn Sie den Befehl **crm migrate** verwenden, stellen Sie sicher, dass Sie die Clusterkonfiguration bereinigen. Diese weist Einschränkungen hinsichtlich des Speicherorts auf, die Ihnen möglicherweise nicht bewusst sind. Diese Einschränkungen wirken sich auf das Clusterverhalten aus. Weitere Informationen dazu finden Sie unter [Geplante Wartung](#planned-maintenance).



## <a name="test-system-description"></a>Beschreibung des Testsystems

 Für die Überprüfung und Zertifizierung der Hochverfügbarkeit bei der horizontalen Skalierung mit SAP HANA wurde ein bestimmtes Setup verwendet. Dieses bestand aus zwei Systemen mit jeweils drei SAP HANA-Knoten: ein Masterknoten und zwei Workerknoten. In der folgenden Tabelle sind die Namen der virtuellen Computer und die internen IP-Adressen aufgelistet. Alle nachfolgenden Überprüfungsbeispiele wurden auf diesen VMs ausgeführt. Diese VM-Namen und IP-Adressen in den Befehlsbeispielen sollen Ihnen ein besseres Verständnis der Befehle und ihrer Ausgaben vermitteln:


| Knotentyp | Name des virtuellen Computers | IP-Adresse |
| --- | --- | --- |
| Masterknoten an Standort 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Workerknoten 1 an Standort 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Workerknoten 2 an Standort 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Masterknoten an Standort 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Workerknoten 1 an Standort 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Workerknoten 2 an Standort 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Majority Maker-Knoten | hso-hana-dm | 10.0.0.13 |
| SBD-Geräteserver | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS-Server 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS-Server 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Mehrere Subnetze und virtuelle Netzwerkkarten

Gemäß den Empfehlungen für SAP HANA-Netzwerke wurden drei Subnetze in einem virtuellen Azure-Netzwerk erstellt. Die horizontale Skalierung mit SAP HANA in Azure muss in einem nicht freigegebenen Modus installiert werden. Dies bedeutet, dass jeder Knoten lokale Datenträgervolumes für **/hana/data** und **/hana/log** verwendet. Da die Knoten nur lokale Datenträgervolumes verwenden, muss kein separates Subnetz für den Speicher definiert werden:

- 10.0.2.0/24 für die Kommunikation zwischen SAP HANA-Knoten
- 10.0.1.0/24 für die SAP HANA-Systemreplikation (HSR)
- 10.0.0.0/24 für alles andere

Informationen zur SAP HANA-Konfiguration im Zusammenhang mit der Verwendung mehrerer Netzwerke finden Sie im Abschnitt [SAP HANA-Datei „global.ini“](#sap-hana-globalini).

Jede VM im Cluster weist drei virtuelle Netzwerkkarten auf – entsprechend der Anzahl von Subnetzen. Der Artikel [Erstellen eines virtuellen Linux-Computers in Azure mit mehreren Netzwerkschnittstellenkarten][azure-linux-multiple-nics] beschreibt ein potenzielles Routingproblem in Azure beim Bereitstellen eines virtuellen Linux-Computers. Dieser spezielle Artikel zum Thema Routing gilt nur bei Verwendung von mehreren virtuellen Netzwerkkarten. Das Problem wurde standardmäßig in SLES 12 SP3 von SUSE gelöst. Weitere Informationen finden Sie unter [Mehrere NICs mit cloud-netconfig in EC2 und Azure][suse-cloud-netconfig].


Um sicherzustellen, dass SAP HANA ordnungsgemäß für die Verwendung mehrerer Netzwerke konfiguriert ist, führen Sie die folgenden Befehle aus. Stellen Sie zunächst sicher, dass auf Betriebssystemebene alle drei internen IP-Adressen für alle drei Subnetze aktiv sind. Falls Sie die Subnetze mit verschiedenen IP-Adressbereichen definiert haben, müssen Sie die Befehle anpassen:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Im Folgenden finden Sie eine Beispielausgabe vom zweiten Workerknoten an Standort 2. Sie können drei verschiedene interne IP-Adressen von „eth0“, „eth1“ und „eth2“ sehen:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Als Nächstes überprüfen Sie die SAP HANA-Ports für den Namenserver und HSR. SAP HANA sollten an den entsprechenden Subnetzen lauschen. Abhängig von der SAP HANA-Instanznummer müssen Sie die Befehle anpassen. Für das Testsystem wurde als Instanznummer **00** festgelegt. Es gibt verschiedene Möglichkeiten, um zu ermitteln, welche Ports verwendet werden. 

Die folgende SQL-Anweisung gibt die Instanz-ID, die Instanznummer und andere Informationen zurück:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Die richtigen Portnummern finden Sie z.B. in HANA Studio unter **Konfiguration** oder durch eine SQL-Anweisung:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Um jeden Port zu finden, der im SAP-Softwarestapel einschließlich SAP HANA verwendet wird, lesen Sie unter [TCP/IP ports of all SAP products][sap-list-port-numbers] (TCP/IP-Ports aller SAP-Produkte) nach.

Basierend auf der Instanznummer **00** im SAP HANA 2.0-Testsystem, lautet die Portnummer für den Namenserver **30001**. Die Portnummer für die HSR-Metadatenkommunikation lautet **40002**. Eine Möglichkeit ist, sich mit einem Workerknoten anzumelden und anschließend die Masterknotendienste zu überprüfen. Für diesen Artikel haben wir Workerknoten 2 an Standort 2 überprüft und versucht, eine Verbindung mit dem Masterknoten an Standort 2 herzustellen.

Überprüfen Sie den Namenserverport:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Um zu bestätigen, dass bei der Kommunikation zwischen Knoten das Subnetz **10.0.2.0/24** verwendet wird, sollte das Ergebnis der folgenden Beispielausgabe entsprechen.
Nur die Verbindung über das Subnetz **10.0.2.0/24** sollte erfolgreich hergestellt werden:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Überprüfen Sie nun den HSR-Port **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Um zu bestätigen, dass bei der HSR-Kommunikation das Subnetz **10.0.1.0/24** verwendet wird, sollte das Ergebnis der folgenden Beispielausgabe entsprechen.
Nur die Verbindung über das Subnetz **10.0.1.0/24** sollte erfolgreich hergestellt werden:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Die **corosync**-Konfigurationsdatei muss auf jedem Knoten im Cluster, einschließlich des Majority Maker-Knotens, korrekt sein. Falls der Beitritt eines Knotens zu einem Cluster nicht wie erwartet funktioniert, erstellen bzw. kopieren Sie **/etc/corosync/corosync.conf** manuell auf allen Knoten, und starten Sie den Dienst neu. 

Der Inhalt von **corosync.conf** vom Testsystem dient als Beispiel.

Der erste Abschnitt ist **totem**, wie unter [Clusterinstallation](./high-availability-guide-suse-pacemaker.md#cluster-installation), Schritt 11, beschrieben. Sie können den Wert für **mcastaddr** ignorieren. Übernehmen Sie einfach den vorhandenen Eintrag. Die Einträge für **token** und **consensus** müssen gemäß der [Microsoft Azure-SAP HANA-Dokumentation][sles-pacemaker-ha-guide] festgelegt werden.

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Im zweiten Abschnitt, **logging**, wurden die vorgegebenen Standardwerte nicht geändert:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

Der dritte Abschnitt enthält **nodelist**. Alle Knoten des Clusters müssen die **nodeid** aufweisen:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Im letzten Abschnitt, **quorum**, ist es wichtig, den Wert für **expected_votes** ordnungsgemäß festzulegen. Dieser muss die Anzahl der Knoten darstellen, einschließlich des Majority Maker-Knotens. Der Wert für **two_node** muss **0** sein. Entfernen Sie den Eintrag nicht vollständig. Legen Sie den Wert nur auf **0** fest.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Starten Sie den Dienst über **systemctl** neu:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD-Gerät

Die Einrichtung eines SBD-Geräts auf einer Azure-VM wird unter [SBD-Umgrenzung](./high-availability-guide-suse-pacemaker.md#sbd-fencing) beschrieben.

Überprüfen Sie als Erstes auf der SBD-Server-VM, ob für jeden Knoten im Cluster ACL-Einträge vorhanden sind. Führen Sie den folgenden Befehl auf der SBD-Server-VM aus:


<pre><code>
targetcli ls
</code></pre>


Im Testsystem sieht die Ausgabe des Befehls wie im folgenden Beispiel aus. ACL-Namen wie **iqn.2006-04.hso-db-0.local:hso-db-0** müssen als entsprechende Initiatornamen auf den VMs eingegeben werden. Jede VM benötigt einen anderen Namen.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Überprüfen Sie anschließend, ob die Initiatornamen auf allen VMs eindeutig sind und den oben gezeigten Einträgen entsprechen. Im Folgenden sehen Sie ein Beispiel zu Workerknoten 1 an Standort 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Die Ausgabe sieht in etwa wie folgt aus:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Als Nächstes überprüfen Sie, ob die **Ermittlung** ordnungsgemäß funktioniert. Führen Sie den folgenden Befehl auf jedem Clusterknoten mit der IP-Adresse der SBD-Server-VM aus:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Die Ausgabe sollte wie folgt aussehen:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Als nächste Maßnahme stellen Sie sicher, dass der Knoten das SDB-Gerät erkennt. Überprüfen Sie auf jedem Knoten, einschließlich des Majority Maker-Knotens, Folgendes:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Die Ausgabe sollte wie im folgenden Beispiel aussehen. Allerdings können die Namen abweichen. Der Gerätename kann sich nach dem Neustart des virtuellen Computers ändern:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Abhängig vom Systemstatus kann es für die Problembehandlung manchmal hilfreich sein, die iSCSI-Dienste neu zu starten. Führen Sie anschließend die folgenden Befehle aus:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Sie können auf einem beliebigen Knoten überprüfen, ob alle Knoten den Status **clear** aufweisen. Stellen Sie sicher, dass Sie auf einem bestimmten Knoten den richtigen Gerätenamen verwenden:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Die Ausgabe sollte **clear** für jeden Knoten im Cluster angeben:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Eine andere SBD-Überprüfung ist die **dump**-Option des **sbd**-Befehls. In diesem Beispiel für den Befehl und die Ausgabe vom Majority Maker-Knoten lautete der Gerätename nicht **sdm**, sondern **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Abgesehen vom Gerätenamen sollte die Ausgabe auf allen Knoten identisch sein:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Eine weitere Überprüfung für SBD ist die Möglichkeit, eine Nachricht an einen anderen Knoten zu senden. Um eine Nachricht an Workerknoten 2 an Standort 2 zu senden, führen Sie den folgenden Befehl auf dem Workerknoten 1 an Standort 2 aus:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Aufseiten der Ziel-VM (in diesem Beispiel **hso-hana-vm-s2-2**) finden Sie unter **/var/log/messages** den folgenden Eintrag:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Überprüfen Sie, ob die Einträge in **/etc/sysconfig/sbd** der Beschreibung in [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](./high-availability-guide-suse-pacemaker.md#sbd-fencing) entsprechen. Überprüfen Sie, ob die Starteinstellung in **/etc/iscsi/iscsid.conf** auf „automatic“ festgelegt ist.

Die folgenden Einträge **/etc/sysconfig/sbd** sind wichtig. Passen Sie den Wert **id** nach Bedarf an:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Prüfen Sie die Starteinstellung in **/etc/iscsi/iscsid.conf**. Die erforderliche Einstellung sollte mit dem folgenden Befehl **iscsiadm** erfolgen, der in der Dokumentation beschrieben wird. Überprüfen Sie ihn, und passen Sie ihn manuell mit **vi** an, falls er anders lautet.

Mit diesem Befehl wird das Startverhalten festgelegt:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Erstellen Sie in **/etc/iscsi/iscsid.conf** den folgenden Eintrag:

<pre><code>
node.startup = automatic
</code></pre>

Während der Tests und Überprüfungen war das SBD-Gerät nach dem Neustart einer VM in manchen Fällen nicht mehr sichtbar. So gab es Abweichungen zwischen der Starteinstellung und der Ausgabe von YaST2. Um die Einstellungen zu überprüfen, gehen Sie folgendermaßen vor:

1. Starten Sie YaST2.
2. Wählen Sie auf der linken Seite **Netzwerkdienste** aus.
3. Scrollen Sie auf der rechten Seite nach unten zu **iSCSI-Initiator**, und klicken Sie darauf.
4. Auf dem nächsten Bildschirm sehen Sie auf der Registerkarte **Dienst** den eindeutigen Initiatornamen für den Knoten.
5. Stellen Sie sicher, dass oberhalb des Initiatornamens der Wert **Dienststart** auf **Beim Starten** festgelegt ist.
6. Falls nicht, legen Sie diesen anstelle von **Manuell** auf **Beim Starten** fest.
7. Wechseln Sie als Nächstes im oberen Bereich zur Registerkarte **Verbundene Ziele**.
8. Auf dem Bildschirm **Verbundene Ziele** sollte ein Eintrag für das SBD-Gerät angezeigt werden, der dem folgenden Beispiel ähnelt: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Überprüfen Sie, ob der Wert für **Start** auf **Beim Starten** festgelegt ist.
10. Falls nicht, wählen Sie **Bearbeiten** aus, und ändern Sie ihn.
11. Speichern Sie die Änderungen, und beenden Sie YaST2.



## <a name="pacemaker"></a>Pacemaker

Nachdem alles ordnungsgemäß eingerichtet ist, können Sie den folgenden Befehl auf jedem Knoten ausführen, um den Status des Pacemaker-Diensts zu überprüfen:

<pre><code>
systemctl status pacemaker
</code></pre>

Der obere Teil der Ausgabe sollte wie im folgenden Beispiel aussehen. Der Status bei **Active** sollte unbedingt **loaded** und **active (running)** lauten. Der Status nach **Loaded** muss **enabled** lauten.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Falls die Einstellung noch auf **disabled** festgelegt ist, führen Sie den folgenden Befehl aus:

<pre><code>
systemctl enable pacemaker
</code></pre>

Um alle konfigurierten Ressourcen in Pacemaker anzuzeigen, führen Sie den folgenden Befehl aus:

<pre><code>
crm status
</code></pre>

Die Ausgabe sollte wie im folgenden Beispiel aussehen. Es ist kein Problem, wenn die **cln**- und **msl**-Ressourcen auf der Majority Maker-VM **hso-hana-dm** als beendet angezeigt werden. Es gibt keine SAP HANA-Installation auf dem Majority Maker-Knoten. Aus diesem Grund werden die **cln**- und **msl**-Ressourcen als beendet angezeigt. In jedem Fall sollte die richtige Gesamtzahl der VMs (**7**) angezeigt werden. Alle VMs, die Teil des Clusters sind, müssen mit dem Status **Online** aufgelistet sein. Der aktuelle primäre Masterknoten muss ordnungsgemäß erkannt werden. In diesem Beispiel lautet er **hso-hana-vm-s1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Ein wichtiges Feature von Pacemaker ist der Wartungsmodus. In diesem Modus können Sie Änderungen vornehmen, ohne eine sofortige Aktion hervorzurufen. Ein Beispiel hierfür ist ein Neustart des virtuellen Computers. Ein typischer Anwendungsfall wäre die geplante Wartung der Betriebssystem- oder Azure-Infrastruktur. Informationen hierzu finden Sie unter [Geplante Wartung](#planned-maintenance). Verwenden Sie den folgenden Befehl, um Pacemaker in den Wartungsmodus zu versetzen:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Bei der Überprüfung mit **crm status** werden Sie feststellen, dass in der Ausgabe alle Ressourcen als **unmanaged** gekennzeichnet sind. In diesem Zustand reagiert der Cluster nicht auf Änderungen wie das Starten oder Beenden von SAP HANA.
Das folgende Beispiel zeigt die Ausgabe des Befehls **crm status** mit dem Cluster im Wartungsmodus:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              **_ Resource management is DISABLED _*_
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Dieses Befehlsbeispiel zeigt, wie der Clusterwartungsmodus beendet wird:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Mit einem weiteren _ *crm**-Befehl wird die vollständige Clusterkonfiguration in einem Editor geöffnet, sodass Sie sie bearbeiten können. Nachdem die Änderungen gespeichert wurden, startet der Cluster die entsprechenden Aktionen:

<pre><code>
crm configure edit
</code></pre>

Um die vollständige Clusterkonfiguration anzuzeigen, verwenden Sie die Option **crm show**:

<pre><code>
crm configure show
</code></pre>



Nach Ausfällen von Clusterressourcen zeigt der **crm status**-Befehl die Liste **Fehlerhafte Aktionen** an. Im Folgenden wird eine Beispielausgabe angezeigt:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Nach Ausfällen muss eine Clusterbereinigung durchgeführt werden. Verwenden Sie erneut den **crm**-Befehl und die Befehlsoption **cleanup**, um diese Einträge zu fehlerhaften Aktionen zu entfernen. Benennen Sie die entsprechende Clusterressource wie folgt:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Die Ausgabe des Befehls sollte dem folgenden Beispiel ähneln:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Failover oder Übernahme

Wie im Abschnitt mit [wichtigen Hinweisen](#important-notes) erwähnt, sollten Sie kein standardmäßiges ordnungsgemäßes Herunterfahren verwenden, um das Clusterfailover oder die SAP HANA-HSR-Übernahme zu testen. Stattdessen wird empfohlen, einen Kernelwarnhinweis auszulösen, eine Ressourcenmigration zu erzwingen oder möglichst alle Netzwerke auf der Betriebssystemebene einer VM herunterzufahren. Eine andere Methode ist der Befehl **crm \<node\> standby**. Informationen hierzu finden Sie im [SUSE-Dokument][sles-12-ha-paper]. 

Mit den folgenden drei Beispielbefehlen können Sie ein Clusterfailover erzwingen:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Wie im Abschnitt [Geplante Wartung](#planned-maintenance) beschrieben wird, ist eine gute Möglichkeit zur Überwachung der Clusteraktivitäten die Ausführung von **SAPHanaSR-showAttr** mit dem Befehl **watch** auszuführen:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Außerdem kann es hilfreich sein, den Status der SAP HANA-Landschaft mit einem SAP-Python-Skript zu untersuchen. Das Clustersetup sucht nach diesem Statuswert. Dies wird deutlich, wenn Sie an einem Ausfall der Workerknoten denken. Wenn ein Workerknoten ausfällt, gibt SAP HANA für die Integrität des gesamten Systems zur horizontalen Skalierung nicht sofort einen Fehler zurück. 

Es gibt einige Wiederholungen, um unnötige Failover zu vermeiden. Der Cluster reagiert nur, wenn sich der Status von **Ok** (Rückgabewert **4**) in **error** (Rückgabewert **1**) ändert. Es ist daher korrekt, wenn die Ausgabe von **SAPHanaSR-showAttr** einen virtuellen Computer mit dem Status **offline** zeigt. Es gibt jedoch noch keine Aktivität für den Wechsel zwischen primärem und sekundärem Knoten. Es wird nur dann eine Clusteraktivität ausgelöst, wenn SAP HANA einen Fehler zurückgibt.

Sie können den Integritätsstatus der SAP HANA-Landschaft als Benutzer **\<HANA SID\>adm** überwachen, indem Sie das SAP-Python-Skript wie folgt aufrufen. Möglicherweise müssen Sie den Pfad anpassen:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Die Ausgabe dieses Befehls sollte wie im folgenden Beispiel aussehen. Wichtig ist die Spalte **Host Status** sowie der **overall host status**. Die tatsächliche Ausgabe ist breiter mit zusätzlichen Spalten.
Die meisten Spalten auf der rechten Seite wurden entfernt, um die Ausgabetabelle in diesem Dokument lesefreundlicher zu gestalten:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Es gibt einen weiteren Befehl, um die aktuellen Clusteraktivitäten zu überprüfen. Nachfolgend finden Sie den Befehl und das Ende der Ausgabe, nachdem der Masterknoten des primären Standorts beendet wurde. Die Liste der Übergangsaktionen wie das **Höherstufen** des früheren sekundären Masterknotens **hso-hana-vm-s2-0** finden Sie als neuen primären Masterknoten. Wenn alles in Ordnung ist und alle Aktivitäten abgeschlossen sind, muss die Liste **Transition Summary** leer sein.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Geplante Wartung 

Es gibt verschiedene Anwendungsfälle in Bezug auf die geplante Wartung. Eine Frage ist z.B., ob es sich lediglich um eine Infrastrukturwartung wie Änderungen auf Betriebssystemebene und Datenträgerkonfiguration oder ein HANA-Upgrade handelt.
Sie finden weitere Informationen in Dokumenten von SUSE, beispielsweise [Towards Zero Downtime][sles-zero-downtime-paper] (Vermeiden von Downtime) oder [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap] (SAP HANA SR – leistungsoptimiertes Szenario). Diese Dokumentation umfasst zudem Beispiele dazu, wie eine manuelle Migration für einen primären Masterknoten durchgeführt werden kann.

Es wurden umfassende interne Tests durchgeführt, um den Anwendungsfall der Infrastrukturwartung zu überprüfen. Um Probleme im Zusammenhang mit der Migration eines primären Masterknotens zu vermeiden, haben wir entschieden, stets einen primären Masterknoten zu migrieren, bevor ein Cluster in den Wartungsmodus versetzt wird. So muss der Cluster nicht dahingehend konfiguriert werden, dass er die vorherige Situation ignoriert (d.h. welche Seite primär und welche Seite sekundär war).

Es gibt zwei verschiedene Situationen in dieser Hinsicht:

- **Geplante Wartung für die aktuelle sekundäre Seite**. In diesem Fall können Sie den Cluster einfach in den Wartungsmodus versetzen und die Arbeit auf dem sekundären Knoten durchführen, ohne dass dies den Cluster beeinflusst.

- **Geplante Wartung für die aktuelle primäre Seite**. Damit Benutzer ihre Arbeit während einer Wartung fortsetzen können, müssen Sie ein Failover erzwingen. Bei diesem Ansatz muss das Clusterfailover nicht nur auf SAP HANA-HSR-Ebene, sondern auch durch Pacemaker ausgelöst werden. Das Pacemaker-Setup löst automatisch die SAP HANA-Übernahme aus. Außerdem müssen Sie das Failover umsetzen, bevor Sie den Cluster in den Wartungsmodus versetzen.

Für die Wartung am aktuellen sekundären Standort gilt folgendes Verfahren:

1. Versetzen Sie den Cluster in den Wartungsmodus.
2. Führen Sie die Arbeit am sekundären Standort durch. 
3. Beenden Sie den Clusterwartungsmodus.

Das Verfahren für die Wartung am aktuellen primären Standort ist komplexer:

1. Lösen Sie manuell ein Failover bzw. eine SAP HANA-Übernahme über eine Migration der Pacemaker-Ressourcen aus. Details hierzu werden im Folgenden aufgeführt.
2. SAP HANA am vorherigen primären Standort wird im Zuge des Clustersetups heruntergefahren.
3. Versetzen Sie den Cluster in den Wartungsmodus.
4. Nachdem die Wartungsarbeiten abgeschlossen sind, registrieren Sie den früheren primären Standort als neuen sekundären Standort.
5. Bereinigen Sie die Clusterkonfiguration. Details hierzu werden im Folgenden aufgeführt.
6. Beenden Sie den Clusterwartungsmodus.


Durch die Migration einer Ressource wird der Clusterkonfiguration ein Eintrag hinzufügt. Ein Beispiel hierfür ist die Erzwingung eines Failovers. Diese Einträge müssen Sie bereinigen, bevor Sie den Wartungsmodus beenden. Sehen Sie sich das folgende Beispiel an.

Zunächst muss ein Clusterfailover erzwungen werden, indem Sie die **msl**-Ressource zum aktuellen sekundären Masterknoten migrieren. Dieser Befehl gibt eine Warnung aus, dass eine **move-Einschränkung** erstellt wurde:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Überprüfen Sie den Failovervorgang mit dem Befehl **SAPHanaSR-showAttr**. Um den Clusterstatus zu überwachen, öffnen Sie ein dediziertes Shellfenster und starten den Befehl mit **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Die Ausgabe sollte dem manuellen Failover entsprechen. Der frühere sekundäre Masterknoten wurde **höhergestuft**, in diesem Beispiel **hso-hana-vm-s2-0**. Die bisherige primäre Website wurde angehalten, **lss**-Wert **1** für den früheren primären Masterknoten **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Versetzen Sie den Cluster nach dem Clusterfailover und der SAP HANA-Übernahme in den Wartungsmodus, wie unter [Pacemaker](#pacemaker) beschrieben.

Die Befehle **SAPHanaSR-showAttr** und **crm status** machen keine Angaben zu den Einschränkungen, die sich durch die Ressourcenmigration ergeben. Eine Option zum Anzeigen dieser Einschränkungen ist, die vollständige Clusterressourcenkonfiguration mithilfe des folgenden Befehls anzuzeigen:

<pre><code>
crm configure show
</code></pre>

In der Clusterkonfiguration finden Sie eine neue Speicherorteinschränkung, die durch die frühere manuelle Ressourcenmigration verursacht wurde. Dieser Beispieleintrag beginnt mit **location cli-** :

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Leider können sich diese Einschränkungen auf das allgemeine Clusterverhalten auswirken. Aus diesem Grund müssen sie wieder entfernt werden, bevor Sie das gesamte System online schalten. Mit dem Befehl **unmigrate** können die zuvor erstellten Speicherorteinschränkungen bereinigt werden. Die Benennung kann etwas verwirrend sein. Es wird nicht versucht, die Ressource wieder zur ursprünglichen VM zu migrieren, von der sie migriert wurde. Es werden lediglich die Speicherorteinschränkungen entfernt und auch entsprechende Informationen zurückgegeben, wenn der Befehl ausgeführt wird:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Am Ende der Wartungsarbeiten beenden Sie den Clusterwartungsmodus, wie unter [Pacemaker](#pacemaker) erläutert.



## <a name="hb_report-to-collect-log-files"></a>„hb_report“ zum Sammeln von Protokolldateien

Um Pacemaker-Clusterprobleme analysieren zu können, ist es hilfreich und wird auch vom SUSE-Support gefordert, das Hilfsprogramm **hb_report** auszuführen. Es erfasst alle wichtigen Protokolldateien, die Sie zur Analyse der Ereignisse benötigen. Dieser Beispielaufruf verwendet eine Start- und Endzeit, zu der ein bestimmter Vorfall aufgetreten ist. Lesen Sie auch die [wichtigen Hinweise](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Der Befehl gibt an, wo die komprimierten Protokolldateien gespeichert wurden:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Anschließend können Sie die einzelnen Dateien über den standardmäßigen **tar**-Befehl extrahieren:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

In den extrahierten Dateien finden Sie alle Protokolldateien. Die meisten davon wurden in separate Verzeichnisse für jeden Knoten im Cluster platziert:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Innerhalb des angegebenen Zeitraums wurde der aktuelle Masterknoten **hso-hana-vm-s1-0** beendet. In **journal.log** finden Sie Einträge im Zusammenhang mit diesem Ereignis:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Ein weiteres Beispiel ist die Pacemaker-Protokolldatei auf dem sekundären Masterknoten, der zum neuen primären Masterknoten wurde. Dieser Auszug zeigt, dass der Status des beendeten primären Masterknotens auf **offline** festgelegt wurde:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA-Datei „global.ini“


Die folgenden Auszüge stammen aus der SAP HANA-Datei **global.ini** an Clusterstandort 2. Dieses Beispiel zeigt die Einträge zur Hostnamenauflösung für die Verwendung verschiedener Netzwerke für die SAP HANA-Kommunikation zwischen Knoten und HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Die Clusterlösung bietet eine Browseroberfläche mit einer grafischen Benutzeroberfläche für Benutzer, die anstelle von Befehlen auf Shellebene Menüs und Grafiken bevorzugen.
Um die Browseroberfläche zu verwenden, ersetzen Sie **\<node\>** in der folgenden URL durch einen tatsächlichen SAP HANA-Knoten. Geben Sie dann die Anmeldeinformationen des Clusters ein (Benutzer **cluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Dieser Screenshot zeigt das Clusterdashboard:


![Hawk-Clusterdashboard](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Dieses Beispiel zeigt die Speicherorteinschränkungen, die aufgrund einer Migration von Clusterressourcen entstehen, wie unter [Geplante Wartung](#planned-maintenance) erläutert wird:


![Einschränkungen der Hawk-Liste](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Außerdem können Sie die **hb_report**-Ausgabe in Hawk unter **Verlauf** hochladen, wie im Folgenden gezeigt. Informationen zum Erfassen von Protokolldateien finden Sie unter „hb_report“: 

![Hochladen einer hb_report-Ausgabe in Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Im **Verlaufs-Explorer** können Sie dann alle Clusterübergänge durchgehen, die in der **hb_report**-Ausgabe enthalten sind:

![Hawk-Übergänge in der hb_report-Ausgabe](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Dieser letzte Screenshot zeigt den Abschnitt **Details** eines einzelnen Übergangs. Der Cluster hat auf den Absturz eines primären Masterknotens reagiert, **hso-hana-vm-s1-0**. Der sekundäre Knoten wird jetzt als neuer Masterknoten **hso-hana-vm-s2-0** höhergestuft:

![Einzelner Hawk-Übergang](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden zur Problembehandlung wird die Hochverfügbarkeit für SAP HANA in einer Konfiguration für die horizontale Skalierung beschrieben. Eine weitere wichtige Komponente in einer SAP-Landschaft neben der Datenbank ist der SAP NetWeaver-Stapel. Informieren Sie sich über [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs mit SUSE Linux Enterprise Server][sap-nw-ha-guide-sles].
