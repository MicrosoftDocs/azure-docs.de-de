---
title: Hochverfügbarkeit für Hadoop – Azure HDInsight
description: Erfahren Sie, wie HDInsight-Cluster durch die Verwendung eines zusätzlichen Hauptknotens eine höhere Zuverlässigkeit und Verfügbarkeit bieten. Sie erfahren, wie dies Hadoop-Dienste wie Ambari und Hive beeinflusst, und wie eine Verbindung mit jedem Hauptknoten über SSH hergestellt wird.
ms.reviewer: jasonh
author: hrasheed-msft
keywords: Hochverfügbarkeit, Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: hrasheed
ms.openlocfilehash: 6cb72730ef3dbef81e2b2c9bc1c5cfd3bbd88b65
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704929"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Verfügbarkeit und Zuverlässigkeit von Apache Hadoop-Clustern in HDInsight

HDInsight-Cluster stellen zwei Hauptknoten bereit, um die Verfügbarkeit und Zuverlässigkeit von ausgeführten Apache Hadoop-Diensten und -Aufträgen zu verbessern.

Hadoop erzielt Hochverfügbarkeit und Zuverlässigkeit durch das Replizieren von Diensten und Daten über mehrere Knoten in einem Cluster. Hadoop-Standarddistributionen weisen jedoch typischerweise nur einen einzigen Hauptknoten auf. Jeder Ausfall dieses Hauptknotens kann dazu führen, dass der Cluster nicht mehr funktioniert. HDInsight bietet zwei Hauptknoten, um die Verfügbarkeit und Zuverlässigkeit von Hadoop zu verbessern.

## <a name="availability-and-reliability-of-nodes"></a>Verfügbarkeit und Zuverlässigkeit von Knoten

Knoten in einem HDInsight-Cluster werden mithilfe von virtuellen Azure-Computern implementiert. In den folgenden Abschnitten werden die einzelnen Arten von Knoten erläutert, die mit HDInsight verwendet werden. 

> [!NOTE]  
> Es werden nicht alle Knotentypen für alle Clustertypen verwendet. Ein Hadoop-Cluster weist z.B. keine Nimbusknoten auf. Weitere Informationen zu den von den HDInsight-Clustertypen verwendeten Knoten finden Sie im Dokument [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) im Abschnitt „Clustertypen“.

### <a name="head-nodes"></a>Hauptknoten

Zum Sicherstellen der Hochverfügbarkeit von Hadoop-Diensten stellt HDInsight zwei Hauptknoten bereit. Beide Hauptknoten sind aktiv und werden gleichzeitig innerhalb des HDInsight-Clusters ausgeführt. Einige Dienste wie Apache HDFS oder Apache Hadoop YARN sind jeweils nur auf einem Hauptknoten „aktiv“. Andere Dienste (z. B. HiveServer2 oder Hive-MetaStore) sind gleichzeitig auf beiden Hauptknoten aktiv.

