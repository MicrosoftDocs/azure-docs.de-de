---
title: 'Schnellstart: Erstellen einer Azure Data Factory mithilfe von Python'
description: Mithilfe einer Data Factory-Instanz können Sie Daten von einem Speicherort in einem Azure Blob-Speicher an einen anderen Speicherort kopieren.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: cc25ce4aa51535bbfd03d99ed413afa66a184fdb
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508780"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Schnellstart: Erstellen einer Data Factory und Pipeline mithilfe von Python

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Schnellstart erstellen Sie eine Data Factory mit Python. Die Pipeline in dieser Data Factory kopiert Daten aus einem Ordner in einen anderen Ordner in Azure Blob Storage.

Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows erstellen können, um die Datenverschiebung und Datentransformation zu orchestrieren und zu automatisieren. Mit Azure Data Factory können Sie datengesteuerte Workflows – sogenannte Pipelines – erstellen und planen.

Pipelines können Daten aus unterschiedlichen Datenspeichern erfassen. Pipelines verarbeiten oder transformieren Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning. Pipelines dienen zum Veröffentlichen von Ausgabedaten für Datenspeicher wie Azure Synapse Analytics für BI-Anwendungen (Business Intelligence).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4 oder höher](https://www.python.org/downloads/)

* [Azure Storage-Konto](../storage/common/storage-account-create.md)

* [Azure Storage Explorer](https://storageexplorer.com/) (optional)

* [Eine Anwendung in Azure Active Directory.](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) Notieren Sie sich die folgenden Werte, die Sie in späteren Schritten benötigen: **Anwendungs-ID**, **Authentifizierungsschlüssel** und **Mandanten-ID**. Weisen Sie die Anwendung der Rolle **Mitwirkender** zu, indem Sie die Anweisungen im gleichen Artikel befolgen.

## <a name="create-and-upload-an-input-file"></a>Erstellen und Hochladen einer Eingabedatei

1. Starten Sie den Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn als **input.txt**-Datei auf Ihrem Datenträger.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Verwenden Sie Tools wie [Azure Storage-Explorer](https://storageexplorer.com/), um den Container **adfv2tutorial** und den Ordner **input.txt** im Container zu erstellen. Laden Sie anschließend die Datei **input.txt** in den Ordner **input** hoch.

## <a name="install-the-python-package"></a>Installieren des Python-Pakets

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung mit Administratorberechtigungen. 
2. Installieren Sie zunächst das Python-Paket für Azure-Verwaltungsressourcen:

    ```python
    pip install azure-mgmt-resource
    ```
3. Um das Python-Paket für Data Factory zu installieren, führen Sie folgenden Befehl aus:

    ```python
    pip install azure-mgmt-datafactory
    ```

    Das [Python SDK für Data Factory](https://github.com/Azure/azure-sdk-for-python) unterstützt Python 2.7, 3.3, 3.4, 3.5, 3.6 und 3.7.

## <a name="create-a-data-factory-client"></a>Erstellen eines Data Factory-Clients

1. Erstellen Sie eine Datei mit dem Namen **datafactory.py**. Fügen Sie die folgenden Anweisungen ein, um Verweise auf Namespaces hinzuzufügen.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Fügen Sie die folgenden Funktionen hinzu, die Informationen ausgeben.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine Instanz der DataFactoryManagementClient-Klasse erstellt. Sie verwenden dieses Objekt, um die Data Factory, einen verknüpften Dienst, Datasets und eine Pipeline zu erstellen. Sie verwenden dieses Objekt ebenfalls zum Überwachen der Ausführungsdetails der Pipeline. Legen Sie die Variable **subscription_id** auf die ID Ihres Azure-Abonnements fest. Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}
    ```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Data Factory** erstellt. Wenn die Ressourcengruppe bereits vorhanden ist, kommentieren Sie die erste `create_or_update`-Anweisung aus.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Erstellen eines verknüpften Diensts

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der einen **verknüpften Azure Storage-Dienst** erstellt.

Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In dieser Schnellstartanleitung müssen Sie nur einen verknüpften Azure Storage-Dienst für die Quell- und Senkendatenspeicher für den Kopiervorgang erstellen – in diesem Beispiel als „AzureStorageLinkedService“ bezeichnet. Ersetzen Sie `<storageaccountname>` und `<storageaccountkey>` durch den Namen und Schlüssel Ihres Azure-Speicherkontos.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Abschnitt erstellen Sie zwei Datasets: eines für die Quelle und das andere für die Senke.

### <a name="create-a-dataset-for-source-azure-blob"></a>Erstellen eines Datasets für das Azure-Quellblob

Fügen Sie der Main-Methode den folgenden Code hinzu, der ein Azure-Blobdataset erstellt. Weitere Informationen zu den Eigenschaften eines Azure-Blobdatasets finden Sie im Artikel [Azure-Blobconnector](connector-azure-blob-storage.md#dataset-properties).

Sie definieren ein Dataset, das die Quelldaten im Azure-Blob darstellt. Dieses Blobdataset verweist auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellt haben.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(linked_service_name=ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Erstellen eines Datasets für eine Azure Blobsenke

Fügen Sie der Main-Methode den folgenden Code hinzu, der ein Azure-Blobdataset erstellt. Weitere Informationen zu den Eigenschaften eines Azure-Blobdatasets finden Sie im Artikel [Azure-Blobconnector](connector-azure-blob-storage.md#dataset-properties).

Sie definieren ein Dataset, das die Quelldaten im Azure-Blob darstellt. Dieses Blobdataset verweist auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellt haben.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Pipeline mit einer Kopieraktivität** erstellt.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der **eine Pipelineausführung auslöst**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Überwachen einer Pipelineausführung

Fügen Sie der **Main**-Methode den folgenden Code hinzu, um die Pipelineausführung zu überwachen:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Fügen Sie nun die folgende Anweisung hinzu, um die **main**-Methode bei Ausführung des Programms aufzurufen:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Vollständiges Skript

Hier sehen Sie den vollständigen Python-Code:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time


def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")


def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")


def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(
            activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(
            activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(
            activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(
        client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location': 'eastus'}
    df_params = {'location': 'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString(
        value='DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(
        connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(
        rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob = AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Ausführen des Codes

Erstellen und starten Sie die Anwendung, und überprüfen Sie dann die Pipelineausführung.

Die Konsole druckt den Status der Erstellung der Data Factory, des verknüpften Diensts, der Datasets, der Pipeline und der Pipelineausführung aus. Warten Sie, bis Sie die Ausführungsdetails der Kopieraktivität mit der Größe der gelesenen/geschriebenen Daten sehen. Verwenden Sie dann Tools wie z.B. [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), um zu überprüfen, ob die Blobs wie von Ihnen in den Variablen angegeben von „inputBlobPath“ nach „outputBlobPath“ kopiert werden.

Hier ist die Beispielausgabe:

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Data Factory zu löschen, fügen Sie den folgenden Code zum Programm hinzu:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Nächste Schritte

Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können.
