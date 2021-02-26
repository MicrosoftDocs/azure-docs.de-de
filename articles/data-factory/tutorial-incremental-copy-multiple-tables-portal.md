---
title: Inkrementelles Kopieren mehrerer Tabellen mithilfe des Azure-Portals
description: In diesem Tutorial erstellen Sie eine Azure Data Factory mit einer Pipeline, bei der Deltadaten aus mehreren Tabellen einer SQL Server-Datenbank in eine Datenbank in Azure SQL-Datenbank geladen werden.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 10ebca487e8149b509935b5eb4c8d9f67ced8ade
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361127"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-a-database-in-azure-sql-database-using-the-azure-portal"></a>Inkrementelles Laden von Daten aus mehreren Tabellen in SQL Server in eine Datenbank in Azure SQL-Datenbank über das Azure-Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Azure Data Factory mit einer Pipeline, bei der Deltadaten aus mehreren Tabellen einer SQL Server-Datenbank in eine Datenbank in Azure SQL-Datenbank geladen werden.    

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten von Quell- und Zieldatenspeichern
> * Erstellen einer Data Factory.
> * Erstellen Sie eine selbstgehostete Integration Runtime.
> * Installieren der Integration Runtime 
> * Erstellen Sie verknüpfte Dienste. 
> * Erstellen des Quell-, Senken-, Grenzwertdatasets
> * Erstellen, Ausführen und Überwachen einer Pipeline
> * Überprüfen Sie die Ergebnisse.
> * Hinzufügen oder Aktualisieren von Daten in Quelltabellen
> * Erneutes Ausführen und Überwachen der Pipeline
> * Überprüfen der Endergebnisse

## <a name="overview"></a>Übersicht
Hier sind die wesentlichen Schritte beim Erstellen dieser Lösung aufgeführt: 

1. **Select the watermark column (Wählen Sie die Grenzwert-Spalte aus)** .
    
    Wählen Sie für jede Tabelle im Quelldatenspeicher eine Spalte aus, die verwendet werden kann, um die neuen oder aktualisierten Datensätze für jede Ausführung zu identifizieren. Normalerweise steigen die Daten in dieser ausgewählten Spalte (z.B. Last_modify_time oder ID), wenn Zeilen erstellt oder aktualisiert werden. Der maximale Wert in dieser Spalte wird als Grenzwert verwendet.

1. **Prepare a data store to store the watermark value (Vorbereiten eines Datenspeichers zum Speichern des Grenzwerts)** .   
    
    In diesem Tutorial speichern Sie den Grenzwert in einer SQL-Datenbank.