Hauptknoten (und andere Knoten in HDInsight) verfügen über numerische Werte als Teil des Hostnamens des Knotens. Beispiel: `hn0-CLUSTERNAME` oder `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> Leiten Sie vom numerischen Wert nicht ab, ob ein Knoten primär oder sekundär ist. Der numerische Wert soll nur sicherstellen, dass jeder Knoten einen eindeutigen Namen hat.

### <a name="nimbus-nodes"></a>Nimbusknoten

Nimbusknoten sind in Apache Storm-Clustern verfügbar. Die Nimbusknoten stellen die gleiche Funktionalität für den Hadoop JobTracker bereit, indem sie die Verarbeitung auf Workerknoten verteilen und überwachen. HDInsight stellt zwei Nimbusknoten für Storm-Cluster zur Verfügung.

### <a name="apache-zookeeper-nodes"></a>Apache ZooKeeper-Knoten

[ZooKeeper](https://zookeeper.apache.org/)-Knoten werden für die Auswahl der übergeordneten Dienste auf Hauptknoten verwendet. Sie werden auch verwendet, um sicherzustellen, dass die Dienste, Datenknoten (Worker) und Gateways wissen, auf welchem Hauptknoten ein übergeordneter Dienst aktiv ist. Standardmäßig stellt HDInsight drei ZooKeeper-Knoten bereit.

### <a name="worker-nodes"></a>Workerknoten

Workerknoten führen die eigentliche Analyse aus, wenn ein Auftrag an den Cluster übermittelt wird. Wenn ein Workerknoten ausfällt, wird die Aufgabe, die auf dem Knoten gerade ausgeführt wurde, an einen anderen Workerknoten übermittelt. HDInsight erstellt standardmäßig vier Workerknoten. Sie können diese Anzahl sowohl während als auch nach der Erstellung des Clusters Ihren Bedürfnissen gemäß ändern.

### <a name="edge-node"></a>Edgeknoten

Ein Edgeknoten nimmt nicht aktiv an der Datenanalyse innerhalb des Clusters teil. Er wird von Entwicklern oder Datenspezialisten bei der Arbeit mit Hadoop verwendet. Der Edgeknoten befindet sich im gleichen virtuellen Azure-Netzwerk wie die anderen Knoten im Cluster und kann direkt auf alle anderen Knoten zugreifen. Der Edgeknoten kann verwendet werden, ohne dass er Ressourcen von wichtigen Hadoop-Diensten oder Analyseaufträgen verbraucht.

Zurzeit ist ML Services in HDInsight der einzige Clustertyp, der standardmäßig einen Edgeknoten bereitstellt. Für ML Services in HDInsight wird der Edgeknoten verwendet, um R-Code lokal auf dem Knoten zu testen, bevor er zur verteilten Verarbeitung an den Cluster übermittelt wird.

Informationen zur Verwendung eines Edgeknotens mit anderen Clustertypen finden Sie im Dokument [Verwenden von Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Zugreifen auf die Knoten

Zugriff auf den Cluster über das Internet ist über ein öffentliches Gateway möglich. Der Zugriff ist auf das Herstellen einer Verbindung mit den Hauptknoten und (falls vorhanden) dem Edgeknoten beschränkt. Der Zugriff auf Dienste, die auf den Hauptknoten ausgeführt werden, wird durch das Vorhandensein mehrerer Hauptknoten nicht beeinflusst. Das öffentliche Gateway leitet Anforderungen an den Hauptknoten, der den angeforderten Dienst hostet. Ein Beispiel: Apache Ambari wird zurzeit auf dem sekundären Hauptknoten gehostet, und das Gateway leitet eingehende Anforderungen für Ambari an diesen Knoten weiter.

Der Zugriff über das öffentliche Gateway ist auf die Ports 443 (HTTPS), 22 und 23 beschränkt.

* Port __443__ wird für den Zugriff auf Ambari und andere Webbenutzeroberflächen oder REST-APIs verwendet, die auf dem Hauptknoten gehostet werden.

* Port __22__ wird für den Zugriff auf den primären Hauptknoten oder den Edgeknoten mit SSH verwendet.

* Port __23__ wird für den Zugriff auf den sekundären Hauptknoten mit SSH verwendet. `ssh username@mycluster-ssh.azurehdinsight.net` stellt z.B. eine Verbindung mit dem primären Hauptknoten des Clusters mit der Bezeichnung **mycluster** her.

Weitere Informationen zur Verwendung von SSH finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interne vollqualifizierte Domänennamen (FQDN)

Knoten in einem HDInsight-Cluster besitzen eine interne IP-Adresse und einen internen FQDN, auf die nur über den Cluster zugegriffen werden kann. Wenn Sie mit dem internen FQDN oder der internen IP-Adresse auf Dienste im Cluster zugreifen, sollten Sie die zu verwendende IP-Adresse oder den FQDN mithilfe von Ambari überprüfen.

Der Apache Oozie-Dienst kann z. B. nur auf einem Hauptknoten ausgeführt werden, und zur Verwendung des `oozie`-Befehls in einer SSH-Sitzung ist die URL des Diensts erforderlich. Diese URL kann mit dem folgenden Befehl aus Ambari abgerufen werden:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Dieser Befehl gibt einen dem folgenden Befehl ähnelnden Wert zurück, der die mit dem `oozie`-Befehl zu verwendende interne URL enthält:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Weitere Informationen zum Arbeiten mit der Ambari-REST-API finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Zugreifen auf andere Knotentypen

Mit den folgenden Methoden können Sie eine Verbindung mit Knoten herstellen, auf die nicht direkt über das Internet zugegriffen werden kann:

* **SSH**: Sobald über SSH eine Verbindung mit dem Hauptknoten hergestellt wurde, können Sie SSH auf dem Hauptknoten verwenden, um Verbindungen mit anderen Knoten im Cluster herzustellen. Weitere Informationen finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH-Tunnel**: Wenn Sie auf einen Webdienst zugreifen müssen, der auf einem der nicht direkt mit dem Internet verbundenen Knoten gehostet wird, müssen Sie einen SSH-Tunnel verwenden. Weitere Informationen finden Sie im Dokument [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

* **Azure Virtual Network**: Wenn Ihr HDInsight-Cluster zu einem Azure Virtual Network gehört, können alle Ressourcen in diesem virtuellen Netzwerk direkt auf alle Knoten im Cluster zugreifen. Weitere Informationen finden Sie im Dokument [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="how-to-check-on-a-service-status"></a>Überprüfen eines Dienststatus

Um den Status von Diensten zu überprüfen, die auf den Hauptknoten ausgeführt werden, können Sie sowohl die Ambari-Webbenutzeroberfläche als auch die Ambari-REST-API verwenden.

### <a name="ambari-web-ui"></a>Ambari-Webbenutzeroberfläche

Die Ambari-Webbenutzeroberfläche kann unter `https://CLUSTERNAME.azurehdinsight.net` angezeigt werden. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters. Wenn Sie dazu aufgefordert werden, geben Sie die HTTP-Anmeldeinformationen für Ihren Cluster ein. Der standardmäßige HTTP-Benutzername ist **admin** , und das Kennwort ist das Kennwort, das Sie beim Erstellen des Clusters eingegeben haben.

