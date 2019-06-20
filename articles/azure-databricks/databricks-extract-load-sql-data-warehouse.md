---
title: 'Tutorial: Durchführen von ETL-Vorgängen mithilfe von Azure Databricks'
description: Hier erfahren Sie, wie Sie Daten aus Data Lake Storage Gen2 in Azure Databricks extrahieren, transformieren und anschließend in Azure SQL Data Warehouse laden.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/17/2019
ms.openlocfilehash: a6a681ace95f9bab3c77e4a0f9982a2281c778b8
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "65966429"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Azure Databricks

In diesem Tutorial führen Sie mithilfe von Azure Databricks einen ETL-Vorgang (Extrahieren, Transformieren und Laden) für Daten durch. Sie extrahieren Daten aus Azure Data Lake Storage Gen2 in Azure Databricks, führen Transformationen für die Daten in Azure Databricks aus und laden die transformierten Daten anschließend in Azure SQL Data Warehouse.

Bei den Schritten in diesem Tutorial wird für die Datenübertragung an Azure Databricks der SQL Data Warehouse-Connector für Azure Databricks verwendet. Dieser Connector verwendet wiederum Azure Blob Storage als temporären Speicher für die Daten, die zwischen einem Azure Databricks-Cluster und Azure SQL Data Warehouse übertragen werden.

In der folgenden Abbildung ist der Anwendungsfluss dargestellt:

![Azure Databricks mit Data Lake Store und SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks mit Data Lake Store und SQL Data Warehouse")

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen Sie einen Azure Databricks-Dienst.
> * Erstellen eines Spark-Clusters in Azure Databricks
> * Erstellen eines Dateisystems im Data Lake Storage Gen2-Konto
> * Hochladen von Beispieldaten in das Azure Data Lake Storage Gen2-Konto
> * Erstellen eines Dienstprinzipals
> * Extrahieren von Daten aus dem Azure Data Lake Storage Gen2-Konto
> * Transformieren von Daten in Azure Databricks
> * Laden von Daten in Azure SQL Data Warehouse

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!Note]
> Dieses Tutorial kann nicht mit dem **kostenlosen Azure-Testabonnement** absolviert werden.
> Navigieren Sie vor dem Erstellen des Clusters zu Ihrem Profil, und legen Sie für Ihr Abonnement die **nutzungsbasierte Bezahlung** fest, um für die Erstellung des Azure Databricks-Clusters ein kostenloses Konto zu verwenden. Weitere Informationen finden Sie unter [Kostenloses Azure-Konto](https://azure.microsoft.com/free/).
     
## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie diese Aufgaben ab, bevor Sie dieses Tutorial beginnen:

* Erstellen Sie eine Azure SQL Data Warehouse-Instanz, erstellen Sie eine Firewallregel auf Serverebene, und stellen Sie als Serveradministrator eine Verbindung mit dem Server her. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Abfragen einer Azure SQL Data Warehouse-Instanz im Azure-Portal](../sql-data-warehouse/create-data-warehouse-portal.md).

