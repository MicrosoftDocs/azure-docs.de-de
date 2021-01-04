---
title: Einrichten von IBM Db2 HADR auf virtuellen Azure-Computern (VMs) unter RHEL | Microsoft-Dokumentation
description: Richten Sie die Hochverfügbarkeit von IBM Db2 für LUW auf virtuellen Azure-Computern (VMs) unter RHEL ein.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 85f268990ac9e0c04cba1b9c409a232a24ce0d61
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608633"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hochverfügbarkeit von IBM DB2 LUW auf virtuellen Azure-Computern unter Red Hat Enterprise Linux Server

IBM Db2 für Linux, UNIX und Windows (LUW) in einer [HADR-Konfiguration (Hochverfügbarkeit und Notfallwiederherstellung)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) besteht aus einem Knoten, auf dem eine primäre Datenbankinstanz ausgeführt wird, und mindestens einem Knoten, auf dem eine sekundäre Datenbankinstanz ausgeführt wird. Änderungen an der primären Datenbankinstanz werden, abhängig von Ihrer Konfiguration, synchron oder asynchron in eine sekundäre Datenbankinstanz repliziert. 

> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.

Dieser Artikel beschreibt die Bereitstellung und Konfiguration der virtuellen Azure-Computer (VMs), die Installation des Clusterframeworks und die Installation von IBM Db2 LUW mit HADR-Konfiguration. 

In diesem Artikel wird nicht behandelt, wie IBM Db2 LUW mit HADR installiert und konfiguriert wird oder die oder SAP-Softwareinstallation erfolgt. Um Ihnen bei der Bewältigung dieser Aufgaben zu helfen, stellen wir Ihnen Verweise auf SAP- und IBM-Installationshandbücher zur Verfügung. Der Schwerpunkt dieses Artikels liegt auf Vorgehensweisen, die speziell für die Azure-Umgebung gelten. 

IBM Db2 wird in der Version 10.5 und höher unterstützt, wie im SAP-Hinweis [1928533] dokumentiert.

Bevor Sie mit einer Installation beginnen, lesen Sie zunächst die folgenden SAP-Hinweise und die -Dokumentation:

| SAP-Hinweis | BESCHREIBUNG |
| --- | --- |
| [1928533] | SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen |
| [2015553] | SAP in Azure: Supportvoraussetzungen |
| [2178632] | Wichtige Überwachungsmetriken für SAP in Azure |
| [2191498] | SAP unter Linux mit Azure: Erweiterte Überwachung |
| [2243692] | Linux auf Azure-VM (IaaS): SAP-Lizenzprobleme |
| [2002167] | Red Hat Enterprise Linux 7.x: Installation und Upgrade |
| [2694118] | Hochverfügbarkeits-Add-On für Red Hat Enterprise Linux in Azure |
| [1999351] | Problembehandlung für die erweiterte Azure-Überwachung für SAP |
| [2233094] | DB6: SAP-Anwendungen in Azure mit IBM Db2 für Linux, UNIX und Windows – weitere Informationen |
| [1612105] | DB6: Häufig gestellte Fragen zu Db2 mit HADR |


