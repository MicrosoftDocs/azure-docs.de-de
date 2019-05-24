---
title: 'Verbinden von Excel über den Hive ODBC-Treiber mit Apache Hadoop: Azure HDInsight'
description: Erfahren Sie, wie Sie den Microsoft Hive ODBC-Treiber für Excel einrichten und zum Abfragen von Daten in HDInsight-Clustern von Microsoft Excel verwenden können.
keywords: Hadoop, Excel, Hive Excel, Hive ODBC
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 100482496df2db1bff07a070bb82c6811208cfd4
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891305"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Verbinden von Excel mit Apache Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Die Big Data-Lösung von Microsoft integriert Microsoft Business Intelligence (BI)-Komponenten in Apache Hadoop-Cluster, die in Azure HDInsight bereitgestellt wurden. Ein Beispiel für diese Integration ist die Möglichkeit, Excel mit dem Hive-Data Warehouse eines Hadoop-Clusters in HDInsight über den Microsoft Hive Open Database Connectivity (ODBC) Driver zu verbinden.

Es ist ebenfalls möglich, die zu einem HDInsight-Cluster gehörigen Daten und andere Datenquellen, einschließlich anderer Hadoop-Cluster (nicht aus HDInsight), zu verbinden. Hierbei verwenden Sie in Excel das Add-In Microsoft Power Query für Excel. Informationen zur Installation und Verwendung von Power Query finden Sie unter [Verbinden von Excel mit HDInsight über Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* einen HDInsight Hadoop-Cluster. Hinweise zum Erstellen finden Sie unter [Erste Schritte mit Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Eine Arbeitsstation mit Office 2010 Professional Plus oder höher oder Excel 2010 oder höher.

## <a name="install-microsoft-hive-odbc-driver"></a>Installieren des Microsoft Hive ODBC-Treibers
Laden Sie die [Microsoft Hive ODBC-Treiberversion][hive-odbc-driver-download] herunter, die der Version der Anwendung entspricht, in der Sie den ODBC-Treiber verwenden, und installieren Sie die Version.  In diesem Lernprogramm wird der Treiber für Office Excel verwendet.

## <a name="create-apache-hive-odbc-data-source"></a>Erstellen einer Apache Hive ODBC-Datenquelle
Die folgenden Schritte zeigen Ihnen, wie Sie eine Hive-ODBC-Datenquelle erstellen können.

1. Navigieren Sie von Windows zu „Start > Windows-Verwaltungsprogramme > ODBC-Datenquellen (32-Bit)/(64-Bit)“.  Hierdurch wird das Dialogfeld **ODBC-Datenquellen-Administrator** geöffnet.
   
    ![ODBC-Datenquellen-Administrator](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Konfigurieren eines DSN mithilfe des ODBC-Datenquellen-Administrators")

2. Wählen Sie auf der Registerkarte **Benutzer-DSN** die Option **Hinzufügen** aus, um das Fenster **Neue Datenquelle erstellen** zu öffnen.

3. Wählen Sie **Microsoft Hive ODBC-Treiber** und dann **Fertig stellen** aus, um das Fenster **DSN-Setup für Microsoft Hive ODBC-Treiber** zu öffnen.

4. Geben Sie folgende Werte ein bzw. wählen diese aus:
   
   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   |  Name der Datenquelle |Geben Sie einen Namen für die Datenquelle an. |
   |  Host(s) |Geben Sie „&lt;HDInsightClusterName&gt;.azurehdinsight.net“ ein. Beispiel: myHDICluster.azurehdinsight.net |
   |  Port |Verwenden Sie <strong>443</strong>. (Dieser Port wurde von 563 in 443 geändert.) |
   |  Datenbank |Verwenden Sie <strong>default</strong>. |
   |  Mechanismus |Wählen Sie <strong>Azure HDInsight Service</strong> aus. |
   |  Benutzername |Geben Sie Ihren HTTP-Benutzernamen für den HDInsight-Cluster an. Der Standard-Benutzername lautet <strong>admin</strong>. |
   |  Kennwort |Geben Sie Ihr Benutzerkennwort für den HDInsight-Cluster an. |

   
5. Optional: Wählen Sie **Erweiterte Optionen** aus.  
   
   | Parameter | BESCHREIBUNG |
   | --- | --- |
   |  Use Native Query |Wenn diese Option ausgewählt ist, versucht der ODBC-Treiber NICHT, TSQL in HiveQL zu konvertieren. Verwenden Sie diese Option nur, wenn Sie sich absolut sicher sind, dass Sie reine HiveQL-Anweisungen absenden. Wenn Sie eine Verbindung mit SQL Server oder Azure SQL-Datenbank herstellen, sollten Sie die Option nicht aktivieren. |
   |  Rows fetched per block |Wenn Sie viele Datensätze abrufen, ist es möglicherweise erforderlich, diesen Parameter zu optimieren, um optimale Leistung zu garantieren. |
   |  Default string column length, Binary column length, Decimal column scale |Längen und Genauigkeiten der Datentypen können beeinflussen, wie die Daten zurückgegeben werden. Bei zu geringer Genauigkeit und/oder Abschneiden werden falsche Informationen zurückgegeben. |

    ![Erweiterte Optionen](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Erweiterte DSN-Konfigurationsoptionen")

5. Wählen Sie **Testen** aus, um die Datenquelle zu testen. Wenn die Datenquelle richtig konfiguriert wurde, wird **SUCCESS!** als Testergebnis angezeigt.  

6. Wählen Sie **OK** aus, um das Testfenster zu schließen.  

7. Wählen Sie **OK** aus, um das Fenster **DSN-Setup für Microsoft Hive ODBC-Treiber** zu schließen.  

8. Wählen Sie **OK** aus, um das Fenster **ODBC-Datenquellen-Administrator** zu schließen.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importieren von Daten aus HDInsight in Excel
In den folgenden Schritten wird beschrieben, wie Sie mithilfe der ODBC-Datenquelle, die Sie im vorangegangenen Abschnitt erstellt haben, Daten aus einer Hive-Tabelle in eine Excel-Arbeitsmappe importieren.

1. Öffnen Sie eine neue oder bereits vorhandene Arbeitsmappe in Excel.

2. Navigieren Sie auf der Registerkarte **Daten** zu **Daten abrufen** > **Aus anderen Quellen** > **Aus ODBC**, um das Fenster **Aus ODBC** zu öffnen.
   
    ![Öffnen des Datenverbindungs-Assistenten](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Öffnen des Datenverbindungs-Assistenten")

3. Wählen Sie aus der Dropdownliste den im letzten Abschnitt erstellten Datenquellennamen und dann **OK** aus.

4. Geben Sie den Hadoop-Benutzernamen (der Standardname lautet „admin“) und das zugehörige Kennwort ein, und wählen Sie dann **Verbinden** aus, um das Fenster **Navigator** zu öffnen.

5. Navigieren Sie von **Navigator** zu **HIVE** > **Standard** > **hivesampletable**, und wählen Sie dann **Laden** aus. Es dauert einige Augenblicke, bis die Daten in Excel importiert werden.

    ![HDInsight-Hive-ODBC-Navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Öffnen des Datenverbindungs-Assistenten")

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie den Microsoft Hive ODBC-Treiber verwenden, um Daten aus dem HDInsight-Dienst nach Excel zu übertragen. Ebenso können Sie Daten aus dem HDInsight-Dienst in eine SQL-Datenbank übertragen. Es ist außerdem möglich, Daten in einen HDInsight-Dienst hochzuladen. Weitere Informationen finden Sie unter:

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualisieren von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](apache-hadoop-connect-excel-power-query.md).
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


