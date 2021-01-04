---
title: Herstellen einer Verbindung mit Speicherdiensten in Azure
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe von Datenspeichern während des Trainings mit Azure Machine Learning eine sichere Verbindung zu Azure Storage-Diensten herstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: bb63ac6de6c48bb3853bd235d908ee745ff5279d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032846"
---
# <a name="connect-to-storage-services-on-azure"></a>Herstellen einer Verbindung mit Speicherdiensten in Azure

In diesem Artikel erfahren Sie, wie Sie **über Azure Machine Learning-Datenspeicher eine Verbindung mit Speicherdiensten in Azure herstellen**. Datenspeicher stellen eine sichere Verbindung mit Ihrem Azure-Speicherdienst her, ohne Ihre Anmeldeinformationen für die Authentifizierung und die Integrität Ihrer ursprünglichen Datenquelle zu gefährden. Darin werden Verbindungsinformationen wie Ihre Abonnement-ID und die Tokenautorisierung in Ihrer mit dem Arbeitsbereich verknüpften [Key Vault](https://azure.microsoft.com/services/key-vault/)-Instanz gespeichert, damit Sie sicher auf Ihren Speicher zuzugreifen können, ohne diese Informationen in Ihren Skripts hartcodieren zu müssen. Sie können Datenspeicher mit dem [Azure Machine Learning-Python-SDK](#python) oder mit [Azure Machine Learning Studio](how-to-connect-data-ui.md) erstellen und registrieren.

Wenn Sie Datenspeicher mithilfe der VS Code-Erweiterung für Azure Machine Learning erstellen und verwalten möchten, finden Sie weitere Informationen in der [Schrittanleitung zur Ressourcenverwaltung für VS Code](how-to-manage-resources-vscode.md#datastores).

Aus diesen [Azure-Speicherlösungen](#matrix) können Sie Datenspeicher erstellen. **Für nicht unterstützte Speicherlösungen** sowie zur Einsparung von Kosten für ausgehende Daten bei ML-Experimenten sollten Sie [Ihre Daten in eine unterstützte Azure-Speicherlösung verschieben](#move).  

Informationen dazu, welche Rolle Datenspeicher im Workflow für den Datenzugriff in Azure Machine Learning spielen, finden Sie im Artikel [Datenzugriff in Azure Machine Learning](concept-data.md#data-workflow).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes:
- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

- Ein Azure Storage-Konto mit einem [unterstützten Speichertyp](#matrix).

- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), oder greifen Sie auf [Azure Machine Learning-Studio](https://ml.azure.com/) zu.

- Ein Azure Machine Learning-Arbeitsbereich.
  
  Sie können wahlweise [einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-workspace.md) oder mithilfe des Python SDKs einen vorhandenen verwenden. 

    Importieren Sie die Klasse `Workspace` und `Datastore`, und laden Sie Ihre Abonnementinformationen mit der Funktion `from_config()` aus der Datei `config.json`. Diese Funktion durchsucht standardmäßig das aktuelle Verzeichnis nach der JSON-Datei. Sie können jedoch auch einen Pfadparameter angeben, um mit `from_config(path="your/file/path")` auf die Datei zu verweisen.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Wenn Sie einen Arbeitsbereich erstellen, werden automatisch ein Azure-Blobcontainer und eine Azure-Dateifreigabe als Datenspeicher im Arbeitsbereich registriert. Sie erhalten die Namen `workspaceblobstore` und `workspacefilestore`. Der `workspaceblobstore` dient zum Speichern von Arbeitsbereichsartefakten und Ihren Protokollen zu Experimenten mit maschinellem Lernen. Er wird außerdem als **Standarddatenspeicher** festgelegt und kann nicht aus dem Arbeitsbereich gelöscht werden. Der `workspacefilestore` dient zum Speichern von Notebooks und R-Skripts, die über [Computeinstanzen](./concept-compute-instance.md#accessing-files) autorisiert werden.
    
    > [!NOTE]
    > Azure Machine Learning-Designer erstellt automatisch einen Datenspeicher namens **azureml_globaldatasets**, wenn Sie ein Beispiel auf der Designerstartseite öffnen. Dieser Datenspeicher enthält nur Beispieldatasets. Verwenden Sie diesen Datenspeicher **nicht** für den Zugriff auf vertrauliche Daten.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Unterstützte Diensttypen für Datenspeicher

Datenspeicher unterstützen derzeit das Speichern von Verbindungsinformationen in den Speicherdiensten, die in der folgenden Matrix aufgeführt sind.

| Speichertyp&nbsp; | Authentifizierungstyp&nbsp; | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python-SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp;-REST-API](/rest/api/azureml/) | VS-Code
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;Storage](../storage/blobs/storage-blobs-overview.md)| Kontoschlüssel <br> SAS-Token | ✓ | ✓ | ✓ |✓ |✓
[Azure-Dateifreigabe&nbsp;&nbsp;](../storage/files/storage-files-introduction.md)| Kontoschlüssel <br> SAS-Token | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](../data-lake-store/index.yml)| Dienstprinzipal| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](../storage/blobs/data-lake-storage-introduction.md)| Dienstprinzipal| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)&nbsp;| SQL-Authentifizierung <br>Dienstprinzipal| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](../postgresql/overview.md) | SQL-Authentifizierung| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](../mysql/overview.md) | SQL-Authentifizierung|  | ✓* | ✓* |✓*|
[Databricks-Dateisystem](/azure/databricks/data/databricks-file-system)&nbsp;&nbsp;| Keine Authentifizierung | | ✓** | ✓ ** |✓** |

