---
title: 'Verbinden von Excel mit Apache Hadoop mithilfe von Power Query: Azure HDInsight'
description: Erfahren Sie, wie Sie Business Intelligence-Komponenten nutzen und wie Sie mithilfe von Power Query für Excel auf Daten in Hadoop in HDInsight zugreifen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e35e86bee4dfee21d0a98867c3e0d1d7850ed0ec
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762006"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Verbinden von Excel mit Apache Hadoop mithilfe von Power Query
Eine der wichtigsten Features der Big Data-Lösung von Microsoft ist die Integration von Microsoft BI-Komponenten (Business Intelligence) in Apache Hadoop-Cluster in Azure HDInsight. Ein gutes Beispiel ist die Möglichkeit, Excel mithilfe von Microsoft Power Query für Excel-Add-Ins mit dem Azure Storage-Konto zu verbinden, das die dem Hadoop-Cluster zugeordneten Daten enthält. Dieser Artikel beschreibt die Einrichtung und Verwendung von Power Query für die Abfrage von Daten aus einem mit HDInsight verwalteten Hadoop-Cluster.

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Einen HDInsight-Cluster**. Hinweise zum Konfigurieren finden Sie unter [Get started with Azure HDInsight (Erste Schritte mit Azure HDInsight)][hdinsight-get-started].
* **Eine Arbeitsstation** mit Windows 7, Windows Server 2008 R2 oder einem späteren Betriebssystem.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone oder Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Installieren von Power Query
Power Query kann Daten importieren, die von einem Hadoop-Auftrag, der in einem HDInsight-Cluster ausgeführt wird, ausgegeben bzw. generiert wurden.

Power Query wurde in Excel 2016 in das Menüband „Daten“ im Abschnitt zum Abrufen und Transformieren integriert. Laden Sie bei älteren Excel-Versionen Microsoft Power Query für Excel aus dem [Microsoft Download Center][powerquery-download] herunter, und installieren Sie es.

## <a name="import-hdinsight-data-into-excel"></a>Importieren von HDInsight-Daten in Excel
Das Power Query für Excel-Add-In erleichtert den Import von Daten aus Ihrem HDInsight-Cluster in Excel. Business Intelligence-Tools wie z. B. PowerPivot und Power Map dienen zur Inspektion, Analyse und Darstellung der Daten.

**So importieren Sie Daten aus einem HDInsight-Cluster**

1. Öffnen Sie Excel.
2. Erstellen Sie eine neue leere Arbeitsmappe.
3. Führen Sie je nach Excel-Version die folgenden Schritte aus:

   - Excel 2016

     - Klicken Sie auf das Menü **Daten**, anschließend auf dem Menüband **Daten abrufen und transformieren** auf **Daten abrufen**, dann auf **Aus Azure** und schließlich auf **Aus Azure HDInsight(HDFS)**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Klicken Sie auf das Menü **Power Query**, klicken Sie auf **Von Azure**, und klicken Sie dann auf **Von Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Hinweis:** Falls das Menü **Power Query** nicht angezeigt wird, klicken Sie auf **Datei** > **Optionen** > **Add-Ins**, und wählen Sie unten auf der Seite im Dropdownfeld **Verwalten** die Option **COM Add-Ins** aus. Klicken Sie auf die Schaltfläche **Los...** , und vergewissern Sie sich, dass das Optionsfeld des Power Query für Excel-Add-Ins markiert ist.
       
       **Hinweis:** Mit Power Query können Sie auch Daten aus dem Hadoop Distributed File System (HDFS) importieren. Klicken Sie dazu auf **Aus anderen Quellen**.
4. Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure Blob Storage-Kontos ein, und klicken Sie dann auf **OK**. Bei diesem Konto kann es sich um das Standardspeicherkonto oder ein verknüpftes Speicherkonto handeln.  Das Format lautet *https://&lt;Speicherkontoname>.blob.core.windows.net/*.
5. Geben Sie den **Kontoschlüssel** für das Blobspeicherkonto ein, und klicken Sie dann auf **Speichern**. (Sie müssen die Kontoinformationen nur beim ersten Zugriff auf diesen Speicher eingeben.)
6. Doppelklicken Sie im **Navigationsbereich** links vom Abfrage-Editor auf den Namen des Blobspeichercontainers. Der Containername ist standardmäßig derselbe Name wie der Clustername.
7. Suchen Sie **HiveSampleData.txt** in der Spalte **Name** (der Pfad zum Ordner lautet **../hive/warehouse/hivesampletable/**) und klicken Sie links von HiveSampleData.txt auf **Binär**. „HiveSampleData.txt“ enthält alle Cluster. Sie können optional auch Ihre eigene Datei verwenden.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Bei Bedarf können Sie die Spaltennamen ändern. Klicken Sie auf, wenn Sie fertig sind, **Schließen und laden**.  Die Daten wurden in Ihre Arbeitsmappe geladen.
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Daten aus HDInsight mithilfe von Power Query in Excel verwenden können. Auf ähnliche Weise können Sie Daten aus HDInsight für die Azure SQL-Datenbank abrufen. Außerdem können Sie Daten nach HDInsight hochladen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualisieren von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
