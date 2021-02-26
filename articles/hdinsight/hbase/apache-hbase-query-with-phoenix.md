---
title: 'Schnellstart: Apache HBase und Apache Phoenix – Azure HDInsight'
description: In diesem Schnellstart erfahren Sie, wie Sie Apache Phoenix in HDInsight verwenden. Darüber hinaus erhalten Sie Informationen zum Installieren und Einrichten von SQLLine für die Verbindung mit einem HBase-Cluster in HDInsight auf Ihrem Computer.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935985"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Schnellstart: Abfragen von Apache HBase in Azure HDInsight mit Apache Phoenix

In diesem Schnellstart erfahren Sie, wie Sie mit Apache Phoenix HBase-Abfragen in Azure HDInsight ausführen können. Apache Phoenix ist eine SQL-Abfrage-Engine für Apache HBase. Der Zugriff erfolgt als JDBC-Treiber. So können Sie HBase-Tabellen mit SQL abfragen und verwalten. [SQLLine](http://sqlline.sourceforge.net/) ist ein Befehlszeilenprogramm zum Ausführen von SQL.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache HBase-Cluster. Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Clustern](../hadoop/apache-hadoop-linux-tutorial-get-started.md).  Stellen Sie sicher, dass Sie den Clustertyp **HBase** auswählen.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifizieren eines ZooKeeper-Knotens

Beim Herstellen der Verbindung mit einem HBase-Cluster müssen Sie eine Verbindung mit einem der Apache ZooKeeper-Knoten herstellen. Jeder HDInsight-Cluster verfügt über drei ZooKeeper-Knoten. Mit cURL kann ein ZooKeeper-Knoten schnell identifiziert werden. Bearbeiten Sie den folgenden cURL-Befehl, indem Sie `PASSWORD` und `CLUSTERNAME` durch die entsprechenden Werte ersetzen, und geben Sie den Befehl dann an einer Eingabeaufforderung ein:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Ein Teil der Ausgabe ähnelt der folgenden:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Notieren Sie den Wert für `host_name`, den Sie später benötigen.

## <a name="create-a-table-and-manipulate-data"></a>Erstellen einer Tabelle und Bearbeiten von Daten

Sie können SSH verwenden, um eine Verbindung mit HBase-Clustern herzustellen, und dann mithilfe von Apache Phoenix HBase-Tabellen erstellen, Daten einfügen und Daten abfragen.

1. Verwenden Sie zum Herstellen der Verbindung mit Ihrem HBase-Cluster `ssh`. Bearbeiten Sie den unten angegebenen Befehl, indem Sie `CLUSTERNAME` durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Wechseln Sie in das Verzeichnis des Phoenix-Clients. Geben Sie den folgenden Befehl ein:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Starten Sie [SQLLine](http://sqlline.sourceforge.net/). Bearbeiten Sie den unten angegebenen Befehl, indem Sie `ZOOKEEPER` durch den weiter oben identifizierten ZooKeeper-Knoten ersetzen, und geben Sie den Befehl dann ein:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Erstellen Sie eine HBase-Tabelle. Geben Sie den folgenden Befehl ein:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Verwenden Sie den SQLLine-Befehl `!tables`, um alle Tabellen in HBase aufzulisten. Geben Sie den folgenden Befehl ein:

    ```sqlline
    !tables
    ```

6. Fügen Sie Werte in die Tabelle ein. Geben Sie den folgenden Befehl ein:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Fragen Sie die Tabelle ab. Geben Sie den folgenden Befehl ein:

    ```sql
    SELECT * FROM Company;
    ```

8. Löschen Sie einen Datensatz. Geben Sie den folgenden Befehl ein:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Löschen Sie die Tabelle. Geben Sie den folgenden Befehl ein:

    ```hbase
    DROP TABLE Company;
    ```

10. Verwenden Sie den SQLLine-Befehl `!quit`, um SQLLine zu beenden. Geben Sie den folgenden Befehl ein:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie mit Apache Phoenix HBase-Abfragen in Azure HDInsight ausführen. Weitere Informationen zu Apache Phoenix finden Sie im nächsten Artikel, der eine ausführlichere Untersuchung bietet.

> [!div class="nextstepaction"]
> [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