Die installierten Dienste werden im linken Bereich der Ambari-Seite aufgelistet.

![Installierte Dienste](./media/hdinsight-high-availability-linux/services.png)

Neben den Diensten werden möglicherweise Statussymbole angezeigt. Alle Warnungen in Zusammenhang mit einem Dienst können über den Link **Warnungen** oben auf der Seite angezeigt werden.  In Ambari stehen mehrere vordefinierte Warnungen zur Verfügung.

Die folgenden Warnungen unterstützen die Überwachung der Verfügbarkeit eines Clusters:

| Name der Warnung                               | BESCHREIBUNG                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Metric Monitor Status (Status der Metriküberwachung)                    | Diese Warnung gibt den Status des Metric Monitor-Prozesses an, wie er vom Überwachungsstatusskript bestimmt wird.                                                                                   |
| Ambari Agent Heartbeat (Ambari-Agent-Heartbeat)                   | Diese Warnung wird ausgelöst, wenn der Server die Verbindung zu einem Agenten verloren hat.                                                                                                                        |
| ZooKeeper Server Process (ZooKeeper-Serverprozess)                 | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der ZooKeeper-Serverprozess aktiv ist und im Netzwerk lauscht.                                                               |
| IOCache Metadata Server Status (IOCache-Metadatenserverstatus)           | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der IOCache-Metadatenserver aktiv ist und auf die Anforderungen des Clients reagiert.                                                            |
| JournalNode Web UI (JournalNode-Webbenutzeroberfläche)                       | Diese Warnung auf Hostebene wird ausgelöst, wenn die JournalNode-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                                 |
| Spark2 Thrift Server (Spark2-Thrift-Server)                     | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Spark2-Thrift-Server aktiv ist.                                                                                                |
| History Server Process (Verlaufsserverprozess)                   | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Verlaufsserverprozess aktiv ist und im Netzwerk lauscht.                                                                |
| History Server Web UI (Verlaufsserver-Webbenutzeroberfläche)                    | Diese Warnung auf Hostebene wird ausgelöst, wenn die Verlaufsserver-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                              |
| ResourceManager-Webbenutzeroberfläche                   | Diese Warnung auf Hostebene wird ausgelöst, wenn die ResourceManager-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                             |
| NodeManager Health Summary (NodeManager-Integritätszusammenfassung)               | Diese Warnung auf Dienstebene wird ausgelöst, wenn es fehlerhafte NodeManager gibt.                                                                                                                    |
| App Timeline Web UI (App-Zeitskala-Webbenutzeroberfläche)                      | Diese Warnung auf Hostebene wird ausgelöst, wenn die App-Zeitskala-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                         |
| DataNode Health Summary (DataNode-Integritätszusammenfassung)                  | Diese Warnung auf Dienstebene wird ausgelöst, wenn es fehlerhafte DataNodes gibt.                                                                                                                       |
| NameNode Web UI (NameNode-Webbenutzeroberfläche)                          | Diese Warnung auf Hostebene wird ausgelöst, wenn die NameNode-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                                    |
| ZooKeeper Failover Controller Process (ZooKeeper-Failover-Controllerprozess)    | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der ZooKeeper-Failover-Controllerprozess aktiv ist und im Netzwerk lauscht.                                                   |
| Oozie Server Web UI (Oozie-Server-Webbenutzeroberfläche)                      | Diese Warnung auf Hostebene wird ausgelöst, wenn die Oozie-Server Webbenutzeroberfläche nicht erreichbar ist.                                                                                                                |
| Oozie Server Status (Oozie-Serverstatus)                      | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Oozie-Server aktiv ist und auf die Anforderungen des Clients reagiert.                                                                      |
| Hive Metastore Process (Hive Metastore-Prozess)                   | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Hive Metastore-Prozess aktiv ist und im Netzwerk lauscht.                                                                 |
| HiveServer2 Process (HiveServer2-Prozess)                      | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der HiveServer aktiv ist und auf die Anforderungen des Clients reagiert.                                                                        |
| WebHCat Server Status (WebHCat-Serverstatus)                    | Diese Warnung auf Hostebene wird ausgelöst, wenn der Templetonserverstatus fehlerhaft ist.                                                                                                            |
| Percent ZooKeeper Servers Available (Prozentsatz verfügbarer ZooKeeper-Server)      | Diese Warnung wird ausgelöst, wenn die Anzahl ausgefallener ZooKeeper-Server im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der ZooKeeper-Prozessüberprüfungen.     |
| Spark2 Livy Server (Spark2 Livy-Server)                       | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Livy2-Server aktiv ist.                                                                                                        |
| Spark2 History Server (Spark2-Verlaufsserver)                    | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Spark2-Verlaufsserver aktiv ist.                                                                                               |
| Metrics Collector Process (Metrics-Collectorprozess)                | Diese Warnung wird ausgelöst, wenn nicht bestätigt werden kann, dass der Metrics-Collector aktiv ist und für eine bestimmte Anzahl an Sekunden auf den konfigurierten Port lauscht, die als Schwellenwert festgelegt wurde.                                 |
| Metrics Collector - HBase Master Process (Metrics-Collector – HBase Master-Prozess) | Diese Warnung wird ausgelöst, wenn nicht bestätigt werden kann, dass die HBase Master-Prozesse des Metrics-Collectors aktiv sind und für eine bestimmte Anzahl an Sekunden im Netzwerk lauschen, die als kritischer Schwellenwert konfiguriert wurde. |
| Percent Metrics Monitors Available (Prozentsatz verfügbarer Metrics Monitor-Prozesse)       | Diese Warnung wird ausgelöst, wenn ein Prozentsatz der Metrics Monitor-Prozesse nicht aktiv ist und im Netzwerk an der konfigurierten Warnung und den kritischen Schwellenwerten lauscht.                             |
| Percent NodeManagers Available (Prozentsatz verfügbarer NodeManager)           | Diese Warnung wird ausgelöst, wenn die Anzahl der ausgefallenen NodeManager im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der NodeManager-Prozessüberprüfungen.        |
| NodeManager Health (NodeManager-Integrität)                       | Diese Warnung auf Hostebene prüft die Knotenintegritätseigenschaft, die über die NodeManager-Komponente zur Verfügung steht.                                                                                              |
| NodeManager Web UI (NodeManager-Webbenutzeroberfläche)                       | Diese Warnung auf Hostebene wird ausgelöst, wenn die NodeManager-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                                 |
| NameNode High Availability Health (NameNode-Hochverfügbarkeitsintegrität)        | Diese Warnung auf Dienstebene wird ausgelöst, wenn entweder der aktive NameNode oder der Standby-NameNode nicht ausgeführt werden.                                                                                     |
| DataNode Process (DataNode-Prozess)                         | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob die individuellen DataNode-Prozesse aktiv sind und im Netzwerk lauschen.                                                         |
| DataNode Web UI (DataNode-Webbenutzeroberfläche)                          | Diese Warnung auf Hostebene wird ausgelöst, wenn die DataNode-Webbenutzeroberfläche nicht erreichbar ist.                                                                                                                    |
| Percent JournalNodes Available (Prozentsatz verfügbarer JournalNodes)           | Diese Warnung wird ausgelöst, wenn die Anzahl der ausgefallenen JournalNodes im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der JournalNode-Prozessüberprüfungen.        |
| Percent DataNodes Available (Prozentsatz verfügbarer DataNodes)              | Diese Warnung wird ausgelöst, wenn die Anzahl der ausgefallenen DataNodes im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der DataNode-Prozessüberprüfungen.              |
| Zeppelin Server Status (Zeppelin Serverstatus)                   | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, ob der Zeppelin-Server aktiv ist und auf die Anforderungen des Clients reagiert.                                                                   |
| HiveServer2 Interactive Process (HiveServer2 Interactive-Prozess)          | Diese Warnung auf Hostebene wird ausgelöst, wenn nicht bestimmt werden kann, dass der HiveServerInteractive-Prozess aktiv ist und auf die Anforderungen des Clients reagiert.                                                             |
| LLAP Application (LLAP-Anwendung)                         | Diese Warnung wird ausgelöst, wenn nicht bestimmt werden kann, ob die LLAP-Anwendung aktiv ist und auf Anforderungen reagiert.                                                                                    |

