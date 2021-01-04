---
title: Excel und Apache Hadoop mit ODBC-Treiber (Open Database Connectivity) – Azure HDInsight
description: Erfahren Sie, wie Sie den Microsoft Hive ODBC-Treiber für Excel einrichten und zum Abfragen von Daten in HDInsight-Clustern von Microsoft Excel verwenden können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: e9b74cc3bff23848bf63859e7e38bb06599956a3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540770"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Verbinden von Excel mit Apache Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Die Big Data-Lösung von Microsoft integriert BI-Komponenten (Business Intelligence) von Microsoft in Apache Hadoop-Cluster, die in HDInsight bereitgestellt wurden. Ein Beispiel hierfür ist die Möglichkeit, Excel mit dem Hive-Data Warehouse eines Hadoop-Clusters zu verbinden. Stellen Sie die Verbindung unter Verwendung des Microsoft Hive ODBC-Treibers (Open Database Connectivity) her.

Sie können die Daten eines HDInsight-Clusters aus Excel mithilfe des Microsoft Power Query-Add-Ins für Excel verbinden. Weitere Informationen finden Sie unter [Verbinden von Excel mit HDInsight mithilfe von Power Query](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* einen HDInsight Hadoop-Cluster. Hinweise zum Erstellen finden Sie unter [Erste Schritte mit Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Eine Arbeitsstation mit Office 2010 Professional Plus oder höher oder Excel 2010 oder höher.

## <a name="install-microsoft-hive-odbc-driver"></a>Installieren des Microsoft Hive ODBC-Treibers

Laden Sie den [Microsoft Hive ODBC-Treiber](https://www.microsoft.com/download/details.aspx?id=40886) herunter, und installieren Sie ihn. Wählen Sie die Version aus, die der Version der Anwendung entspricht, in der Sie den ODBC-Treiber verwenden.  In diesem Artikel wird der Treiber für Office Excel verwendet.

## <a name="create-apache-hive-odbc-data-source"></a>Erstellen einer Apache Hive ODBC-Datenquelle

Die folgenden Schritte zeigen Ihnen, wie Sie eine Hive-ODBC-Datenquelle erstellen können.

1. Navigieren Sie in Windows zu **Start > Windows-Verwaltungsprogramme > ODBC-Datenquellen (32-Bit)/(64-Bit)** .  Durch diesen Vorgang wird das Dialogfeld **ODBC-Datenquellen-Administrator** geöffnet.

    ![ODBC-Datenquellen-Administrator](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurieren eines DSN mithilfe des ODBC-Datenquellen-Administrators")

1. Wählen Sie auf der Registerkarte **Benutzer-DSN** die Option **Hinzufügen** aus, um das Fenster **Neue Datenquelle erstellen** zu öffnen.

1. Wählen Sie **Microsoft Hive ODBC-Treiber** und dann **Fertig stellen** aus, um das Fenster **DSN-Setup für Microsoft Hive ODBC-Treiber** zu öffnen.

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   |  Datenquellenname |Geben Sie einen Namen für die Datenquelle an. |
   |  Host(s) |Geben Sie `HDInsightClusterName.azurehdinsight.net` ein. Beispiel: `myHDICluster.azurehdinsight.net`. Hinweis: `HDInsightClusterName-int.azurehdinsight.net` wird unterstützt, solange ein Peering der Client-VM mit dem gleichen virtuellen Netzwerk besteht. |
   |  Port |Verwenden Sie **443** . (Dieser Port wurde von 563 in 443 geändert.) |
   |  Datenbank |Verwenden Sie **default** . |
   |  Mechanismus |Wählen Sie **Microsoft Azure HDInsight Service** . |
   |  Benutzername |Geben Sie Ihren HTTP-Benutzernamen für den HDInsight-Cluster an. Der Standard-Benutzername lautet **admin** . |
   |  Kennwort |Geben Sie Ihr Benutzerkennwort für den HDInsight-Cluster an. Aktivieren Sie das Kontrollkästchen **Kennwort speichern (verschlüsselt)** .|

1. Optional: Wählen Sie **Erweiterte Optionen** aus.  

   | Parameter | BESCHREIBUNG |
   | --- | --- |
   |  Use Native Query |Wenn diese Option ausgewählt ist, versucht der ODBC-Treiber NICHT, TSQL in HiveQL zu konvertieren. Verwenden Sie diese Option nur, wenn Sie sich absolut sicher sind, dass Sie reine HiveQL-Anweisungen absenden. Wenn Sie eine Verbindung mit SQL Server oder Azure SQL-Datenbank herstellen, sollten Sie die Option nicht aktivieren. |
   |  Rows fetched per block |Wenn Sie viele Datensätze abrufen, ist es möglicherweise erforderlich, diesen Parameter zu optimieren, um optimale Leistung zu garantieren. |
   |  Default string column length, Binary column length, Decimal column scale |Längen und Genauigkeiten der Datentypen können beeinflussen, wie die Daten zurückgegeben werden. Aufgrund einer zu geringen Genauigkeit und/oder von Abschneidevorgängen werden falsche Informationen zurückgegeben. |

    ![Erweiterte DSN-Konfigurationsoptionen](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Erweiterte DSN-Konfigurationsoptionen")

1. Wählen Sie **Testen** aus, um die Datenquelle zu testen. Wenn die Datenquelle richtig konfiguriert wurde, wird als Testergebnis **SUCCESS!** angezeigt.

1. Wählen Sie **OK** aus, um das Testfenster zu schließen.  

1. Wählen Sie **OK** aus, um das Fenster **DSN-Setup für Microsoft Hive ODBC-Treiber** zu schließen.  

1. Wählen Sie **OK** aus, um das Fenster **ODBC-Datenquellen-Administrator** zu schließen.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importieren von Daten aus HDInsight in Excel

In den folgenden Schritten wird beschrieben, wie Sie mithilfe der ODBC-Datenquelle, die Sie im vorangegangenen Abschnitt erstellt haben, Daten aus einer Hive-Tabelle in eine Excel-Arbeitsmappe importieren.

1. Öffnen Sie eine neue oder bereits vorhandene Arbeitsmappe in Excel.

2. Navigieren Sie auf der Registerkarte **Daten** zu **Daten abrufen** > **Aus anderen Quellen** > **Aus ODBC** , um das Fenster **Aus ODBC** zu öffnen.

    ![Öffnen des Excel-Datenverbindungs-Assistenten](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Öffnen des Excel-Datenverbindungs-Assistenten")

3. Wählen Sie in der Dropdownliste den im letzten Abschnitt erstellten Datenquellennamen aus, und wählen Sie anschließend **OK** .

4. Bei der ersten Verwendung wird das Dialogfeld **ODBC-Treiber** geöffnet. Wählen Sie im Menü auf der linken Seite die Option **Windows** . Wählen Sie anschließend **Verbinden** , um das Fenster **Navigator** zu öffnen.

5. Navigieren Sie von **Navigator** zu **HIVE** > **Standard** > **hivesampletable** , und wählen Sie dann **Laden** aus. Es dauert einige Augenblicke, bis die Daten in Excel importiert werden.

    ![Excel-Hive ODBC-Navigator in HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Excel-Hive ODBC-Navigator in HDInsight")

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Microsoft Hive ODBC-Treiber verwenden, um Daten aus dem HDInsight-Dienst nach Excel zu übertragen. Ebenso können Sie Daten aus dem HDInsight-Dienst in eine SQL-Datenbank übertragen. Es ist außerdem möglich, Daten in einen HDInsight-Dienst hochzuladen. Weitere Informationen finden Sie unter:

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualisieren von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](apache-hadoop-connect-excel-power-query.md)
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)