* Erstellen Sie einen Datenbank-Hauptschlüssel für die Azure SQL Data Warehouse-Instanz. Informationen hierzu finden Sie unter [Erstellen eines Datenbank-Hauptschlüssels](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Erstellen Sie ein Azure Blob Storage-Konto und einen Container darin. Rufen Sie außerdem den Zugriffsschlüssel für das Speicherkonto ab. Weitere Informationen finden Sie unter [Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Erstellen Sie ein Azure Data Lake Storage Gen2-Speicherkonto. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Erstellen eines Dienstprinzipals Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Bei den Schritten in diesem Artikel müssen einige bestimmte Aktionen ausgeführt werden.

   * Achten Sie beim Ausführen der Schritte im Abschnitt [Zuweisen der Anwendung zu einer Rolle](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) des Artikels darauf, dem Dienstprinzipal die Rolle **Mitwirkender an Storage-Blobdaten** im Bereich des Data Lake Storage Gen2-Kontos zuzuweisen. Wenn Sie die Rolle der übergeordneten Ressourcengruppe oder dem übergeordneten Abonnement zuweisen, treten jedoch Berechtigungsfehler auf, bis die Rollenzuweisungen an das Speicherkonto weitergegeben wurden.

      Wenn Sie es vorziehen, eine Zugriffssteuerungsliste (ACL) zu verwenden, um den Dienstprinzipal mit einer bestimmten Datei oder einem bestimmten Verzeichnis zu verknüpfen, verweisen Sie auf [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Fügen Sie beim Ausführen der Schritte im Abschnitt [Abrufen von Werten für die Anmeldung](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) des Artikels die Werte für Mandanten-ID, App-ID und Kennwort in eine Textdatei ein. Sie benötigen sie in Kürze.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="gather-the-information-that-you-need"></a>Sammeln der benötigten Informationen

Stellen Sie sicher, dass die Voraussetzungen für dieses Tutorial erfüllt sind.

   Vergewissern Sie sich, dass Sie über folgende Informationen verfügen:

   :heavy_check_mark:  Datenbankname, Name des Datenbankservers, Benutzername und Kennwort Ihres Azure SQL Data Warehouse

   :heavy_check_mark:  Zugriffsschlüssel Ihres Blobspeicherkontos

   :heavy_check_mark:  Name Ihres Data Lake Storage Gen2-Speicherkontos

   :heavy_check_mark:  Mandanten-ID Ihres Abonnements

   :heavy_check_mark:  Anwendungs-ID der App, die Sie bei Azure Active Directory (Azure AD) registriert haben

   :heavy_check_mark:  Authentifizierungsschlüssel für die App, die Sie bei Azure AD registriert haben

## <a name="create-an-azure-databricks-service"></a>Erstellen eines Azure Databricks-Diensts

In diesem Abschnitt erstellen Sie einen Azure Databricks-Dienst über das Azure-Portal.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Analysen** > **Azure Databricks**.

    ![Databricks im Azure-Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks im Azure-Portal")

2. Geben Sie unter **Azure Databricks-Dienst** die folgenden Werte an, um einen Databricks-Dienst zu erstellen:

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |**Arbeitsbereichsname**     | Geben Sie einen Namen für Ihren Databricks-Arbeitsbereich an.        |
    |**Abonnement**     | Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.        |
    |**Ressourcengruppe**     | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe verwenden möchten. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Weitere Informationen finden Sie in der [Übersicht über den Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
    |**Location**     | Wählen Sie **USA, Westen 2** aus.  Informationen zu weiteren verfügbaren Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).      |
    |**Tarif**     |  Wählen Sie **Standard** aus.     |

3. Die Kontoerstellung dauert einige Minuten. Den Status des Vorgangs können Sie anhand der Statusanzeige im oberen Bereich verfolgen.

4. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, und klicken Sie anschließend auf **Erstellen**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Erstellen eines Spark-Clusters in Azure Databricks

1. Navigieren Sie im Azure-Portal zu dem erstellten Databricks-Dienst, und wählen Sie **Launch Workspace** (Arbeitsbereich starten) aus.

2. Sie werden zum Azure Databricks-Portal weitergeleitet. Klicken Sie im Portal auf **Cluster**.

    ![Databricks in Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks in Azure")

3. Geben Sie auf der Seite **Neuer Cluster** die erforderlichen Werte an, um einen Cluster zu erstellen.

    ![Erstellen eines Databricks-Spark-Clusters in Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Erstellen eines Databricks-Spark-Clusters in Azure")

4. Geben Sie Werte für die folgenden Felder an, und übernehmen Sie bei den anderen Feldern die Standardwerte:

    * Geben Sie einen Namen für den Cluster ein.

    * Erstellen Sie für diesen Artikel einen Cluster mit der Runtime **5.1**.

    * Aktivieren Sie das Kontrollkästchen **Terminate after \_\_ minutes of inactivity** (Nach \_\_ Minuten Inaktivität beenden). Falls der Cluster nicht verwendet wird, geben Sie an, nach wie vielen Minuten er beendet werden soll.

    * Klicken Sie auf **Cluster erstellen**. Wenn der Cluster ausgeführt wird, können Sie Notebooks an den Cluster anfügen und Spark-Aufträge ausführen.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Erstellen eines Dateisystems im Azure Data Lake Storage Gen2-Konto

In diesem Abschnitt erstellen Sie ein Notebook im Azure Databricks-Arbeitsbereich und führen anschließend Codeausschnitte aus, um das Speicherkonto zu konfigurieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem erstellten Azure Databricks-Dienst, und wählen Sie **Launch Workspace** (Arbeitsbereich starten) aus.

2. Wählen Sie im linken Bereich die Option **Arbeitsbereich** aus. Wählen Sie in der Dropdownliste **Arbeitsbereich** die Option **Erstellen** > **Notebook** aus.

    ![Erstellen eines Notebooks in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Erstellen eines Notebooks in Databricks")

3. Geben Sie im Dialogfeld **Notizbuch erstellen** einen Namen für das Notebook ein. Wählen Sie **Scala** als Sprache und anschließend den zuvor erstellten Spark-Cluster aus.

    ![Angeben von Details für ein Notebook in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Angeben von Details für ein Notebook in Databricks")

4. Klicken Sie auf **Erstellen**.

5. Der folgende Codeblock legt die Standardanmeldeinformationen für jedes ADLS Gen 2-Konto fest, auf das in der Spark-Sitzung zugegriffen wird. Der zweite Codeblock fügt den Kontonamen an die Einstellung an, um Anmeldeinformationen für ein bestimmtes ADLS Gen 2-Konto anzugeben.  Kopieren Sie jeden Codeblock, und fügen Sie diese in die erste Zelle Ihres Azure Databricks-Notebooks ein.

   **Sitzungskonfiguration**

   ```scala
   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

   **Kontokonfiguration**

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Ersetzen Sie in diesem Codeblock die Platzhalterwerte `appID`, `password`, `tenant-id` und `storage-account-name` durch die Werte, die Sie bei der Vorbereitung dieses Tutorials gesammelt haben. Ersetzen Sie den Platzhalterwert `file-system-name` durch den gewünschten Namen für das Dateisystem.

   * `appID` und `password` stammen aus der App, die Sie im Rahmen der Dienstprinzipalerstellung bei Active Directory registriert haben.

   * `tenant-id` stammt aus Ihrem Abonnement.

   * `storage-account-name` ist der Name Ihres Azure Data Lake Storage Gen2-Speicherkontos.

7. Drücken Sie **UMSCHALT+EINGABE**, um den Code in diesem Block auszuführen.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Erfassen von Beispieldaten im Azure Data Lake Storage Gen2-Konto

Bevor Sie mit diesem Abschnitt beginnen, müssen folgende Schritte ausgeführt werden:

Geben Sie den folgenden Code in eine Zelle des Notebooks ein:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Drücken Sie in der Zelle**UMSCHALT+EINGABE**, um den Code auszuführen.

Geben Sie als Nächstes in einer darunterliegenden Zelle den folgenden Code ein, und ersetzen Sie dabei die in Klammern angegebenen Werte durch die zuvor verwendeten Werte:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Drücken Sie in der Zelle**UMSCHALT+EINGABE**, um den Code auszuführen.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrahieren von Daten aus dem Azure Data Lake Storage Gen2-Konto

1. Nun können Sie die JSON-Beispieldatei als Datenrahmen in Azure Databricks laden. Fügen Sie den folgenden Code in eine neue Zelle ein. Ersetzen Sie die in Klammern angegebenen Platzhalter durch Ihre Werte.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Ersetzen Sie den Platzhalterwert `file-system-name` durch den Namen, den Sie für Ihr Dateisystem in Storage-Explorer angegeben haben.

   * Ersetzen Sie den Platzhalter `storage-account-name` durch den Namen Ihres Speicherkontos.

2. Drücken Sie **UMSCHALT+EINGABE**, um den Code in diesem Block auszuführen.

3. Führen Sie den folgenden Code aus, um den Inhalt des Datenrahmens anzuzeigen:

    ```scala
    df.show()
    ```
   Es wird ungefähr folgender Codeausschnitt angezeigt:

   ```bash
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Damit haben Sie die Daten aus Azure Data Lake Storage Gen2 in Azure Databricks extrahiert.

## <a name="transform-data-in-azure-databricks"></a>Transformieren von Daten in Azure Databricks

Die unformatierten Beispieldaten aus der Datei **small_radio_json.json** dienen zur Erfassung der Zuhörer eines Radiosenders und enthalten eine Reihe von Spalten. In diesem Abschnitt transformieren Sie die Daten, um nur bestimmte Spalten aus dem Dataset abzurufen.

1. Rufen Sie zunächst nur die Spalten **firstName**, **lastName**, **gender**, **location** und **level** aus dem bereits erstellten Datenrahmen ab.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Daraufhin erhalten Sie eine Ausgabe wie die folgende:

   ```bash
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Diese Daten können noch weiter transformiert werden, um die Spalte **level** in **subscription_type** umzubenennen:

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Daraufhin erhalten Sie eine Ausgabe wie die folgende:

   ```bash
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Laden von Daten in Azure SQL Data Warehouse

In diesem Abschnitt laden Sie die transformierten Daten in Azure SQL Data Warehouse hoch. Sie verwenden den Azure SQL Data Warehouse-Connector für Azure Databricks, um einen Datenrahmen direkt als Tabelle in eine SQL Data Warehouse-Instanz hochzuladen.

Der SQL Date Warehouse-Connector verwendet, wie bereits erwähnt, Azure Blob Storage als temporären Speicher, um Daten zwischen Azure Databricks und Azure SQL Data Warehouse hochzuladen. Geben Sie daher zunächst die Konfiguration für die Verbindungsherstellung mit dem Speicherkonto an. Das Konto muss im Rahmen der Vorbereitung für diesen Artikel bereits erstellt worden sein.

1. Geben Sie die Konfiguration für den Zugriff auf das Azure Storage-Konto über Azure Databricks an.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Geben Sie einen temporären Ordner für die Verschiebung von Daten zwischen Azure Databricks und Azure SQL Data Warehouse an.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Führen Sie den folgenden Codeausschnitt aus, um Azure Blob-Speicherzugriffsschlüssel in der Konfiguration zu speichern. Dadurch wird sichergestellt, dass Sie den Zugriffsschlüssel im Notebook nicht als Klartext speichern müssen.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Geben Sie die Werte für die Verbindungsherstellung mit der Azure SQL Data Warehouse-Instanz an. Im Rahmen der Vorbereitung muss bereits eine SQL Data Warehouse-Instanz erstellt worden sein. Verwenden Sie den vollqualifizierten Servernamen für **dwServer**. Beispiel: `<servername>.database.windows.net`.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Führen Sie den folgenden Codeausschnitt aus, um den transformierten Datenrahmen (**renamedColumnsDF**) als Tabelle in einer SQL Data Warehouse-Instanz zu laden. Dieser Codeausschnitt erstellt in der SQL-Datenbank eine Tabelle namens **SampleTable**.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write
       .format("com.databricks.spark.sqldw")
       .option("url", sqlDwUrlSmall) 
       .option("dbtable", "SampleTable")
       .option( "forward_spark_azure_storage_credentials","True")
       .option("tempdir", tempDir)
       .mode("overwrite")
       .save()
   ```

   > [!NOTE]
   > Dieses Beispiel verwendet das Flag `forward_spark_azure_storage_credentials`, das bewirkt, dass SQL Data Warehouse über einen Zugriffsschlüssel auf Daten im Blobspeicher zugreift. Dies ist die einzige unterstützte Authentifizierungsmethode.
   >
   > Wenn Ihr Azure Blob Storage auf ausgewählte virtuelle Netzwerke beschränkt ist, benötigt SQL Data Warehouse eine [verwaltete Dienstidentität anstelle von Zugriffsschlüsseln](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Dies führt zum Fehler „Diese Anforderung ist nicht berechtigt, diesen Vorgang auszuführen“.

6. Stellen Sie eine Verbindung mit der SQL-Datenbank her, und vergewissern Sie sich, dass eine Datenbank namens **SampleTable** vorhanden ist.

   ![Überprüfen der Beispieltabelle](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Überprüfen der Beispieltabelle")

7. Führen Sie zur Überprüfung des Tabelleninhalts eine SELECT-Abfrage aus. Die Tabelle sollte die gleichen Daten enthalten wie der Datenrahmen **renamedColumnsDF**.

    ![Überprüfen des Inhalts der Beispieltabelle](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Überprüfen des Inhalts der Beispieltabelle")

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Tutorials können Sie den Cluster beenden. Wählen Sie hierzu im linken Bereich des Azure Databricks-Arbeitsbereichs die Option **Cluster** aus. Zeigen Sie zum Beenden des Clusters unter **Aktionen** auf die Auslassungspunkte (...), und wählen Sie das Symbol **Beenden** aus.

![Beenden eines Databricks-Clusters](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Beenden eines Databricks-Clusters")

Wenn Sie den Cluster nicht manuell beenden, wird er automatisch beendet, sofern Sie bei der Erstellung des Clusters das Kontrollkästchen **Terminate after \_\_ minutes of inactivity** (Nach \_\_ Minuten Inaktivität beenden) aktiviert haben. In diesem Fall wird der Cluster automatisch beendet, wenn er für den angegebenen Zeitraum inaktiv war.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Azure Databricks-Diensts
> * Erstellen eines Spark-Clusters in Azure Databricks
> * Erstellen eines Notebooks in Azure Databricks
> * Extrahieren von Daten aus einem Data Lake Storage Gen2-Konto
> * Transformieren von Daten in Azure Databricks
> * Laden von Daten in Azure SQL Data Warehouse

Das nächste Tutorial beschäftigt sich mit dem Streamen von Echtzeitdaten an Azure Databricks unter Verwendung von Azure Event Hubs.

> [!div class="nextstepaction"]
>[Stream data into Azure Databricks using Event Hubs](databricks-stream-from-eventhubs.md) (Streamen von Daten an Azure Databricks unter Verwendung von Event Hubs)
