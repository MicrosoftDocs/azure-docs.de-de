---
title: 'Schnellstart: Abfragen von Apache HBase in Azure HDInsight – HBase Shell'
description: In diesem Schnellstart erfahren Sie, wie Sie Apache HBase Shell zum Ausführen von Apache HBase-Abfragen verwenden.
keywords: HDInsight, Hadoop, HBase
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 358e058667cb1750a6fc97dffebcbfb2c6e06f84
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942935"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Schnellstart: Abfragen von Apache HBase in Azure HDInsight mit HBase Shell

In diesem Schnellstart erfahren Sie, wie Sie mit Apache HBase Shell HBase-Tabellen erstellen, Daten einfügen und dann die Tabelle abfragen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache HBase-Cluster. Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Clustern](../hadoop/apache-hadoop-linux-tutorial-get-started.md).  Stellen Sie sicher, dass Sie den Clustertyp **HBase** auswählen.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Erstellen einer Tabelle und Bearbeiten von Daten

Den meisten Benutzern werden die Daten im Tabellenformat angezeigt:

![HDInsight Apache HBase-Tabellendaten](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

In HBase (eine Implementierung von [Cloud BigTable](https://cloud.google.com/bigtable/)) sehen die gleichen Daten wie folgt aus:

![HDInsight Apache HBase-BigTable-Daten](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Sie können SSH verwenden, um eine Verbindung mit Apache HBase-Clustern herzustellen, und dann mithilfe von Apache HBase Shell HBase-Tabellen erstellen, Daten einfügen und Daten abfragen.

1. Verwenden Sie zum Herstellen der Verbindung mit Ihrem HBase-Cluster `ssh`. Bearbeiten Sie den unten angegebenen Befehl, indem Sie `CLUSTERNAME` durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Verwenden Sie den Befehl `hbase shell`, um die interaktive HBase-Shell zu starten. Geben Sie den folgenden Befehl in Ihrer SSH-Verbindung ein:

    ```bash
    hbase shell
    ```

3. Verwenden Sie den Befehl `create`, um eine HBase-Tabelle mit zwei Spaltenfamilien zu erstellen. Geben Sie den folgenden Befehl ein:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Verwenden Sie den Befehl `list`, um alle Tabellen in HBase aufzulisten. Geben Sie den folgenden Befehl ein:

    ```hbase
    list
    ```

5. Verwenden Sie den Befehl `put`, um Werte in einer angegebenen Spalte einer angegebenen Zeile in einer bestimmten Tabelle einzufügen. Geben Sie den folgenden Befehl ein:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Verwenden Sie den Befehl `scan`, um die Daten der Tabelle `Contacts` zu überprüfen und zurückzugeben. Geben Sie den folgenden Befehl ein:

    ```hbase
    scan 'Contacts'
    ```

7. Verwenden Sie den Befehl `get`, um den Inhalt einer Zeile abzurufen. Geben Sie den folgenden Befehl ein:

    ```hbase
    get 'Contacts', '1000'
    ```

    Sie erhalten ähnliche Ergebnisse wie mit dem Befehl `scan`, da nur eine Zeile vorhanden ist.

8. Verwenden Sie den Befehl `delete`, um einen Zellenwert in einer Tabelle zu löschen. Geben Sie den folgenden Befehl ein:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Verwenden Sie den Befehl `disable`, um die Tabelle zu deaktivieren. Geben Sie den folgenden Befehl ein:

    ```hbase
    disable 'Contacts'
    ```

10. Verwenden Sie den Befehl `drop`, um eine Tabelle in HBase zu löschen. Geben Sie den folgenden Befehl ein:

    ```hbase
    drop 'Contacts'
    ```

11. Verwenden Sie den Befehl `exit`, um die interaktive HBase-Shell zu beenden. Geben Sie den folgenden Befehl ein:

    ```hbase
    exit
    ```

Weitere Informationen zum HBase-Tabellenschema finden Sie unter [Einführung in das Apache HBase-Schemadesign](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Beschreibungen weiterer HBase-Befehle finden Sie im [Apache HBase-Referenzhandbuch](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie mit Apache HBase Shell HBase-Tabellen erstellen, Daten einfügen und dann die Tabelle abfragen. Im nächsten Artikel erfahren Sie, wie Sie Abfragen mit Apache Spark ausführen können, um mehr Informationen zu in HBase gespeicherten Daten zu erhalten.

> [!div class="nextstepaction"]
> [Verwenden von Apache Spark zum Lesen und Schreiben von Apache HBase-Daten](../hdinsight-using-spark-query-hbase.md)