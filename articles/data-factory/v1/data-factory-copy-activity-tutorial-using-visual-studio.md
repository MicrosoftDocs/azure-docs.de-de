---
title: 'Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio '
description: In diesem Tutorial erstellen Sie mithilfe von Visual Studio eine Azure Data Factory-Pipeline mit Kopieraktivität.
author: linda33wj
ms.service: data-factory
ms.custom: vs-azure
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6ff31c3c0d37078f8dfbd934593adb4b6a6ef4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377107"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe von Visual Studio
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 

In diesem Artikel erfahren Sie, wie Sie mithilfe von Microsoft Visual Studio eine Data Factory mit einer Pipeline erstellen, die Daten aus Azure Blob Storage in Azure SQL-Datenbank kopiert. Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Tutorials den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).   

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: die Kopieraktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Lesen Sie sich den Artikel mit der [Übersicht über das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.       
2. Zum Erstellen von Data Factory-Instanzen müssen Sie Mitglied der Rolle [Data Factory-Mitwirkender](../../role-based-access-control/built-in-roles.md#data-factory-contributor) auf Abonnement- bzw. Ressourcengruppenebene sein.
3. Folgendes muss auf Ihrem Computer installiert sein: 
   * Visual Studio 2013 oder Visual Studio 2015
   * Laden Sie das Azure-SDK für Visual Studio 2013 oder Visual Studio 2015 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013** oder **VS 2015** im Abschnitt **.NET**.
   * Laden Sie das neueste Azure Data Factory-Plug-In für Visual Studio herunter: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) oder [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Sie können das Plug-In auch aktualisieren, indem Sie die folgenden Schritte ausführen: Klicken Sie im Menü auf **Tools** -> **Erweiterungen und Updates** -> **Online** -> **Visual Studio-Katalog** -> **Microsoft Azure Data Factory-Tools für Visual Studio** -> **Aktualisieren**.

## <a name="steps"></a>Schritte

Hier sind die Schritte angegeben, die Sie im Rahmen dieses Tutorials ausführen:

1. Erstellen Sie **verknüpfte Dienste** in der Data Factory. In diesem Schritt erstellen Sie zwei verknüpfte Dienste dieses Typs: Azure Storage und Azure SQL-Datenbank. 

    Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und Daten in dieses Speicherkonto hochgeladen.   

    „AzureSqlLinkedService“ verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) eine SQL-Tabelle in dieser Datenbank erstellt.     

2. Erstellen Sie Eingabe- und Ausgabe **datasets** in der Data Factory.  

    Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Ein Eingabeblobdataset gibt den Container und den Ordner an, der die Eingabedaten enthält.  

    Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Azure SQL-Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden.

3. Erstellen Sie eine **Pipeline** in der Data Factory. In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität.   

    Die Kopieraktivität kopiert Daten aus einem Blob in Azure Blob Storage in eine Tabelle in Azure SQL-Datenbank. Sie können eine Kopieraktivität in einer Pipeline verwenden, um Daten aus einer beliebigen unterstützten Quelle in ein beliebiges unterstütztes Ziel zu kopieren. Eine Liste der unterstützten Datenspeicher finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

4. Erstellen Sie durch das Bereitstellen von Data Factory-Entitäten (verknüpfte Dienste, Datasets/Tabellen und Pipelines) eine **Data Factory** in Azure. 

## <a name="create-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

1. Starten Sie **Visual Studio 2015**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.  

2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**.  

    ![Dialogfeld "Neues Projekt"](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Geben Sie den Namen des Projekts, den Speicherort für die Projektmappe und den Namen der Projektmappe an. Klicken Sie dann auf **OK**.

    ![Projektmappen-Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Tutorial werden keine Compute Services wie Azure HDInsight oder Azure Data Lake Analytics verwendet. Sie verwenden zwei Datenspeicher vom Typ „Azure Storage“ (Quelle) und „Azure SQL-Datenbank“ (Ziel). 

Daher erstellen Sie zwei verknüpfte Dienste dieses Typs: „AzureStorage“ und „AzureSqlDatabase“.  

Die mit Ihrem Dienst verknüpfte Azure Storage-Instanz verbindet Ihr Azure-Speicherkonto mit der Data Factory. Dieses Speicherkonto ist das Konto, in dem Sie im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und die Daten hochgeladen haben.   

Ein mit Azure SQL verknüpfter Dienst verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt.

Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Informationen zu allen Quellen und Senken, die von der Kopieraktivität unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Eine Liste mit Computediensten, die von Data Factory unterstützt werden, finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md) . In diesem Tutorial werden keine Computedienste verwendet. 

### <a name="create-the-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      

2. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure-Speicher verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 

    ![Neuer verknüpfter Dienst](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)

3. Ersetzen Sie `<accountname>` und `<accountkey>`* durch den Namen Ihres Azure-Speicherkontos bzw. durch den dazugehörigen Schlüssel. 

    ![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json** .

    Weitere Informationen zu JSON-Eigenschaften in der Definition von verknüpften Diensten finden Sie im Artikel [Azure Blob Storage-Connector](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Erstellen des mit Azure SQL verknüpften Diensts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf den Knoten **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie dieses Mal **Azure SQL Linked Service** aus, und klicken Sie dann auf **Hinzufügen**. 
3. Ersetzen Sie in der Datei **AzureSqlLinkedService1.json** die Platzhalter `<servername>`, `<databasename>`, `<username@servername>` und `<password>` durch die Namen Ihres Servers, Ihrer Datenbank, Ihres Benutzerkontos und Ihres Kennworts.    
4. Speichern Sie die Datei **AzureSqlLinkedService1.json** . 

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie unter [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Erstellen von Datasets

Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. In diesem Schritt definieren Sie die beiden Datasets „InputDataset“ und „OutputDataset“. Sie stellen Eingabe- und Ausgabedaten dar, die in den Datenspeichern gespeichert werden, auf die mit „AzureStorageLinkedService1“ und „AzureSqlLinkedService1“ verwiesen wird.

Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Das Eingabeblobdataset („InputDataset“) gibt den Container und den Ordner an, der die Eingabedaten enthält.  

Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Azure SQL-Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset („OututDataset“) die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden. 

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets

In diesem Schritt erstellen Sie ein Dataset namens „InputDataset“, das auf eine Blobdatei (emp.txt) im Stammordner eines Blobcontainers („adftutorial“) in Azure Storage (dargestellt durch den verknüpften Dienst „AzureStorageLinkedService1“) verweist. Wenn Sie keinen Wert für „fileName“ festlegen (oder diesen überspringen), werden Daten aus allen Blobs im Eingabeordner in das Ziel kopiert. In diesem Tutorial legen Sie einen Wert für „fileName“ fest. 

Hier wird anstelle von „Datasets“ der Begriff „Tabellen“ verwendet. Eine Tabelle ist ein rechteckiges Dataset und die einzige Art von Dataset, die derzeit unterstützt wird. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
2. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure-Blob** aus, und klicken Sie auf **Hinzufügen**.   
3. Ersetzen Sie den JSON-Text durch den folgenden Text, und speichern Sie die **AzureBlobLocation1.json** -Datei. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

    | Eigenschaft | BESCHREIBUNG |
    |:--- |:--- |
    | type | Die Eigenschaft „type“ wird auf **AzureBlob** festgelegt, weil sich Daten in einem Azure-Blobspeicher befinden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den Dienst **AzureStorageLinkedService**, den Sie zuvor erstellt haben. |
    | folderPath | Diese Eigenschaft gibt den Blob **container** und den **Ordner** an, der Eingabeblobs enthält. In diesem Tutorial ist „adftutorial“ der Blobcontainer und „folder“ der Stammordner. | 
    | fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien aus „folderPath“ übernommen. In diesem Tutorial wird **emp.txt** für „fileName“ angegeben. Daher wird nur diese Datei für die Verarbeitung gewählt. |
    | format -> type |Die Eingabedatei wird im Textformat erstellt. Daher verwenden wir **TextFormat**. |
    | columnDelimiter | Die Spalten in der Eingabedatei werden durch ein **Komma (`,`)** voneinander getrennt. |
    | frequency/interval | Der Wert für „frequency“ wird auf **Hour** und für „interval“ auf **1** festgelegt. Dies bedeutet, dass die Eingabeslices **stündlich** verfügbar sind. Der Data Factory-Dienst sucht also stündlich im Stammordner des angegebenen Blobcontainers (**adftutorial**) nach Eingabedaten. Er sucht innerhalb der Start- und Endzeit der Pipeline nach den Daten, nicht vor oder nach diesen Zeiten.  |
    | external | Diese Eigenschaft wird auf **true** festgelegt, wenn die Daten von dieser Pipeline nicht generiert werden. Die Eingabedaten in diesem Tutorial sind in der Datei „emp.txt“ enthalten, die von dieser Pipeline nicht generiert wird. Daher legen wir diese Eigenschaft auf „true“ fest. |

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).   

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets

In diesem Schritt erstellen Sie ein Ausgabedataset namens **OutputDataset**. Dieses Dataset verweist auf eine SQL-Tabelle in der durch **AzureSqlLinkedService1** dargestellten Azure SQL-Datenbank. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
2. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure SQL** aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **AzureSqlTableLocation1.json** .

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
        }
    }
    ```

    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

    | Eigenschaft | BESCHREIBUNG |
    |:--- |:--- |
    | type | Die Eigenschaft „type“ wird auf **AzureSqlTable** festgelegt, weil Daten in eine Tabelle in Azure SQL-Datenbank kopiert werden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den **AzureSqlLinkedService**-Dienst, den Sie zuvor erstellt haben. |
    | tableName | Diese Eigenschaft gibt die **Tabelle** an, in die die Daten kopiert werden. | 
    | frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Ausgabeslices innerhalb der Start- und Endzeit der Pipeline **stündlich** erstellt werden, nicht vor oder nach diesen Zeiten.  |

    Die emp-Tabelle in der Datenbank enthält drei Spalten: **ID**, **FirstName** und **LastName**. Da es sich bei „ID“ um eine Identitätsspalte handelt, müssen Sie hier lediglich **FirstName** und **LastName** angeben.

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>Erstellen der Pipeline

In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, für die **InputDataset** als Eingabe und **OutputDataset** als Ausgabe verwendet wird.

Derzeit steuert das Ausgabedataset den Zeitplan. In diesem Tutorial wird ein Ausgabedataset konfiguriert, um einmal pro Stunde einen Slice zu erzeugen. Für die Pipeline ist eine Start- und Endzeit festgelegt, die einen Tag, d.h. 24 Stunden, auseinander liegen. Aus diesem Grund werden 24 Ausgabedatasetslices von der Pipeline erzeugt. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.  
2. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Copy Data Pipeline**, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den JSON-Code durch den folgenden JSON-Code, und speichern Sie die Datei **CopyActivity1.json** .

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
         "inputs": [
           {
             "name": "InputDataset"
           }
         ],
         "outputs": [
           {
             "name": "OutputDataset"
           }
         ],
         "typeProperties": {
           "source": {
             "type": "BlobSource"
           },
           "sink": {
             "type": "SqlSink",
             "writeBatchSize": 10000,
             "writeBatchTimeout": "60:00:00"
           }
         },
         "Policy": {
           "concurrency": 1,
           "executionPriorityOrder": "NewestFirst",
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). In Data Factory-Lösungen können Sie auch [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) verwenden.
   - Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt. 
   - Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Eine vollständige Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Um Informationen zum Verwenden eines bestimmten unterstützten Datenspeichers als Quelle/Senke zu erhalten, klicken Sie auf den Link in der Tabelle.  
     
     Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den der **end**-Eigenschaft durch den nächsten Tag. Sie können auch nur den Datumsteil angeben und den Uhrzeitteil überspringen. „2016-02-03“ entspricht beispielsweise „2016-02-03T00:00:00Z“.
     
     Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601)angegeben werden. Beispiel: 2016-10-14T16:32:41Z. Die Zeitangabe **end** ist optional, wird aber in diesem Tutorial verwendet. 
     
     Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an.
     
     Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.

     Beschreibungen der JSON-Eigenschaften in einer Pipelinedefinition finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Beschreibungen der JSON-Eigenschaften in der Definition einer Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). Beschreibungen der JSON-Eigenschaften, die von BlobSource unterstützt werden, finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md). Beschreibungen der JSON-Eigenschaften, die von SqlSink unterstützt werden, finden Sie im Artikel [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Veröffentlichen/Bereitstellen der Data Factory-Entitäten
In diesem Schritt veröffentlichen Sie zuvor erstellte Data Factory-Entitäten (verknüpfte Dienste, Datasets und Pipeline). Außerdem geben Sie den Namen der neuen Data Factory an, die für diese Entitäten erstellt wird.  

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**. 
2. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
3. Das folgende Dialogfeld sollte angezeigt werden:
   
   ![Dialogfeld „Veröffentlichen“](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Führen Sie auf der Seite zum Konfigurieren der Data Factory die folgenden Schritte aus: 
   
   1. Wählen Sie die Option **Neue Data Factory erstellen** .
   2. Geben Sie **VSTutorialFactory** als **Name** ein.  
      
      > [!IMPORTANT]
      > Der Name der Azure Data Factory muss global eindeutig sein. Falls beim Veröffentlichen ein Fehler in Verbindung mit dem Namen der Data Factory auftritt, ändern Sie den Namen der Data Factory (beispielsweise in „IhrNameVSTutorialFactory“), und wiederholen Sie den Veröffentlichungsvorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .        
      > 
      > 
   3. Wählen Sie im Feld **Abonnement** Ihr Azure-Abonnement aus.
      
      > [!IMPORTANT]
      > Gehen Sie wie folgt vor, wenn keine Abonnements angezeigt werden: Stellen Sie sicher, dass Sie mit einem Konto angemeldet sind, bei dem es sich um den Administrator oder Co-Admin des Abonnements handelt.  
      > 
      > 
   4. Wählen Sie die **Ressourcengruppe** für die zu erstellende Data Factory aus. 
   5. Wählen Sie die **Region** für die Data Factory aus. In der Dropdownliste werden nur Regionen angezeigt, die vom Data Factory-Dienst unterstützt werden.
   6. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln.
      
       ![Seite zum Konfigurieren der Data Factory](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.
   
   ![Seite zum Veröffentlichen von Elementen](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
   
   ![Seite mit der Veröffentlichungszusammenfassung](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist.
 
   ![Seite mit dem Bereitstellungsstatus](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Beachten Sie folgende Punkte: 

* Bei Anzeige der Fehlermeldung „Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert“. Führen Sie einen der folgenden Schritte aus, und versuchen Sie, die Veröffentlichung erneut durchzuführen: 
  
  * Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  Sie können den folgenden Befehl ausführen, um sich zu vergewissern, dass der Data Factory-Anbieter registriert ist. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie eine Data Factory im Azure-Portal. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.
* Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

> [!IMPORTANT]
> Data Factory-Instanzen können nur von einem Administrator oder Co-Administrator des Azure-Abonnements erstellt werden.

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
Navigieren Sie zur Startseite Ihrer Data Factory:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Menü auf **Weitere Dienste** und dann auf **Data Factorys**.

  ![Durchsuchen von Data Factorys](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Beginnen Sie mit der Eingabe der Data Factory.

  ![Name der Data Factory](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Klicken Sie in der Liste der Ergebnisse auf Ihre Data Factory, um die Startseite für Ihre Data Factory anzuzeigen.

  ![Data Factory-Startseite](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Befolgen Sie die Anweisungen unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md), um die in diesem Tutorial erstellte Pipeline und die erstellten Datasets zu überwachen. Visual Studio unterstützt derzeit keine Überwachung von Data Factory-Pipelines. 

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in Azure SQL-Datenbank zu kopieren. Sie haben Visual Studio verwendet, um die Data Factory, verknüpfte Dienste, Datasets und eine Pipeline zu erstellen. Im Anschluss sind die allgemeinen Schritte aufgeführt, die Sie in diesem Tutorial ausgeführt haben:  

1. Sie haben eine Azure **Data Factory** erstellt.
2. Sie haben **verknüpfte Dienste** erstellt:
   1. Einen verknüpften **Azure Storage** -Dienst zum Verknüpfen Ihres Azure Storage-Kontos, in dem Eingabedaten enthalten sind.     
   2. Einen verknüpften **Azure SQL**-Dienst zum Verknüpfen Ihrer Datenbank, in der die Ausgabedaten gespeichert sind. 
3. Sie haben **Datasets** erstellt, mit denen Eingabedaten und Ausgabedaten für Pipelines beschrieben werden.
4. Sie haben eine **Pipeline** mit einer **Kopieraktivität**, mit **BlobSource** als Quelle und mit **qlSink** als Senke erstellt. 

Informationen zur Verwendung einer HDInsight Hive-Aktivität zum Transformieren von Daten mit dem Azure HDInsight-Cluster finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zum Transformieren von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Ausführliche Informationen finden Sie unter [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md). 

## <a name="view-all-data-factories-in-server-explorer"></a>Anzeigen von Data Factorys im Server-Explorer
In diesem Abschnitt wird beschrieben, wie mithilfe des Server-Explorers in Visual Studio alle Data Factorys in Ihrem Azure-Abonnement angezeigt werden und ein Visual Studio-Projekt basierend auf einer vorhandenen Data Factory erstellt wird. 

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Bei Visual Studio anmelden** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen zu allen Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt.

    ![Server-Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Erstellen eines Visual Studio-Projekts für eine vorhandene Data Factory

- Klicken Sie mit der rechten Maustaste auf eine Data Factory im Server-Explorer, und wählen Sie **Data Factory in neues Projekt exportieren** aus, um anhand einer vorhandenen Data Factory ein Visual Studio-Projekt zu erstellen.

  ![Exportieren einer Data Factory in ein Visual Studio-Projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Aktualisieren von Data Factory-Tools für Visual Studio
Führen Sie die folgenden Schritte aus, um die Azure Data Factory-Tools für Visual Studio zu aktualisieren:

1. Klicken Sie im Menü auf **Extras**, und wählen Sie **Erweiterungen und Updates** aus. 
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
3. Wählen Sie **Azure Data Factory tools for Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

## <a name="use-configuration-files"></a>Verwenden von Konfigurationsdateien
Sie können Konfigurationsdateien in Visual Studio verwenden, um Eigenschaften für verknüpfte Dienste/Tabellen/Pipelines für jede Umgebung unterschiedlich zu konfigurieren.

Sehen Sie sich die folgende JSON-Definition für einen verknüpften Azure Storage-Dienst an. Sie wird zum Angeben von **connectionString** mit unterschiedlichen Werten für „accountname“ und „accountkey“ basierend auf der Umgebung (Entwicklung/Test/Produktion) verwendet, in der Sie Data Factory-Entitäten bereitstellen. Dieses Verhalten erreichen Sie, indem Sie für jede Umgebung eine separate Konfigurationsdatei nutzen.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Hinzufügen einer Konfigurationsdatei
Führen Sie die folgenden Schritte aus, um eine Konfigurationsdatei für jede Umgebung hinzuzufügen:   

1. Klicken Sie mit der rechten Maustaste auf das Data Factory-Projekt in Ihrer Visual Studio-Projektmappe, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neuer Eintrag**.
2. Wählen Sie links in der Liste mit den installierten Vorlagen die Option **Config** aus, klicken Sie auf **Konfigurationsdatei**, geben Sie einen **Namen** für die Konfigurationsdatei ein, und klicken Sie auf **Hinzufügen**.

    ![Konfigurationsdatei hinzufügen](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Fügen Sie Konfigurationsparameter und deren Werte im folgenden Format hinzu:

  ```json
  {
      "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
      "AzureStorageLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
          }
      ],
      "AzureSqlLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
          }
      ]
  }
    ```

    In diesem Beispiel wird die connectionString-Eigenschaft eines verknüpften Azure Storage-Diensts und eines verknüpften Azure SQL-Diensts konfiguriert. Beachten Sie, dass die Syntax zum Angeben des Namens [JsonPath](https://goessner.net/articles/JsonPath/)lautet.   

    Falls JSON wie im folgenden Code gezeigt über eine Eigenschaft mit einem Array von Werten verfügt:  

  ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Konfigurieren Sie Eigenschaften wie in der folgenden Konfigurationsdatei dargestellt (nullbasierte Indizierung verwenden):

  ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Eigenschaftennamen mit Leerzeichen
Wenn ein Eigenschaftenname ein Leerzeichen enthält, verwenden Sie eckige Klammern wie im folgenden Beispiel (Datenbankservername):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Bereitstellen der Projektmappe mit einer Konfiguration
Wenn Sie Azure Data Factory-Entitäten in VS veröffentlichen, können Sie die Konfiguration angeben, die Sie für diesen Veröffentlichungsvorgang verwenden möchten.

Gehen Sie wie folgt vor, um Entitäten in einem Azure Data Factory-Projekt mit der Konfigurationsdatei zu veröffentlichen:   

1. Klicken Sie mit der rechten Maustaste auf das Data Factory-Projekt, und klicken Sie dann auf **Veröffentlichen**, um das Dialogfeld **Elemente veröffentlichen** anzuzeigen.
2. Wählen Sie eine vorhandene Data Factory aus, oder geben Sie Werte zum Erstellen einer Data Factory auf der Seite **Data Factory konfigurieren** an, und klicken Sie auf **Weiter**.   
3. Auf der Seite **Elemente veröffentlichen** wird eine Dropdownliste mit verfügbaren Konfigurationen für das Feld **Bereitstellungskonfiguration auswählen** angezeigt.

    ![Konfigurationsdatei auswählen](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Wählen Sie die **Konfigurationsdatei** aus, die Sie verwenden möchten, und klicken Sie auf **Weiter**.
5. Vergewissern Sie sich, dass der Name der JSON-Datei auf der Seite **Zusammenfassung** angezeigt wird, und klicken Sie auf **Weiter**.
6. Klicken Sie auf **Fertig stellen** , wenn der Bereitstellungsvorgang abgeschlossen ist.

Bei der Bereitstellung werden die Werte aus der Konfigurationsdatei zum Festlegen der Werte für Eigenschaften in den JSON-Dateien verwendet, bevor die Entitäten für den Azure Data Factory-Dienst bereitgestellt werden.   

## <a name="use-azure-key-vault"></a>Verwenden von Azure Key Vault
Es ist nicht ratsam und häufig gemäß der Sicherheitsrichtlinie auch nicht zulässig, für sensible Daten, z.B. Verbindungszeichenfolgen, ein Commit in das Coderepository durchzuführen. Informationen zum Speichern von sensiblen Informationen in Azure Key Vault und zum Verwenden beim Veröffentlichen von Data Factory-Entitäten finden Sie im Beispiel [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) (Sichere ADF-Veröffentlichung) auf GitHub. Mit der Secure Publish-Erweiterung für Visual Studio können Geheimnisse in Key Vault gespeichert werden, und in verknüpften Diensten bzw. Bereitstellungskonfigurationen werden dann nur Verweise darauf angegeben. Diese Verweise werden aufgelöst, wenn Sie Data Factory-Entitäten in Azure veröffentlichen. Für diese Dateien können dann Commits in das Quellrepository durchgeführt werden, ohne Geheimnisse offenzulegen.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher.
