---
title: 'Tutorial: Erstellen einer Pipeline zum Verschieben von Daten mithilfe von Azure PowerShell '
description: In diesem Tutorial erstellen Sie mithilfe von Azure PowerShell eine Azure Data Factory-Pipeline mit Kopieraktivität.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aa97fd2ac12ca73d1a317fb09e03e49d0056ceb1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608628"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: Erstellen einer Data Factory-Pipeline zum Verschieben von Daten mithilfe von Azure PowerShell
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-powershell.md). 

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell eine Data Factory mit einer Pipeline erstellen, die Daten aus Azure Blob Storage in Azure SQL-Datenbank kopiert. Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Tutorials den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).   

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: die Kopieraktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> In diesem Artikel werden nicht alle Data Factory-Cmdlets behandelt. Eine umfassende Dokumentation zu diesen Cmdlets finden Sie in der [Data Factory-Cmdlet-Referenz](/powershell/module/az.datafactory).
> 
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Führen Sie die Schritte zur Erfüllung der Voraussetzungen aus, die im Artikel [Voraussetzungen für das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aufgeführt sind.
- Installieren Sie **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).

## <a name="steps"></a>Schritte
Hier sind die Schritte angegeben, die Sie im Rahmen dieses Tutorials ausführen:

1. Erstellen Sie eine Azure **Data Factory**. In diesem Schritt erstellen Sie eine Data Factory mit dem Namen „ADFTutorialDataFactoryPSH“. 
1. Erstellen Sie **verknüpfte Dienste** in der Data Factory. In diesem Schritt erstellen Sie zwei verknüpfte Dienste dieses Typs: Azure Storage und Azure SQL-Datenbank. 

    Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und Daten in dieses Speicherkonto hochgeladen.   

    „AzureSqlLinkedService“ verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) eine SQL-Tabelle in dieser Datenbank erstellt.   
1. Erstellen Sie Eingabe- und Ausgabe **datasets** in der Data Factory.  

    Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Ein Eingabeblobdataset gibt den Container und den Ordner an, der die Eingabedaten enthält.  

    Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrer Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden.
1. Erstellen Sie eine **Pipeline** in der Data Factory. In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität.   

    Die Kopieraktivität kopiert Daten aus einem Blob in Azure Blob Storage in eine Tabelle in Azure SQL-Datenbank. Sie können eine Kopieraktivität in einer Pipeline verwenden, um Daten aus einer beliebigen unterstützten Quelle in ein beliebiges unterstütztes Ziel zu kopieren. Eine Liste der unterstützten Datenspeicher finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
1. Überwachen Sie die Pipeline. In diesem Schritt **überwachen** Sie die Slices von Eingabe- und Ausgabedatasets mithilfe von PowerShell.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
> [!IMPORTANT]
> Sofern dies noch nicht geschehen ist, führen Sie die Schritte zur Erfüllung der [Voraussetzungen für das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) durch.   

Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.

1. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.

    Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:

    ```powershell
    Connect-AzAccount
    ```   

    Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```

    Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **&lt;NameOfAzureSubscription**&gt; durch den Namen Ihres Azure-Abonnements:

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** , indem Sie den folgenden Befehl ausführen:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden.
1. Führen Sie das Cmdlet **New-AzDataFactory** aus, um eine Data Factory namens **ADFTutorialDataFactoryPSH** zu erstellen:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Dieser Name wird möglicherweise bereits verwendet. Verwenden Sie daher einen eindeutigen Namen für die Data Factory, indem Sie ein Präfix oder Suffix (z.B. ADFTutorialDataFactoryPSH05152017) hinzufügen, und führen Sie den Befehl dann erneut aus.  

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen (beispielsweise in „<IhrName>ADFTutorialDataFactoryPSH“). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialDataFactoryPSH". Informationen zu Data Factory-Artefakten finden Sie unter [Data Factory – Benennungsregeln](data-factory-naming-rules.md).

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Data Factory-Instanzen können nur von Mitwirkenden oder Administratoren des Azure-Abonnements erstellt werden.
* Der Name der Data Factory kann später möglicherweise als DNS-Name registriert und so öffentlich sichtbar werden.
* Unter Umständen wird der folgende Fehler angezeigt: „**Dieses Abonnement ist nicht für die Verwendung des Namespace „Microsoft.DataFactory“ registriert.** “ Führen Sie eine der folgenden Aktionen aus, und wiederholen Sie die Veröffentlichung:

  * Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Data Factory-Anbieter registriert wurde:

    ```powershell
    Get-AzResourceProvider
    ```
  * Melden Sie sich unter Verwendung Ihres Azure-Abonnements beim [Azure-Portal](https://portal.azure.com) an. Navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie über das Azure-Portal eine Data Factory. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Tutorial werden keine Compute Services wie Azure HDInsight oder Azure Data Lake Analytics verwendet. Sie verwenden zwei Datenspeicher vom Typ „Azure Storage“ (Quelle) und „Azure SQL-Datenbank“ (Ziel). 

Aus diesem Grund erstellen Sie zwei verknüpfte Dienste mit dem Namen „AzureStorageLinkedService“ und „AzureSqlLinkedService“ vom Typ „AzureStorage“ und „AzureSqlDatabase“.  

Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Dieses Speicherkonto ist das Konto, in dem Sie im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und die Daten hochgeladen haben.   

„AzureSqlLinkedService“ verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Erstellen eines verknüpften Diensts für ein Azure-Speicherkonto
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory.

1. Erstellen Sie im Ordner **C:\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **AzureStorageLinkedService.json** und dem unten angegebenen Inhalt: (Erstellen Sie den Ordner „ADFGetStartedPSH“, sofern er noch nicht vorhanden ist.)

    > [!IMPORTANT]
    > Ersetzen Sie &lt;accountname&gt; und &lt;accountkey&gt; durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos, bevor Sie die Datei speichern. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. Wechseln Sie in **Azure PowerShell** zum Ordner **ADFGetStartedPSH**.
1. Führen Sie das Cmdlet **New-AzDataFactoryLinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureStorageLinkedService**. Für dieses Cmdlet und die anderen Data Factory-Cmdlets, die Sie in diesem Tutorial verwenden, ist das Übergeben von Werten für die Parameter **ResourceGroupName** und **DataFactoryName** erforderlich. Alternativ können Sie das vom Cmdlet „New-AzDataFactory“ zurückgegebene DataFactory-Objekt ohne Eingabe von ResourceGroupName und DataFactoryName bei jeder Ausführung eines Cmdlets übergeben. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Hier ist die Beispielausgabe:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Eine andere Möglichkeit zum Erstellen dieses verknüpften Diensts besteht darin, den Namen der Ressourcengruppe und der Data Factory anstelle des DataFactory-Objekts anzugeben.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-azure-sql-database"></a>Erstellen eines verknüpften Diensts für Azure SQL-Datenbank
In diesem Schritt verknüpfen Sie Azure SQL-Datenbank mit Ihrer Data Factory.

1. Erstellen Sie im Ordner „C:\ADFGetStartedPSH“ eine JSON-Datei mit dem Namen „AzureSqlLinkedService.json“ und dem unten angegebenen Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; und &lt;password&gt; durch die Namen Ihres Servers, Ihrer Datenbank, Ihr Benutzerkonto und Ihr Kennwort.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Führen Sie den folgenden Befehl aus, um einen verknüpften Dienst zu erstellen:

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

    Hier ist die Beispielausgabe:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Bestätigen Sie, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihren Server auf „ein“ festgelegt ist. Führen Sie zum Prüfen und Aktivieren die folgenden Schritte aus:

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
    1. Klicken Sie links auf **Weitere Dienste >** und in der Kategorie **DATENBANKEN** dann auf **SQL Server**.
    1. Wählen Sie Ihren Server in der Liste der SQL Server aus.
    1. Klicken Sie auf dem Blatt „SQL Server“ auf den Link **Firewalleinstellungen anzeigen**.
    1. Klicken Sie auf dem Blatt **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.
    1. Klicken Sie in der Symbolleiste auf **Speichern** . 

## <a name="create-datasets"></a>Erstellen von Datasets
Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. In diesem Schritt definieren Sie die beiden Datasets „InputDataset“ und „OutputDataset“. Sie stellen Eingabe- und Ausgabedaten dar, die in den Datenspeichern gespeichert werden, auf die mit „AzureStorageLinkedService“ und „AzureSqlLinkedService“ verwiesen wird.

Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Das Eingabeblobdataset („InputDataset“) gibt den Container und den Ordner an, der die Eingabedaten enthält.  

Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrer Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset („OututDataset“) die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden. 

### <a name="create-an-input-dataset"></a>Erstellen eines Eingabedatasets
In diesem Schritt erstellen Sie ein Dataset namens „InputDataset“, das auf eine Blobdatei (emp.txt) im Stammordner eines Blobcontainers („adftutorial“) in Azure Storage (dargestellt durch den verknüpften Dienst „AzureStorageLinkedService“) verweist. Wenn Sie keinen Wert für „fileName“ festlegen (oder diesen überspringen), werden Daten aus allen Blobs im Eingabeordner in das Ziel kopiert. In diesem Tutorial legen Sie einen Wert für „fileName“ fest.  

1. Erstellen Sie im Ordner **C:\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **InputDataset.json** und dem folgenden Inhalt:

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
                "fileName": "emp.txt",
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
    | type | Die Eigenschaft „type“ wird auf **AzureBlob** festgelegt, da sich Daten in Azure Blob Storage befinden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den **AzureStorageLinkedService**-Dienst, den Sie zuvor erstellt haben. |
    | folderPath | Diese Eigenschaft gibt den **Blobcontainer** und den **Ordner** an, der Eingabeblobs enthält. In diesem Tutorial ist „adftutorial“ der Blobcontainer und „folder“ der Stammordner. | 
    | fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Tutorial wurde **emp.txt** für „fileName“ angegeben. Daher wird nur diese Datei für die Verarbeitung gewählt. |
    | format -> type |Die Eingabedatei weist das Textformat auf. Daher verwenden wir **TextFormat**. |
    | columnDelimiter | Die Spalten in der Eingabedatei werden per **Komma (`,`)** voneinander getrennt. |
    | frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Eingabeslices **stündlich** verfügbar sind. Der Data Factory-Dienst sucht also stündlich im Stammordner des angegebenen Blobcontainers (**adftutorial**) nach Eingabedaten. Er sucht innerhalb der Start- und Endzeit der Pipeline nach den Daten, nicht vor oder nach diesen Zeiten.  |
    | external | Diese Eigenschaft wird auf **true** festgelegt, wenn die Daten nicht von dieser Pipeline generiert werden. Die Eingabedaten in diesem Tutorial sind in der Datei „emp.txt“ enthalten, die nicht von dieser Pipeline generiert wurde. Daher legen wir diese Eigenschaft auf „true“ fest. |

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).
1. Führen Sie den folgenden Befehl zum Erstellen des Data Factory-Datensatzes aus.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Hier ist die Beispielausgabe:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Erstellen eines Ausgabedatasets
In diesem Teilschritt erstellen Sie ein Ausgabedataset namens **OutputDataset**. Dieses Dataset verweist auf eine SQL-Tabelle in der durch **AzureSqlLinkedService** dargestellten Azure SQL-Datenbank. 

1. Erstellen Sie im Ordner **C:\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **OutputDataset.json** und dem folgenden Inhalt:

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
    | type | Die Eigenschaft „type“ wird auf **AzureSqlTable** festgelegt, weil Daten in eine Tabelle in Azure SQL-Datenbank kopiert werden. |
    | linkedServiceName | Diese Eigenschaft verweist auf den **AzureSqlLinkedService**-Dienst, den Sie zuvor erstellt haben. |
    | tableName | Diese Eigenschaft gibt die **Tabelle** an, in die die Daten kopiert werden. | 
    | frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Ausgabeslices innerhalb der Start- und Endzeit der Pipeline **stündlich** erstellt werden, nicht vor oder nach diesen Zeiten.  |

    Die emp-Tabelle in der Datenbank enthält drei Spalten: **ID**, **FirstName** und **LastName**. Da es sich bei „ID“ um eine Identitätsspalte handelt, müssen Sie hier lediglich **FirstName** und **LastName** angeben.

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#dataset-properties).
1. Führen Sie den folgenden Befehl aus, um das Data Factory-Dataset zu erstellen:

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Hier ist die Beispielausgabe:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, für die **InputDataset** als Eingabe und **OutputDataset** als Ausgabe verwendet wird.

Derzeit steuert das Ausgabedataset den Zeitplan. In diesem Tutorial wird ein Ausgabedataset konfiguriert, um einmal pro Stunde einen Slice zu erzeugen. Für die Pipeline ist eine Start- und Endzeit festgelegt, die einen Tag, d.h. 24 Stunden, auseinander liegen. Aus diesem Grund werden 24 Ausgabedatasetslices von der Pipeline erzeugt. 

1. Erstellen Sie im Ordner **C:\ADFGetStartedPSH** eine JSON-Datei namens **ADFTutorialPipeline.json** mit folgendem Inhalt:

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

     Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den der **end**-Eigenschaft durch den nächsten Tag. Sie können auch nur den Datumsteil angeben und den Uhrzeitteil überspringen. „2016-02-03“ entspricht beispielsweise „2016-02-03T00:00:00Z“.

     Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601)angegeben werden. Beispiel: 2016-10-14T16:32:41Z. Die Zeitangabe **end** ist optional, wird aber in diesem Tutorial verwendet. 

     Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an.

     Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.

     Beschreibungen der JSON-Eigenschaften in einer Pipelinedefinition finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Beschreibungen der JSON-Eigenschaften in der Definition einer Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). Beschreibungen der JSON-Eigenschaften, die von BlobSource unterstützt werden, finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md). Beschreibungen der JSON-Eigenschaften, die von SqlSink unterstützt werden, finden Sie im Artikel [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md).
1. Führen Sie den folgenden Befehl aus, um die Data Factory-Tabelle zu erstellen:

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Hier ist die Beispielausgabe: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Glückwunsch!** Sie haben eine Azure Data Factory mit einer Pipeline zum Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank erfolgreich erstellt. 

## <a name="monitor-the-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einer Azure Data Factory.

1. Ersetzen Sie &lt;DataFactoryName&gt; durch den Namen Ihrer Data Factory, führen Sie **Get-AzDataFactory** aus, und weisen Sie der Ausgabe eine $df-Variable zu.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Beispiel:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```

    Geben Sie anschließend die Inhalte von $df aus, um die folgende Ausgabe anzuzeigen: 

    ```
    PS C:\ADFGetStartedPSH> $df

    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Führen Sie **Get-AzDataFactorySlice** aus, um Details zu allen Slices von **OutputDataset** zu erhalten. Dies ist das Ausgabedataset der Pipeline.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Diese Einstellung muss mit dem Wert **Start** im JSON-Code der Pipeline übereinstimmen. Es sollten 24 Slices angezeigt werden (einer für jede Stunde von 12:00 Uhr des aktuellen Tages bis 12:00 Uhr des nächsten Tages).

   Im Folgenden werden drei Beispielslices basierend auf der Ausgabe vorgestellt: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Führen Sie **Get-AzDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen **bestimmten** Slice abzurufen. Kopieren Sie den Datums-/Zeitwert aus der Ausgabe des vorherigen Befehls, um den Wert für den Parameter „StartDateTime“ anzugeben. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Hier ist die Beispielausgabe: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

In der [Cmdlet-Referenz der Data Factory](/powershell/module/az.datafactory) finden Sie eine umfassende Dokumentation zu den Data Factory-Cmdlets.

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in Azure SQL-Datenbank zu kopieren. Sie haben PowerShell verwendet, um die Data Factory, verknüpfte Dienste, Datasets und eine Pipeline zu erstellen. Im Anschluss sind die allgemeinen Schritte aufgeführt, die Sie in diesem Tutorial ausgeführt haben:  

1. Sie haben eine Azure **Data Factory** erstellt.
1. Sie haben **verknüpfte Dienste** erstellt:

   a. Einen verknüpften **Azure Storage**-Dienst zum Verknüpfen Ihres Azure-Speicherkontos, in dem die Eingabedaten enthalten sind.     
   b. Einen verknüpften **Azure SQL**-Dienst zum Verknüpfen Ihrer SQL-Datenbank, in der die Ausgabedaten enthalten sind.
1. Sie haben **Datasets** erstellt, die Eingabedaten und Ausgabedaten für Pipelines beschreiben.
1. Sie haben eine **Pipeline** mit **Kopieraktivität**, mit **BlobSource** als Quelle und mit **SqlSink** als Senke erstellt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher. 

