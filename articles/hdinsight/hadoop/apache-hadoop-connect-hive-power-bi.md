---
title: Visualisieren von Apache Hive-Daten mit Power BI – Azure HDInsight
description: Erfahren Sie, wie Sie Microsoft Power BI verwenden, um von Azure HDInsight verarbeitete Hive-Daten zu visualisieren.
keywords: hdinsight, hadoop, hive, interactive query, interactive hive, LLAP, odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 1e0c043e484e4eaf2639f76c9af3fef15ad85047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237499"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualisieren von Apache Hive-Daten mit Microsoft Power BI mithilfe von ODBC in Azure HDInsight

Hier erfahren Sie, wie Sie Microsoft Power BI Desktop mithilfe von ODBC mit Azure HDInsight verbinden und Apache Hive-Daten visualisieren.

>[!IMPORTANT]
> Sie können den Hive-ODBC-Treiber verwenden, um Importe über den generischen ODBC-Connector in Power BI Desktop durchzuführen. Aufgrund der fehlenden Interaktivität der Hive-Abfrage-Engine wird die Verwendung für BI-Workloads jedoch nicht empfohlen. Aus Leistungsgründen sind der [interaktive HDInsight-Abfrageconnector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) und der [HDInsight Spark-Connector](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) eine bessere Wahl.

In diesem Tutorial laden Sie die Daten aus der Hive-Tabelle `hivesampletable` in Power BI. Die Hive-Tabelle enthält einige Mobiltelefon-Nutzungsdaten. Anschließend zeichnen Sie die Nutzungsdaten auf eine Weltkarte:

![HDInsight Power BI Kartenbericht](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Die Informationen gelten auch für den neuen Clustertyp [Interactive Query](../interactive-query/apache-interactive-query-get-started.md). Weitere Informationen zum Herstellen einer Verbindung mit HDInsight Interactive Query mithilfe von direkten Abfragen finden Sie unter [Visualisieren von Interactive Query-Hive-Daten mit Power BI mithilfe direkter Abfragen in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie diesen Artikel durchgehen können, benötigen Sie Folgendes:

* **HDInsight-Cluster**. Bei dem Cluster kann es sich entweder um einen HDInsight-Cluster mit Hive oder um einen neu veröffentlichten Interactive Query-Cluster handeln. Informationen zum Erstellen von Clustern finden Sie unter [Cluster erstellen](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Sie können eine Kopie aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) herunterladen.

## <a name="create-hive-odbc-data-source"></a>Erstellen einer Hive ODBC-Datenquelle

Informationen finden Sie unter [Erstellen einer Hive ODBC-Datenquelle](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Laden von Daten aus HDInsight

Die Hive-Tabelle „hivesampletable“ ist in allen HDInsight-Clustern enthalten.

1. Starten Sie Power BI Desktop.

2. Navigieren Sie im oberen Menü zu **Start** > **Daten abrufen** > **Mehr...** .

    ![HDInsight Power BI Daten öffnen](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Wählen Sie im Dialogfeld **Daten abrufen** links die Option **Andere** und rechts die Option **ODBC** aus, und wählen Sie anschließend unten **Verbinden** aus.

4. Wählen Sie in der Dropdownliste des Dialogfelds **Aus ODBC** den im letzten Abschnitt erstellten Datenquellennamen und anschließend **OK** aus.

5. Erweitern Sie im Dialogfeld **Navigator** die Optionen **ODBC > HIVE > Standard**, wählen Sie **hivesampletable** aus, und wählen Sie anschließend **Laden** aus.

6. Wählen Sie im Dialogfeld **ODBC-Treiber** die Option **Standard oder Benutzerdefiniert** und anschließend **Verbinden** aus.

## <a name="visualize-data"></a>Visualisieren von Daten

Fahren Sie ab der vorherigen Prozedur fort.

1. Wählen Sie im Bereich „Visualisierungen“ **Karte** aus.  Dabei handelt es sich um ein Globussymbol.

    ![HDInsight Power BI passt Bericht an](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Wählen Sie im Bereich **Felder** die Optionen **country** und **devicemake** aus. Ihnen werden die auf die Karte gezeichneten Daten angezeigt.
3. Erweitern Sie die Karte.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Daten aus HDInsight mithilfe von Power BI visualisieren.  Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](apache-hadoop-connect-excel-power-query.md)
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](./../hdinsight-upload-data.md).
