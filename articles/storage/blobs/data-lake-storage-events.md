---
title: 'Tutorial: Implementieren des Data Lake-Erfassungsmusters zum Aktualisieren einer Azure Databricks Delta-Tabelle | Microsoft-Dokumentation'
description: In diesem Tutorial wird veranschaulicht, wie Sie ein Event Grid-Abonnement, eine Azure-Funktion und einen Azure Databricks-Auftrag verwenden, um Zeilen mit Daten in eine Tabelle einzufügen, die in Azure DataLake Storage Gen2 gespeichert ist.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516395"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Tutorial: Implementieren des Data Lake-Erfassungsmusters zum Aktualisieren einer Databricks Delta-Tabelle

In diesem Tutorial wird veranschaulicht, wie Sie Ereignisse in einem Speicherkonto mit einem hierarchischen Namespace verarbeiten.

Sie erstellen eine kleine Lösung, mit der ein Benutzer eine Databricks Delta-Tabelle füllen kann, indem er eine CSV-Datei (Comma-Separated Values) mit Daten zur Beschreibung eines Verkaufsauftrags hochlädt. Sie erstellen diese Lösung, indem Sie in Azure Databricks ein Event Grid-Abonnement, eine Azure-Funktion und einen [Auftrag](/azure/databricks/jobs) verbinden.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein Event Grid-Abonnement, das eine Azure-Funktion aufruft.
> * Erstellen Sie eine Azure-Funktion, die eine Benachrichtigung von einem Ereignis empfängt und den Auftrag dann in Azure Databricks ausführt.
> * Erstellen Sie einen Databricks-Auftrag, mit dem eine Kundenbestellung in eine Databricks Delta-Tabelle eingefügt wird, die unter dem Speicherkonto vorhanden ist.

