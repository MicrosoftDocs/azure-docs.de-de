---
title: Einrichten von IBM Db2 HADR auf virtuellen Azure-Computern (VMs) | Microsoft-Dokumentation
description: Richten Sie die Hochverfügbarkeit von IBM Db2 LUW auf virtuellen Azure-Computern (VMs) ein.
author: msjuergent
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 54bde8c9dd47e88ffdc831ccb9f7833720583238
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621381"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hochverfügbarkeit von IBM Db2 LUW auf Azure-VMs unter SUSE Linux Enterprise Server mit Pacemaker

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
| [1984787] | SUSE LINUX Enterprise Server 12: Installationshinweise |
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
| [Leitfäden für bewährte Methoden zu SUSE Linux Enterprise Server for SAP Applications 12 SP4][sles-for-sap-bp] |
| [SUSE Linux Enterprise High Availability Extension 12 SP4][sles-ha-guide] |
| [Azure Virtual Machines – IBM Db2-DBMS-Bereitstellung für SAP-Workload][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Übersicht
Um Hochverfügbarkeit zu erreichen, wird IBM Db2 LUW mit HADR auf mindestens zwei virtuellen Azure-Computern installiert, die in einer [Azure-Verfügbarkeitsgruppe](../../windows/tutorial-availability-sets.md) oder über [Azure-Verfügbarkeitszonen](./sap-ha-availability-zones.md) bereitgestellt werden. 

Die folgenden Grafiken zeigen eine Konfiguration von zwei auf virtuellen Azure-Computern installierten Datenbankservern. Jeder dieser auf einem virtuellen Azure-Computer installierten Datenbankserver hat seinen eigenen zugeordneten Speicher und ist aktiv und wird ausgeführt. In HADR hat eine Datenbankinstanz auf einem der virtuellen Azure-Computer die Rolle der primären Instanz. Alle Clients werden mit dieser primären Instanz verbunden. Alle in Datenbanktransaktionen vorgenommenen Änderungen werden lokal im Db2-Transaktionsprotokoll gespeichert. Da die Transaktionsprotokolldatensätze lokal gespeichert werden, werden die Datensätze über TCP/IP an die Datenbankinstanz auf dem zweiten Datenbankserver übertragen, der als Standbyserver oder Standbyinstanz bezeichnet wird. In der Standbyinstanz wird die lokale Datenbank aktualisiert, indem ein Rollforward mit den übertragenen Transaktionsprotokolldatensätzen ausgeführt wird. Auf diese Weise bleibt der Standbyserver mit dem primären Server synchronisiert.

HADR ist nur eine Replikationsfunktionalität. Sie bietet weder Fehlererkennung noch automatische Übernahme noch Failoverkomponenten. Eine Übernahme durch den oder Übertragung an den Standbyserver muss manuell von einem Datenbankadministrator initiiert werden. Um eine automatische Übernahme und Fehlererkennung zu erreichen, können Sie den Linux-Clusterressourcen-Manager Pacemaker verwenden. Pacemaker überwacht die beiden Datenbankserverinstanzen. Wenn die primäre Datenbankserverinstanz abstürzt, initiiert Pacemaker eine *automatische* HADR-Übernahme durch den Standbyserver. Der Herzschrittmacher stellt auch sicher, dass die virtuelle IP-Adresse dem neuen primären Server zugewiesen wird.

![Übersicht über die Hochverfügbarkeit von IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Damit SAP-Anwendungsservern eine Verbindung mit einer primären Datenbank herstellen können, benötigen Sie einen virtuellen Hostnamen und eine virtuelle IP-Adresse. Im Fall eines Failovers werden die SAP-Anwendungsserver mit der neuen primären Datenbankinstanz verbunden. In einer Azure-Umgebung ist ein [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) erforderlich, um eine virtuelle IP-Adresse so zu verwenden, wie dies für HADR von IBM Db2 erforderlich ist. 

Damit Sie vollständig verstehen zu können, wie IBM Db2 LUW mit HADR und Pacemaker in die Konfiguration eines hochverfügbaren SAP-Systems passen, enthält die folgende Abbildung eine Übersicht über ein hochverfügbares SAP-System, das auf einer IBM Db2-Datenbank basiert. In diesem Artikel geht es nur um IBM Db2, es werden jedoch Verweise auf andere Artikel zum Einrichten anderer Komponenten eines SAP-Systems bereitgestellt.

![Übersicht über die vollständige Umgebung von IBM DB2-Hochverfügbarkeit](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Allgemeine Übersicht über die erforderlichen Schritte
Um eine IBM Db2-Konfiguration bereitzustellen, müssen die folgenden Schritte ausgeführt werden:

  + Planen der Umgebung.
  + Stellen Sie die VMs bereit.
  + Aktualisieren von SUSE Linux und Konfigurieren von Dateisystemen.
  + Installieren und Konfigurieren von Pacemaker.
  + Installieren eines [hochverfügbaren NFS][nfs-ha].
  + Installieren von [ASCS/ERS in einem separaten Cluster][ascs-ha].
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
| Azure-Umgrenzung | Azure-Umgrenzung oder SBD-Umgrenzung (dringend empfohlen); Methode zum Verhindern von Split Brain-Situationen. |
| Virtueller SBD-Computer | Größe, Speicher und Netzwerk des virtuellen SBD-Computers. |
| Azure Load Balancer | Verwendung von Basic oder Standard (empfohlen), Testport für die Db2-Datenbank (Empfehlung ist 62500) Prüfpunkt; **probe-port**. |
| Namensauflösung| Funktionsweise der Namensauflösung in der Umgebung. DNS-Dienst wird dringend empfohlen. Lokale hosts-Datei kann verwendet werden. |
    
Weitere Informationen zu Linux Pacemaker in Azure finden Sie unter [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Bereitstellung unter SUSE Linux

Der Ressourcen-Agent für IBM Db2 LUW ist in SUSE Linux Enterprise Server for SAP Applications enthalten. Für das in diesem Dokument beschriebene Setup müssen Sie SUSE Linux Server for SAP Applications verwenden. Der Azure Marketplace enthält ein Image für SUSE Linux Enterprise Server for SAP Applications 12, das Sie zum Bereitstellen neuer virtueller Azure-Computer verwenden können. Beachten Sie die verschiedenen Support- oder Servicemodelle, die SUSE über den Azure Marketplace anbietet, wenn Sie ein VM-Image im Azure VM Marketplace auswählen. 

### <a name="hosts-dns-updates"></a>Hosts: DNS-Updates
Erstellen Sie eine Liste aller Hostnamen, einschließlich der Namen virtueller Hosts, und aktualisieren Sie Ihre DNS-Server, um die richtige Auflösung von IP-Adressen in Hostnamen zu aktivieren. Wenn kein DNS-Server vorhanden ist oder Sie keine DNS-Einträge aktualisieren und erstellen können, müssen Sie die lokalen host-Dateien der einzelnen VMs verwenden, die an diesem Szenario teilnehmen. Wenn Sie Einträge aus host-Dateien verwenden, stellen Sie sicher, dass die Einträge auf alle virtuellen Computer in der SAP-Systemumgebung angewendet werden. Allerdings empfehlen wir, dass Sie Ihr DNS verwenden, das im Idealfall in Azure erweitert wird.


### <a name="manual-deployment"></a>Manuelle Bereitstellung

Vergewissern Sie sich, dass das ausgewählte Betriebssystem von IBM/SAP für IBM Db2 LUW unterstützt wird. Die Liste der unterstützten Betriebssystemversionen für virtuelle Azure-Computer und Db2-Versionen finden Sie im SAP-Hinweis [1928533]. Die Liste der Betriebssystemversionen zu den einzelnen Db2-Releases ist in der SAP-Produktverfügbarkeitsmatrix verfügbar. Es wird dringend empfohlen, mindestens SLES 12 SP4 zu verwenden, denn ab dieser SUSE Linux-Version gibt es Azure-bezogene Leistungsverbesserungen.

1. Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus.
1. Erstellen Sie ein virtuelles Netzwerks und ein Subnetz, oder wählen Sie vorhandene Ressourcen aus.
1. Erstellen Sie eine Azure-Verfügbarkeitsgruppe, oder stellen Sie eine Verfügbarkeitszone bereit.
    + Legen Sie für die Verfügbarkeitsgruppe die maximale Anzahl von Updatedomänen auf 2 fest.
1. Erstellen Sie den virtuellen Computer 1.
    + Verwenden Sie SLES für das SAP-Images im Azure Marketplace.
    + Wählen Sie die Azure-Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben, oder wählen Sie eine Verfügbarkeitszone aus.
1.  Erstellen Sie den virtuellen Computer 2.
    + Verwenden Sie SLES für das SAP-Images im Azure Marketplace.
    + Wählen Sie die Azure-Verfügbarkeitsgruppe aus, die Sie in Schritt 3 erstellt haben, oder wählen Sie eine Verfügbarkeitszone (nicht die gleiche Zone wie in Schritt 3) aus.
1. Fügen Sie den virtuellen Computern Datenträger hinzu. Prüfen Sie dann die Empfehlung für die Dateisystemkonfiguration, die Sie im Artikel [Azure Virtual Machines – IBM DB2-DBMS-Bereitstellung für SAP-Workload][dbms-db2] finden.

## <a name="create-the-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters
    
Informationen zum Erstellen eines grundlegenden Pacemaker-Clusters für diesen IBM Db2-Server finden Sie unter [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure][sles-pacemaker]. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationshinweise für das Einrichten von IBM Db2 LUW mit HADR

So richten Sie die primäre IBM Db2 LUW-Datenbankinstanz ein:

- Verwenden Sie die Option für Hochverfügbarkeit oder für Verteilung (Distributed).
- Installieren Sie SAP ASCS/ERS und die Datenbankinstanz.
- Erstellen Sie eine Sicherung der neu installierten Datenbank.


> [!IMPORTANT] 
> Notieren Sie sich den „Database Communication port“, den Sie während der Installation festgelegt haben. Für beide Datenbankinstanzen muss dieselbe Portnummer verwendet werden.

Um den Standbydatenbankserver mit der SAP-Prozedur „Homogeneous System Copy“ einzurichten, führen Sie die folgenden Schritte aus:

1. Verwenden Sie die Option **System copy** > **Target systems** > **Distributed** > **Datenbankinstanz**.
1. Wählen Sie als Kopiermethode die Option **Homogeneous System** aus, damit Sie „backup“ verwenden können, um eine Sicherung auf der Standbyserverinstanz wiederherstellen zu können.
1. Wenn Sie den Schritt erreichen, in dem für „Homogeneous System Copy“ das Wiederherstellen beendet werden soll, beenden Sie das Installationsprogramm. Stellen Sie die Datenbank aus einer Sicherung des primären Hosts wieder her. Alle weiteren Installationsphasen wurden bereits auf dem primären Datenbankserver ausgeführt.
1. Richten Sie HADR für IBM Db2 ein.

   > [!NOTE]
   > Installation und Konfiguration speziell für Azure und Pacemaker: Während der Installation durch SAP Software Provisioning Manager gibt es eine explizite Frage zu Hochverfügbarkeit für IBM Db2 LUW:
   >+ Wählen Sie nicht **IBM Db2 pureScale** aus.
   >+ Wählen Sie nicht **Install IBM Tivoli System Automation for Multiplatforms** aus.
   >+ Wählen Sie nicht **Generate cluster configration files** aus.

   Wenn Sie ein SBD-Gerät für Linux Pacemaker verwenden, legen Sie die folgenden Db2 HADR-Parameter fest.
   + HADR-Peerfensterdauer (Sekunden) (HADR_PEER_WINDOW) = 300  
   + HADR-Timeoutwert (HADR_TIMEOUT) = 60

   Wenn Sie einen Azure Pacemaker-Fencing-Agent verwenden, legen Sie die folgenden Parameter fest:
   + HADR-Peerfensterdauer (Sekunden) (HADR_PEER_WINDOW) = 900  
   + HADR-Timeoutwert (HADR_TIMEOUT) = 60

Wie empfehlen die oben genannten Parameterwerte basierend auf dem anfänglichen Testen für Failover/Übernahme. Es ist zwingend erforderlich, die ordnungsgemäße Funktionalität von Übernahme und Failover mit diesen Parametereinstellungen zu testen. Da die einzelne Konfigurationen variieren können, müssen diese Parameter möglicherweise angepasst werden. 

> [!IMPORTANT]
> Spezifisch für IBM Db2 mit HADR-Konfiguration mit normalem Start: Die sekundäre oder Standbydatenbankinstanz muss aktiv sein und ausgeführt werden, bevor Sie die primäre Datenbankinstanz starten können.

Zu Demonstrationszwecken und für die Vorgehensweisen, die in diesem Artikel beschrieben werden, lautet die Datenbank-SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR-Überprüfung
Nachdem Sie HADR konfiguriert haben und der Status PEER und CONNECTED für den primären und die Standbyknoten lautet, führen Sie die folgende Überprüfung durch:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-Konfiguration

Wenn Sie Pacemaker für automatisches Failover im Fall eines Knotenausfalls verwenden, müssen Sie Ihre Db2-Instanzen und Pacemaker entsprechend konfigurieren. In diesem Abschnitt werden die zugehörigen Konfigurationsschritte beschrieben.

Die folgenden Elemente haben eines der folgenden Präfixe:

- **[A]** : Gilt für alle Knoten
- **[1]** : Gilt nur für Knoten 1 
- **[2]** : Gilt nur für Knoten 2

**[A]** Voraussetzungen für die Pacemaker-Konfiguration
1. Fahren Sie beide Datenbankserver mit dem Benutzer „db2\<sid>“ mit „db2stop“ herunter.
1. Ändern Sie die Shellumgebung für „db2\<sid>“ in */bin/ksh*. Es wird empfohlen, das Yast-Tool zu verwenden. 


### <a name="pacemaker-configuration"></a>Pacemaker-Konfiguration

> [!IMPORTANT]
> Kürzlich durchgeführte Tests haben Situationen aufgezeigt, in denen netcat aufgrund von Backlog und der Einschränkung, nur eine Verbindung zu verarbeiten, nicht mehr auf Anforderungen reagiert. Die netcat-Ressource lauscht dann nicht mehr auf Azure Load Balancer-Anforderungen, und die Floating IP-Adresse ist nicht mehr verfügbar.  
> Für vorhandene Pacemaker-Cluster wurde zuvor empfohlen, netcat durch socat zu ersetzen. Zurzeit wird empfohlen, den Ressourcen-Agent azure-lb zu verwenden, der Teil des Pakets resource-agents ist. Dabei gelten die folgenden Versionsanforderungen für das Paket:
> - Für SLES 12 SP4/SP5 muss die Version mindestens resource-agents-4.3.018.a7fb5035-3.30.1 sein.  
> - Für SLES 15/15 SP1 muss die Version mindestens resource-agents-4.3.0184.6ee15eb2-4.13.1 sein.  
>
> Beachten Sie, dass für die Änderung eine kurze Ausfallzeit erforderlich ist.  
> Wenn die Konfiguration bei vorhandenen Pacemaker-Clustern bereits für die Verwendung von socat geändert wurde, wie unter [Azure Load Balancer-Erkennungshärtung](https://www.suse.com/support/kb/doc/?id=7024128) beschrieben, müssen Sie nicht sofort zum Ressourcen-Agent azure-lb wechseln.

**[1]** IBM Db2 HADR-spezifische Pacemaker-Konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Erstellen Sie IBM Db2-Ressourcen:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Starten Sie IBM Db2-Ressourcen:
* Nehmen Sie Pacemaker aus dem Wartungsmodus.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Stellen Sie sicher, dass der Clusterstatus „OK“ ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodes configured
# <a name="5-resources-configured"></a>5 resources configured

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Full list of resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Started azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb):      Started azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaves: [ azibmdb01 ]
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
 
1. Melden Sie sich beim primären Anwendungsserver der J2EE-Instanz an, und führen Sie Folgendes aus: `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Wählen Sie im linken Bereich die Option **security store** aus.
1. Wählen Sie im rechten Bereich den Schlüssel „jdbc/pool/\<SAPSID>/url“ aus.
1. Ändern Sie den Hostnamen in der JDBC-URL in den virtuellen Hostnamen.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Wählen Sie **Hinzufügen**.
1. Um die Änderungen zu speichern, klicken Sie auf das Datenträgersymbol in der oberen linken Ecke.
1. Schließen Sie das Konfigurationstool.
1. Starten Sie die Java-Instanz neu.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurieren von Protokollarchivierung für die HADR-Einrichtung
Um die Db2-Protokollarchivierung für die HADR-Einrichtung zu konfigurieren, empfiehlt es sich, dass Sie sowohl die primäre als auch die Standbydatenbank so konfigurieren, dass sie automatisch Protokolle aus allen Protokollspeicherorten abrufen können. Sowohl die primäre als auch die Standbydatenbank müssen in der Lage sein, Protokollarchivdateien aus allen Protokollarchivspeicherorten abrufen zu können, in denen eine der Datenbankinstanzen Protokolldateien archivieren könnte. 

Die Protokollarchivierung wird nur von der primären Datenbank ausgeführt. Wenn Sie die HADR-Rollen der Datenbankserver ändern, oder wenn ein Fehler auftritt, ist die neue primäre Datenbank für die Protokollarchivierung verantwortlich. Wenn Sie mehrere Speicherorte für das Protokollarchiv eingerichtet haben, werden Ihre Protokolle möglicherweise zwei Mal archiviert. Im Falle eines lokalen oder Remoteabgleichs müssen Sie möglicherweise auch die archivierten Protokolle vom alten primären Server manuell in den aktiven Protokollspeicher des neuen primären Servers kopieren.

Es empfiehlt sich, eine allgemeine NFS-Freigabe zu konfigurieren, in die Protokolle von beiden Knoten geschrieben werden. Die NFS-Freigabe muss hochverfügbar sein. 

Sie können vorhandene hochverfügbare NFS-Freigaben für Datentransporte oder ein Profilverzeichnis verwenden. Weitere Informationen finden Sie unter

- [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server][nfs-ha] 
- [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (zum Erstellen von NFS-Freigaben)


## <a name="test-the-cluster-setup"></a>Testen der Clustereinrichtung

In diesem Abschnitt ist beschrieben, wie Sie Ihre Db2 HADR-Einrichtung testen können. Für jeden Test wird vorausgesetzt, dass Sie als Benutzer *root* angemeldet sind und dass die primäre IBM Db2-Datenbank auf dem virtuellen Computer *azibmdb01* ausgeführt wird.

Der Anfangsstatus für alle Testfälle sieht wie folgt aus: („crm_mon -r“  oder „crm status“)

- **crm status** ist eine Momentaufnahme des Pacemaker-Status zum Ausführungszeitpunkt. 
- **crm_mon -r** ist eine kontinuierliche Ausgabe des Pacemaker-Status.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Der ursprüngliche Status in einem SAP-System ist in „Transaction DBACOCKPIT --> Configuration --> Overview“ dokumentiert, wie die folgende Abbildung zeigt:

![DBACockpit: vor Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testübernahme von IBM Db2


> [!IMPORTANT] 
> Bevor Sie den Test zu starten, stellen Sie Folgendes sicher:
> * Pacemaker keine fehlerhaften Aktionen (crm Status) aufweist.
> * Es keine Speicherorteinschränkungen (Überbleibsel von Migrationstests) gibt.
> * Die IBM Db2-HADR-Synchronisierung funktioniert. Überprüfen Sie die Funktionsweise mit dem Benutzer „db2\<sid>“ <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrieren Sie den Knoten, auf dem die primäre Db2-Datenbank ausgeführt wird, indem Sie folgenden Befehl ausführen:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Nachdem die Migration abgeschlossen ist, sieht die Ausgabe von „crm status“ wie folgt aus:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Der ursprüngliche Status in einem SAP-System ist in „Transaction DBACOCKPIT --> Configuration --> Overview“ dokumentiert, wie die folgende Abbildung zeigt:

![DBACockpit: nach der Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Die Ressourcenmigration mit „crm resource migrate“ führt zu Speicherorteinschränkungen. Die Speicherorteinschränkungen müssen gelöscht werden. Werden die Speicherorteinschränkungen nicht gelöscht, kann kein Failback für die Ressource ausgeführt werden, oder es kann zu unerwünschten Übernahmen kommen. 

Migrieren Sie die Ressourcen zurück zu *azibmdb01*, und löschen Sie die Speicherorteinschränkungen.
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm resource migrate \<res_name> \<host>:** Erstellt Speicherorteinschränkungen und kann Probleme mit Übernahmen verursachen.
- **crm resource clear \<res_name>** : Löscht Speicherorteinschränkungen.
- **crm resource cleanup \<res_name>** : Löscht alle Fehler der Ressource.

### <a name="test-the-fencing-agent"></a>Testen des Umgrenzungs-Agents

In diesem Fall testen wir SBD-Fencing, das wir Ihnen empfehlen, wenn Sie SUSE Linux verwenden.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Der Clusterknoten *azibmdb01* sollte neu gestartet werden. Die primäre IBM Db2 HADR-Rolle wird in *azibmdb02* verschoben. Wenn *azibmdb01* später wieder online ist, übernimmt die Db2-Instanz die Rolle einer sekundären Datenbankinstanz verschieben. 

Wenn der Pacemaker-Dienst auf dem neu gestarteten, vorherigen primären Server nicht automatisch gestartet wird, müssen Sie ihn manuell starten:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testen einer manuellen Übernahme

Sie können eine manuelle Übernahme testen, indem Sie den Pacemaker-Dienst auf dem Knoten *azibmdb01* stoppen:
<pre><code>service pacemaker stop</code></pre>

Status auf *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Nach dem Failover können Sie den Dienst wieder auf *azibmdb01* starten.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Beenden des Db2-Prozesses auf dem Knoten, auf dem die primäre HADR-Datenbank ausgeführt wird

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Die Db2-Instanz fällt aus, und Pacemaker meldet den folgenden Status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker startet die primäre Db2-Datenbankinstanz auf demselben Knoten neu oder führt ein Failover auf den Knoten aus, auf dem die sekundäre Datenbankinstanz ausgeführt wird. Danach wird ein Fehler gemeldet.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Beenden des Db2-Prozesses auf den Knoten, auf dem die sekundäre Datenbankinstanz ausgeführt wird

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Der Knoten wird in den Status „Ausgefallen“ versetzt, und es wird ein Fehler gemeldet.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Die Db2-Instanz wird in der sekundären Rolle neu gestartet, die ihr zuvor zugewiesen war.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Beenden von Db2 durch „db2stop force“ auf dem Knoten, auf dem die primäre HADR-Datenbankinstanz ausgeführt wird

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Führen Sie als Benutzer „db2\<sid>“ den Befehl „db2stop force“ aus:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Fehler erkannt
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Die sekundäre Db2 HADR-Datenbankinstanz wurde in die primäre Rolle hochgestuft.
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Absturz des virtuellen Computers mit Neustart auf dem Knoten, auf dem die primäre HADR-Datenbankinstanz ausgeführt wird

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker stuft die sekundäre Instanz in die primäre Instanzrolle hoch. Die alte primäre Instanz übernimmt die sekundäre Rolle, nachdem der virtuelle Computer und alle Dienste nach dem Neustart des virtuellen Computers vollständig wiederhergestellt sind:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Absturz des virtuellen Computers, auf dem die primäre HADR-Datenbankinstanz ausgeführt wird, mit „halt“

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In diesem Fall erkennt Pacemaker, dass der Knoten, auf dem die primäre Datenbankinstanz ausgeführt wird, nicht antwortet.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Der nächste Schritt besteht darin zu prüfen, ob eine *Split Brain*-Situation vorliegt. Sobald der weiterhin funktionsfähige Knoten ermittelt hat, dass der Knoten, auf dem die primäre Datenbankinstanz zuletzt ausgeführt wurde, nicht mehr verfügbar ist, wird ein Failover der Ressourcen ausgeführt.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Wurde der Knoten über „halt“ beendet, muss der ausgefallene Knoten über ein Azure-Verwaltungstool (im Azure-Portal, in PowerShell oder mit der Azure CLI) neu gestartet werden. Nachdem der ausgefallene Knoten wieder online ist, wird die Db2-Instanz in die sekundäre Rolle gestartet.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Nächste Schritte
- [Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Einrichten von Pacemaker unter SUSE Linux Enterprise Server in Azure](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