\* MySQL wird nur für die Pipeline [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py) unterstützt.<br />
\*\* Databricks wird nur für die Pipeline [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py) unterstützt.


### <a name="storage-guidance"></a>Leitfaden für Speicher

Es wird empfohlen, einen Datenspeicher für einen [Azure-Blobcontainer](../storage/blobs/storage-blobs-introduction.md) zu erstellen. Für Blobs stehen sowohl der Standard- als auch der Premiumspeicher zur Verfügung. Der Premiumspeicher ist zwar teurer, ermöglicht aber höhere Durchsätze. Dies kann sich vor allem bei einem Training mit einem großen Dataset positiv auf die Ausführungsgeschwindigkeit auswirken. Informationen zu den Kosten für Speicherkonten finden Sie unter [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) baut auf Azure Blob Storage auf und ist für Big Data-Analysen in Unternehmen ausgelegt. Ein wesentlicher Bestandteil von Data Lake Storage Gen2 ist das Hinzufügen eines [hierarchischen Namespace](../storage/blobs/data-lake-storage-namespace.md) zum Blobspeicher. Der hierarchische Namespace organisiert Objekte/Dateien in einer Hierarchie von Verzeichnissen für den effizienten Datenzugriff.

## <a name="storage-access-and-permissions"></a>Speicherzugriff und Berechtigungen

Um sicherzustellen, dass eine sichere Verbindung mit Ihrem Azure-Speicherdienst hergestellt wird, erfordert Azure Machine Learning, dass Sie über die Berechtigung zum Zugreifen auf den entsprechenden Datenspeichercontainer verfügen. Dieser Zugriff ist von den Anmeldeinformationen für die Authentifizierung abhängig, die zum Registrieren des Datenspeichers verwendet werden. 

### <a name="virtual-network"></a>Virtuelles Netzwerk 

Wenn sich Ihr Datenspeicherkonto in einem **virtuellen Netzwerk** befindet, sind zusätzliche Konfigurationsschritte erforderlich, um sicherzustellen, dass Azure Machine Learning auf Ihre Daten zugreifen kann. Stellen Sie wie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md) beschrieben sicher, dass die erforderlichen Konfigurationsschritte ausgeführt werden, wenn Sie Ihren Datenspeicher erstellen und registrieren.  

### <a name="access-validation"></a>Zugriffsüberprüfung

**Im Rahmen des ersten Erstellungs- und Registrierungsvorgangs des Datenspeichers** überprüft Azure Machine Learning automatisch, ob der zugrunde liegende Speicherdienst vorhanden ist und der vom Benutzer bereitgestellte Prinzipal (Benutzername, Dienstprinzipal oder SAS-Token) Zugriff auf den angegebenen Speicher hat.

**Nach dem Erstellen des Datenspeichers** wird diese Überprüfung nur noch für Methoden ausgeführt, die Zugriff auf den zugrunde liegenden Speichercontainer benötigen, und **nicht** bei jedem Abruf von Datenspeicherobjekten. Beispielsweise erfolgt eine Überprüfung, wenn Sie Dateien aus Ihrem Datenspeicher herunterladen möchten. Wenn Sie jedoch nur den Standarddatenspeicher ändern möchten, findet keine Überprüfung statt.

