---
title: Ausführen von Apache HBase-Abfragen in Azure HDInsight mit Apache Phoenix
description: Erfahren Sie, wie Sie Apache Zeppelin zum Ausführen von Apache HBase-Abfragen mit Phoenix verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: e85226e7a2544cda01f40a1c1be6be32f11d9dfc
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542793"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Verwenden von Apache Zeppelin zum Ausführen von Apache Phoenix-Abfragen per Apache HBase in Azure HDInsight

Apache Phoenix ist eine relationale Open-Source-Datenbankschicht mit hochgradig parallelisierter Verarbeitung, die auf HBase basiert. Mit Phoenix können Sie SQL-ähnliche Abfragen per HBase verwenden. Phoenix nutzt im Hintergrund JDBC-Treiber, um Ihnen das Erstellen, Löschen und Ändern von SQL-Tabellen, -Indizes, -Sichten und -Sequenzen zu ermöglichen.  Sie können Phoenix auch verwenden, um Zeilen einzeln oder als Massenvorgang zu aktualisieren. Für Phoenix wird anstelle von MapReduce die native NOSQL-Kompilierung verwendet, um Abfragen zu kompilieren und so basierend auf HBase die Erstellung von Anwendungen mit geringer Wartezeit zu ermöglichen.

Apache Zeppelin ist ein webbasiertes Open-Source-Notebook, mit dem Sie datengesteuerte, gemeinschaftliche Dokumente anhand interaktiver Datenanalysen und Sprachen wie SQL und Scala erstellen können. Es unterstützt Datenentwickler und Datenanalysten bei der Entwicklung, Organisation, Ausführung und Freigabe von Code für die Datenbearbeitung. Es ermöglicht Ihnen das Visualisieren von Ergebnissen, ohne auf die Befehlszeile zu verweisen oder die Clusterdetails zu benötigen.

Apache Zeppelin kann von HDInsight-Benutzern zum Abfragen von Phoenix-Tabellen verwendet werden. Apache Zeppelin ist in den HDInsight-Cluster integriert, und es sind keine weiteren Schritte für die Verwendung erforderlich. Erstellen Sie einfach ein Zeppelin Notebook mit dem JDBC-Interpreter, und beginnen Sie mit dem Schreiben Ihrer Phoenix SQL-Abfragen.

## <a name="prerequisites"></a>Voraussetzungen

Ein Apache HBase-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Erstellen einer Apache Zeppelin-Notiz

1. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters in der folgenden URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Geben Sie dann die URL in einem Webbrowser ein. Geben Sie Ihren Benutzernamen und das Kennwort für die Clusteranmeldung ein.

1. Wählen Sie auf der Zeppelin-Seite die Option **Neue Notiz erstellen** aus.

    ![HDInsight Interactive Query Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Geben Sie im Dialogfeld **Neue Notiz erstellen** die folgenden Werte ein oder wählen Sie sie aus:

    - Notizname: Geben Sie einen Namen für die Notiz ein.
    - Standardinterpreter: Wählen Sie **jdbc** aus der Dropdownliste aus.

    Klicken Sie anschließend auf **Notiz erstellen** .

1. Stellen Sie sicher, dass im Header des Notebooks der Status „Verbunden“ angezeigt wird. Dies wird durch einen grünen Punkt in der oberen rechten Ecke angezeigt.

    ![Zeppelin Notebook-Status](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin Notebook-Status")

1. Erstellen Sie eine HBase-Tabelle. Geben Sie den folgenden Befehl ein, und drücken Sie dann **UMSCHALT+EINGABETASTE** :

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Die Anweisung **%jdbc(phoenix)** in der ersten Zeile gibt dem Notebook an, dass es den Phoenix-JDBC-Interpreter verwenden soll.

1. Sehen Sie sich die erstellten Tabellen an.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Fügen Sie Werte in die Tabelle ein.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Fragen Sie die Tabelle ab.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Löschen Sie einen Datensatz.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Löschen Sie die Tabelle.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Apache Phoenix unterstützt jetzt Zeppelin in Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix-Grammatik](https://phoenix.apache.org/language/index.html)