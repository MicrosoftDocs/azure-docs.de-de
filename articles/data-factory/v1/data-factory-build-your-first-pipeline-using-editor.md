---
title: Erstellen Ihrer ersten Data Factory (Azure-Portal)
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mit dem Data Factory-Editor im Azure-Portal.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 449c8ec976a71d68e3ddddfea839ef06094c2176
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392220"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Tutorial: Erstellen der ersten Data Factory mit dem Azure-Portal
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory, die allgemein verfügbar ist. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, helfen Ihnen die Informationen unter [Schnellstart: Erstellen einer Data Factory mit Data Factory](../quickstart-create-data-factory-dot-net.md).

> [!WARNING]
> Der JSON-Editor im Azure-Portal zum Erstellen und Bereitstellen von Pipelines für ADF v1 wird am 31. Juli 2019 deaktiviert. Nach dem 31. Juli 2019 können Sie zum Erstellen und Bereitstellen von Pipelines für ADF v1 weiterhin [Powershell-Cmdlets für ADF v1](/powershell/module/az.datafactory/), das [ADF v1 .NET SDK](/dotnet/api/microsoft.azure.management.datafactories.models) und [REST-APIs für ADF v1](/rest/api/datafactory/) verwenden.

In diesem Artikel erfahren Sie, wie Sie mithilfe des [Azure-Portals](https://portal.azure.com/) Ihre erste Data Factory erstellen. Falls Sie das Tutorial mit anderen Tools/SDKs absolvieren möchten, wählen Sie in der Dropdownliste eine andere Option aus. 

Die Pipeline in diesem Tutorial enthält eine Aktivität: Azure HDInsight Hive-Aktivität. Bei dieser Aktivität wird ein Hive-Skript in einem HDInsight-Cluster ausgeführt, mit dem Eingabedaten transformiert werden, um Ausgabedaten zu erhalten. Die Pipeline zwischen dem Start- und Endzeitpunkt wird einmal pro Monat ausgeführt. 

> [!NOTE]
> Die Datenpipeline in diesem Tutorial transformiert Eingabedaten in Ausgabedaten. Ein Tutorial zum Kopieren von Daten mithilfe von Data Factory finden Sie unter [Tutorial: Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Planung und Ausführung in einer Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Voraussetzungen
Lesen Sie die [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md), und führen Sie die Schritte im Abschnitt mit den Voraussetzungen aus.

Dieser Artikel bietet keine grundlegende Übersicht über den Data Factory-Dienst. Weitere Informationen zum Dienst finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Ein Beispiel ist eine Copy-Aktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher. Ein weiteres Beispiel ist eine HDInsight Hive-Aktivität zum Ausführen eines Hive-Skripts, um Eingabedaten zu transformieren und Ausgabedaten zu erhalten. 

Führen Sie die folgenden Schritte aus, um eine Data Factory zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie auf **Neu** > **Daten + Analysen** > **Data Factory**.

   ![Blatt "Erstellen"](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. Geben Sie auf dem Blatt **Neue Data Factory** unter **Name** den Namen **GetStartedDF** ein.

   ![Blatt "Neue Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Der Name der Data Factory muss global eindeutig sein. Wenn die Fehlermeldung „Die Data Factory mit dem Namen „CustomActivityFactory“ ist nicht verfügbar.“ angezeigt wird, ändern Sie den Namen der Data Factory. Verwenden Sie beispielsweise „IhrNameGetStartedDF“, und erstellen Sie die Data Factory erneut. Weitere Informationen zu Benennungsregeln finden Sie unter [Data Factory: Benennungsregeln](data-factory-naming-rules.md).
   >
   > Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
   >
   >
1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, in dem die Data Factory erstellt werden soll.

1. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe. Erstellen Sie für das Tutorial eine Ressourcengruppe mit dem Namen **ADFGetStartedRG**.

1. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur Regionen angezeigt, die vom Data Factory-Dienst unterstützt werden.

1. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**.

1. Klicken Sie auf **Erstellen**.

   > [!IMPORTANT]
   > Zum Erstellen von Data Factory-Instanzen müssen Sie Mitglied der Rolle [Data Factory-Mitwirkender](../../role-based-access-control/built-in-roles.md#data-factory-contributor) auf Abonnement- bzw. Ressourcengruppenebene sein.
   >
   >
1. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status **Deploying Data Factory** (Data Factory wird bereitgestellt...):    

   ![Status „Deploying Data Factory“ (Data Factory wird bereitgestellt...)](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Nachdem die Data Factory erstellt wurde, wird die Seite **Data Factory** mit dem Inhalt der Data Factory angezeigt.     

    ![Blatt "Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Vor dem Erstellen einer Pipeline in der Data Factory müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste, um Datenspeicher/Computerdienste mit Ihrem Datenspeicher zu verknüpfen. Anschließend definieren Sie Ein- und Ausgabedatasets, um die Eingabe- oder Ausgabedaten in verknüpften Datenspeichern darzustellen. Zum Schluss erstellen Sie die Pipeline mit einer Aktivität, die diese Datasets verwendet.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Das Speicherkonto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um ein in der Aktivität der Pipeline in diesem Beispiel angegebenes Hive-Skript auszuführen. Ermitteln Sie, welche [Datenspeicher](data-factory-data-movement-activities.md)/[Computedienste](data-factory-compute-linked-services.md) in Ihrem Szenario verwendet werden. Verbinden Sie anschließend diese Dienste mit der Data Factory, indem Sie verknüpfte Dienste erstellen.  

### <a name="create-a-storage-linked-service"></a>Erstellen eines verknüpften Speicherdiensts
In diesem Schritt verknüpfen Sie Ihr Speicherkonto mit Ihrer Data Factory. In diesem Tutorial verwenden Sie das gleiche Speicherkonto, um Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

1. Klicken Sie auf dem Blatt **Data Factory** für **GetStartedDF** auf **Erstellen und bereitstellen**. Der Data Factory-Editor wird angezeigt.

   ![Kachel "Erstellen und bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Klicken Sie auf **Neuer Datenspeicher** und dann auf **Azure Storage**.

   ![Blatt „Neuer Datenspeicher“](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Das JSON-Skript zum Erstellen eines mit Storage verknüpften Diensts wird im Editor angezeigt.

   ![Mit Storage verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersetzen Sie **account name** durch den Namen Ihres Speicherkontos. Ersetzen Sie **account key** durch den Zugriffsschlüssel Ihres Speicherkontos. Weitere Informationen zum Abrufen der Speicherzugriffsschlüssel finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

    ![Schaltfläche "Bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Nachdem der verknüpfte Dienst erfolgreich bereitgestellt wurde, wird das Fenster „Draft-1“ geschlossen. **AzureStorageLinkedService** wird in der Strukturansicht links angezeigt.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Erstellen eines verknüpften HDInsight-Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt. Der Cluster wird gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Zeit im Leerlaufzustand verstrichen ist.

1. Wählen Sie im Data Factory-Editor **Weitere** > **Neue Computeressource** > **Bedarfsgesteuerter HDInsight-Cluster** aus.

    ![Neu berechnen](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein. Der JSON-Codeausschnitt beschreibt die Eigenschaften, die zum Erstellen des bedarfsgesteuerten HDInsight-Clusters verwendet werden.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

   | Eigenschaft | BESCHREIBUNG |
   |:--- |:--- |
   | clusterSize |Gibt die Größe des HDInsight-Clusters an. |
   | timeToLive | Gibt die Leerlaufzeit des HDInsight-Clusters an, bevor er gelöscht wird. |
   | linkedServiceName | Gibt das Speicherkonto an, das verwendet wird, um die von HDInsight generierten Protokolle zu speichern. |

    Beachten Sie folgende Punkte:

     a. Die Data Factory erstellt mit den JSON-Eigenschaften einen Linux-basierten HDInsight-Cluster für Sie. Weitere Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Anstelle eines bedarfsgesteuerten HDInsight-Clusters können Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. Der HDInsight-Cluster erstellt einen Standardcontainer im Blobspeicher, den Sie in der JSON-Eigenschaft angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dieses Verhalten ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet wird. Dies gilt nur dann nicht, wenn ein aktiver Cluster (**timeToLive**) vorhanden ist. Der Cluster wird nach Abschluss der Verarbeitung automatisch gelöscht.

     Wenn mehr Slices verarbeitet werden, werden in Ihrem Blobspeicher viele Container angezeigt. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Die Namen dieser Container basieren auf dem folgenden Muster: „adf **ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel“. Verwenden Sie Tools wie den [Azure Storage-Explorer](https://storageexplorer.com/), um Container in Ihrem Blobspeicher zu löschen.

     Weitere Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

    ![Bereitstellungsoption](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Vergewissern Sie sich, dass in der Strukturansicht auf der linken Seite **AzureStorageLinkedService** und **HDInsightOnDemandLinkedService** angezeigt werden.

    ![Screenshot der Verknüpfung von „AzureStorageLinkedService“ and „HDInsightOnDemandLinkedService“](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf „AzureStorageLinkedService“, den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst verweist auf ein Speicherkonto. Datasets geben den Container, den Ordner und den Dateinamen im Speicher mit Eingabe- und Ausgabedaten an.   

### <a name="create-the-input-dataset"></a>Erstellen des Eingabedatasets
1. Wählen Sie im Data Factory-Editor **Weitere** > **Neues Dataset** > **Azure Blob Storage** aus.

    ![Neues Dataset](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein. Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobInput**, das Eingabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Eingabedaten im Blobcontainer **adfgetstarted** und im Ordner **inputdata** gespeichert werden.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

   | Eigenschaft | Geschachtelt unter | BESCHREIBUNG |
   |:--- |:--- |:--- |
   | type | properties |Die Type-Eigenschaft wird auf **AzureBlob** festgelegt, da sich Daten im Blobspeicher befinden. |
   | linkedServiceName | format |Diese Eigenschaft verweist auf den AzureStorageLinkedService-Dienst, den Sie zuvor erstellt haben. |
   | folderPath | typeProperties | Diese Eigenschaft gibt den Blobcontainer und den Ordner an, der Eingabeblobs enthält. | 
   | fileName | typeProperties |Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Tutorial wird nur die Datei „input.log“ verarbeitet. |
   | type | format |Da die Protokolldateien im Textformat vorliegen, verwenden Sie **TextFormat**. |
   | columnDelimiter | format |Spalten werden in den Protokolldateien per Komma (`,`) voneinander getrennt. |
   | frequency/interval | availability |„frequency“ wird auf **Month** und „interval“ auf **1** festgelegt, was bedeutet, dass die Eingabeslices monatlich verfügbar sind. |
   | external | properties | Diese Eigenschaft wird auf **true** festgelegt, wenn die Daten nicht von dieser Pipeline generiert werden. In diesem Tutorial wird die Datei „input.log“ nicht von dieser Pipeline generiert. Daher wird die Eigenschaft auf **true** festgelegt. |

    Weitere Informationen zu diesen JSON-Eigenschaften finden Sie unter [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das neu erstellte DataSet bereitzustellen. Das Dataset wird in der Strukturansicht auf der linken Seite angezeigt.

### <a name="create-the-output-dataset"></a>Erstellen des Ausgabedatasets
Nun erstellen Sie das Ausgabedataset, das die im Blobspeicher gespeicherten Ausgabedaten darstellt.

1. Wählen Sie im Data Factory-Editor **Weitere** > **Neues Dataset** > **Azure Blob Storage** aus.

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein. Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobOutput**, um die Struktur der Daten anzugeben, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfgetstarted** und im Ordner **partitioneddata** gespeichert werden. Der Abschnitt **availability** gibt an, dass das Ausgabedataset monatlich erzeugt wird.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Im Abschnitt „Erstellen des Eingabedatasets“ werden diese Eigenschaften beschrieben. Sie legen die Eigenschaft „external“ nicht für ein Ausgabedataset fest, da das Dataset vom Data Factory-Dienst erstellt wird.

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das neu erstellte DataSet bereitzustellen.

1. Überprüfen Sie, ob das Dataset erfolgreich erstellt wurde.

    ![Strukturansicht mit verknüpften Diensten](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer HDInsight Hive-Aktivität. Der Eingabeslice ist monatlich verfügbar („frequency“: „Month“, „interval“: „1“). Der Ausgabeslice wird monatlich erstellt. Die scheduler-Eigenschaft für die Aktivität ist ebenfalls auf ein monatliches Intervall festgelegt. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Zurzeit steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe generiert. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Am Ende dieses Abschnitts werden die im folgenden JSON-Codeausschnitt verwendeten Eigenschaften erläutert.

1. Klicken Sie im Data Factory-Editor auf **Weitere** > **Neue Pipeline**.

    ![Option für neue Pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein.

   > [!IMPORTANT]
   > Ersetzen Sie im JSON-Codeausschnitt **storageaccountname** durch den Namen Ihres Speicherkontos.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster zu verarbeiten.

    Die Hive-Skriptdatei **partitionweblogs.hql** ist im Speicherkonto gespeichert, das durch den scriptLinkedService-Dienst namens **AzureStorageLinkedService1** angegeben wird. Sie befindet sich im Ordner **script** im Container **adfgetstarted**.

    Der Abschnitt **defines** wird zum Angeben der Runtimeeinstellungen verwendet, die als Hive-Konfigurationswerte an das Hive-Skript übergeben werden. Beispiele: ${hiveconf:inputtable} und ${hiveconf:partitionedtable}.

    Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

    Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript in der Computeinstanz ausgeführt wird, die durch **linkedServiceName**: **HDInsightOnDemandLinkedService** angegeben ist.

   > [!NOTE]
   > Weitere Informationen zu den in diesem Beispiel verwendeten JSON-Eigenschaften finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md) unter „Pipeline-JSON“.
   >
   >
1. Überprüfen Sie Folgendes:

   a. Die Datei **input.log** ist im Ordner **inputdata** des Containers **adfgetstarted** im Blobspeicher enthalten.

   b. Die Datei **partitionweblogs.hql** ist im Ordner **script** des Containers **adfgetstarted** im Blobspeicher enthalten. Führen Sie die Schritte im Abschnitt mit den Voraussetzungen in der [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) aus, wenn diese Dateien nicht vorhanden sind.

   c. Sie haben im JSON-Code der Pipeline **storageaccountname** durch den Namen Ihres Speicherkontos ersetzt.

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen. Da die Zeiten für **start** und **end** in der Vergangenheit festgelegt sind und **isPaused** auf **false** festgelegt ist, wird die Pipeline (Aktivität in der Pipeline) sofort nach der Bereitstellung ausgeführt.

1. Vergewissern Sie sich, dass die Pipeline in der Strukturansicht angezeigt wird.

    ![Strukturansicht mit Pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Überwachen einer Pipeline
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Überwachen einer Pipeline mithilfe der Diagrammansicht
1. Klicken Sie auf dem Blatt **Data Factory** auf **Diagramm**.

    ![Kachel "Diagramm"](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. In der **Diagrammansicht** sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.

    ![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Um alle Aktivitäten in der Pipeline anzuzeigen, klicken Sie im Diagramm mit der rechten Maustaste auf die Pipeline und klicken dann auf **Pipeline öffnen**.

    ![Menü "Pipeline öffnen"](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Vergewissern Sie sich, dass **Hive-Aktivität** in der Pipeline angezeigt wird.

    ![Ansicht „Pipeline öffnen“](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Um zur vorherigen Ansicht zurückzukehren, klicken Sie oben im Menü auf **Data Factory**.

1. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobInput**. Vergewissern Sie sich, dass sich der Slice im Zustand **Bereit** befindet. Es kann ein paar Minuten dauern, bis für den Slice **Bereit** angezeigt wird. Wenn dies nicht geschieht, nachdem Sie einige Zeit gewartet haben, sollten Sie prüfen, ob Sie die Eingabedatei (**input.log**) im richtigen Container (**adfgetstarted**) und Ordner (**inputdata**) platziert haben.

   ![Eingabeslice im Status „Bereit“](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Schließen Sie das Blatt **AzureBlobInput**.

1. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

   ![Aktuelle Datasetverarbeitung](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. Nach Abschluss der Verarbeitung wird für den Slice der Zustand **Bereit** angezeigt.

   ![DataSet im Zustand „Bereit“](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel etwa 20 Minuten. Es ist damit zu rechnen, dass die Pipeline etwa 30 Minuten zum Verarbeiten des Slice benötigt.
   >
   >

1. Sobald der Slice den Status **Bereit** hat, überprüfen Sie, ob sich die Ausgabedaten in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.  

   ![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Wählen Sie den Slice aus, um weitere Informationen dazu auf dem Blatt **Datenslice** anzuzeigen.

    ![Informationen zum Datenslice](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Wählen Sie in der Liste **Aktivitätsausführung** eine Aktivitätsausführung aus, um weitere Informationen dazu anzuzeigen. (In diesem Szenario wird eine Hive-Aktivität verwendet.) Die Informationen werden auf dem Blatt **Details zur Aktivitätsausführung** angezeigt.   

    ![Fenster„Details zur Aktivitätsausführung“](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   In den Protokolldateien werden die ausgeführte Hive-Abfrage und Statusinformationen angezeigt. Diese Protokolle sind zur Behandlung von Problemen hilfreich.
   Weitere Informationen finden Sie im Artikel [Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Die Eingabedatei wird bei erfolgreicher Verarbeitung des Slice gelöscht. Wenn Sie den Slice erneut ausführen oder das Tutorial nochmals durchgehen möchten, laden Sie die Eingabedatei (**input.log**) daher in den Ordner **inputdata** des Containers **adfgetstarted** hoch.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Überwachen einer Pipeline mithilfe der App „Überwachung und Verwaltung“
Sie können die App „Überwachung und Verwaltung“ auch zum Überwachen Ihrer Pipelines verwenden. Weitere Informationen zur Verwendung dieser App finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md).

1. Klicken Sie auf der Startseite Ihrer Data Factory auf die Kachel **Überwachung und Verwaltung**.

    ![Kachel „Überwachung und Verwaltung“](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. Legen Sie in der App „Überwachung und Verwaltung“ die **Startzeit** und **Endzeit** auf die entsprechenden Werte Ihrer Pipeline fest. Wählen Sie **Übernehmen**.

    ![App „Überwachung und Verwaltung“](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Wählen Sie in der Liste **Aktivitätsfenster** ein Aktivitätsfenster aus, um Informationen dazu anzuzeigen.

    ![Liste „Aktivitätsfenster“](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie eine Data Factory zum Verarbeiten von Daten erstellt, indem Sie ein Hive-Skript in einem HDInsight Hadoop-Cluster ausgeführt haben. Sie haben den Data Factory-Editor im Azure-Portal verwendet, um die folgenden Schritte auszuführen:  

* Erstellen einer Data Factory.
* Erstellen zwei verknüpfter Dienste:
   * Ein mit Storage verknüpfter Dienst zum Verknüpfen Ihres Blobspeichers, in dem die Eingabe- und Ausgabedateien der Data Factory enthalten sind
   * Ein bedarfsgesteuerter verknüpfter HDInsight-Dienst zum Verknüpfen eines bedarfsgesteuerten HDInsight Hadoop-Clusters mit der Data Factory Data Factory erstellt einen HDInsight Hadoop-Cluster „just in time“, um Eingabedaten zu verarbeiten und Ausgabedaten zu erzeugen.
* Erstellen von zwei Datasets, in denen Eingabe- und Ausgabedaten für eine HDInsight Hive-Aktivität in der Pipeline beschrieben werden
* Erstellen einer Pipeline mit einer HDInsight Hive-Aktivität

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Kopieren von Daten aus einem Blobspeicher in Azure SQL-Datenbank mithilfe einer Copy-Aktivität finden Sie unter [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Weitere Informationen
| Thema | BESCHREIBUNG |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) |Dieser Artikel enthält Informationen zu Datasets in Data Factory. |
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) |In diesem Artikel werden die Planungs- und Ausführungsaspekte des Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines mit der App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) |Dieser Artikel beschreibt das Überwachen, Verwalten und Debuggen von Pipelines mit der App „Überwachung und Verwaltung“. |