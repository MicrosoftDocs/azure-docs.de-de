---
title: 'Schnellstart: Ausführen von Apache Hive-Abfragen in Azure HDInsight – Apache Zeppelin'
description: Erfahren Sie, wie Sie Apache Zeppelin zum Ausführen von Apache Hive-Abfragen verwenden.
keywords: hdinsight, hadoop, hive, interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: f4b8495646e83005dc48e8a729a0e5987b832721
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801034"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Schnellstart: Ausführen von Apache Hive-Abfragen in Azure HDInsight mit Apache Zeppelin

In diesem Schnellstart erfahren Sie, wie Sie mit Apache Zeppelin [Apache Hive](https://hive.apache.org/)-Abfragen in Azure HDInsight ausführen können. Interactive Query-Cluster von HDInsight umfassen [Apache Zeppelin](https://zeppelin.apache.org/)-Notebooks, mit denen Sie interaktive Hive-Abfragen ausführen können.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Ein HDInsight Interactive Query-Cluster. Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Clustern](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Stellen Sie sicher, dass Sie den Clustertyp **Interactive Query** auswählen.

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

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie den Schnellstart abgeschlossen haben, können Sie den Cluster löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie mit Apache Zeppelin Apache Hive-Abfragen in Azure HDInsight ausführen können. Im nächsten Artikel erfahren Sie, wie Sie Abfragen mit Visual Studio ausführen können, um mehr Informationen über Hive-Abfragen zu erhalten.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mit Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>Weitere Informationen

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualisieren von Interactive Query-Apache Hive-Daten mit Power BI in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)
* [Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](../hdinsight-upload-data.md).
