---
title: 'Herstellen einer Verbindung mit Azure Databricks über Excel, Python oder R '
description: Hier erfahren Sie, wie Sie mithilfe des Simba-Treibers eine Verbindung zwischen Azure Databricks und Excel, Python oder R herstellen.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: c57550a8b683ad8f184884374c4f09216417fc40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236398"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Herstellen einer Verbindung mit Azure Databricks über Excel, Python oder R

In diesem Artikel erfahren Sie, wie Sie mithilfe des ODBC-Treibers von Databricks eine Verbindung von Azure Databricks mit Microsoft Excel, Python oder R herstellen. Nachdem die Verbindung hergestellt wurde, können Sie über Excel-, Python- oder R-Clients auf die Daten in Azure Databricks zugreifen. Sie können die Clients auch zur näheren Analyse der Daten verwenden. 

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen einen Azure Databricks-Arbeitsbereich, einen Spark-Cluster und dem Cluster zugeordnete Beispieldaten. Falls diese erforderlichen Komponenten nicht bereits vorhanden sind, führen Sie die Schnellstartanleitung unter [Schnellstart: Ausführen eines Spark-Auftrags in Azure Databricks mit dem Azure-Portal](quickstart-create-databricks-workspace-portal.md) aus.

* Laden Sie den ODBC-Treiber von Databricks von der [Downloadseite für Databricks-Treiber](https://databricks.com/spark/odbc-driver-download) herunter. Installieren Sie die 32-Bit- oder 64-Bit-Version abhängig von der Anwendung, über die Sie eine Verbindung mit Azure Databricks herstellen möchten. Beispiel: Wenn Sie eine Verbindung über Excel herstellen möchten, installieren Sie die 32-Bit-Version des Treibers. Zum Herstellen einer Verbindung über R und Python installieren Sie die 64-Bit-Version des Treibers.

* Richten Sie ein persönliches Zugriffstoken in Databricks ein. Anweisungen hierzu finden Sie im Artikel zur [Tokenverwaltung](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Einrichten eines DNS

Ein Datenquellenname (Data Source Name, DSN) enthält die Informationen zu einer bestimmten Datenquelle. Ein ODBC-Treiber benötigt diesen DSN, um eine Verbindung mit einer Datenquelle herzustellen. In diesem Abschnitt richten Sie einen DSN ein, der mit dem Databricks-ODBC-Treiber verwendet werden kann, um über Clients wie Microsoft Excel, Python oder R eine Verbindung mit Azure Databricks herzustellen.

1. Navigieren Sie im Azure Databricks-Arbeitsbereich zum Databricks-Cluster.

    ![Öffnen des Databricks-Clusters](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Öffnen des Databricks-Clusters")

2. Klicken Sie auf der Registerkarte **Konfiguration** auf die Registerkarte **JDBC/ODBC**, und kopieren Sie die Werte für **Server Hostname** (Serverhostname) und **HTTP Path** (HTTP-Pfad). Sie benötigen diese Werte, um die in diesem Artikel aufgeführten Schritte ausführen zu können.

    ![Abrufen der Databricks-Konfiguration](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Abrufen der Databricks-Konfiguration")

3. Starten Sie auf Ihrem Computer abhängig von der Anwendung die 32-Bit- oder 64-Bit Version der Anwendung **ODBC-Datenquellen**. Verwenden Sie zum Herstellen einer Verbindung über Excel die 32-Bit-Version. Verwenden Sie zum Herstellen einer Verbindung über R und Python die 64-Bit-Version.

    ![Starten von ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Starten der ODBC-App")

4. Klicken Sie auf der Registerkarte **Benutzer-DSN** auf **Hinzufügen**. Wählen Sie im Dialogfeld **Neue Datenquelle erstellen** die Option **Simba Spark ODBC Driver** (Simba Spark-ODBC-Treiber), und klicken Sie dann auf **Fertig stellen**.

    ![Starten von ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Starten der ODBC-App")

5. Geben Sie im Dialogfeld **Simba Spark ODBC Driver** (Simba Spark-ODBC-Treiber) die folgenden Werte ein:

    ![Konfigurieren des DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurieren des DSN")

    Die folgende Tabelle enthält Informationen zu den Werten, die im Dialogfeld angegeben werden:
    
    |Feld  | Wert  |
    |---------|---------|
    |**Datenquellenname**     | Geben Sie einen Namen für die Datenquelle ein.        |
    |**Host(s)**     | Geben Sie den Wert ein, den Sie aus dem Databricks-Arbeitsbereich für *Server hostname* (Serverhostname) kopiert haben.        |
    |**Port**     | Geben Sie *443* ein.        |
    |**Authentifizierung** > **Mechanismus**     | Wählen Sie *Benutzername und Kennwort* aus.        |
    |**Benutzername**     | Geben Sie *Token* ein.        |
    |**Kennwort**     | Geben Sie den Tokenwert ein, den Sie aus dem Databricks-Arbeitsbereich kopiert haben. |
    
    Führen Sie die folgenden zusätzlichen Schritte im Dialogfeld zur DNS-Einrichtung aus:
    
    * Klicken Sie auf **HTTP Options** (HTTP-Optionen). Fügen Sie im daraufhin angezeigten Dialogfeld den Wert für *HTTP Path* (HTTP-Pfad) ein, den Sie aus dem Databricks-Arbeitsbereich kopiert haben. Klicken Sie auf **OK**.
    * Klicken Sie auf **SSL Options** (SSL-Optionen). Aktivieren Sie im daraufhin angezeigten Dialogfeld das Kontrollkästchen **SSL aktivieren**. Klicken Sie auf **OK**.
    * Klicken Sie auf **Testen**, um die Verbindung mit Azure Databricks zu testen. Klicken Sie auf **OK**, um die Konfiguration zu speichern.
    * Klicken Sie im Dialogfeld **ODBC-Datenquellenadministrator** auf **OK**.

Die Einrichtung des DSN ist nun abgeschlossen. In den nächsten Abschnitten verwenden Sie diesen DSN, um über Excel, Python oder R eine Verbindung mit Azure Databricks herzustellen.

## <a name="connect-from-microsoft-excel"></a>Herstellen einer Verbindung über Microsoft Excel

In diesem Abschnitt übertragen Sie mithilfe des zuvor erstellten DSNS Daten aus Azure Databricks per Pullvorgang in Microsoft Excel. Stellen Sie vorab sicher, dass Microsoft Excel auf dem Computer installiert ist. Sie können über den [Link zur Microsoft Excel-Testversion](https://products.office.com/excel) eine Testversion von Excel herunterladen.

1. Öffnen Sie eine leere Arbeitsmappe in Microsoft Excel. Klicken Sie auf dem Menüband **Daten** auf **Daten abrufen**. Klicken Sie auf **Aus anderen Quellen** und dann auf **Aus ODBC**.

    ![Starten von ODBC über Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Starten von ODBC über Excel")

2. Wählen Sie im Dialogfeld **Aus ODBC** den zuvor erstellten DSN aus, und klicken Sie auf **OK**.

    ![Auswählen des DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Auswählen des DSN")

3. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie als Benutzernamen **Token** ein. Geben Sie als Kennwort den Tokenwert ein, den Sie aus dem Databricks-Arbeitsbereich abgerufen haben.

    ![Angeben von Anmeldeinformationen für Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Auswählen des DSN")

4. Wählen Sie im Navigatorfenster die Tabelle in Databricks aus, die Sie in Excel laden möchten, und klicken Sie dann auf **Laden**. 

    ![Laden von Daten in Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Laden von Daten in Excel")

Sobald die Daten in die Excel-Arbeitsmappe geladen wurden, können Sie Analysevorgänge dafür ausführen.

## <a name="connect-from-r"></a>Herstellen einer Verbindung über R

> [!NOTE]
> Dieser Abschnitt enthält Informationen zum Integrieren eines RStudio-Clients, der mit Azure Databricks auf Ihrem Desktop ausgeführt wird. Anweisungen zur Verwendung von RStudio im Azure Databricks-Cluster finden Sie unter [RStudio in Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

In diesem Abschnitt verwenden Sie eine IDE in der Sprache R, um auf in Azure Databricks verfügbare Daten zu verweisen. Bevor Sie beginnen, muss Folgendes auf Ihrem Computer installiert sein:

* Eine IDE für R. In diesem Artikel wird RStudio für den Desktop verwendet. Sie können die Version von der [Downloadseite für RStudio](https://www.rstudio.com/products/rstudio/download/) herunterladen.
* Wenn Sie RStudio für den Desktop als IDE verwenden, installieren Sie zusätzlich Microsoft R Client über [https://aka.ms/rclient/](https://aka.ms/rclient/). 

Öffnen Sie RStudio, und führen Sie die folgenden Schritte aus:

- Verweisen Sie auf das `RODBC`-Paket. Dies ermöglicht es Ihnen, mithilfe des zuvor erstellten DNS eine Verbindung mit Azure Databricks herzustellen.
- Stellen Sie mithilfe des DNS eine Verbindung her.
- Führen Sie für die Daten in Azure Databricks eine SQL-Abfrage aus. Im folgenden Codeausschnitt ist *radio_sample_data* eine Tabelle, die in Azure Databricks bereits vorhanden ist.
- Führen Sie einige Vorgänge für die Abfrage aus, um die Ausgabe zu überprüfen. 

Der folgende Codeausschnitt führt diese Aufgaben aus:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Herstellen einer Verbindung über Python

In diesem Abschnitt verwenden Sie eine Python-IDE (etwa IDLE), um auf in Azure Databricks verfügbare Daten zu verweisen. Bevor Sie beginnen, müssen folgende erforderliche Schritte ausgeführt werden:

* Installieren Sie Python über [diesen Link](https://www.python.org/downloads/). Bei der Installation von Python über diesen Link wird auch IDLE installiert.

* Installieren Sie über eine Eingabeaufforderung auf dem Computer das `pyodbc`-Paket. Führen Sie den folgenden Befehl aus:

      pip install pyodbc

Öffnen Sie IDLE, und führen Sie die folgenden Schritte aus:

- Importieren Sie das `pyodbc`-Paket. Dies ermöglicht es Ihnen, mithilfe des zuvor erstellten DNS eine Verbindung mit Azure Databricks herzustellen.
- Stellen Sie mithilfe des zuvor erstellten DNS eine Verbindung her.
-  Führen Sie über die von Ihnen hergestellte Verbindung eine SQL-Abfrage aus. Im folgenden Codeausschnitt ist *radio_sample_data* eine Tabelle, die in Azure Databricks bereits vorhanden ist.
- Führen Sie Vorgänge für die Abfrage aus, um die Ausgabe zu überprüfen.

Der folgende Codeausschnitt führt diese Aufgaben aus:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Quellen, aus denen Sie Daten in Azure Databricks importieren können, finden Sie im Artikel zu [Datenquellen für Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