Um Ihren Zugriff auf den zugrunde liegenden Speicherdienst zu authentifizieren, können Sie Ihren Kontoschlüssel, Ihr SAS-Token (Shared Access Signature) oder Ihren Dienstprinzipal in der entsprechenden `register_azure_*()`-Methode des zu erstellenden Datenspeichertyps angeben. In der [Speichertypmatrix](#matrix) werden die unterstützten Authentifizierungstypen aufgeführt, die den einzelnen Datenspeichertypen entsprechen.

Informationen zu Kontoschlüssel, SAS-Token und Dienstprinzipal finden Sie im [Azure-Portal](https://portal.azure.com).

* Wenn Sie beabsichtigen, einen Kontoschlüssel oder ein SAS-Token für die Authentifizierung zu verwenden, wählen Sie im linken Bereich **Speicherkonten** und dann das Speicherkonto aus, das Sie registrieren möchten. 
  * Die Seite **Übersicht** enthält Informationen wie den Kontonamen und den Namen des Containers oder der Dateifreigabe. 
      1. Wechseln Sie für Kontoschlüssel zu **Kontoschlüssel** im Bereich **Einstellungen**. 
      1. Für SAS-Token wechseln Sie zu **Shared Access Signatures** im Bereich **Einstellungen**.

* Wenn Sie einen Dienstprinzipal für die Authentifizierung verwenden möchten, navigieren Sie zu Ihren **App-Registrierungen**, und wählen Sie die gewünschte App aus. 
    * Auf der entsprechenden Übersichtsseite werden erforderliche Informationen wie Mandanten-ID und Client-ID angezeigt.

> [!IMPORTANT]
> * Wenn Sie Ihre Zugriffsschlüssel für ein Azure Storage-Konto (Kontoschlüssel oder SAS-Token) ändern müssen, stellen Sie sicher, dass die neuen Anmeldeinformationen mit Ihrem Arbeitsbereich und den damit verbundenen Datenspeichern synchronisiert werden. Eine Anleitung zum Synchronisieren Ihrer aktualisierten Anmeldeinformationen finden Sie [hier](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Berechtigungen

Stellen Sie für Azure-Blobcontainer und Azure Data Lake Gen2-Speicher sicher, dass Ihre Anmeldeinformationen für die Authentifizierung über den Zugriff **Storage-Blobdatenleser** verfügen. Erfahren Sie mehr über [Storage-Blobdatenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Ein Konto-SAS-Token besitzt standardmäßig keine Berechtigungen. 
* Für den **Lesezugriff** auf Daten müssen Ihre Anmeldeinformationen für die Authentifizierung mindestens die Berechtigungen zum Auflisten und Lesen für Container und Objekte besitzen. 

* Für den **Datenschreibzugriff** sind auch Berechtigungen zum Schreiben und Hinzufügen erforderlich.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Erstellen und Registrieren von Datenspeichern

Wenn Sie eine Azure-Speicherlösung als Datenspeicher registrieren, erstellen Sie diesen Datenspeicher automatisch und registrieren ihn in einem bestimmten Arbeitsbereich. Lesen Sie den Abschnitt [Speicherzugriff und Berechtigungen](#storage-access-and-permissions), um Informationen zu Szenarios für virtuelle Netzwerke zu erhalten und um zu erfahren, wo Sie die erforderlichen Anmeldeinformationen für die Authentifizierung finden. 

In diesem Abschnitt finden Sie Beispiele für das Erstellen und Registrieren eines Datenspeichers über das Python-SDK für die folgenden Speichertypen. Bei den in diesen Beispielen verwendeten Parametern handelt es sich um die **erforderlichen Parameter** zum Erstellen und Registrieren eines Datenspeichers.

* [Azure-Blobcontainer](#azure-blob-container)
* [Azure-Dateifreigabe](#azure-file-share)
* [Azure Data Lake Storage Gen2](#azure-data-lake-storage-generation-2)

 Informationen zum Erstellen von Datenspeichern für andere unterstützte Speicherdienste finden Sie in der [Referenzdokumentation für die entsprechenden `register_azure_*`-Methoden](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=truemethods).

Wenn Sie eine Umgebung mit weniger Code bevorzugen, finden Sie weitere Informationen unter [Verbinden mit Daten mit Azure Machine Learning Studio](how-to-connect-data-ui.md).
>[!IMPORTANT]
> Wenn Sie die Registrierung aufheben und einen Datenspeicher mit dem gleichen Namen erneut registrieren und dabei ein Fehler auftritt, ist bei dem Azure Key Vault für Ihren Arbeitsbereich vorläufiges Löschen möglicherweise nicht aktiviert. Standardmäßig ist vorläufiges Löschen für die Key Vault-Instanz aktiviert, die von Ihrem Arbeitsbereich erstellt wurde. Es ist jedoch möglicherweise nicht aktiviert, wenn Sie einen vorhandenen Schlüsseltresor verwendet haben oder einen Arbeitsbereich vor Oktober 2020 erstellt haben. Informationen zum Aktivieren des vorläufigen Löschens finden Sie unter [Aktivieren des vorläufigen Löschens für einen vorhandenen Schlüsseltresor]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

> [!NOTE]
> Der Datenspeichername darf nur Kleinbuchstaben, Ziffern und Unterstriche enthalten. 

### <a name="azure-blob-container"></a>Azure-Blobcontainer

Um einen Azure-Blobcontainer als Datenspeicher zu registrieren, verwenden Sie [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Der folgende Code erstellt den Datenspeicher `blob_datastore_name` und registriert ihn im Arbeitsbereich `ws`. Dieser Datenspeicher greift auf den `my-container-name`-Blobcontainer im `my-account-name`-Speicherkonto zu und verwendet dazu den angegebenen Zugriffsschlüssel des Kontos. Lesen Sie den Abschnitt [Speicherzugriff und Berechtigungen](#storage-access-and-permissions), um Informationen zu Szenarios für virtuelle Netzwerke zu erhalten und um zu erfahren, wo Sie die erforderlichen Anmeldeinformationen für die Authentifizierung finden. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure-Dateifreigabe

Um einen eine Azure-Dateifreigabe als Datenspeicher zu registrieren, verwenden Sie [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Der folgende Code erstellt den Datenspeicher `file_datastore_name` und registriert ihn im Arbeitsbereich `ws`. Dieser Datenspeicher greift auf die `my-fileshare-name`-Dateifreigabe im `my-account-name`-Speicherkonto zu und verwendet dazu den angegebenen Zugriffsschlüssel des Kontos. Lesen Sie den Abschnitt [Speicherzugriff und Berechtigungen](#storage-access-and-permissions), um Informationen zu Szenarios für virtuelle Netzwerke zu erhalten und um zu erfahren, wo Sie die erforderlichen Anmeldeinformationen für die Authentifizierung finden. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Für einen Datenspeicher von Azure Data Lake Storage Generation 2 (ADLS Gen 2) verwenden Sie [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-), um einen Datenspeicher für Anmeldeinformationen zu registrieren, der mit einem Azure DataLake Gen 2-Speicher mit [Dienstprinzipalberechtigungen](../active-directory/develop/howto-create-service-principal-portal.md) verbunden ist.  

Damit Sie Ihren Dienstprinzipal nutzen können, müssen Sie [Ihre Anwendung registrieren](../active-directory/develop/app-objects-and-service-principals.md) und dem Dienstprinzipal Datenzugriff über die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) oder Zugriffssteuerungslisten (ACL) gewähren. Erfahren Sie mehr über die [für ADLS Gen 2 eingerichtete Zugriffssteuerung](../storage/blobs/data-lake-storage-access-control-model.md). 

Der folgende Code erstellt den Datenspeicher `adlsgen2_datastore_name` und registriert ihn im Arbeitsbereich `ws`. Dieser Datenspeicher greift auf das Dateisystem `test` im `account_name`-Speicherkonto zu und verwendet dazu die bereitgestellten Anmeldeinformationen des Dienstprinzipals. Lesen Sie den Abschnitt [Speicherzugriff und Berechtigungen](#storage-access-and-permissions), um Informationen zu Szenarios für virtuelle Netzwerke zu erhalten und um zu erfahren, wo Sie die erforderlichen Anmeldeinformationen für die Authentifizierung finden. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>Erstellen von Datenspeichern mithilfe von Azure Resource Manager

Es gibt eine Reihe von Vorlagen unter [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/), die zum Erstellen von Datenspeichern verwendet werden können.

Informationen zur Verwendung dieser Vorlagen finden Sie unter [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md).

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Verwenden von Daten in Ihren Datenspeichern

Nachdem Sie einen Datenspeicher erstellt haben, [erstellen Sie ein Azure Machine Learning-Dataset](how-to-create-register-datasets.md), um mit Ihren Daten zu interagieren. Datasets packen Ihre Daten in ein selten ausgewertetes Objekt für Aufgaben des maschinellen Lernens wie das Training. Sie bieten auch die Möglichkeit, Dateien beliebiger Formate von Azure-Speicherdiensten wie Azure Blob Storage und ADLS Gen2 [herunterzuladen oder einzubinden](how-to-train-with-datasets.md#mount-vs-download). Sie können sie auch verwenden, um Tabellendaten in einen Pandas- oder Spark-Datenrahmen zu laden.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Abrufen von Datenspeichern aus Ihrem Arbeitsbereich

Verwenden Sie die statische Methode [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--datastore-name-) mit der `Datastore`-Klasse, um einen bestimmten Datenspeicher im aktuellen Arbeitsbereich zu registrieren:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Um die Liste der in einem bestimmten Arbeitsbereich registrierten Datenspeicher abzurufen, können Sie die [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedatastores)-Eigenschaft für ein Arbeitsbereichsobjekt verwenden:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Über diese Zeile rufen Sie den Standarddatenspeicher des Arbeitsbereichs ab:

```Python
datastore = ws.get_default_datastore()
```
Mithilfe des folgenden Codes können Sie den Standarddatenspeicher ändern. Diese Option wird nur über das SDK unterstützt. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Zugreifen auf Daten während der Bewertung

Azure Machine Learning bietet mehrere Möglichkeiten, Ihre Modelle zur Bewertung zu verwenden. Einige dieser Methoden bieten keinen Zugriff auf Datenspeicher. Verwenden Sie die folgende Tabelle, um zu verstehen, welche Methoden Ihnen ermöglichen, während der Bewertung auf Datenspeicher zuzugreifen:

| Methode | Datenspeicherzugriff | BESCHREIBUNG |
| ----- | :-----: | ----- |
| [Batchvorhersage](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Treffen Sie asynchron Vorhersagen für große Datenmengen. |
| [Webdienst](how-to-deploy-and-where.md) | &nbsp; | Stellen Sie Modelle als Webdienste bereit. |
| [Azure IoT Edge-Modul](how-to-deploy-and-where.md) | &nbsp; | Stellen Sie Modelle auf IoT Edge-Geräten bereit. |

Für Situationen, in denen das SDK keinen Zugriff auf Datenspeicher bietet, können Sie möglicherweise benutzerdefinierten Code mit dem entsprechenden Azure-SDK erstellen, um auf die Daten zuzugreifen. Das [Azure Storage SDK für Python](https://github.com/Azure/azure-storage-python) ist beispielsweise eine Clientbibliothek, mit der Sie auf in Blobs oder Dateien gespeicherte Daten zugreifen können.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Verschieben von Daten in unterstützte Azure Storage-Lösungen

Azure Machine Learning unterstützt den Zugriff auf Daten aus Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, einer Azure SQL-Datenbank und Azure Database for PostgreSQL. Wenn Sie einen nicht unterstützten Speicher verwenden, wird empfohlen, Ihre Daten mithilfe von [Azure Data Factory und diesen Schritten](../data-factory/quickstart-create-data-factory-copy-data-tool.md) in unterstützte Azure-Speicherlösungen zu verschieben. Durch das Verschieben von Daten auf einen unterstützten Speicher können Sie bei Machine Learning-Experimenten Kosten für die Datenausgabe sparen. 

Azure Data Factory bietet eine effiziente und robuste Datenübertragung mit mehr als 80 vorkonfigurierten Connectors ohne zusätzliche Kosten. Diese Connectors umfassen Azure-Datendienste, lokale Datenquellen, Amazon S3 und Redshift sowie Google BigQuery.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure Machine Learning-Datasets](how-to-create-register-datasets.md)
* [Trainieren eines Modells](how-to-set-up-training-targets.md)
* [Bereitstellen eines Modells](how-to-deploy-and-where.md)