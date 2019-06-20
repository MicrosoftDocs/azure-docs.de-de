---
title: Zugreifen auf Daten in Datenspeichern/Blobs für das Training
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mithilfe von Datenspeichern während des Trainings mit dem Azure Machine Learning Service auf den Blobdatenspeicher zugreifen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 93fc9a4e9e44bd7e8db3d49fe390ebe273c45ce9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239043"
---
# <a name="access-data-from-your-datastores"></a>Zugreifen auf Daten aus Ihren Datenspeichern

 Im Azure Machine Learning Service sind Datenspeicher vom Computingstandort unabhängige Mechanismen für den Speicherzugriff, ohne dass Änderungen am Quellcode erforderlich sind. Ob Sie Trainingscode schreiben, um einen Pfad als Parameter zu nutzen, oder einem Estimator direkt einen Datenspeicher bereitstellen, Azure Machine Learning-Workflows stellen sicher, dass auf Ihre Datenspeicherorte zugegriffen werden kann und sie Ihrem Computekontext zur Verfügung gestellt werden.

In dieser Vorgehensweise finden Sie Beispiele für die folgenden Aufgaben:
* [Auswählen eines Datenspeichers](#access)
* [Abrufen von Daten](#get)
* [Hochladen und herunterladen von Daten in Datenspeicher](#up-and-down)
* [Zugreifen auf Datenspeicher während des Trainings](#train)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie einen Datenspeicher verwenden möchten, benötigen Sie zunächst einen [Arbeitsbereich](concept-workspace.md).

Sie können entweder einen [neuen Arbeitsbereich erstellen](setup-create-workspace.md#sdk) oder einen vorhandenen Arbeitsbereich abrufen:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Auswählen eines Datenspeichers

Sie können einen Standarddatenspeicher oder Ihren eigenen verwenden.

### <a name="use-the-default-datastore-in-your-workspace"></a>Verwenden des Standarddatenspeichers in Ihrem Arbeitsbereich

 Jeder Arbeitsbereich verfügt über einen registrierten Standarddatenspeicher, den Sie sofort verwenden können.

So rufen Sie den Standarddatenspeicher des Arbeitsbereichs ab:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrieren Ihres eigenen Datenspeichers im Arbeitsbereich

Wenn Sie bereits über eine Azure Storage-Instanz verfügen, können Sie diesen Speicher als Datenspeicher in Ihrem Arbeitsbereich registrieren.   Alle Registriermethoden befinden sich in der [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)-Klasse und weisen das Format „register_azure_*“ auf. 

In den folgenden Beispielen wird gezeigt, wie Sie einen Azure-Blobcontainer oder eine Azure-Dateifreigabe als Datenspeicher registrieren können.

+ Verwenden Sie [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) für einen **Azure-Blobcontainer-Datenspeicher**.

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Verwenden Sie [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) für einen **Azure-Dateifreigabe-Datenspeicher**. Beispiel: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Suchen und definieren von Datenspeichern

Verwenden Sie [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-), um einen bestimmten Datenspeicher im aktuellen Arbeitsbereich zu registrieren:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Verwenden Sie diesen Code, um eine Liste aller Datenspeicher in einem bestimmten Arbeitsbereich anzuzeigen:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Verwenden Sie [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-), um einen anderen Standarddatenspeicher für den aktuellen Arbeitsbereich zu definieren:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Hoch- und herunterladen von Daten
Die in den folgenden Beispielen beschriebenen [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)- und [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)-Methoden sind für die [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)- und [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)-Klassen bestimmt und werden identisch ausgeführt.

### <a name="upload"></a>Hochladen

 Laden Sie entweder ein Verzeichnis oder einzelne Dateien mit dem Python SDK in den Datenspeicher hoch.

So laden Sie ein Verzeichnis in einen Datenspeicher `ds` hoch:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` gibt den Speicherort in der Dateifreigabe (oder im Blobcontainer) für den Upload an. Standardmäßig lautet er `None`. In diesem Fall werden die Daten in den Stamm hochgeladen. `overwrite=True` überschreibt alle vorhandenen Daten in `target_path`.

Alternativ können Sie eine Liste einzelner Dateien mithilfe der `upload_files()`-Methode des Datenspeichers in den Datenspeicher hochladen.

### <a name="download"></a>Download
Ebenso lassen sich Daten aus einem Datenspeicher in Ihr lokales Dateisystem herunterladen.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` ist der Speicherort des lokalen Verzeichnisses, in das die Daten heruntergeladen werden sollen. Um einen Pfad zu dem Ordner in der Dateifreigabe (oder im Blobcontainer) für den Download anzugeben, geben Sie diesen Pfad mit `prefix` an. Wenn `prefix` gleich `None` ist, werden alle Inhalte Ihrer Dateifreigabe (oder Ihres Blobcontainers) heruntergeladen.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Zugreifen auf Datenspeicher während des Trainings

Wenn Sie Ihren Datenspeicher auf dem Training-Computeziel bereitgestellt haben, können Sie während des Trainings auf diesen zugreifen (z.B. für Trainings- oder Überprüfungsdaten), indem Sie einfach den Pfad als Parameter in Ihrem Trainingsskript übergeben.

Die folgende Tabelle enthält die [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)-Methoden, die dem Computeziel mitteilen, wie der Datenspeicher während der Ausführungen verwendet wird.

Weg|Methode|BESCHREIBUNG|
----|-----|--------
Einbinden| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Wird zum Einbinden des Datenspeichers auf dem Computeziel verwendet.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Wird zum Herunterladen der Inhalte Ihres Datenspeichers in den unter `path_on_compute` angegebenen Speicherort verwendet. <br> Für den Trainingsausführungskontext findet dieser Download vor der Ausführung statt.
Hochladen|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Wird zum Hochladen einer Datei aus dem von `path_on_compute` angegebenen Speicherort in Ihren Datenspeicher verwendet. <br> Für den Trainingsausführungskontext findet dieser Upload nach der Ausführung statt.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Um auf bestimmte Ordner oder Dateien in Ihrem Datenspeicher zu verweisen und sie im Computeziel verfügbar zu machen, verwenden Sie die Datenspeicherfunktion [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-).

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Jedes `ds`- oder `ds.path`-Objekt wird in den Namen einer Umgebungsvariablen des Formats `"$AZUREML_DATAREFERENCE_XXXX"` aufgelöst, deren Wert für den Einbindungs-/Downloadpfad im Zielcompute steht. Der Datenspeicherpfad auf dem Zielcompute stimmt möglicherweise nicht mit dem Ausführungspfad für das Trainingsskript überein.

### <a name="training-compute-and-datastore-matrix"></a>Training-Compute- und Datenspeichermatrix

Die folgende Matrix zeigt die verfügbaren Datenzugriffsfunktionen für die verschiedenen Training-Computeziele- und Datenspeicherszenarien. Weitere Informationen zu den [Training-Computezielen für Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Azure Machine Learning Compute |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;Pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;Pipelines](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtuelle Computer               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Datenübertragung                  |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                               |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)            |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                              |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                               |–                                           |–         |–                                                                         |
| Azure DataLake Analytics       |–                                           |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Es gibt möglicherweise Szenarien, in denen hoch iterativ, umfangreiche Datenverarbeitungsprozesse schneller mit [`as_download()`] anstelle von [`as_mount()`] ausgeführt werden; dies kann im Experiment überprüft werden.

### <a name="examples"></a>Beispiele 

Die folgenden Codebeispiele beziehen sich speziell auf die [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)-Klasse für den Zugriff auf Ihren Datenspeicher während des Trainings.

Dieser Code erstellt einen Estimator mit dem Trainingsskript `train.py`, aus dem angegebenen Quellverzeichnis, mit den in `script_params` definierten Parametern, alle auf dem angegebenen Training-Computeziel.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Sie können auch eine Liste von Datenspeichern an den Parameter `inputs` des Estimatorkonstruktors übergeben, um Einbindungs- oder Kopiervorgänge in oder aus Ihrem/n Datenspeicher(n) auszuführen. Dieses Codebeispiel:
* Lädt alle Inhalte im `ds1`-Datenspeicher auf das Computeziel herunter, bevor das Trainingsskript `train.py` ausgeführt wird.
* Lädt den Ordner `'./foo'` im `ds2`-Datenspeicher auf das Computeziel herunter, bevor `train.py` ausgeführt wird.
* Lädt die Datei `'./bar.pkl'` aus dem Computeziel in den Datenspeicher `ds3` hoch, nachdem Ihr Skript ausgeführt wurde.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren eines Modells](how-to-train-ml-models.md)

* [Bereitstellen eines Modells](how-to-deploy-and-where.md)