| Dokumentation | 
| --- |
| [Wiki der SAP Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Enthält alle erforderlichen SAP-Hinweise für Linux |
| [SAP NetWeaver auf virtuellen Azure-Computern unter Linux – Planungs- und Implementierungshandbuch][planning-guide] |
| [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide] (dieser Artikel) |
| Anleitung [Azure Virtual Machines DBMS-Bereitstellung (Datenbank-Manager) für SAP unter Linux][dbms-guide] |
| [Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure][azr-sap-plancheck] |
| [Übersicht über das Hochverfügbarkeits-Add-On für Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Verwaltung des Hochverfügbarkeits-Add-Ons][rhel-ha-admin] |
| [Referenz zum Hochverfügbarkeits-Add-On][rhel-ha-ref] |
| [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder][rhel-azr-supp]
| [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure][rhel-azr-inst]
| [Azure Virtual Machines – IBM Db2-DBMS-Bereitstellung für SAP-Workload][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Supportrichtlinie für RHEL-Hochverfügbarkeitscluster: Verwaltung von IBM Db2 für Linux, Unix und Windows in einem Cluster][rhel-db2-supp]



## <a name="overview"></a>Übersicht
Um Hochverfügbarkeit zu erreichen, wird IBM Db2 LUW mit HADR auf mindestens zwei virtuellen Azure-Computern installiert, die in einer [Azure-Verfügbarkeitsgruppe](../../windows/tutorial-availability-sets.md) oder über [Azure-Verfügbarkeitszonen](./sap-ha-availability-zones.md) bereitgestellt werden. 

Die folgenden Grafiken zeigen eine Konfiguration von zwei auf virtuellen Azure-Computern installierten Datenbankservern. Jeder dieser auf einem virtuellen Azure-Computer installierten Datenbankserver hat seinen eigenen zugeordneten Speicher und ist aktiv und wird ausgeführt. In HADR hat eine Datenbankinstanz auf einem der virtuellen Azure-Computer die Rolle der primären Instanz. Alle Clients werden mit der primären Instanz verbunden. Alle in Datenbanktransaktionen vorgenommenen Änderungen werden lokal im Db2-Transaktionsprotokoll gespeichert. Da die Transaktionsprotokolldatensätze lokal gespeichert werden, werden die Datensätze über TCP/IP an die Datenbankinstanz auf dem zweiten Datenbankserver übertragen, der als Standbyserver oder Standbyinstanz bezeichnet wird. In der Standbyinstanz wird die lokale Datenbank aktualisiert, indem ein Rollforward mit den übertragenen Transaktionsprotokolldatensätzen ausgeführt wird. Auf diese Weise bleibt der Standbyserver mit dem primären Server synchronisiert.

HADR ist nur eine Replikationsfunktionalität. Sie bietet weder Fehlererkennung noch automatische Übernahme noch Failoverkomponenten. Eine Übernahme durch den oder Übertragung an den Standbyserver muss manuell von einem Datenbankadministrator initiiert werden. Um eine automatische Übernahme und Fehlererkennung zu erreichen, können Sie den Linux-Clusterressourcen-Manager Pacemaker verwenden. Pacemaker überwacht die beiden Datenbankserverinstanzen. Wenn die primäre Datenbankserverinstanz abstürzt, initiiert Pacemaker eine *automatische* HADR-Übernahme durch den Standbyserver. Der Herzschrittmacher stellt auch sicher, dass die virtuelle IP-Adresse dem neuen primären Server zugewiesen wird.

![Übersicht über die Hochverfügbarkeit von IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Damit SAP-Anwendungsservern eine Verbindung mit einer primären Datenbank herstellen können, benötigen Sie einen virtuellen Hostnamen und eine virtuelle IP-Adresse. Im Fall eines Failovers werden die SAP-Anwendungsserver mit der neuen primären Datenbankinstanz verbunden. In einer Azure-Umgebung ist ein [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) erforderlich, um eine virtuelle IP-Adresse so zu verwenden, wie dies für HADR von IBM Db2 erforderlich ist. 

Damit Sie vollständig verstehen zu können, wie IBM Db2 LUW mit HADR und Pacemaker in die Konfiguration eines hochverfügbaren SAP-Systems passen, enthält die folgende Abbildung eine Übersicht über ein hochverfügbares SAP-System, das auf einer IBM Db2-Datenbank basiert. In diesem Artikel geht es nur um IBM Db2, es werden jedoch Verweise auf andere Artikel zum Einrichten anderer Komponenten eines SAP-Systems bereitgestellt.

![Übersicht über die vollständige Umgebung von IBM DB2-Hochverfügbarkeit](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Allgemeine Übersicht über die erforderlichen Schritte
Um eine IBM Db2-Konfiguration bereitzustellen, müssen die folgenden Schritte ausgeführt werden:

  + Planen der Umgebung.
  + Stellen Sie die VMs bereit.
  + Aktualisieren von RHEL Linux und Konfigurieren von Dateisystemen.
  + Installieren und Konfigurieren von Pacemaker.
  + Einrichten des [glusterfs-Clusters][glusterfs] oder von [Azure NetApp Files][anf-rhel].
  + Installieren von [ASCS/ERS in einem separaten Cluster][ascs-ha-rhel].
  + Installieren einer IBM Db2-Datenbank mit der Distributed/High Availability-Option (SWPM).
  + Installieren und Erstellen eines sekundären Datenbankknotens und einer sekundären Datenbankinstanz und Konfigurieren von HADR.
  + Bestätigen, dass HADR funktioniert.
  + Anwenden der Pacemaker-Konfiguration, um IBM Db2 zu steuern.
  + Konfigurieren von Azure Load Balancer.
  + Installieren des primären Servers und der Dialog Application-Server.
  + Überprüfen und Anpassen der Konfiguration der SAP-Anwendungsserver.
  + Ausführen von Failover und Übernahmetests.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planen der Azure-Infrastruktur zum Hosten von IBM Db2 LUW mit HADR.

Abschließen des Planungsprozesses, bevor Sie die Bereitstellung ausführen. Planung bildet die Grundlage für die Bereitstellung einer Konfiguration von Db2 mit HADR in Azure. Schlüsselelemente, die in der Planung für IMB Db2 LUW (Datenbankteil der SAP-Umgebung) berücksichtigt werden müssen, werden in der folgenden Tabelle aufgeführt.

| Thema | Kurze Beschreibung |
| --- | --- |
| Definieren von Azure-Ressourcengruppen | Ressourcengruppen, in denen Sie virtuelle Computer, VNET, Azure Load Balancer und andere Ressourcen bereitstellen. Können vorhanden oder neu sein. |
| Definition von virtuellem Netzwerk/Subnetz | Umgebung, in der virtuelle Computer für IBM Db2 und Azure Load Balancer bereitgestellt werden. Kann vorhanden sein oder neu erstellt werden. |
| Virtuelle Computer zum Hosten von IBM Db2 LUW | Größe, Speicher, Netzwerke, IP-Adressen der virtuellen Computer. |
| Virtueller Hostname und virtuelle IP-Adresse für die IBM Db2-Datenbank.| Die virtuelle IP-Adresse oder der virtuelle Hostname, die für die Verbindung von SAP-Anwendungsservern verwendet werden. **db-virt-hostname**, **db-virt-ip**. |
| Azure-Umgrenzung | Methode zum Verhindern von Split Brain-Situationen. |
| Azure Load Balancer | Verwendung von Basic oder Standard (empfohlen), Testport für die Db2-Datenbank (Empfehlung ist 62500) Prüfpunkt; **probe-port**. |
| Namensauflösung| Funktionsweise der Namensauflösung in der Umgebung. DNS-Dienst wird dringend empfohlen. Lokale hosts-Datei kann verwendet werden. |
    
Weitere Informationen zu Linux Pacemaker in Azure finden Sie unter [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Bereitstellung unter Red Hat Enterprise Linux

Der Ressourcen-Agent für IBM Db2 für LUW ist im Hochverfügbarkeits-Add-On für Red Hat Enterprise Linux Server enthalten. Für das in diesem Dokument beschriebene Setup sollten Sie Red Hat Enterprise Linux für SAP verwenden. Der Azure Marketplace enthält ein Image für Red Hat Enterprise Linux 7.4 für SAP oder höher, mit dem Sie neue virtuelle Azure-Computer bereitstellen können. Beachten Sie die verschiedenen Support- oder Servicemodelle, die Red Hat über den Azure Marketplace anbietet, wenn Sie ein VM-Image im Azure-VM-Marketplace auswählen.

### <a name="hosts-dns-updates"></a>Hosts: DNS-Updates
Erstellen Sie eine Liste aller Hostnamen, einschließlich der Namen virtueller Hosts, und aktualisieren Sie Ihre DNS-Server, um die richtige Auflösung von IP-Adressen in Hostnamen zu aktivieren. Wenn kein DNS-Server vorhanden ist oder Sie keine DNS-Einträge aktualisieren und erstellen können, müssen Sie die lokalen host-Dateien der einzelnen VMs verwenden, die an diesem Szenario teilnehmen. Wenn Sie Einträge aus host-Dateien verwenden, stellen Sie sicher, dass die Einträge auf alle virtuellen Computer in der SAP-Systemumgebung angewendet werden. Allerdings empfehlen wir, dass Sie Ihr DNS verwenden, das im Idealfall in Azure erweitert wird.


### <a name="manual-deployment"></a>Manuelle Bereitstellung

Vergewissern Sie sich, dass das ausgewählte Betriebssystem von IBM/SAP für IBM Db2 LUW unterstützt wird. Die Liste der unterstützten Betriebssystemversionen für virtuelle Azure-Computer und Db2-Versionen finden Sie im SAP-Hinweis [1928533]. Die Liste der Betriebssystemversionen zu den einzelnen Db2-Releases ist in der SAP-Produktverfügbarkeitsmatrix verfügbar. Wir empfehlen Ihnen, mindestens Red Hat Enterprise Linux 7.4 für SAP zu verwenden, weil diese Version bzw. höhere Red Hat Enterprise Linux-Versionen Leistungsverbesserungen für Azure aufweisen.

1. Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus.
1. Erstellen Sie ein virtuelles Netzwerks und ein Subnetz, oder wählen Sie vorhandene Ressourcen aus.
1. Erstellen Sie eine Azure-Verfügbarkeitsgruppe, oder stellen Sie eine Verfügbarkeitszone bereit.
    + Legen Sie für die Verfügbarkeitsgruppe die maximale Anzahl von Updatedomänen auf 2 fest.
1. Erstellen Sie den virtuellen Computer 1.
    + Verwenden Sie das Red Hat Enterprise Linux für SAP-Image im Azure Marketplace.
    + Wählen Sie die Azure-Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben, oder wählen Sie eine Verfügbarkeitszone aus.
1.  Erstellen Sie den virtuellen Computer 2.
    + Verwenden Sie das Red Hat Enterprise Linux für SAP-Image im Azure Marketplace.
    + Wählen Sie die Azure-Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben, oder wählen Sie eine Verfügbarkeitszone (nicht die gleiche Zone wie in Schritt 3) aus.
1. Fügen Sie den virtuellen Computern Datenträger hinzu. Prüfen Sie dann die Empfehlung für die Dateisystemkonfiguration, die Sie im Artikel [Azure Virtual Machines – IBM DB2-DBMS-Bereitstellung für SAP-Workload][dbms-db2] finden.

## <a name="create-the-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters
    
Informationen zum Erstellen eines grundlegenden Pacemaker-Clusters für diesen IBM Db2-Server finden Sie unter [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installieren von IBM Db2 LUW und der SAP-Umgebung

Bevor Sie mit der Installation einer SAP-Umgebung beginnen, die auf IBM Db2 LUW aufsetzt, sollten Sie die folgende Dokumentation lesen:

+ Azure-Dokumentation
+ SAP-Dokumentation
+ IBM-Dokumentation

Links zu dieser Dokumentation werden in der Einleitung dieses Artikels bereitgestellt.

Lesen Sie die SAP-Installationshandbücher, um zu erfahren, wie NetWeaver-basierte Anwendungen für IBM Db2 LUW installiert werden.
Sie finden die Handbücher im SAP-Hilfeportal über die [Handbuchsuche für SAP NetWeaver und ABAP-Plattform][sap-instfind] (Guide Finder for SAP NetWeaver and ABAP Platform).

Sie können die Anzahl der im Portal angezeigten Leitfäden verringern, indem Sie die folgenden Filter festlegen:
- I want to: „Install a new system“
- My Database: „IBM Db2 for Linux, Unix, and Windows“
- Zusätzliche Filter für SAP NetWeaver-Versionen, Stack-Konfiguration oder Betriebssystem

#### <a name="red-hat-firewall-rules"></a>Red Hat-Firewallregeln
Für Red Hat Enterprise Linux ist die Firewall standardmäßig aktiviert. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationshinweise für das Einrichten von IBM Db2 LUW mit HADR

So richten Sie die primäre IBM Db2 LUW-Datenbankinstanz ein:

- Verwenden Sie die Option für Hochverfügbarkeit oder für Verteilung (Distributed).
- Installieren Sie SAP ASCS/ERS und die Datenbankinstanz.
- Erstellen Sie eine Sicherung der neu installierten Datenbank.

> [!IMPORTANT] 
> Notieren Sie sich den „Database Communication port“, den Sie während der Installation festgelegt haben. Für beide Datenbankinstanzen muss dieselbe Portnummer verwendet werden.
>![SAP SWPM-Port: Definition](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2-Einstellungen zu Hochverfügbarkeit und Notfallwiederherstellung für Azure

   Wenn Sie einen Azure Pacemaker-Fencing-Agent verwenden, legen Sie die folgenden Parameter fest:

   - HADR-Peerfensterdauer (Sekunden) (HADR_PEER_WINDOW) = 240  
   - HADR-Timeoutwert (HADR_TIMEOUT) = 45

Wie empfehlen die oben genannten Parameterwerte basierend auf dem anfänglichen Testen für Failover/Übernahme. Es ist zwingend erforderlich, die ordnungsgemäße Funktionalität von Übernahme und Failover mit diesen Parametereinstellungen zu testen. Da die einzelne Konfigurationen variieren können, müssen diese Parameter möglicherweise angepasst werden. 

> [!NOTE]
> Spezifisch für IBM Db2 mit HADR-Konfiguration mit normalem Start: Die sekundäre oder Standbydatenbankinstanz muss aktiv sein und ausgeführt werden, bevor Sie die primäre Datenbankinstanz starten können.

   
> [!NOTE]
> Installation und Konfiguration speziell für Azure und Pacemaker: Während der Installation durch SAP Software Provisioning Manager gibt es eine explizite Frage zu Hochverfügbarkeit für IBM Db2 LUW:
>+ Wählen Sie nicht **IBM Db2 pureScale** aus.
>+ Wählen Sie nicht **Install IBM Tivoli System Automation for Multiplatforms** aus.
>+ Wählen Sie nicht **Generate cluster configration files** aus.
>![SAP SWPM: Db2-Optionen für Hochverfügbarkeit](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Um den Standbydatenbankserver mit der SAP-Prozedur „Homogeneous System Copy“ einzurichten, führen Sie die folgenden Schritte aus:

1. Verwenden Sie die Option **System copy** > **Target systems** > **Distributed** > **Datenbankinstanz**.
1. Wählen Sie als Kopiermethode die Option **Homogeneous System** aus, damit Sie „backup“ verwenden können, um eine Sicherung auf der Standbyserverinstanz wiederherstellen zu können.
1. Wenn Sie den Schritt erreichen, in dem für „Homogeneous System Copy“ das Wiederherstellen beendet werden soll, beenden Sie das Installationsprogramm. Stellen Sie die Datenbank aus einer Sicherung des primären Hosts wieder her. Alle weiteren Installationsphasen wurden bereits auf dem primären Datenbankserver ausgeführt.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat-Firewallregeln für Db2 (Hochverfügbarkeit und Notfallwiederherstellung)
Fügen Sie Firewallregeln hinzu, damit Datenverkehr an Db2 und für Db2 für Hochverfügbarkeit und Notfallwiederherstellung fließen kann:
+ Port für die Datenbankkommunikation. Wenn Sie Partitionen verwenden, müssen Sie die entsprechenden Ports ebenfalls hinzufügen.
+ HADR-Port (Wert von Db2-Parameter HADR_LOCAL_SVC)
+ Azure-Testport
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR-Überprüfung
Für Demonstrationszwecke und für die Vorgehensweisen, die in diesem Artikel beschrieben werden, lautet die Datenbank-SID **ID2**.

Nachdem Sie HADR konfiguriert haben und der Status PEER und CONNECTED für den primären und die Standbyknoten lautet, führen Sie die folgende Überprüfung durch:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-Konfiguration

Wenn Sie Pacemaker für automatisches Failover im Fall eines Knotenausfalls verwenden, müssen Sie Ihre Db2-Instanzen und Pacemaker entsprechend konfigurieren. In diesem Abschnitt werden die zugehörigen Konfigurationsschritte beschrieben.

Die folgenden Elemente haben eines der folgenden Präfixe:

- **[A]** : Gilt für alle Knoten
- **[1]** : Gilt nur für Knoten 1 
- **[2]** : Gilt nur für Knoten 2

**[A]** Voraussetzung für die Pacemaker-Konfiguration:
1. Fahren Sie beide Datenbankserver mit dem Benutzer „db2\<sid>“ mit „db2stop“ herunter.
1. Ändern Sie die Shellumgebung für „db2\<sid>“ in */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker-Konfiguration

**[1]** IBM Db2 HADR-spezifische Pacemaker-Konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Erstellen Sie IBM Db2-Ressourcen:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** Starten Sie IBM Db2-Ressourcen:
* Nehmen Sie Pacemaker aus dem Wartungsmodus.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Stellen Sie sicher, dass der Clusterstatus „OK“ ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01 nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status: corosync: active/disabled pacemaker: active/disabled pcsd: active/enabled
</pre>

> [!IMPORTANT]
> Sie müssen die geclusterte Db2-Instanz von Pacemaker mithilfe von Pacemaker-Tools verwalten. Wenn Sie db2-Befehle wie db2stop verwenden, erkennt Pacemaker die Aktion als Fehler der Ressource. Wenn Sie Wartungsarbeiten durchführen, können Sie die Knoten oder Ressourcen in den Wartungsmodus versetzen. Pacemaker hält die Überwachung von Ressourcen an, und anschließend können Sie normale db2-Verwaltungsbefehle verwenden.


### <a name="configure-azure-load-balancer"></a>Konfigurieren von Azure Load Balancer
Um Azure Load Balancer zu konfigurieren, empfehlen wir Ihnen, die [SKU „Azure Load Balancer Standard“](../../../load-balancer/load-balancer-overview.md) zu verwenden und dann wie folgt vorzugehen:

> [!NOTE]
> Bei der SKU „Azure Load Balancer Standard“ gibt es Einschränkungen, wenn von den Knoten unterhalb des Load Balancers aus auf öffentliche IP-Adressen zugegriffen wird. Im Artikel [Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarien verwenden](./high-availability-guide-standard-load-balancer-outbound-connections.md) werden Verfahren beschrieben, wie diesen Knoten der Zugriff auf öffentliche IP-Adressen ermöglicht werden kann.

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  


1. Erstellen eines Front-End-IP-Pools:

   a. Öffnen Sie im Azure-Portal den Azure Load Balancer, wählen Sie **Front-End-IP-Pool** aus, und wählen Sie dann **Hinzufügen** aus.

   b. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **Db2-Verbindung**).

   c. Legen Sie **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse **db-virt-ip** ein, die Sie zu Beginn definiert haben.

   d. Klicken Sie auf **OK**.

   e. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.

1. Erstellen eines Back-End-Pools:

   a. Öffnen Sie im Azure-Portal den Azure Load Balancer, wählen Sie **Back-End-Pools** aus, und wählen Sie dann **Hinzufügen** aus.

   b. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **Db2-Back-End**).

   c. Wählen Sie **Virtuellen Computer hinzufügen** aus.

   d. Wählen Sie die Verfügbarkeitsgruppe oder die virtuellen Computer aus, in der oder auf denen die IBM Db2-Datenbank gehostet wird, die Sie im vorherigen Schritt erstellt haben.

   e. Wählen Sie die virtuellen Computer des IBM Db2-Clusters aus.

   f. Klicken Sie auf **OK**.

1. Erstellen eines Integritätstests:

   a. Öffnen Sie im Azure-Portal den Azure Load Balancer, wählen Sie **Integritätstests** aus, und wählen Sie dann **Hinzufügen** aus.

   b. Geben Sie den Namen des neuen Integritätstests ein (z. B. **Db2-IntTest**).

   c. Wählen Sie **TCP** als Protokoll und den Port **62500** aus. Behalten Sie für das **Intervall** den Wert **5** und als **Fehlerschwellenwert** den Wert **2** bei.

   d. Klicken Sie auf **OK**.

1. Erstellen Sie die Lastenausgleichsregeln:

   a. Öffnen Sie im Azure-Portal den Azure Load Balancer, wählen Sie **Lastenausgleichsregeln** aus, und wählen Sie dann **Hinzufügen** aus.

   b. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **Db2-SID**).

   c. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **Db2-Front-End**).

   d. Übernehmen Sie für **Protokoll** den Wert **TCP**, und geben Sie als Portnummer den Wert von *Database Communication port* ein.

   e. Erhöhen Sie die **Leerlaufzeitüberschreitung** auf 30 Minuten.

   f. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.

   g. Klicken Sie auf **OK**.

**[A]** Hinzufügen der Firewallregel für den Testport:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Ändern des SAP-Profils, damit die virtuelle IP-Adresse für Verbindungen verwendet wird
Um Verbindungen mit der primären Instanz der HADR-Konfiguration herzustellen, muss die SAP-Anwendungsschicht die virtuelle IP-Adresse verwenden, die Sie für Azure Load Balancer definiert und konfiguriert haben. Die folgenden Änderungen sind erforderlich:

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installieren des primären und des Dialog Application-Servers

Wenn Sie den primären und den Dialog Application-Server für eine Db2 HADR-Konfiguration installieren, verwenden Sie den virtuellen Hostnamen, den Sie für die Konfiguration ausgewählt haben. 

Wenn Sie die Installation vor der Erstellung der Db2 HADR-Konfiguration durchgeführt haben, nehmen Sie die Änderungen wie im vorherigen Abschnitt beschrieben und für SAP Java-Stapel wie folgt vor.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>JDBC-URL-Prüfung für ABAP + Java- oder Java-Stapel-Systeme

Verwenden Sie das Konfigurationstool J2EE, um die JDBC-URL zu überprüfen oder zu aktualisieren. Da das Konfigurationstool J2EE ein grafisches Tool ist, muss ein X-Server installiert sein:
 
1. Melden Sie sich beim primären Anwendungsserver der J2EE-Instanz an, und führen Sie Folgendes aus:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Wählen Sie im linken Bereich die Option **security store** aus.
1. Wählen Sie im rechten Bereich den Schlüssel `jdbc/pool/\<SAPSID>/url` aus.
1. Ändern Sie den Hostnamen in der JDBC-URL in den virtuellen Hostnamen.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Wählen Sie **Hinzufügen**.
1. Um die Änderungen zu speichern, klicken Sie auf das Datenträgersymbol in der oberen linken Ecke.
1. Schließen Sie das Konfigurationstool.
1. Starten Sie die Java-Instanz neu.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurieren von Protokollarchivierung für die HADR-Einrichtung
Um die Db2-Protokollarchivierung für die HADR-Einrichtung zu konfigurieren, empfiehlt es sich, dass Sie sowohl die primäre als auch die Standbydatenbank so konfigurieren, dass sie automatisch Protokolle aus allen Protokollspeicherorten abrufen können. Sowohl die primäre als auch die Standbydatenbank müssen in der Lage sein, Protokollarchivdateien aus allen Protokollarchivspeicherorten abrufen zu können, in denen eine der Datenbankinstanzen Protokolldateien archivieren könnte. 

Die Protokollarchivierung wird nur von der primären Datenbank ausgeführt. Wenn Sie die HADR-Rollen der Datenbankserver ändern, oder wenn ein Fehler auftritt, ist die neue primäre Datenbank für die Protokollarchivierung verantwortlich. Wenn Sie mehrere Speicherorte für das Protokollarchiv eingerichtet haben, werden Ihre Protokolle möglicherweise zwei Mal archiviert. Im Falle eines lokalen oder Remoteabgleichs müssen Sie möglicherweise auch die archivierten Protokolle vom alten primären Server manuell in den aktiven Protokollspeicher des neuen primären Servers kopieren.

Es empfiehlt sich, eine allgemeine NFS-Freigabe oder ein GlusterFS zu konfigurieren, in die bzw. das Protokolle beider Knoten geschrieben werden. Die NFS-Freigabe oder das GlusterFS müssen hoch verfügbar sein. 

Sie können vorhandene hoch verfügbare NFS-Freigaben oder GlusterFS für Datentransporte oder ein Profilverzeichnis verwenden. Weitere Informationen finden Sie unter

- [GlusterFS auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP NetWeaver][glusterfs] 
- [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (zum Erstellen von NFS-Freigaben)

## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

In diesem Abschnitt ist beschrieben, wie Sie Ihre Db2 HADR-Einrichtung testen können. Bei jedem Test wird vorausgesetzt, dass die primäre IBM Db2-Einheit auf dem virtuellen Computer *az-idb01* ausgeführt wird. Es muss ein Benutzer mit sudo-Berechtigungen oder root-Zugriff (nicht empfohlen) verwendet werden.

Hier ist der Anfangsstatus für alle Testfälle beschrieben: („crm_mon -r“ oder „pcs status“)

- **pcs status** ist eine Momentaufnahme des Pacemaker-Status zum Ausführungszeitpunkt. 
- **crm_mon -r** ist eine kontinuierliche Ausgabe des Pacemaker-Status.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Der ursprüngliche Status in einem SAP-System ist in „Transaction DBACOCKPIT --> Configuration --> Overview“ dokumentiert, wie die folgende Abbildung zeigt:

![DBACockpit: vor Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Testübernahme von IBM Db2


> [!IMPORTANT] 
> Bevor Sie den Test zu starten, stellen Sie Folgendes sicher:
> * Pacemaker keine fehlerhaften Aktionen (pcs status) aufweist.
> * Es keine Speicherorteinschränkungen (Überbleibsel von Migrationstests) gibt.
> * Die IBM Db2-HADR-Synchronisierung funktioniert. Überprüfen Sie die Funktionsweise mit dem Benutzer „db2\<sid>“ <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrieren Sie den Knoten, auf dem die primäre Db2-Datenbank ausgeführt wird, indem Sie folgenden Befehl ausführen:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Nachdem die Migration abgeschlossen ist, sieht die Ausgabe von „crm status“ wie folgt aus:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Der ursprüngliche Status in einem SAP-System ist in „Transaction DBACOCKPIT --> Configuration --> Overview“ dokumentiert, wie die folgende Abbildung zeigt:

![DBACockpit: nach der Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Die Ressourcenmigration mit „pcs resource move“ führt zu Ortseinschränkungen. Ortseinschränkungen führen in diesem Fall dazu, dass die Ausführung einer IBM Db2-Instanz auf az-idb01 verhindert wird. Wenn die Ortseinschränkungen nicht gelöscht werden, kann für die Ressource kein Failback durchgeführt werden.

Entfernen Sie die Ortseinschränkung. Der Standbyknoten wird auf az-idb01 gestartet.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
Außerdem ändert sich der Clusterstatus in:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit: Ortseinschränkung entfernt](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrieren Sie die Ressourcen zurück zu *az-idb01*, und löschen Sie die Ortseinschränkungen.
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs resource move \<res_name> <host>:** Erstellt Speicherorteinschränkungen und kann Probleme mit Übernahmen verursachen.
- **pcs resource clear \<res_name>** : Löscht Speicherorteinschränkungen.
- **pcs resource cleanup \<res_name>** : Löscht alle Fehler der Ressource.

### <a name="test-a-manual-takeover"></a>Testen einer manuellen Übernahme

Sie können eine manuelle Übernahme testen, indem Sie den Pacemaker-Dienst auf dem Knoten *az-idb01* beenden:
<pre><code>systemctl stop pacemaker</code></pre>

status on *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Nach dem Failover können Sie den Dienst auf *az-idb01* wieder starten.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Beenden des Db2-Prozesses auf dem Knoten, auf dem die primäre HADR-Datenbank ausgeführt wird

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Die Db2-Instanz fällt aus, und Pacemaker verschiebt den Masterknoten und meldet den folgenden Status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker startet die primäre Db2-Datenbankinstanz auf demselben Knoten neu oder führt ein Failover auf den Knoten aus, auf dem die sekundäre Datenbankinstanz ausgeführt wird. Danach wird ein Fehler gemeldet.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Beenden des Db2-Prozesses auf den Knoten, auf dem die sekundäre Datenbankinstanz ausgeführt wird

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Der Knoten wird in den Status „Ausgefallen“ versetzt, und es wird ein Fehler gemeldet.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Die Db2-Instanz wird in der sekundären Rolle neu gestartet, die ihr zuvor zugewiesen war.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Beenden von Db2 durch „db2stop force“ auf dem Knoten, auf dem die primäre HADR-Datenbankinstanz ausgeführt wird

Führen Sie als Benutzer „db2\<sid>“ den Befehl „db2stop force“ aus:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Fehler erkannt:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Die sekundäre Db2 HADR-Datenbankinstanz wurde auf die primäre Rolle hochgestuft.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Absturz des virtuellen Computers, auf dem die primäre HADR-Datenbankinstanz ausgeführt wird, mit „halt“

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

In diesem Fall erkennt Pacemaker, dass der Knoten, auf dem die primäre Datenbankinstanz ausgeführt wird, nicht antwortet.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Der nächste Schritt besteht darin zu prüfen, ob eine *Split Brain*-Situation vorliegt. Sobald der weiterhin funktionsfähige Knoten ermittelt hat, dass der Knoten, auf dem die primäre Datenbankinstanz zuletzt ausgeführt wurde, nicht mehr verfügbar ist, wird ein Failover der Ressourcen ausgeführt.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


Bei einem Kernel Panic-Status wird der ausgefallene Knoten vom Fencing-Agent neu gestartet. Wenn sich der ausgefallene Knoten wieder im Onlinezustand befindet, müssen Sie den Pacemaker-Cluster starten.
<pre><code>sudo pcs cluster start</code></pre> Die Db2-Instanz wird für die sekundäre Rolle gestartet.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Nächste Schritte
- [Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