Sie können einen Dienst auswählen, um weitere Informationen anzuzeigen.

Auf der Dienstseite werden zwar Informationen zum Status und zur Konfiguration jedes Diensts angezeigt, aber keine Informationen dazu, auf welchem Hauptknoten der Dienst ausgeführt wird. Diese Informationen können über den Link **Hosts** oben auf der Seite aufgerufen werden. Wenn Sie auf den Link klicken, werden die Hosts innerhalb des Clusters angezeigt, einschließlich der Hauptknoten.

![Liste der Hosts](./media/hdinsight-high-availability-linux/hosts.png)

Wenn Sie auf den Link für einen der Hauptknoten klicken, werden die auf diesem Knoten ausgeführten Dienste und Komponenten angezeigt.

![Komponentenstatus](./media/hdinsight-high-availability-linux/nodeservices.png)

Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari-REST-API

Die Ambari-REST-API ist über das Internet verfügbar. Das öffentliche Gateway von HDInsight verarbeitet Routinganforderungen an den Hauptknoten, auf dem die REST-API derzeit gehostet wird.

Sie können den folgenden Befehl verwenden, um den Status eines Diensts über die Ambari-REST-API zu überprüfen:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Ersetzen Sie **PASSWORD** durch das Kontokennwort des HTTP-Benutzers (Administrator).
* Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters.
* Ersetzen Sie **SERVICENAME** durch den Namen des Diensts, dessen Status Sie überprüfen möchten.

