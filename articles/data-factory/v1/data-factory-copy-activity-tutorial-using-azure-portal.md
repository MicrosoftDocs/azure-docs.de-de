---
title: 'Tutorial: Erstellen einer Azure Data Factory-Pipeline zum Kopieren von Daten (Azure-Portal) | Microsoft-Dokumentation'
description: In diesem Tutorial erstellen Sie mit dem Azure-Portal eine Azure Data Factory-Pipeline mit einer Kopieraktivität, um Daten aus Azure Blob Storage in Azure SQL-Datenbank zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 168e5eea2004a31b108a8a00d883298f8e2198ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090269"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Verwenden des Azure-Portals zum Erstellen einer Data Factory-Pipeline zum Kopieren von Daten 
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 

In diesem Artikel erfahren Sie, wie Sie mithilfe des [Azure-Portals](https://portal.azure.com) eine Data Factory mit einer Pipeline erstellen, die Daten aus Azure Blob Storage in eine Azure SQL-Datenbank kopiert. Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Tutorials den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).   

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: der Copy-Aktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie vor der Durchführung dieses Tutorials die Schritte zur Erfüllung der Voraussetzungen aus, die im Artikel [Voraussetzungen für das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aufgeführt sind.

## <a name="steps"></a>Schritte
Hier sind die Schritte angegeben, die Sie im Rahmen dieses Tutorials ausführen:

1. Erstellen Sie eine Azure **Data Factory**. In diesem Schritt erstellen Sie eine Data Factory mit dem Namen „ADFTutorialDataFactory“. 
2. Erstellen Sie **verknüpfte Dienste** in der Data Factory. In diesem Schritt erstellen Sie zwei verknüpfte Dienste dieses Typs: Azure Storage und Azure SQL-Datenbank. 
    
    Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und Daten in dieses Speicherkonto hochgeladen.   

    AzureSqlLinkedService verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) eine SQL-Tabelle in dieser Datenbank erstellt.   
3. Erstellen Sie Eingabe- und Ausgabe**datasets** in der Data Factory.  
    
    Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Ein Eingabeblobdataset gibt den Container und den Ordner an, der die Eingabedaten enthält.  

    Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Azure SQL-Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden.
4. Erstellen Sie eine **Pipeline** in der Data Factory. In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität.   
    
    Die Kopieraktivität kopiert Daten aus Azure Blob Storage in eine Tabelle in Azure SQL-Datenbank. Sie können eine Kopieraktivität in einer Pipeline verwenden, um Daten aus einer beliebigen unterstützten Quelle in ein beliebiges unterstütztes Ziel zu kopieren. Eine Liste der unterstützten Datenspeicher finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Überwachen Sie die Pipeline. In diesem Schritt **überwachen** Sie die Slices von Eingabe- und Ausgabedatasets mithilfe des Azure-Portals. 

## <a name="create-data-factory"></a>Erstellen einer Data Factory
> [!IMPORTANT]
> Sofern dies noch nicht geschehen ist, führen Sie die Schritte zur Erfüllung der [Voraussetzungen für das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) durch.   

Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.

1. Klicken Sie nach der Anmeldung beim [Azure-Portal](https://portal.azure.com/) im Menü auf der linken Seite auf **Ressource erstellen**, auf **Daten + Analysen** und anschließend auf **Data Factory**. 
   
   ![Neu -> Data Factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
   
   1. Geben Sie **ADFTutorialDataFactory** als **Namen** ein. 
      
         ![Blatt "Neue Data Factory"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Der Name der Azure Data Factory muss **global eindeutig**sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Data Factory-Name nicht verfügbar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll. 
   3. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:
      
      - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
      - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
         
          Bei einigen Schritten in diesem Tutorial wird davon ausgegangen, dass Sie den Namen **ADFTutorialResourceGroup** für die Ressourcengruppe verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../../azure-resource-manager/resource-group-overview.md).  
   4. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur Regionen angezeigt, die vom Data Factory-Dienst unterstützt werden.
   5. Wählen Sie die Option **An Dashboard anheften** aus.     
   6. Klicken Sie auf **Create**.
      
      > [!IMPORTANT]
      > Zum Erstellen von Data Factory-Instanzen müssen Sie Mitglied der Rolle [Data Factory-Mitwirkender](../../role-based-access-control/built-in-roles.md#data-factory-contributor) auf Abonnement- bzw. Ressourcengruppenebene sein.
      > 
      > Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.                
      > 
      > 
3. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...). 

    ![Kachel „Die Data Factory wird bereitgestellt“](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Nach Abschluss der Erstellung wird das Blatt **Data Factory** wie in dieser Abbildung angezeigt:
   
   ![Data Factory-Startseite](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Tutorial werden keine Compute Services wie Azure HDInsight oder Azure Data Lake Analytics verwendet. Sie verwenden zwei Datenspeicher vom Typ „Azure Storage“ (Quelle) und „Azure SQL-Datenbank“ (Ziel). 

Aus diesem Grund erstellen Sie zwei verknüpfte Dienste mit dem Namen „AzureStorageLinkedService“ und „AzureSqlLinkedService“ vom Typ „AzureStorage“ und „AzureSqlDatabase“.  

Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Dieses Speicherkonto ist das Konto, in dem Sie im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und die Daten hochgeladen haben.   

AzureSqlLinkedService verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt.  

### <a name="create-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. In diesem Abschnitt geben Sie Name und Schlüssel Ihres Azure Storage-Kontos an.  

1. Klicken Sie auf dem Blatt **Data Factory** auf die Kachel **Verfassen und bereitstellen**.
   
   ![Kachel „Erstellen und bereitstellen“](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Der **Data Factory-Editor** wird wie in der folgenden Abbildung dargestellt angezeigt: 

    ![Data Factory Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. Klicken Sie im Editor auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure Storage** aus. Die JSON-Vorlage zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im rechten Bereich angezeigt werden. 
   
    ![Editor – Schaltfläche „Neuer Datenspeicher“](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Ersetzen Sie `<accountname>` und `<accountkey>` durch die Werte für den Kontonamen und -schlüssel Ihres Azure-Speicherkontos. 
   
    ![Editor – Blobspeicher – JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Klicken Sie in der Symbolleiste auf **Bereitstellen** . Das bereitgestellte **AzureStorageLinkedService** -Element wird jetzt in der Strukturansicht angezeigt. 
   
    ![Editor – Blobspeicher bereitstellen](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Weitere Informationen zu JSON-Eigenschaften in der Definition von verknüpften Diensten finden Sie im Artikel [Azure Blob Storage-Connector](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Erstellen eines verknüpften Diensts für die Azure SQL-Datenbank
In diesem Schritt verknüpfen Sie die Azure SQL-Datenbank mit Ihrer Data Factory. In diesem Abschnitt geben Sie den Namen der Azure SQL Server-Instanz, den Datenbanknamen, den Benutzernamen und das Benutzerkennwort an. 

1. Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü die Option **Azure SQL-Datenbank** aus. Die JSON-Vorlage zum Erstellen eines mit der Azure SQL-Datenbank verknüpften Diensts sollte im rechten Bereich angezeigt werden.
2. Ersetzen Sie `<servername>`, `<databasename>`, `<username>@<servername>` und `<password>` durch die Namen für Ihren Azure SQL-Server, die Datenbank, das Benutzerkonto und das Kennwort. 
3. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst **AzureSqlLinkedService** zu erstellen und bereitzustellen.
4. Vergewissern Sie sich, dass **AzureSqlLinkedService** in der Strukturansicht unter **Verknüpfte Dienste** angezeigt wird.  

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie unter [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Erstellen von Datasets
Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. In diesem Schritt definieren Sie die beiden Datasets „InputDataset“ und „OutputDataset“. Sie stellen Eingabe- und Ausgabedaten dar, die in den Datenspeichern gespeichert werden, auf die mit „AzureStorageLinkedService“ und „AzureSqlLinkedService“ verwiesen wird.

Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Das Eingabeblobdataset („InputDataset“) gibt den Container und den Ordner an, der die Eingabedaten enthält.  

Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Azure SQL-Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset („OututDataset“) die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden. 

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets
In diesem Schritt erstellen Sie ein Dataset namens „InputDataset“, das auf eine Blobdatei (emp.txt) im Stammordner eines Blobcontainers („adftutorial“) in Azure Storage (dargestellt durch den verknüpften Dienst „AzureStorageLinkedService“) verweist. Wenn Sie keinen Wert für „fileName“ festlegen (oder diesen überspringen), werden Daten aus allen Blobs im Eingabeordner in das Ziel kopiert. In diesem Tutorial legen Sie einen Wert für „fileName“ fest. 

1. Klicken Sie im **Editor** für die Data Factory auf **... More** (Mehr), klicken Sie auf **Neues Dataset**, und klicken Sie im Dropdownmenü auf **Azure Blob Storage**. 
   
    ![Menü „Neues Dataset“](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt: 
   
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
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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
    | type | Die Eigenschaft „type“ wird auf **AzureBlob** festgelegt, da sich Daten in Azure Blob Storage befinden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den **AzureStorageLinkedService**-Dienst, den Sie zuvor erstellt haben. |
    | folderPath | Diese Eigenschaft gibt den **Blobcontainer** und den **Ordner** an, der Eingabeblobs enthält. In diesem Tutorial ist „adftutorial“ der Blobcontainer und „folder“ der Stammordner. | 
    | fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Tutorial wurde **emp.txt** für „fileName“ angegeben. Daher wird nur diese Datei für die Verarbeitung gewählt. |
    | format -> type |Die Eingabedatei weist das Textformat auf. Daher verwenden wir **TextFormat**. |
    | columnDelimiter | Die Spalten in der Eingabedatei werden per **Komma (`,`)** voneinander getrennt. |
    | frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Eingabeslices **stündlich** verfügbar sind. Der Data Factory-Dienst sucht also stündlich im Stammordner des angegebenen Blobcontainers (**adftutorial**) nach Eingabedaten. Er sucht innerhalb der Start- und Endzeit der Pipeline nach den Daten, nicht vor oder nach diesen Zeiten.  |
    | external | Diese Eigenschaft wird auf **true** festgelegt, wenn die Daten nicht von dieser Pipeline generiert werden. Die Eingabedaten in diesem Tutorial sind in der Datei „emp.txt“ enthalten, die nicht von dieser Pipeline generiert wurde. Daher legen wir diese Eigenschaft auf „true“ fest. |

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).      
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um das Dataset **InputDataset** zu erstellen und bereitzustellen. Vergewissern Sie sich, dass **InputDataset** in der Strukturansicht angezeigt wird.

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets
Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung mit der Azure SQL-Datenbank verwendet. Das in diesem Schritt erstellte SQL-Tabellenausgabedataset (OututDataset) gibt die Tabelle in der Datenbank an, in die die Daten aus dem Blobspeicher kopiert werden.

1. Klicken Sie im **Editor** für die Data Factory auf **... More** (Mehr), klicken Sie auf **Neues Dataset**, und klicken Sie im Dropdownmenü auf **Azure SQL**. 
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:

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
        "linkedServiceName": "AzureSqlLinkedService",
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
    | type | Die Eigenschaft „type“ wird auf **AzureSqlTable** festgelegt, da Daten in eine Tabelle in Azure SQL-Datenbank kopiert werden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den **AzureSqlLinkedService**-Dienst, den Sie zuvor erstellt haben. |
    | tableName | Diese Eigenschaft gibt die **Tabelle** an, in die die Daten kopiert werden. | 
    | frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Ausgabeslices innerhalb der Start- und Endzeit der Pipeline **stündlich** erstellt werden, nicht vor oder nach diesen Zeiten.  |

    Die emp-Tabelle in der Datenbank enthält drei Spalten: **ID**, **FirstName** und **LastName**. Da es sich bei „ID“ um eine Identitätsspalte handelt, müssen Sie hier lediglich **FirstName** und **LastName** angeben.

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#dataset-properties).
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um das Dataset **OutputDataset** zu erstellen und bereitzustellen. Vergewissern Sie sich, dass **OutputDataset** in der Strukturansicht unter **Datasets** angezeigt wird. 

## <a name="create-pipeline"></a>Erstellen der Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, für die **InputDataset** als Eingabe und **OutputDataset** als Ausgabe verwendet wird.

Derzeit steuert das Ausgabedataset den Zeitplan. In diesem Tutorial wird ein Ausgabedataset konfiguriert, um einmal pro Stunde einen Slice zu erzeugen. Für die Pipeline ist eine Start- und Endzeit festgelegt, die einen Tag, d.h. 24 Stunden, auseinander liegen. Aus diesem Grund werden 24 Ausgabedatasetslices von der Pipeline erzeugt. 

1. Klicken Sie im **Editor** für die Data Factory auf **... More** (Mehr), und klicken Sie auf **Neue Pipeline**. Alternativ können Sie in der Strukturansicht mit der rechten Maustaste auf **Pipelines** und dann auf **Neue Pipeline** klicken.
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt: 

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Beachten Sie folgende Punkte:
   
   - Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). In Data Factory-Lösungen können Sie auch [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) verwenden.
   - Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt. 
   - Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Eine vollständige Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Um Informationen zum Verwenden eines bestimmten unterstützten Datenspeichers als Quelle/Senke zu erhalten, klicken Sie auf den Link in der Tabelle.
   - Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601)angegeben werden. Beispiel:  2016-10-14T16:32:41Z. Die Zeitangabe **end** ist optional, wird aber in diesem Tutorial verwendet. Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an.
     
     Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.

     Beschreibungen der JSON-Eigenschaften in einer Pipelinedefinition finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Beschreibungen der JSON-Eigenschaften in der Definition einer Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). Beschreibungen der JSON-Eigenschaften, die von BlobSource unterstützt werden, finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md). Beschreibungen der JSON-Eigenschaften, die von SqlSink unterstützt werden, finden Sie im Artikel [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md).
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Pipeline **ADFTutorialPipeline** bereitzustellen. Vergewissern Sie sich, dass die Pipeline in der Strukturansicht angezeigt wird. 
4. Schließen Sie jetzt das Blatt **Editor**, indem Sie auf **X** klicken. Klicken Sie erneut auf **X**, um die **Data Factory**-Startseite für **ADFTutorialDataFactory** anzuzeigen.

**Glückwunsch!** Sie haben die Erstellung einer Azure Data Factory mit einer Pipeline zum Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank erfolgreich abgeschlossen. 


## <a name="monitor-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Überwachen der Pipeline mit der App „Überwachung und Verwaltung“
In den folgenden Schritten wird gezeigt, wie Sie mithilfe der Anwendung „Überwachung und Verwaltung“ Pipelines in Ihrer Data Factory überwachen: 

1. Klicken Sie auf der Startseite Ihrer Data Factory auf die Kachel **Überwachung und Verwaltung**.
   
    ![Kachel „Überwachung und Verwaltung“](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Nun sollte die Anwendung **Überwachung und Verwaltung** auf einer separaten Registerkarte angezeigt werden. 

    > [!NOTE]
    > Wenn Sie feststellen, dass der Webbrowser bei der Autorisierung hängen bleibt, führen Sie eine der folgenden Maßnahmen durch: Deaktivieren Sie das Kontrollkästchen **Cookies und Websitedaten von Drittanbietern blockieren** oder erstellen Sie eine Ausnahme für **login.microsoftonline.com**. Versuchen Sie anschließend erneut, die App zu öffnen.

    ![App „Überwachung und Verwaltung“](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Ändern Sie die **Startzeit** und **Endzeit** in die Startzeit (2017-05-11) und Endzeit (2017-05-12) Ihrer Pipeline, und klicken Sie auf **Übernehmen**.       
3. Die **Aktivitätsfenster**, die innerhalb der Start- und Endzeit der Pipeline stündlich zugeordnet werden, werden in der Liste im mittleren Bereich angezeigt. 
4. Um Details zu einem Aktivitätsfenster anzuzeigen, wählen Sie in der Liste **Aktivitätsfenster** das entsprechende Aktivitätsfenster aus. 
    ![Details zum Aktivitätsfenster](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    Im Aktivitätsfenster-Explorer auf der rechten Seite wird angezeigt, dass alle Slices bis zur aktuellen UTC-Zeit (20:12 Uhr) (in Grün) verarbeitet wurden. Die Slices von 20-21 Uhr, 21-22 Uhr, 22-23 Uhr und 23-24 Uhr wurden noch nicht verarbeitet.

    Der Abschnitt **Versuche** im rechten Bereich enthält Informationen zur Aktivität, die für den Datenslice ausgeführt wurde. Wenn ein Fehler aufgetreten ist, werden Informationen zum Fehler angezeigt. Wenn der Eingabeordner oder -container beispielsweise nicht vorhanden ist und die Verarbeitung des Slice fehlschlägt, wird eine Fehlermeldung angezeigt, die darauf hinweist, dass der Container oder Ordner nicht vorhanden ist.

    ![Versuche zur Aktivitätsausführung](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Starten Sie **SQL Server Management Studio**, stellen Sie eine Verbindung mit der Azure SQL-Datenbank her, und überprüfen Sie, ob die Zeilen in die Tabelle **emp** der Datenbank eingefügt wurden.
    
    ![SQL-Abfrageergebnisse](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Ausführliche Informationen zur Verwendung dieser App finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md).

### <a name="monitor-pipeline-using-diagram-view"></a>Überwachen der Pipeline mit der Diagrammansicht
Sie können Datenpipelines auch mithilfe der Diagrammansicht überwachen.  

1. Klicken Sie auf dem Blatt **Data Factory** auf **Diagramm**.
   
    ![Blatt "Data Factory-Blade" – Kachel "Diagramm"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Ein Diagramm wie in der folgenden Abbildung wird angezeigt: 
   
    ![Diagrammansicht](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. Doppelklicken Sie in der Diagrammansicht auf **InputDataset**, um die Slices für das Dataset anzuzeigen.  
   
    ![Datasets mit ausgewähltem InputDataset-Element](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Klicken Sie auf den Link **Mehr anzeigen**, um alle Datenslices anzuzeigen. Es werden 24 stündliche Slices zwischen Start- und Endzeit der Pipeline angezeigt. 
   
    ![Alle Eingabedatenslices](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Beachten Sie, dass alle Datenslices bis zur aktuellen UTC-Zeit den Status **Bereit** aufweisen, da sich die Datei **emp.txt** durchgängig im Blobcontainer unter **adftutorial\input** befindet. Die Slices für die zukünftigen Zeiten befinden sich noch nicht im Status „Bereit“. Überprüfen Sie, ob unten im Abschnitt **Letzte fehlerhafte Slices** keine Slices angezeigt werden.
6. Schließen Sie die Blätter, bis Sie die Diagrammansicht sehen, oder scrollen Sie nach links, um die Diagrammansicht anzuzeigen. Doppelklicken Sie dann auf **OutputDataset**. 
8. Klicken Sie auf dem Blatt **Tabelle** unter **OutputDataset** auf den Link **Mehr anzeigen**, damit alle Slices angezeigt werden.

    ![Blatt "Datenslices"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Beachten Sie, die alle Slices bis zur aktuellen UTC-Zeit vom Status **Ausstehende Ausführung** in den Status **In Bearbeitung** ==> **Bereit** wechseln. Die Slices aus der Vergangenheit (vor der aktuellen Zeit) werden standardmäßig in der Reihenfolge von den neuesten zu den ältesten Slices verarbeitet. Wenn die aktuelle Zeit beispielsweise 20:12 Uhr (UTC) lautet, wird der Slice für 19–20 Uhr vor dem Slice für 18–19 Uhr verarbeitet. Der Slice für 20–21 Uhr wird standardmäßig am Ende des Zeitintervalls verarbeitet, d.h. nach 21 Uhr.  
10. Klicken Sie in der Liste auf einen beliebigen Datenslice. Das Blatt **Datenslice** wird angezeigt. Ein Datenelement, das einem Aktivitätsfenster zugeordnet ist, wird als „Slice“ bezeichnet. Ein Slice kann eine oder mehrere Dateien enthalten.  
    
     ![Blatt "Datenslice"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.
11. Auf dem Blatt **DATENSLICE** sollten in der Liste im unteren Bereich alle Aktivitätsausführungen angezeigt werden. Klicken Sie auf eine **Aktivitätsausführung**, um das Blatt **Details zur Aktivitätsausführung** anzuzeigen. 
    
    ![Aktivitätsausführung – Details](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    Auf diesem Blatt werden u.a. Dauer des Kopiervorgangs, Durchsatz, Anzahl gelesener und geschriebener Datenbytes sowie Start- und Endzeit der Ausführung angezeigt.  
12. Klicken Sie auf **X**, um alle Blätter zu schließen, bis Sie sich wieder im Startblatt für **ADFTutorialDataFactory** befinden.
13. (Optional) Klicken Sie auf die Kachel **Datasets** oder **Pipelines**, um die in den vorangehenden Schritten angezeigten Blätter abzurufen. 
14. Starten Sie **SQL Server Management Studio**, stellen Sie eine Verbindung mit der Azure SQL-Datenbank her, und überprüfen Sie, ob die Zeilen in die Tabelle **emp** der Datenbank eingefügt wurden.
    
    ![SQL-Abfrageergebnisse](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Zusammenfassung
In diesem Lernprogramm haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Sie haben mithilfe des Azure-Portals die Data Factory, verknüpfte Dienste, Datasets und eine Pipeline erstellt. Im Anschluss sind die allgemeinen Schritte aufgeführt, die Sie in diesem Tutorial ausgeführt haben:  

1. Sie haben eine Azure **Data Factory**erstellt.
2. Sie haben **verknüpfte Dienste**erstellt:
   1. Einen verknüpften **Azure Storage** -Dienst zum Verknüpfen Ihres Azure Storage-Kontos, in dem Eingabedaten enthalten sind.     
   2. Einen verknüpften **Azure SQL** -Dienst zum Verknüpfen Ihrer Azure SQL-Datenbank, in der die Ausgabedaten enthalten sind. 
3. Sie haben **Datasets** erstellt, die Eingabedaten und Ausgabedaten für Pipelines beschreiben.
4. Sie haben eine **Pipeline** mit einer **Kopieraktivität**, mit **BlobSource** als Quelle und mit **qlSink** als Senke erstellt.  

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher.