Wir erstellen diese Lösung in umgekehrter Reihenfolge, indem wir mit dem Azure Databricks-Arbeitsbereich beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Erstellen Sie ein Speicherkonto mit einem hierarchischen Namespace (Azure Data Lake Storage Gen2). In diesem Tutorial wird ein Speicherkonto mit dem Namen `contosoorders` verwendet. Vergewissern Sie sich, dass Ihrem Benutzerkonto die Rolle [Mitwirkender an Storage-Blobdaten](../common/storage-auth-aad-rbac-portal.md) zugewiesen ist.

   Lesen Sie die Informationen unter [Erstellen eines Speicherkontos für die Verwendung mit Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Erstellen eines Dienstprinzipals Unter [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

  Bei den Schritten in diesem Artikel müssen einige bestimmte Aktionen ausgeführt werden.

  :heavy_check_mark: Achten Sie beim Ausführen der Schritte im Abschnitt [Zuweisen der Anwendung zu einer Rolle](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) des Artikels darauf, dem Dienstprinzipal die Rolle **Mitwirkender an Storage-Blobdaten** zuzuweisen.

  > [!IMPORTANT]
  > Achten Sie darauf, die Rolle im Kontext des Data Lake Storage Gen2-Kontos zuzuweisen. Sie können eine Rolle der übergeordneten Ressourcengruppe oder dem übergeordneten Abonnement zuweisen. In diesem Fall tritt jedoch ein Berechtigungsfehler auf, bis die Rollenzuweisungen an das Speicherkonto weitergegeben wurden.

  :heavy_check_mark: Fügen Sie beim Ausführen der Schritte im Abschnitt [Abrufen von Werten für die Anmeldung](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) des Artikels die Werte für Mandanten-ID, App-ID und Kennwort in eine Textdatei ein. Sie benötigen diese Werte später noch.

## <a name="create-a-sales-order"></a>Erstellen eines Verkaufsauftrags

Erstellen Sie zuerst eine CSV-Datei, mit der ein Verkaufsauftrag beschrieben wird, und laden Sie diese Datei dann in das Speicherkonto hoch. Später verwenden Sie die Daten aus dieser Datei dann, um die erste Zeile der Databricks Delta-Tabelle zu füllen.

1. Öffnen Sie den Azure Storage-Explorer. Navigieren Sie anschließend zu Ihrem Speicherkonto, und erstellen Sie im Abschnitt **Blobcontainer** einen neuen Container mit dem Namen **data**.

   ![Ordner „data“](./media/data-lake-storage-events/data-container.png "Ordner „data“")

   Weitere Informationen zur Verwendung von Storage-Explorer finden Sie unter [Verwenden von Azure Storage-Explorer zum Verwalten von Daten in einem Azure Data Lake Storage Gen2-Konto](data-lake-storage-explorer.md).

2. Erstellen Sie im Container **data** einen Ordner mit dem Namen **input**.

3. Fügen Sie in einem Text-Editor den folgenden Text ein:

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Speichern Sie diese Datei auf Ihrem lokalen Computer, und geben Sie ihr den Namen **data.csv**.

5. Laden Sie diese Datei in Storage-Explorer in den Ordner **input** hoch.  

## <a name="create-a-job-in-azure-databricks"></a>Erstellen eines Auftrags in Azure Databricks

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Erstellen eines Azure Databricks-Arbeitsbereichs
* Erstellen Sie ein Notebook.
* Erstellen und Auffüllen einer Databricks Delta-Tabelle
* Hinzufügen von Code, mit dem Zeilen in die Databricks Delta-Tabelle eingefügt werden
* Erstellen eines Auftrags

### <a name="create-an-azure-databricks-workspace"></a>Erstellen eines Azure Databricks-Arbeitsbereichs

In diesem Abschnitt erstellen Sie einen Azure Databricks-Arbeitsbereich über das Azure-Portal.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Analysen** > **Azure Databricks**.

    ![Databricks im Azure-Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks im Azure-Portal")

2. Geben Sie unter **Azure Databricks-Dienst** die Werte für die Erstellung eines Databricks-Arbeitsbereichs an.

    ![Erstellen eines Azure Databricks-Arbeitsbereichs](./media/data-lake-storage-events/new-databricks-service.png "Erstellen eines Azure Databricks-Arbeitsbereichs")

    Die Erstellung des Arbeitsbereichs dauert einige Minuten. Den Status des Vorgangs können Sie anhand der Statusanzeige im oberen Bereich verfolgen.

### <a name="create-a-spark-cluster-in-databricks"></a>Erstellen eines Spark-Clusters in Databricks

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum erstellten Azure Databricks-Arbeitsbereich, und klicken Sie auf **Launch Workspace** (Arbeitsbereich starten).

2. Sie werden zum Azure Databricks-Portal weitergeleitet. Klicken Sie im Portal auf **Neu** > **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks in Azure")

3. Geben Sie auf der Seite **Neuer Cluster** die erforderlichen Werte an, um einen Cluster zu erstellen.

    ![Erstellen eines Databricks Spark-Clusters in Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Erstellen eines Databricks Spark-Clusters in Azure")

    Übernehmen Sie alle anderen Standardwerte bis auf Folgendes:

    * Geben Sie einen Namen für den Cluster ein.
    * Aktivieren Sie das Kontrollkästchen **Terminate after 120 minutes of inactivity** (Nach 120 Minuten Inaktivität beenden). Geben Sie an, nach wie vielen Minuten der Cluster beendet werden soll, wenn er nicht verwendet wird.

4. Klicken Sie auf **Cluster erstellen**. Sobald der Cluster ausgeführt wird, können Sie Notizbücher an den Cluster anfügen und Spark-Aufträge ausführen.

Weitere Informationen zum Erstellen von Clustern in Azure Databricks finden Sie unter [Creating Clusters](/azure/databricks/clusters/create) (Erstellen von Clustern).

### <a name="create-a-notebook"></a>Erstellen eines Notebooks

1. Klicken Sie im linken Bereich auf **Arbeitsbereich**. Wählen Sie in der Dropdownliste **Arbeitsbereich** die Option **Erstellen** > **Notebook** aus.

    ![Erstellen eines Notebooks in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Erstellen eines Notebooks in Databricks")

2. Geben Sie im Dialogfeld **Notizbuch erstellen** einen Namen für das Notebook ein. Wählen Sie **Python** als Sprache und anschließend den zuvor erstellten Spark-Cluster aus.

    ![Der Screenshot zeigt das Dialogfeld „Notebook erstellen“ und die Auswahl von Python als Sprache.](./media/data-lake-storage-events/new-databricks-notebook.png "Erstellen eines Notebooks in Databricks")

    Klicken Sie auf **Erstellen**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Erstellen und Auffüllen einer Databricks Delta-Tabelle

1. Kopieren Sie im erstellten Notebook den folgenden Codeblock, und fügen Sie ihn in die erste Zelle ein, aber führen Sie den Code noch nicht aus.  

   Ersetzen Sie die Platzhalterwerte `appId`, `password` und `tenant` durch die Werte, die Sie bei der Vorbereitung dieses Tutorials gesammelt haben.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Mit diesem Code wird ein Widget mit dem Namen **source_file** erstellt. Später erstellen Sie eine Azure-Funktion, mit der dieser Code aufgerufen und ein Dateipfad an das Widget übergeben wird.  Mit diesem Code wird auch Ihr Dienstprinzipal beim Speicherkonto authentifiziert, und es werden einige Variablen erstellt, die Sie in anderen Zellen verwenden.

    > [!NOTE]
    > In einer Produktionsumgebung empfiehlt es sich, Ihren Authentifizierungsschlüssel in Azure Databricks zu speichern. Fügen Sie dem Codeblock dann einen Suchschlüssel anstelle des Authentifizierungsschlüssels hinzu. <br><br>Anstelle der Codezeile `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` verwenden Sie die folgende Codezeile: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`. <br><br>Sehen Sie sich nach Abschluss dieses Tutorials die Beispiele für diesen Ansatz im Artikel [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) auf der Azure Databricks-Website an.

2. Drücken Sie **UMSCHALT+EINGABE**, um den Code in diesem Block auszuführen.

3. Kopieren Sie den folgenden Codeblock, und fügen Sie ihn in eine andere Zelle ein. Drücken Sie anschließend **UMSCHALT+EINGABE**, um den Code in diesem Block auszuführen.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Mit diesem Code wird die Databricks Delta-Tabelle in Ihrem Speicherkonto erstellt, und anschließend werden einige Anfangsdaten aus der CSV-Datei geladen, die Sie zuvor hochgeladen haben.

4. Nachdem dieser Codeblock erfolgreich ausgeführt wurde, sollten Sie ihn aus dem Notebook entfernen.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Hinzufügen von Code, mit dem Zeilen in die Databricks Delta-Tabelle eingefügt werden

1. Kopieren Sie den folgenden Codeblock, und fügen Sie ihn in eine andere Zelle ein, aber führen Sie diese Zelle nicht aus.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Mit diesem Code werden Daten in eine temporäre Tabellenansicht eingefügt, indem Daten aus einer CSV-Datei verwendet werden. Der Pfad zu dieser CSV-Datei stammt aus dem Eingabewidget, das Sie in einem früheren Schritt erstellt haben.

2. Fügen Sie den folgenden Code hinzu, um den Inhalt der temporären Tabellenansicht mit der Databricks Delta-Tabelle zusammenzuführen.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Erstellen eines Auftrags

Erstellen Sie einen Auftrag, mit dem das zuvor erstellte Notebook ausgeführt wird. Später erstellen Sie eine Azure-Funktion, mit der dieser Auftrag bei Auslösung eines Ereignisses ausgeführt wird.

1. Klicken Sie auf **Aufträge**.

2. Klicken Sie auf der Seite **Aufträge** auf **Auftrag erstellen**.

3. Geben Sie dem Auftrag einen Namen, und wählen Sie dann die Arbeitsmappe `upsert-order-data`.

   ![Erstellen eines Auftrags](./media/data-lake-storage-events/create-spark-job.png "Erstellen eines Auftrags")

## <a name="create-an-azure-function"></a>Erstellen einer Azure Function

Erstellen Sie eine Azure-Funktion, die den Auftrag ausführt.

1. Wählen Sie in der oberen Ecke des Databricks-Arbeitsbereichs das Symbol „Personen“, und wählen Sie dann **Benutzereinstellungen**.

   ![Verwalten eines Kontos](./media/data-lake-storage-events/generate-token.png "Benutzereinstellungen")

2. Klicken Sie auf die Schaltfläche **Neues Token generieren** und dann auf die Schaltfläche **Generieren**.

   Kopieren Sie das Token an einen sicheren Speicherort. Ihre Azure-Funktion benötigt dieses Token für die Authentifizierung mit Databricks, damit der Auftrag ausgeführt werden kann.
  
3. Wählen Sie in der linken oberen Ecke des Azure-Portals die Schaltfläche **Ressource erstellen** und dann **Compute > Funktions-App**.

   ![Erstellen einer Azure-Funktion](./media/data-lake-storage-events/function-app-create-flow.png "Azure-Funktion erstellen")

4. Stellen Sie auf der Seite **Erstellen** der Funktions-App sicher, dass Sie **.NET Core** für den Laufzeitstapel auswählen und eine Application Insights-Instanz konfigurieren.

   ![Konfigurieren der Funktions-App](./media/data-lake-storage-events/new-function-app.png "Konfigurieren der Funktions-App")

5. Klicken Sie auf der Seite **Übersicht** der Funktions-App auf **Konfiguration**.

   ![Der Screenshot hebt die Option „Konfiguration“ unter den konfigurierten Features hervor.](./media/data-lake-storage-events/configure-function-app.png "Konfigurieren der Funktions-App")

6. Wählen Sie auf der Seite **Anwendungseinstellungen** die Schaltfläche **Neue Anwendungseinstellung**, um die einzelnen Einstellungen hinzuzufügen.

   ![Hinzufügen der Konfigurationseinstellungen](./media/data-lake-storage-events/add-application-setting.png "Hinzufügen der Konfigurationseinstellungen")

   Fügen Sie die folgenden Einstellungen hinzu:

   |Einstellungsname | Wert |
   |----|----|
   |**DBX_INSTANCE**| Die Region Ihres Databricks-Arbeitsbereichs. Beispiel: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Das persönliche Zugriffstoken, das Sie zuvor generiert haben. |
   |**DBX_JOB_ID**|Der Bezeichner des ausgeführten Auftrags. In unserem Fall lautet dieser Wert `1`.|
7. Klicken Sie auf der Übersichtsseite der Funktions-App auf die Schaltfläche **Neue Funktion**.

   ![Neue Funktion](./media/data-lake-storage-events/new-function.png "Neue Funktion")

8. Wählen Sie **Azure Event Grid-Trigger**.

   Installieren Sie die Erweiterung **Microsoft.Azure.WebJobs.Extensions.EventGrid**, wenn Sie dazu aufgefordert werden. Bei der Installation müssen Sie erneut **Azure Event Grid-Trigger** wählen, um die Funktion zu erstellen.

   Der Bereich **Neue Funktion** wird angezeigt.

9. Geben Sie im Bereich **Neue Funktion** der Funktion den Namen **UpsertOrder**, und klicken Sie anschließend auf die Schaltfläche **Erstellen**.

10. Ersetzen Sie den Inhalt der Codedatei durch diesen Code, und klicken Sie dann auf die Schaltfläche **Speichern**:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Mit diesem Code werden Informationen zum ausgelösten Speicherereignis analysiert, und anschließend wird eine Anforderungsnachricht mit der URL der Datei, die das Ereignis ausgelöst hat, erstellt. Im Rahmen der Nachricht übergibt die Funktion einen Wert an das Widget **source_file**, das Sie zuvor erstellt haben. Der Funktionscode sendet die Nachricht an den Databricks-Auftrag und verwendet das Token, das Sie abgerufen haben, als Authentifizierung.

## <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements

In diesem Abschnitt erstellen Sie ein Event Grid-Abonnement, mit dem die Azure-Funktion aufgerufen wird, wenn Dateien in das Speicherkonto hochgeladen werden.

1. Klicken Sie auf der Seite mit dem Funktionscode auf die Schaltfläche **Event Grid-Abonnement hinzufügen**.

   ![Der Screenshot hebt die Schaltfläche „Event Grid-Abonnement hinzufügen“ hervor.](./media/data-lake-storage-events/new-event-subscription.png "Neues Ereignisabonnement")

2. Geben Sie dem Abonnement auf der Seite **Ereignisabonnement erstellen** einen Namen, und verwenden Sie dann die Felder auf der Seite, um Ihr Speicherkonto auszuwählen.

   ![Neues Ereignisabonnement](./media/data-lake-storage-events/new-event-subscription-2.png "Neues Ereignisabonnement")

3. Wählen Sie in der Dropdownliste **Nach Ereignistyp filtern** die Ereignisse **Blob erstellt** und **Blob gelöscht** aus, und klicken Sie dann auf die Schaltfläche **Erstellen**.

## <a name="test-the-event-grid-subscription"></a>Testen des Event Grid-Abonnements

1. Erstellen Sie eine Datei mit dem Namen `customer-order.csv`, fügen Sie die folgenden Informationen in diese Datei ein, und speichern Sie sie auf Ihrem lokalen Computer.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Laden Sie diese Datei im Storage-Explorer in den Ordner **input** Ihres Speicherkontos hoch.

   Durch das Hochladen einer Datei wird das Ereignis **Microsoft.Storage.BlobCreated** ausgelöst. Event Grid benachrichtigt alle Abonnenten über dieses Ereignis. In unserem Fall ist die Azure-Funktion der einzige Abonnent. Die Azure-Funktion analysiert die Ereignisparameter, um zu ermitteln, welches Ereignis eingetreten ist. Anschließend übergibt sie die URL der Datei an den Databricks-Auftrag. Der Databricks-Auftrag liest die Datei und fügt der Databricks Delta-Tabelle in Ihrem Speicherkonto eine Zeile hinzu.

3. Gehen Sie wie folgt vor, um zu überprüfen, ob der Auftrag erfolgreich war: Öffnen Sie Ihren Databricks-Arbeitsbereich, klicken Sie auf die Schaltfläche **Aufträge**, und öffnen Sie anschließend Ihren Auftrag.

4. Wählen Sie den Auftrag aus, um die Auftragsseite zu öffnen.

   ![Spark-Auftrag](./media/data-lake-storage-events/spark-job.png "Spark-Auftrag")

   Nachdem der Auftrag abgeschlossen ist, wird der Fertigstellungsstatus angezeigt.

   ![Erfolgreich abgeschlossener Auftrag](./media/data-lake-storage-events/spark-job-completed.png "Erfolgreich abgeschlossener Auftrag")

5. Führen Sie in einer neuen Arbeitsmappenzelle diese Abfrage in einer Zelle aus, um die aktualisierte Deltatabelle anzuzeigen.

   ```
   %sql select * from customer_data
   ```

   In der zurückgegebenen Tabelle wird der aktuelle Datensatz angezeigt.

   ![Der aktuelle Datensatz wird in Tabelle angezeigt](./media/data-lake-storage-events/final_query.png "Der aktuelle Datensatz wird in Tabelle angezeigt")

6. Erstellen Sie zum Aktualisieren dieses Datensatzes eine Datei mit dem Namen `customer-order-update.csv`, fügen Sie die folgenden Informationen in diese Datei ein, und speichern Sie sie auf Ihrem lokalen Computer.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Diese CSV-Datei ist fast vollständig mit der vorherigen identisch. Lediglich die Bestellmenge wird von `228` in `22` geändert.

7. Laden Sie diese Datei im Storage-Explorer in den Ordner **input** Ihres Speicherkontos hoch.

8. Führen Sie die Abfrage `select` erneut aus, um die aktualisierte Delta-Tabelle anzuzeigen.

   ```
   %sql select * from customer_data
   ```

   In der zurückgegebenen Tabelle wird der aktualisierte Datensatz angezeigt.

   ![Der aktualisierte Datensatz wird in Tabelle angezeigt](./media/data-lake-storage-events/final_query-2.png "Der aktualisierte Datensatz wird in Tabelle angezeigt")

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe für das Speicherkonto und anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion))