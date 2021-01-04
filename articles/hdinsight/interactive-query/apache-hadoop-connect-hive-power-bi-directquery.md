---
title: Anzeigen von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight
description: Erfahren Sie, wie Sie Microsoft Power BI verwenden, um Interactive Query-Hive-Daten von Azure HDInsight zu visualisieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 92e94c911acb701b1ccf8e39636d152cc5bfb575
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534803"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualisieren von Interactive Query Apache Hive-Daten mit Microsoft Power BI mithilfe direkter Abfragen in HDInsight

In diesem Artikel wird beschrieben, wie Sie Microsoft Power BI mithilfe von direkten Abfragen mit Azure HDInsight Interactive Query-Clustern verbinden und die Apache Hive-Daten visualisieren. In dem angegebenen Beispiel werden Daten aus einer Hive-Tabelle namens `hivesampletable` in Power BI geladen. Die Hive-Tabelle `hivesampletable` enthält einige Mobilfunk-Nutzungsdaten. Anschließend zeichnen Sie die Nutzungsdaten auf eine Weltkarte:

![HDInsight Power BI Kartenbericht](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Sie können den [Apache Hive-ODBC-Treiber](../hadoop/apache-hadoop-connect-hive-power-bi.md) verwenden, um Importe über den generischen ODBC-Connector in Power BI Desktop durchzuführen. Aufgrund der fehlenden Interaktivität der Hive-Abfrage-Engine wird die Verwendung für BI-Workloads jedoch nicht empfohlen. Aus Leistungsgründen sind der [HDInsight Interactive Query-Connector](./apache-hadoop-connect-hive-power-bi-directquery.md) und der [HDInsight Apache Spark-Connector](/power-bi/spark-on-hdinsight-with-direct-connect) die bessere Wahl.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie diesen Artikel durchgehen können, benötigen Sie Folgendes:

* **HDInsight-Cluster** . Bei dem Cluster kann es sich entweder um einen HDInsight-Cluster mit Apache Hive oder um einen neu veröffentlichten Interactive Query-Cluster handeln. Informationen zum Erstellen von Clustern finden Sie unter [Cluster erstellen](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Sie können eine Kopie aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) herunterladen.

## <a name="load-data-from-hdinsight"></a>Laden von Daten aus HDInsight

Die Hive-Tabelle `hivesampletable` ist in allen HDInsight-Clustern enthalten.

1. Starten Sie Power BI Desktop.

2. Navigieren Sie in der Menüleiste zu **Start** > **Daten abrufen** > **Mehr...** .

    ![HDInsight – Power BI: Abrufen zusätzlicher Daten](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Geben Sie im Fenster **Daten abrufen** im Suchfeld **hdinsight** ein.  

4. Wählen Sie **HDInsight Interactive Query** in den Suchergebnissen aus, und wählen Sie dann **Verbinden** aus.  Wenn **HDInsight Interactive Query** nicht angezeigt wird, müssen Sie Power BI Desktop auf die neueste Version aktualisieren.

5. Wählen Sie **Weiter** aus, um das Dialogfeld **Verbindung zu einem Drittanbieterdienst wird hergestellt** zu schließen.

6. Geben Sie im Fenster **HDInsight Interactive Query** die folgenden Informationen ein, und wählen Sie dann **OK** aus:

    |Eigenschaft | value |
    |---|---|
    |Server |Geben Sie den Namen des Clusters ein, z.B. *myiqcluster.azurehdinsight.net* .|
    |Datenbank |Geben Sie für diesen Artikel **Standard** ein.|
    |Datenkonnektivitätsmodus |Wählen Sie für diesen Artikel **DirectQuery** aus.|

    ![HDInsight Interactive Query – Verbindung mit Power BI DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Geben Sie die HTTP-Anmeldeinformationen ein, und wählen Sie dann **Verbinden** aus. Der Standardbenutzername lautet **admin** .

8. Wählen Sie im Fenster **Navigator** im linken Bereich **hivesampletable** aus.

9. Wählen Sie im Hauptfenster **Laden** aus.

    ![HDInsight Interactive Query – „hivesampletable“ in Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualisieren von Daten auf einer Karte

Fahren Sie ab der vorherigen Prozedur fort.

1. Wählen Sie im Bereich „Visualisierungen“ die Option **Karte** (das Globussymbol) aus. Daraufhin wird eine allgemeine Karte im Hauptfenster angezeigt.

    ![HDInsight Power BI passt Bericht an](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Wählen Sie im Bereich „Felder“ **Land** und **devicemake** aus. Nach einigen Momenten wird im Hauptfenster eine Weltkarte mit den Datenpunkten angezeigt.

3. Erweitern Sie die Karte.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Daten aus HDInsight mithilfe von Microsoft Power BI visualisieren.  Weitere Informationen zur Datenvisualisierung finden Sie in den folgenden Artikeln:

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI mithilfe von ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](./../hdinsight-upload-data.md).