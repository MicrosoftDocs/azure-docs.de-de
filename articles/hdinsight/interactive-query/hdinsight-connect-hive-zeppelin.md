---
title: 'Schnellstart: Apache Hive in Azure HDInsight mit Apache Zeppelin'
description: In diesem Schnellstart erfahren Sie, wie Sie Apache Hive-Abfragen mit Apache Zeppelin ausführen können.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 1199fee82532eacf1f0ef41b877b970d7d9f2cf5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941068"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Schnellstart: Ausführen von Apache Hive-Abfragen in Azure HDInsight mit Apache Zeppelin

In diesem Schnellstart erfahren Sie, wie Sie mit Apache Zeppelin [Apache Hive](https://hive.apache.org/)-Abfragen in Azure HDInsight ausführen können. Interactive Query-Cluster von HDInsight umfassen [Apache Zeppelin](https://zeppelin.apache.org/)-Notebooks, mit denen Sie interaktive Hive-Abfragen ausführen können.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Ein HDInsight Interactive Query-Cluster. Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Clustern](../hadoop/apache-hadoop-linux-tutorial-get-started.md).  Stellen Sie sicher, dass Sie den Clustertyp **Interactive Query** auswählen.

## <a name="create-an-apache-zeppelin-note"></a>Erstellen einer Apache Zeppelin-Notiz

1. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters in der folgenden URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Geben Sie dann die URL in einem Webbrowser ein.

2. Geben Sie Ihren Benutzernamen und das Kennwort für die Clusteranmeldung ein. Auf der Zeppelin-Seite können Sie entweder eine neue Notiz erstellen oder vorhandene Notizen öffnen. **HiveSample** enthält einige Hive-Beispielabfragen.  

    ![HDInsight Interactive Query Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Wählen Sie **Neue Notiz erstellen** aus.

4. Geben Sie im Dialogfeld **Neue Notiz erstellen** die folgenden Werte ein oder wählen Sie sie aus:

    - Notizname: Geben Sie einen Namen für die Notiz ein.
    - Standardinterpreter: Wählen Sie **jdbc** aus der Dropdownliste aus.

5. Wählen Sie **Notiz erstellen** aus.

6. Geben Sie die folgende Hive-Abfrage in den Codeabschnitt ein, und drücken Sie dann **UMSCHALT + EINGABETASTE**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query Zeppelin führt Abfrage aus](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Die Anweisung **%jdbc(hive)** in der ersten Zeile gibt dem Notebook an, dass es den Hive-JDBC-Interpreter verwenden soll.

    Die Abfrage soll eine Hive-Tabelle namens **hivesampletable** zurückgeben.

    Im Folgenden finden Sie zwei weitere Hive-Abfragen, die Sie für **hivesampletable** ausführen können:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Verglichen mit früheren Hive-Abfragen werden die Abfrageergebnisse viel schneller zurückgegeben.

### <a name="additional-examples"></a>Weitere Beispiele

1. Erstellen Sie eine Tabelle. Führen Sie den folgenden Code im Zeppelin-Notebook aus:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Laden Sie Daten in die neue Tabelle. Führen Sie den folgenden Code im Zeppelin-Notebook aus:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Fügen Sie einen einzelnen Datensatz ein. Führen Sie den folgenden Code im Zeppelin-Notebook aus:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Weitere Syntaxoptionen finden Sie im [Handbuch zur Hive-Sprache](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie mit Apache Zeppelin Apache Hive-Abfragen in Azure HDInsight ausführen können. Im nächsten Artikel erfahren Sie, wie Sie Abfragen mit Visual Studio ausführen können, um mehr Informationen über Hive-Abfragen zu erhalten.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mit Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
