---
title: Herstellen einer Verbindung mit oder Installieren von Apache Beeline – Azure HDInsight
description: Erfahren Sie, wie Sie eine Verbindung mit dem Beeline-Client herstellen, um Hive-Abfragen mit Hadoop in HDInsight auszuführen. Beeline ist ein Dienstprogramm zum Arbeiten mit HiveServer2 über JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: a0a693b2dc2c236cbac79ec4789b2a480ce65427
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028936"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Herstellen einer Verbindung mit Apache Beeline in HDInsight oder lokales Installieren

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) ist ein Hive-Client, der auf den Hauptknoten des HDInsight-Clusters enthalten ist. In diesem Artikel wird beschrieben, wie Sie über verschiedene Verbindungstypen eine Verbindung zu dem auf Ihrem HDInsight-Cluster installierten Beeline-Client herstellen können. Außerdem wird erläutert, wie Sie den [Beeline-Client lokal installieren](#install-beeline-client). 

## <a name="types-of-connections"></a>Arten von Verbindungen

### <a name="from-an-ssh-session"></a>Über eine SSH-Sitzung

Beim Herstellen einer Verbindung aus einer SSH-Sitzung mit einem Clusterhauptknoten können Sie anschließend eine Verbindung mit der `headnodehost`-Adresse an Port `10001` herstellen:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Über ein virtuelles Azure-Netzwerk

Beim Herstellen einer Verbindung von einem Client zu HDInsight über ein virtuelles Azure-Netzwerk müssen Sie den vollqualifizierten Domänennamen (FQDN) eines Clusterhauptknotens angeben. Da diese Verbindung direkt mit den Clusterknoten hergestellt wird, wird für die Verbindung Port `10001` verwendet:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersetzen Sie `<headnode-FQDN>` durch den vollqualifizierten Domänennamen eines Clusterhauptknotens. Verwenden Sie die Informationen im Dokument [Verwalten von HDInsight mithilfe der Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes), um den vollqualifizierten Domänennamen eines Hauptknotens zu ermitteln.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Mit einem Cluster des HDInsight-Enterprise-Sicherheitspakets (ESP) mit Kerberos

Beim Herstellen einer Verbindung vom Client mit einem ESP-Cluster (Enterprise-Sicherheitspaket), der mit Azure Active Directory (AAD)-DS auf einem Computer im selben Bereich des Clusters verknüpft ist, müssen Sie auch den Domänennamen `<AAD-Domain>` und den Namen eines Domänenbenutzerkontos mit Berechtigungen für den Zugriff auf den Cluster `<username>` angeben:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersetzen Sie `<username>` durch den Namen eines Kontos in der Domäne mit der Berechtigung für den Zugriff auf den Cluster. Ersetzen Sie `<AAD-DOMAIN>` durch den Namen der Azure Active Directory-Instanz (AAD), der der Cluster angehört. Verwenden Sie eine Zeichenfolge aus Großbuchstaben für den Wert `<AAD-DOMAIN>`, da die Anmeldeinformationen sonst nicht gefunden werden. Überprüfen Sie `/etc/krb5.conf` bei Bedarf auf die Bereichsnamen.

So finden Sie die JDBC-URL über Ambari:

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, wobei `CLUSTERNAME` der Name Ihres Clusters ist. Stellen Sie sicher, dass HiveServer2 ausgeführt wird.

1. Verwenden Sie die Zwischenablage zum Kopieren der JDBC-URL von HiveServer2.

### <a name="over-public-or-private-endpoints"></a>Über öffentliche oder private Endpunkte

Wenn Sie eine Verbindung zu einem Cluster über öffentliche oder private Endpunkte herstellen möchten, müssen Sie den Kontonamen für die Clusteranmeldung (Standard ist `admin`) und das Kennwort angeben. Beispiel: Verwenden von Beeline von einem Clientsystem zum Herstellen einer Verbindung mit der `clustername.azurehdinsight.net`-Adresse. Diese Verbindung erfolgt über Port `443` und wird mithilfe von TLS/SSL verschlüsselt.

Ersetzen Sie `clustername` durch den Namen Ihres HDInsight-Clusters. Ersetzen Sie `admin` durch das Anmeldekonto für Ihren Cluster. Verwenden Sie bei ESP-Clustern den vollständigen Benutzerprinzipalnamen (z. B. user@domain.com). Ersetzen Sie `password` durch das Kennwort des Anmeldekontos für den Cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Oder beim privaten Endpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Private Endpunkte zeigen auf einen Load Balancer im Tarif „Basic“, auf den nur über die VNETs zugegriffen werden kann, die mittels Peering in derselben Region verknüpft sind. Weitere Informationen finden Sie unter den [Einschränkungen im Zusammenhang mit globalem VNET-Peering und Load Balancern](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Sie können den `curl`-Befehl mit der Option `-v` verwenden, um Konnektivitätsprobleme mit öffentlichen oder privaten Endpunkten vor der Verwendung von Beeline zu beheben.

### <a name="use-beeline-with-apache-spark"></a>Verwenden von Beeline mit Apache Spark

Apache Spark stellt eine eigene Implementierung von HiveServer2 bereit, die manchmal als Spark Thrift-Server bezeichnet wird. Dieser Dienst verwendet Spark SQL anstelle von Hive zum Auflösen von Abfragen. Die Leistung kann abhängig von Ihrer Abfrage besser ausfallen.

#### <a name="through-public-or-private-endpoints"></a>Durch öffentliche oder private Endpunkte

Die verwendete Verbindungszeichenfolge ist etwas anders. Sie enthält `httpPath/sparkhive2` anstelle von `httpPath=/hive2`. Ersetzen Sie `clustername` durch den Namen Ihres HDInsight-Clusters. Ersetzen Sie `admin` durch das Anmeldekonto für Ihren Cluster. Verwenden Sie bei ESP-Clustern den vollständigen Benutzerprinzipalnamen (z. B. user@domain.com). Ersetzen Sie `password` durch das Kennwort des Anmeldekontos für den Cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Oder beim privaten Endpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Private Endpunkte zeigen auf einen Load Balancer im Tarif „Basic“, auf den nur über die VNETs zugegriffen werden kann, die mittels Peering in derselben Region verknüpft sind. Weitere Informationen finden Sie unter den [Einschränkungen im Zusammenhang mit globalem VNET-Peering und Load Balancern](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Sie können den `curl`-Befehl mit der Option `-v` verwenden, um Konnektivitätsprobleme mit öffentlichen oder privaten Endpunkten vor der Verwendung von Beeline zu beheben.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Vom Clusterhauptknoten oder in Azure Virtual Network mit Apache Spark

Wenn Sie direkt vom Clusterhauptknoten oder von einer Ressource, die sich in der gleichen Azure Virtual Network-Instanz wie der HDInsight-Cluster befindet, eine Verbindung herstellen, muss für den Spark Thrift-Server Port `10002` anstelle von Port `10001` verwendet werden. Das folgende Beispiel zeigt, wie eine direkte Verbindung mit dem Hauptknoten hergestellt wird:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Installieren des Beeline-Clients

Obwohl Beeline auf den Hauptknoten enthalten ist, können Sie es lokal installieren.  Die Schritte zum Installieren von Beeline auf einem lokalen Computer basieren auf einem [Windows-Subsystem für Linux](/windows/wsl/install-win10).

1. Aktualisieren Sie die Paketlisten. Geben Sie in der Bash-Shell den folgenden Befehl ein:

    ```bash
    sudo apt-get update
    ```

1. Installieren Sie Java, wenn dies noch nicht geschehen ist. Das können Sie mit dem Befehl `which java` überprüfen.

    1. Wenn kein Java-Paket installiert ist, geben Sie den folgenden Befehl ein:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Öffnen Sie die BASHRC-Datei (häufig enthalten in „~/.bashrc“): `nano ~/.bashrc`.

    1. Ergänzen Sie die BASHRC-Datei. Fügen Sie am Ende der Datei die folgende Zeile hinzu:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Drücken Sie **STRG+X**, dann **Y** und schließlich die EINGABETASTE.

1. Geben Sie zum Herunterladen der Hadoop- und Beeline-Archive die folgenden Befehle ein:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Geben Sie zum Entpacken der Archive die folgenden Befehle ein:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Nehmen Sie weitere Änderungen an der BASHRC-Datei vor. Sie müssen den Pfad angeben, in den die Archive entpackt wurden. Wenn Sie das [Windows-Subsystem für Linux](/windows/wsl/install-win10) verwenden und die Schritte genau befolgt haben, lautet der Pfad `/mnt/c/Users/user/`, wobei `user` für Ihren Benutzernamen steht.

    1. Öffnen Sie die Datei mit `nano ~/.bashrc`.

    1. Ändern Sie die folgenden Befehle mit dem entsprechenden Pfad, und geben Sie diese dann am Ende der BASHRC-Datei ein:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Drücken Sie **STRG+X**, dann **Y** und schließlich die EINGABETASTE.

1. Schließen Sie die Bash-Sitzung, und öffnen Sie sie erneut.

1. Testen Sie die Verbindung. Verwenden Sie das Verbindungsformat aus dem Abschnitt [Über öffentliche oder private Endpunkte](#over-public-or-private-endpoints) weiter oben.

## <a name="next-steps"></a>Nächste Schritte

* Beispiele für die Verwendung des Beeline-Clients mit Apache Hive finden Sie unter [Verwenden von Apache Beeline mit Apache Hive](apache-hadoop-use-hive-beeline.md)
* Allgemeinere Informationen zu Hive in HDInsight finden Sie unter [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md).