1. **Erstellen Sie eine Pipeline mit den folgenden Aktivitäten:** 
    
    a. Erstellen Sie eine ForEach-Aktivität zum Durchlaufen einer Liste mit Namen von Quelltabellen, die als Parameter an die Pipeline übergeben wird. Für jede Quelltabelle werden die folgenden Aktivitäten aufgerufen, um den Deltaladevorgang für diese Tabelle durchzuführen:

    b. Erstellen Sie zwei Lookup-Aktivitäten. Verwenden Sie die erste Lookup-Aktivität, um den letzten Grenzwert abzurufen. Verwenden Sie die zweite Lookup-Aktivität, um den neuen Grenzwert abzurufen. Diese Grenzwerte werden an die Copy-Aktivität übergeben.

    c. Erstellen Sie eine Copy-Aktivität, die Zeilen aus dem Quelldatenspeicher kopiert, wobei der Wert der Grenzwertspalte größer als der alte Grenzwert und kleiner als der neue Grenzwert ist. Anschließend werden die Deltadaten aus dem Quelldatenspeicher als neue Datei in Azure Blob Storage kopiert.

    d. Erstellen Sie eine StoredProcedure-Aktivität, die den Grenzwert für die Pipeline aktualisiert, die nächstes Mal ausgeführt wird. 

    Allgemeines Lösungsdiagramm: 

    ![Lädt Daten inkrementell](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
* **SQL Server**. In diesem Tutorial verwenden Sie eine SQL Server-Datenbank als Quelldatenspeicher. 
* **Azure SQL-Datenbank**. Sie verwenden eine Datenbank in Azure SQL-Datenbank als Senkendatenspeicher. Wenn Sie in SQL-Datenbank noch keine Datenbank haben, lesen Sie [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Erstellen von Quelltabellen in Ihrer SQL Server-Datenbank

1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der SQL Server-Datenbank her.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage**.

1. Führen Sie den folgenden SQL-Befehl für Ihre Datenbank aus, um Tabellen mit den Namen `customer_table` und `project_table` zu erstellen:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-database"></a>Erstellen von Zieltabellen in Ihrer Datenbank

1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit Ihrer Datenbank in Azure SQL-Datenbank her.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage**.

1. Führen Sie den folgenden SQL-Befehl für Ihre Datenbank aus, um Tabellen mit den Namen `customer_table` und `project_table` zu erstellen:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-your-database-to-store-the-high-watermark-value"></a>Erstellen einer weiteren Tabelle in Ihrer Datenbank zum Speichern des hohen Grenzwerts

1. Führen Sie den folgenden SQL-Befehl für Ihre Datenbank aus, um eine Tabelle mit dem Namen `watermarktable` zum Speichern des Grenzwerts zu erstellen: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Fügen Sie die anfänglichen Grenzwerte für beide Quelltabellen in die Tabelle mit den Grenzwerten ein.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-your-database"></a>Erstellen einer gespeicherten Prozedur in Ihrer Datenbank

Führen Sie den folgenden Befehl zum Erstellen einer gespeicherten Prozedur in Ihrer Datenbank aus. Mit dieser gespeicherten Prozedur wird der Grenzwert nach jeder Pipelineausführung aktualisiert. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-your-database"></a>Erstellen von Datentypen und zusätzlichen gespeicherten Prozeduren in Ihrer Datenbank

Führen Sie die folgende Abfrage aus, um zwei gespeicherte Prozeduren und zwei Datentypen in Ihrer Datenbank zu erstellen. Sie werden zum Zusammenführen der Daten aus Quelltabellen in Zieltabellen verwendet.

Um den Einstieg zu erleichtern, verwenden wir diese gespeicherten Prozeduren direkt. Dabei übergeben wir die Deltadaten mithilfe einer Tabellenvariablen und führen sie anschließend im Zielspeicher zusammen. In der Tabellenvariablen sollten maximal 100 Deltazeilen gespeichert werden.  

Falls Sie im Zielspeicher eine große Anzahl von Deltazeilen zusammenführen möchten, empfiehlt es sich, die Deltadaten zunächst mithilfe der Kopieraktivität in eine temporäre Stagingtabelle im Zielspeicher zu kopieren und anschließen eine eigene gespeicherte Prozedur ohne Tabellenvariable zu erstellen, um die Daten aus der Stagingtabelle in der finalen Tabelle zusammenzuführen. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
2. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus: 
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFMultiIncCopyTutorialDF** ein. 
 
   Der Name der Azure Data Factory muss **global eindeutig** sein. Wenn ein rotes Ausrufezeichen mit dem folgenden Fehler angezeigt wird, können Sie den Namen der Data Factory ändern (z.B. in „<IhrName>ADFIncCopyTutorialDF“) und dann versuchen, die Erstellung erneut durchzuführen. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll. 
5. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:
     
    - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
    - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
    Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
6. Wählen Sie **V2** als **Version** aus.
7. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.
8. Klicken Sie auf **Erstellen**.      
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.
   
   ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)
10. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.

## <a name="create-self-hosted-integration-runtime"></a>Erstellen der selbstgehosteten Integration Runtime
Wenn Sie Daten aus einem Datenspeicher in einem privaten Netzwerk (lokal) in einen Azure-Datenspeicher verschieben, installieren Sie eine selbstgehostete Integration Runtime (IR) in Ihrer lokalen Umgebung. Mit der selbstgehosteten IR werden Daten zwischen Ihrem privaten Netzwerk und Azure verschoben. 

1. Wählen Sie in der Azure Data Factory-Benutzeroberfläche auf der Seite **Erste Schritte** im Bereich ganz links die Registerkarte [Verwalten](./author-management-hub.md) aus.

   ![Schaltfläche „Verwalten“ auf der Startseite](media/doc-common-process/get-started-page-manage-button.png)

1. Wählen Sie im linken Bereich **Integration Runtime** und dann **+Neu** aus.

   ![Erstellen einer Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Wählen Sie im Fenster **Integration Runtime Setup** (Integration Runtime-Setup) die Option **Perform data movement and dispatch activities to external computes** (Datenverschiebung und -verteilung an externe Computeressourcen ausführen), und klicken Sie auf **Weiter**. 

1. Wählen Sie **Selbstgehostet** aus, und klicken Sie auf **Weiter**. 
1. Geben Sie unter **Name** die Zeichenfolge **MySelfHostedIR** ein, und klicken Sie auf **Erstellen**. 

1. Klicken Sie auf **Klicken Sie hier, um das Express-Setup für diesen Computer zu starten** im Abschnitt **Option 1: Express-Setup**. 

   ![Klicken auf den Link für das Express-Setup](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Klicken Sie im Fenster **Express-Setup von Integration Runtime (selbstgehostet)** auf **Schließen**. 

   ![Integration Runtime-Setup erfolgreich](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Klicken Sie im Webbrowser im Fenster **Integration Runtime Setup** (Integration Runtime-Setup) auf **Fertig stellen**. 

 
1. Vergewissern Sie sich, dass **MySelfHostedIR** in der Liste mit den Integration Runtimes angezeigt wird.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Abschnitt erstellen Sie verknüpfte Dienste für Ihre SQL Server-Datenbank und Ihre Datenbank in Azure SQL-Datenbank. 

### <a name="create-the-sql-server-linked-service"></a>Erstellen des mit SQL Server verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihre SQL Server-Datenbank mit der Data Factory.

1. Wechseln Sie im Fenster **Verbindungen** von der Registerkarte **Integration Runtimes** zur Registerkarte **Verknüpfte Dienste**, und klicken Sie auf **+ Neu**.

   ![Neuer verknüpfter Dienst](./media/doc-common-process/new-linked-service.png)
1. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **SQL Server**, und klicken Sie auf **Weiter**. 

1. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** die Zeichenfolge **SqlServerLinkedService** ein. 
    1. Wählen Sie unter **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) die Option **MySelfHostedIR** aus. Dies ist ein **wichtiger** Schritt. Mit der Standard-Integration Runtime kann keine Verbindung mit einem lokalen Datenspeicher hergestellt werden. Verwenden Sie die selbstgehostete Integration Runtime, die Sie weiter oben erstellt haben. 
    1. Geben Sie unter **Servername** den Namen Ihres Computers ein, auf dem sich die SQL Server-Datenbank befindet.
    1. Geben Sie als **Datenbankname** den Namen der Datenbank Ihrer SQL Server-Instanz an, die über die Quelldaten verfügt. Während der Erfüllung der Voraussetzungen haben Sie eine Tabelle erstellt und Daten in diese Datenbank eingefügt. 
    1. Wählen Sie unter **Authentifizierungstyp** den **Typ der Authentifizierung** aus, den Sie zum Herstellen der Verbindung mit der Datenbank verwenden möchten. 
    1. Geben Sie unter **Benutzername** den Namen des Benutzers ein, der Zugriff auf die SQL Server-Datenbank hat. Wenn Sie im Benutzerkonto- oder Servernamen einen Schrägstrich (`\`) verwenden müssen, verwenden Sie das Escapezeichen (`\`). z. B. `mydomain\\myuser`.
    1. Geben Sie unter **Kennwort** das **Kennwort** für den Benutzer ein. 
    1. Klicken Sie auf **Verbindung testen**, um zu testen, ob für die Data Factory eine Verbindung mit Ihrer SQL Server-Datenbank hergestellt werden kann. Beheben Sie alle Fehler, bis die Verbindung erfolgreich hergestellt wird. 
    1. Klicken Sie auf **Fertig stellen**, um den verknüpften Dienst zu speichern.

### <a name="create-the-azure-sql-database-linked-service"></a>Erstellen des mit Azure SQL-Datenbank verknüpften Diensts
Im letzten Schritt erstellen Sie einen verknüpften Dienst, um Ihre SQL Server-Quelldatenbank mit der Data Factory zu verknüpfen. In diesem Schritt verknüpfen Sie Ihre Ziel-/Senkendatenbank mit der Data Factory. 

1. Wechseln Sie im Fenster **Verbindungen** von der Registerkarte **Integration Runtimes** zur Registerkarte **Verknüpfte Dienste**, und klicken Sie auf **+ Neu**.
1. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Azure SQL-Datenbank**, und klicken Sie auf **Weiter**. 
1. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** den Namen **AzureSqlDatabaseLinkedService** ein. 
    1. Wählen Sie unter **Servername** in der Dropdownliste den Namen Ihres Servers aus. 
    1. Wählen Sie unter **Datenbankname** die Datenbank aus, in der Sie im Rahmen der Schritte zur Erfüllung der Voraussetzungen die Elemente „customer_table“ und „project_table“ erstellt haben. 
    1. Geben Sie unter **Benutzername** den Namen des Benutzers ein, der Zugriff auf die Datenbank hat. 
    1. Geben Sie unter **Kennwort** das **Kennwort** für den Benutzer ein. 
    1. Klicken Sie auf **Verbindung testen**, um zu testen, ob für die Data Factory eine Verbindung mit Ihrer SQL Server-Datenbank hergestellt werden kann. Beheben Sie alle Fehler, bis die Verbindung erfolgreich hergestellt wird. 
    1. Klicken Sie auf **Fertig stellen**, um den verknüpften Dienst zu speichern.

1. Vergewissern Sie sich, dass in der Liste zwei verknüpfte Dienste angezeigt werden. 
   
    ![Zwei verknüpfte Dienste](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets zur Darstellung der Datenquelle, des Datenziels und des Speicherorts des Grenzwerts.

### <a name="create-a-source-dataset"></a>Erstellen eines Quelldatasets

1. Klicken Sie im linken Bereich auf **+** (Pluszeichen) und dann auf **Dataset**.

1. Wählen Sie im Fenster **Neues Dataset** die Option **SQL Server** aus, und klicken Sie auf **Weiter**. 

1. Im Webbrowser wird eine neue Registerkarte für die Konfiguration des Datasets geöffnet. Außerdem wird in der Strukturansicht ein Dataset angezeigt. Geben Sie im Eigenschaftenfenster auf der Registerkarte **Allgemein** unten für **Name** den Namen **SourceDataset** ein. 

1. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Verbindung**, und wählen Sie unter **Verknüpfter Dienst** die Option **SqlServerLinkedService**. Hier wählen Sie keine Tabelle aus. Für die Copy-Aktivität in der Pipeline wird eine SQL-Abfrage zum Laden der Daten anstelle der gesamten Tabelle verwendet.

   ![Quelldataset – Verbindung](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Erstellen Sie ein Senkendataset
1. Klicken Sie im linken Bereich auf **+** (Pluszeichen) und dann auf **Dataset**.

1. Wählen Sie im Fenster **Neues Dataset** die Option **Azure SQL-Datenbank** aus, und klicken Sie auf **Weiter**. 

1. Im Webbrowser wird eine neue Registerkarte für die Konfiguration des Datasets geöffnet. Außerdem wird in der Strukturansicht ein Dataset angezeigt. Geben Sie im Eigenschaftenfenster auf der Registerkarte **Allgemein** unter **Name** den Namen **SinkDataset** ein.

1. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Parameter**, und führen Sie die folgenden Schritte aus: 

    1. Klicken Sie im Abschnitt **Parameter erstellen/aktualisieren** die Option **Neu**. 
    1. Geben Sie **SinkTableName** als **Namen** und **Zeichenfolge** als **Typ** ein. Für dieses Dataset wird **SinkTableName** als Parameter verwendet. Der Parameter SinkTableName wird von der Pipeline zur Laufzeit dynamisch festgelegt. Die ForEach-Aktivität in der Pipeline durchläuft eine Liste mit Tabellennamen und übergibt den Tabellennamen bei jedem Durchlauf an dieses Dataset.
   
        ![Senkendataset – Eigenschaften](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Verbindung**, und wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService** aus. Klicken Sie für die **Table**-Eigenschaft auf **Dynamischen Inhalt hinzufügen**.   
    
1. Wählen Sie im Fenster **Dynamischen Inhalt hinzufügen** im Abschnitt **Parameter** die Option **SinkTableName** aus. 
 
1. Nachdem Sie auf **Fertig stellen** geklickt haben, wird „@dataset().SinkTableName“ als Tabellenname angezeigt.

   ![Senkendataset – Verbindung](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Erstellen eines Datasets für einen Grenzwert
In diesem Schritt erstellen Sie ein Dataset zum Speichern eines hohen Grenzwerts. 

1. Klicken Sie im linken Bereich auf **+** (Pluszeichen) und dann auf **Dataset**.

1. Wählen Sie im Fenster **Neues Dataset** die Option **Azure SQL-Datenbank** aus, und klicken Sie auf **Weiter**. 

1. Geben Sie im Eigenschaftenfenster auf der Registerkarte **Allgemein** unten für **Name** den Namen **WatermarkDataset** ein.
1. Wechseln Sie zur Registerkarte **Verbindung**, und führen Sie die folgenden Schritte aus: 

    1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService**.
    1. Wählen Sie unter **Tabelle** die Option **[dbo].[watermarktable]** .

        ![Grenzwertdataset – Verbindung](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
Die Pipeline verwendet die Liste mit den Tabellennamen als Parameter. Die ForEach-Aktivität durchläuft die Liste mit den Tabellennamen und führt die folgenden Vorgänge aus: 

1. Verwenden Sie die Lookup-Aktivität, um den alten Grenzwert abzurufen (anfänglicher Wert oder der im letzten Durchlauf verwendete Wert).

1. Verwenden Sie die Lookup-Aktivität, um den neuen Grenzwert abzurufen (Höchstwert der Grenzwertspalte in der Quelltabelle).

1. Verwenden Sie die Copy-Aktivität, um Daten zwischen diesen beiden Grenzwerten aus der Quelldatenbank in die Zieldatenbank zu kopieren.

1. Verwenden Sie die StoredProcedure-Aktivität, um den alten Grenzwert zu aktualisieren, damit er im ersten Schritt des nächsten Durchlaufs verwendet werden kann. 

### <a name="create-the-pipeline"></a>Erstellen der Pipeline

1. Klicken Sie im linken Bereich auf **+** (Pluszeichen) und dann auf **Pipeline**.

1. Geben Sie im Bereich „Allgemein“ unter **Eigenschaften** die Eigenschaft **IncrementalCopyPipeline** für **Name** an. Reduzieren Sie dann den Bereich, indem Sie in der oberen rechten Ecke auf das Symbol „Eigenschaften“ klicken.  

1. Führen Sie auf der Registerkarte **Parameter** die folgenden Schritte aus: 

    1. Klicken Sie auf **+ NEU**. 
    1. Geben Sie **tableList** für den Parameter **name** ein. 
    1. Wählen Sie für den Parameter **type** die Option **Array** aus.

1. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Iteration & Conditionals** (Iteration und konditionelle Abschnitte), und ziehen Sie die **ForEach**-Aktivität auf die Oberfläche des Pipeline-Designers. Geben Sie im **Eigenschaftenfenster** auf der Registerkarte **Allgemein** den Text **IterateSQLTables** ein. 

1. Wechseln Sie zur Registerkarte **Einstellungen**, und geben Sie unter **Elemente** die Zeichenfolge `@pipeline().parameters.tableList` ein. Die ForEach-Aktivität durchläuft eine Liste mit Tabellen und führt den inkrementellen Kopiervorgang durch. 

    ![ForEach-Aktivität – Einstellungen](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Wählen Sie die **ForEach**-Aktivität in der Pipeline, falls sie nicht bereits ausgewählt wurde. Klicken Sie auf die Schaltfläche **Bearbeiten** (Stiftsymbol).

1. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein**, ziehen Sie die **Lookup**-Aktivität auf die Oberfläche des Pipeline-Designers, und geben Sie unter **Name** den Namen **LookupOldWaterMarkActivity** ein.

1. Wechseln Sie im **Eigenschaftenfenster** zur Registerkarte **Einstellungen** , und führen Sie die folgenden Schritte aus: 

    1. Wählen Sie **WatermarkDataset** für **Source Dataset**.
    1. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage**. 
    1. Geben Sie unter **Abfrage** die folgende SQL-Abfrage ein: 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Erste Lookup-Aktivität – Einstellungen](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Ziehen Sie die **Lookup**-Aktivität aus der Toolbox **Aktivitäten**, und geben Sie unter **Name** den Namen **LookupNewWaterMarkActivity** ein.
        
1. Wechseln Sie zur Registerkarte **Einstellungen**.

    1. Wählen Sie unter **Source Dataset** (Quelldataset) die Option **SourceDataset**. 
    1. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage**.
    1. Geben Sie unter **Abfrage** die folgende SQL-Abfrage ein:

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Zweite Lookup-Aktivität – Einstellungen](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Ziehen Sie die **Copy**-Aktivität aus der Toolbox **Aktivitäten**, und geben Sie unter **Name** den Namen **IncrementalCopyActivity** ein. 

1. Verbinden Sie die einzelnen **Lookup**-Aktivitäten jeweils mit der **Copy**-Aktivität. Ziehen Sie das **grüne** Feld, das an die **Lookup**-Aktivität angefügt ist, auf die **Copy**-Aktivität, um die Verbindung herzustellen. Lassen Sie die Maustaste los, wenn sich die Rahmenfarbe der Copy-Aktivität in **Blau** ändert.

    ![Verbinden von Lookup- und Copy-Aktivitäten](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Wählen Sie die **Copy**-Aktivität in der Pipeline aus. Wechseln Sie im **Eigenschaftenfenster** zur Registerkarte **Quelle**. 

    1. Wählen Sie unter **Source Dataset** (Quelldataset) die Option **SourceDataset**. 
    1. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage**. 
    1. Geben Sie unter **Abfrage** die folgende SQL-Abfrage ein:

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Copy-Aktivität – Quelleinstellungen](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Wechseln Sie zur Registerkarte **Senke**, und wählen Sie unter **Sink Dataset** (Senkendataset) die Option **SinkDataset**. 
        
1. Führen Sie die folgenden Schritte aus:

    1. Geben Sie in den **Dataseteigenschaften** für den Parameter **SinkTableName** Folgendes ein: `@{item().TABLE_NAME}`.
    1. Geben Sie für die Eigenschaft **Stored Procedure Name** die Zeichenfolge `@{item().StoredProcedureNameForMergeOperation}` ein.
    1. Geben Sie für die Eigenschaft **Tabellentyp** die Zeichenfolge `@{item().TableType}` ein.
    1. Geben Sie unter **Table type parameter name** (Parametername des Tabellentyps) die Zeichenfolge `@{item().TABLE_NAME}` ein.

        ![Copy-Aktivität – Parameter](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Ziehen Sie die **Stored Procedure**-Aktivität aus der Toolbox **Aktivitäten** in die Oberfläche des Pipeline-Designers. Verbinden Sie die **Copy**-Aktivität mit der **Stored Procedure**-Aktivität. 

1. Wählen Sie die **Stored Procedure**-Aktivität in der Pipeline aus, und geben Sie im **Eigenschaftenfenster** auf der Registerkarte **Allgemein** unter **Name** den Namen **StoredProceduretoWriteWatermarkActivity** ein. 

1. Wechseln Sie zur Registerkarte **SQL-Konto**, und wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService**.

    ![Stored Procedure-Aktivität – SQL-Konto](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Wechseln Sie zur Registerkarte **Gespeicherte Prozedur**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Name der gespeicherten Prozedur** den Namen `[dbo].[usp_write_watermark]` aus. 
    1. Wählen Sie die Option **Import parameter** (Importparameter). 
    1. Geben Sie die folgenden Werte für die Parameter an: 

        | Name | type | Wert | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Stored Procedure-Aktivität – Einstellungen für gespeicherte Prozeduren](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Wählen Sie zum Veröffentlichen der erstellten Entitäten im Data Factory-Dienst **Alle veröffentlichen** aus. 

1. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird. Klicken Sie zum Anzeigen der Benachrichtigungen auf den Link **Benachrichtigungen anzeigen**. Schließen Sie das Benachrichtigungsfenster, indem Sie auf das **X** klicken.

 
## <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.

1. Klicken Sie auf der Symbolleiste der Pipeline auf **Trigger hinzufügen** und dann auf **Trigger Now** (Jetzt auslösen).     

1. Geben Sie im Fenster **Pipelineausführung** für den Parameter **tableList** den folgenden Wert ein, und klicken Sie auf **Fertig stellen**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumente der Pipelineausführung](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

1. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen**. Sie können die Pipelineausführung anzeigen, die vom **manuellen Trigger** ausgelöst wird. Über die Links unter der Spalte **PIPELINENAME** können Sie Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

1. Wenn Sie die der Pipelineausführung zugeordneten Aktivitätsausführungen anzeigen möchten, wählen Sie den Link unter der Spalte **PIPELINENAME** aus. Wenn Sie Details zu den Aktivitätsausführungen anzeigen möchten, wählen Sie unter der Spalte **AKTIVITÄTSNAME** den Link **Details** (das Brillensymbol) aus. 

1. Wählen Sie oben **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.


## <a name="review-the-results"></a>Überprüfen der Ergebnisse
Führen Sie in SQL Server Management Studio die folgenden Abfragen für die SQL-Zieldatenbank aus, um sicherzustellen, dass die Daten aus den Quelltabellen in die Zieltabellen kopiert wurden: 

**Abfrage** 
```sql
select * from customer_table
```

**Ausgabe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Abfrage**

```sql
select * from project_table
```

**Ausgabe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Abfrage**

```sql
select * from watermarktable
```

**Ausgabe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Beachten Sie, dass die Grenzwerte für beide Tabellen aktualisiert wurden. 

## <a name="add-more-data-to-the-source-tables"></a>Hinzufügen von weiteren Daten zu den Quelltabellen

Führen Sie die folgende Abfrage für die SQL Server-Quelldatenbank aus, um in der Kundentabelle (customer_table) eine vorhandene Zeile zu aktualisieren. Fügen Sie eine neue Zeile in die Projekttabelle (project_table) ein. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Erneutes Ausführen der Pipeline
1. Wechseln Sie im Webbrowserfenster zur Registerkarte **Bearbeiten** auf der linken Seite. 
1. Klicken Sie auf der Symbolleiste der Pipeline auf **Trigger hinzufügen** und dann auf **Trigger Now** (Jetzt auslösen).   
1. Geben Sie im Fenster **Pipelineausführung** für den Parameter **tableList** den folgenden Wert ein, und klicken Sie auf **Fertig stellen**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Erneutes Überwachen der Pipeline

1. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen**. Sie können die Pipelineausführung anzeigen, die vom **manuellen Trigger** ausgelöst wird. Über die Links unter der Spalte **PIPELINENAME** können Sie Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

1. Wenn Sie die der Pipelineausführung zugeordneten Aktivitätsausführungen anzeigen möchten, wählen Sie den Link unter der Spalte **PIPELINENAME** aus. Wenn Sie Details zu den Aktivitätsausführungen anzeigen möchten, wählen Sie unter der Spalte **AKTIVITÄTSNAME** den Link **Details** (das Brillensymbol) aus. 

1. Wählen Sie oben **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

## <a name="review-the-final-results"></a>Überprüfen der Endergebnisse
Führen Sie in SQL Server Management Studio die folgenden Abfragen für die SQL-Zieldatenbank aus, um sicherzustellen, dass die aktualisierten bzw. neuen Daten aus den Quelltabellen in die Zieltabellen kopiert wurden. 

**Abfrage** 
```sql
select * from customer_table
```

**Ausgabe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Beachten Sie die neuen Werte in **Name** und **LastModifytime** für **PersonID** für Nummer 3. 

**Abfrage**

```sql
select * from project_table
```

**Ausgabe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Beachten Sie, dass der Projekttabelle (project_table) der Eintrag **NewProject** hinzugefügt wurde. 

**Abfrage**

```sql
select * from watermarktable
```

**Ausgabe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Beachten Sie, dass die Grenzwerte für beide Tabellen aktualisiert wurden.
     
## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Vorbereiten von Quell- und Zieldatenspeichern
> * Erstellen einer Data Factory.
> * Erstellen einer selbstgehosteten Integration Runtime (IR)
> * Installieren der Integration Runtime
> * Erstellen Sie verknüpfte Dienste. 
> * Erstellen des Quell-, Senken-, Grenzwertdatasets
> * Erstellen, Ausführen und Überwachen einer Pipeline
> * Überprüfen Sie die Ergebnisse.
> * Hinzufügen oder Aktualisieren von Daten in Quelltabellen
> * Erneutes Ausführen und Überwachen der Pipeline
> * Überprüfen der Endergebnisse

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Inkrementelles Laden von Daten aus Azure SQL-Datenbank in Azure Blob Storage mit Informationen der Änderungsnachverfolgung](tutorial-incremental-copy-change-tracking-feature-portal.md)