Wenn Sie z.B. den Status des **HDFS**-Diensts in einem Cluster mit dem Namen **mycluster** überprüfen möchten und das Kennwort **password** lautet, würden Sie folgenden Befehl verwenden:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Die Antwort ähnelt dem folgenden JSON-Code:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Der URL können Sie entnehmen, dass der Dienst zurzeit auf einem Hauptknoten namens **hn0-CLUSTERNAME** ausgeführt wird.

Der Statuswert (state) gibt an, dass der Dienst zurzeit ausgeführt wird bzw. sich im Status **STARTED** befindet.

Falls Sie nicht wissen, welche Dienste auf dem Cluster installiert sind, können Sie mit dem folgenden Befehl eine Liste abrufen:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Weitere Informationen zum Arbeiten mit der Ambari-REST-API finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Dienstkomponenten

Dienste können Komponenten enthalten, deren Status Sie einzeln überprüfen möchten. HDFS enthält z. B. die NameNode-Komponente. Der Befehl zum Anzeigen von Informationen zu einer Komponente lautet wie folgt:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Falls Sie nicht wissen, welche Komponenten von einem Dienst bereitgestellt werden, können Sie mit dem folgenden Befehl eine Liste abrufen:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Zugreifen auf Protokolldateien auf den Hauptknoten

### <a name="ssh"></a>SSH

Wenn Sie über SSH mit einem Hauptknoten verbunden sind, sind die Protokolldateien unter **/Var/Log** verfügbar. **/var/log/hadoop-yarn/yarn** enthält z.B. Protokolle für YARN.

Da für jeden Hauptknoten spezifische Protokolleinträge vorhanden sein können, sollten Sie die Protokolle auf beiden Hauptknoten überprüfen.

### <a name="sftp"></a>SFTP

Sie können auch über das SSH File Transfer Protocol oder das Secure File Transfer Protocol (SFTP) eine Verbindung mit dem Hauptknoten herstellen und die Protokolldateien direkt herunterladen.

Ähnlich wie bei der Verwendung eines SSH-Clients müssen Sie beim Herstellen einer Verbindung mit dem Cluster den SSH-Benutzerkontonamen und die SSD-Adresse des Clusters angeben. Beispiel: `sftp username@mycluster-ssh.azurehdinsight.net`. Geben Sie das Kennwort für das Konto ein, wenn Sie dazu aufgefordert werden, oder stellen Sie mit dem Parameter `-i` einen öffentlichen Schlüssel bereit.

Wenn die Verbindung hergestellt ist, wird Ihnen eine `sftp>`-Eingabeaufforderung angezeigt. An dieser Eingabeaufforderung können Sie Verzeichnisse wechseln sowie Dateien hoch- und herunterladen. Durch die folgenden Befehle wird z.B. zum Verzeichnis **/var/log/hadoop/hdfs** gewechselt, und anschließend werden alle Dateien in diesem Verzeichnis heruntergeladen.

    cd /var/log/hadoop/hdfs
    get *

Um eine Liste aller verfügbaren Befehle zu erhalten, geben Sie an der `sftp>`-Eingabeaufforderung den Befehlt `help` ein.

> [!NOTE]  
> Es gibt auch grafische Benutzeroberflächen, mit denen Sie das Dateisystem visuell anzeigen können, wenn eine Verbindung über SFTP hergestellt wurde. [MobaXTerm](https://mobaxterm.mobatek.net/) z. B. ermöglicht das Durchsuchen des Dateisystems in einer Benutzeroberfläche ähnlich Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Um mit Ambari auf die Protokolldateien zuzugreifen, verwenden Sie einen SSH-Tunnel. Die Webschnittstellen für die einzelnen Dienste sind nicht im Internet veröffentlicht. Informationen zum Verwenden eines SSH-Tunnels finden Sie im Artikel [Verwenden von SSH-Tunneling](hdinsight-linux-ambari-ssh-tunnel.md).

Wählen Sie auf der Ambari-Webbenutzeroberfläche den Dienst, für den Sie Protokolle anzeigen möchten (z.B. YARN). Wählen Sie dann mit **Quick Links** den Hauptknoten aus, dessen Protokolle Sie anzeigen möchten.

![Verwenden von Quicklinks zum Anzeigen von Protokollen](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Konfigurieren der Knotengröße

Die Größe eines Knotens kann nur beim Erstellen des Clusters ausgewählt werden. Eine Liste der verschiedenen VM-Größen, die für HDInsight verfügbar sind, finden Sie auf der Seite [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

Beim Erstellen eines Clusters können Sie die Größe der Knoten angeben. Die folgenden Informationen bieten eine Anleitung, wie die Größe über das [Azure-Portal][preview-portal], das [Azure PowerShell-Modul Az][azure-powershell] und mit der [Azure CLI][azure-cli] festgelegt werden kann:

* **Azure-Portal**: Beim Erstellen eines Clusters können Sie die Größe der vom Cluster genutzten Knoten festlegen:

    ![Screenshot des Assistenten zur Clustererstellung mit der Auswahl der Knotengröße](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: Die Größe der Haupt-, Worker- und ZooKeeper-Knoten kann bei Verwendung des Befehls [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) mit den Parametern `--headnode-size`, `--workernode-size` und `--zookeepernode-size` festgelegt werden.

* **Azure PowerShell:** Die Größe der Haupt-, Worker- und ZooKeeper-Knoten kann bei Verwendung des Cmdlets [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) mit den Parametern `-HeadNodeSize`, `-WorkerNodeSize` und `-ZookeeperNodeSize` festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

Über die folgenden Links erhalten Sie weiterführende Informationen.

* [Apache Ambari-REST-Referenz](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installieren und Konfigurieren des Azure PowerShell-Moduls Az](/powershell/azure/overview)
* [Verwalten von HDInsight mit Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
