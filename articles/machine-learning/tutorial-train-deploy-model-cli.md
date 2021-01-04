---
title: Trainieren und Bereitstellen von Modellen über die Befehlszeilenschnittstelle
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Machine Learning-Erweiterung für die Azure-Befehlszeilenschnittstelle verwenden können, um ein Modell über die Befehlszeile zu trainieren, zu registrieren und bereitzustellen.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 119f1eaa838b404a3dbdc22f692f559816810dde
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578687"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: Trainieren und Bereitstellen eines Modells über die Befehlszeilenschnittstelle


In diesem Tutorial verwenden Sie die Machine Learning-Erweiterung für die Azure-Befehlszeilenschnittstelle, um ein Modell zu trainieren, zu registrieren und bereitzustellen.

Die Python-Trainingsskripts in diesem Tutorial verwenden [scikit-learn](https://scikit-learn.org/), um ein einfaches Modell zu trainieren. Der Schwerpunkt dieses Tutorials liegt nicht auf den Skripts oder dem Modell, sondern auf der Verwendung der Befehlszeilenschnittstelle für die Arbeit mit Azure Machine Learning.

Erfahren Sie, wie Sie die folgenden Maßnahmen durchführen:

> [!div class="checklist"]
> * Installieren der Erweiterung für maschinelles Lernen
> * Erstellen eines Azure Machine Learning-Arbeitsbereichs
> * Erstellen der zum Trainieren des Modells verwendeten Computeressource
> * Definieren und Registrieren des Datasets zum Trainieren des Modells
> * Starten einer Trainingsausführung
> * Registrieren und Herunterladen eines Modells
> * Bereitstellen des Modells als Webdienst
> * Bewerten von Daten mithilfe des Webdiensts

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

    Wenn Sie die [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) verwenden, befindet sich die CLI in der Cloud, und der Zugriff erfolgt über den Browser.

## <a name="download-the-example-project"></a>Herunterladen des Beispielprojekts

Laden Sie für dieses Tutorial das Projekt [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) herunter. In den Schritten in diesem Tutorial werden die Dateien im Verzeichnis `examples/cli-train-deploy` verwendet.

Um eine lokale Kopie der Dateien zu erhalten, laden Sie entweder ein [ZIP-Archiv herunter](https://github.com/microsoft/MLOps/archive/master.zip), oder verwenden Sie den folgenden Git-Befehl, um das Repository zu klonen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Trainingsdateien

Das Verzeichnis `examples/cli-train-deploy` des Projekts enthält die folgenden Dateien, die für das Training eines Modells verwendet werden:

* `.azureml\mnist.runconfig`: Eine __Laufzeitkonfigurationsdatei__. Diese Datei definiert die Laufzeitumgebung, die zum Trainieren des Modells erforderlich ist. In diesem Beispiel werden auch die Daten zum Trainieren des Modells in der Trainingsumgebung eingebunden.
* `scripts\train.py`: Das Trainingsskript. Mit dieser Datei wird das Modell trainiert.
* `scripts\utils.py`: Eine vom Trainingsskript verwendete Hilfsdatei.
* `.azureml\conda_dependencies.yml`: Definiert die Softwareabhängigkeiten, die für die Ausführung des Trainingsskripts erforderlich sind.
* `dataset.json`: Die Datasetdefinition. Wird verwendet, um das MNIST-Dataset im Azure Machine Learning-Arbeitsbereich zu registrieren.

### <a name="deployment-files"></a>Bereitstellungsdateien

Das Repository enthält die folgenden Dateien, mit denen das trainierte Modell als Webdienst bereitgestellt wird:

* `aciDeploymentConfig.yml`: Eine Datei für die __Bereitstellungskonfiguration__. Diese Datei definiert die für das Modell erforderliche Hostingumgebung.
* `inferenceConfig.json`: Eine Datei für die __Rückschlusskonfiguration__. Diese Datei definiert die Softwareumgebung, die vom Dienst verwendet wird, um Daten mit dem Modell zu bewerten.
* `score.py`: Ein Python-Skript, das eingehende Daten akzeptiert, sie mit dem Modell bewertet und dann eine Antwort zurückgibt.
* `scoring-env.yml`: Die Conda-Abhängigkeiten, die für die Ausführung des Modells und des `score.py`-Skripts erforderlich sind.
* `testdata.json`: Eine Datendatei, die zum Testen des bereitgestellten Webdiensts verwendet werden kann.

## <a name="connect-to-your-azure-subscription"></a>Verbinden mit Ihrem Azure-Abonnement

Ihnen stehen mehrere Möglichkeiten zur Verfügung, sich über die CLI bei Ihrem Azure-Abonnement zu authentifizieren. Am einfachsten ist die interaktive Authentifizierung mithilfe eines Browsers. Öffnen Sie zur interaktiven Authentifizierung eine Befehlszeile oder ein Terminal, und verwenden Sie den folgenden Befehl:

```azurecli-interactive
az login
```

Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie einen Browser öffnen und die Anweisungen in der Befehlszeile befolgen. Die Anweisungen umfassen das Navigieren zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin) und Eingeben eines Autorisierungscodes.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>Installieren der Erweiterung für maschinelles Lernen

Um die Erweiterung für maschinelles Lernen zu installieren, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Wenn Sie die Meldung erhalten, dass die Erweiterung bereits installiert ist, verwenden Sie den folgenden Befehl, um auf die neueste Version zu aktualisieren:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein Container mit Ressourcen auf der Azure-Plattform. Wenn Sie mit Azure Machine Learning arbeiten, enthält die Ressourcengruppe Ihren Azure Machine Learning-Arbeitsbereich. Sie enthält auch andere Azure-Dienste, die vom Arbeitsbereich verwendet werden. Wenn Sie Ihr Modell z. B. mit einer cloudbasierten Computeressource trainieren, wird diese Ressource in der Ressourcengruppe erstellt.

Um __eine neue Ressourcengruppe zu erstellen__, verwenden Sie den folgenden Befehl. Ersetzen Sie `<resource-group-name>` durch den Namen, der für diese Ressourcengruppe verwendet werden soll. Ersetzen Sie `<location>` durch die Azure-Region, die für diese Ressourcengruppe verwendet werden soll:

> [!TIP]
> Wählen Sie eine Region aus, in der Azure Machine Learning verfügbar ist. Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Die Antwort dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Weitere Informationen zum Arbeiten mit Ressourcengruppen finden Sie unter [az group](/cli/azure/group?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Verwenden Sie den folgenden Befehl, um einen neuen Arbeitsbereich zu erstellen. Ersetzen Sie `<workspace-name>` durch den Namen, den Sie für diesen Arbeitsbereich verwenden möchten. Ersetzen Sie `<resource-group-name>` durch den Namen der Ressourcengruppe:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Verbinden eines lokalen Projekts mit dem Arbeitsbereich

Verwenden Sie über ein Terminal oder eine Eingabeaufforderung die folgenden Befehle, um Verzeichnisse in das Verzeichnis `cli-train-deploy` zu ändern und sich dann mit Ihrem Arbeitsbereich zu verbinden:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Dieser Befehl erstellt eine `.azureml/config.json`-Datei, die Informationen enthält, die für die Verbindung mit Ihrem Arbeitsbereich erforderlich sind. Der Rest der in diesem Tutorial verwendeten `az ml`-Befehle verwendet diese Datei, sodass Sie den Arbeitsbereich und die Ressourcengruppe nicht zu allen Befehlen hinzufügen müssen.

## <a name="create-the-compute-target-for-training"></a>Erstellen des Computeziels für das Training

In diesem Beispiel wird ein Azure Machine Learning Compute-Cluster verwendet, um das Modell zu trainieren. Verwenden Sie den folgenden Befehl, um einen neuen Cluster zu erstellen:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Dieser Befehl erstellt ein neues Computeziel namens `cpu-cluster` mit maximal vier Knoten. Die gewählte VM-Größe stellt einer VM eine GPU-Ressource zur Verfügung. Informationen zur VM-Größe finden Sie unter [VM-Typen und -Größen].

> [!IMPORTANT]
> Der Name des Computeziels (in diesem Fall `cpu-cluster`) ist wichtig, da die im nächsten Abschnitt verwendete `.azureml/mnist.runconfig`-Datei auf ihn verweist.

## <a name="define-the-dataset"></a>Definieren des Datasets

Sie können die Trainingsdaten zum Trainieren eines Modells mithilfe eines Datasets bereitstellen. Zum Erstellen eines Datasets über die Befehlszeilenschnittstelle müssen Sie eine Dataset-Definitionsdatei bereitstellen. Die im Repository bereitgestellte Datei `dataset.json` erstellt mithilfe der MNIST-Daten ein neues Dataset. Das erstellte Dataset trägt den Namen `mnist-dataset`.

Verwenden Sie den folgenden Befehl, um das Dataset mithilfe der Datei `dataset.json` zu registrieren:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Kopieren Sie den Wert des Eintrags `id`, da er im nächsten Abschnitt verwendet wird.

Eine umfassendere Vorlage für ein Dataset erhalten Sie mit dem folgenden Befehl:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Verweisen auf das Dataset

Um das Dataset in der Trainingsumgebung verfügbar zu machen, müssen Sie in der Datei „runconfig“ darauf verweisen. Die Datei `.azureml/mnist.runconfig` enthält die folgenden YAML-Einträge:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Ändern Sie den Wert des Eintrags `id` so, dass er mit dem beim Registrieren des Datasets zurückgegebenen Wert übereinstimmt. Mit diesem Wert werden die Daten während des Trainings in das Computeziel geladen.

Dieser YAML-Code führt zu den folgenden Aktionen während des Trainings:

* Einbinden des Datasets (basierend auf der ID des Datasets) in der Trainingsumgebung und Speichern des Pfads zum Bereitstellungspunkt in der Umgebungsvariable `mnist`
* Übergeben des Speicherorts der Daten (Bereitstellungspunkt) innerhalb der Trainingsumgebung an das Skript mithilfe des Arguments `--data-folder`.

Die Datei „runconfig“ enthält außerdem Informationen zum Konfigurieren der für die Trainingsausführung verwendeten Umgebung. Wenn Sie diese Datei untersuchen, werden Sie feststellen, dass sie auf das zuvor erstellte `cpu-compute`-Computeziel verweist. Sie listet auch die Anzahl der Knoten auf, die beim Training verwendet werden sollen (`"nodeCount": "4"`), und enthält einen Abschnitt `"condaDependencies"`, der die Python-Pakete auflistet, die für die Ausführung des Trainingsskripts erforderlich sind.

> [!TIP]
> Die Datei „runconfig“ kann manuell erstellt werden, die in diesem Beispiel wurde jedoch mithilfe der im Repository enthaltenen Datei `generate-runconfig.py` erstellt. Diese Datei erhält einen Verweis auf das registrierte Dataset, erstellt programmgesteuert eine Ausführungskonfiguration und speichert diese dann in der Datei.

Weitere Informationen zu Dateien für Laufzeitkonfigurationen finden Sie unter [Verwenden von Computezielen für das Modelltraining](how-to-set-up-training-targets.md#whats-a-run-configuration). Eine vollständige JSON-Referenz finden Sie in [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Übermitteln der Trainingsausführung

Verwenden Sie den folgenden Befehl, um eine Trainingsausführung auf dem Computeziel `cpu-cluster` zu starten:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Dieser Befehl gibt einen Namen für das Experiment an (`myexperiment`). Das Experiment speichert Informationen zu dieser Ausführung im Arbeitsbereich.

Der Parameter `-c mnist` gibt die Datei `.azureml/mnist.runconfig` an.

Der Parameter `-t` speichert einen Verweis auf diese Ausführung in einer JSON-Datei und wird in den nächsten Schritten zur Registrierung und zum Herunterladen des Modells verwendet.

Während der Trainingsausführung werden Informationen aus der Trainingssitzung auf die Remotecomputeressource gestreamt. Ein Teil der Informationen ähnelt dem folgenden Text:

```output
Predict the test set
Accuracy is 0.9185
```

Dieser Text wird aus dem Trainingsskript protokolliert und zeigt die Genauigkeit des Modells an. Andere Modelle weisen andere Leistungsmetriken auf.

Wenn Sie das Trainingsskript untersuchen, sehen Sie, dass es beim Speichern der trainierten Modelle in `outputs/sklearn_mnist_model.pkl` auch den Alphawert verwendet.

Das Modell wurde im Verzeichnis `./outputs` auf dem Computeziel gespeichert, auf dem es trainiert wurde. In diesem Fall ist dies die Azure Machine Learning Compute-Instanz in der Azure-Cloud. Der Trainingsprozess lädt automatisch die Inhalte des Verzeichnisses `./outputs` aus dem Computeziel, in dem das Training erfolgt, in Ihren Azure Machine Learning-Arbeitsbereich hoch. Es wird als Teil des Experiments gespeichert (`myexperiment` in diesem Beispiel).

## <a name="register-the-model"></a>Registrieren des Modells

Um das Modell direkt aus der gespeicherten Version in Ihrem Experiment zu registrieren, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Dieser Befehl registriert die von der Trainingsausführung erstellte Datei `outputs/sklearn_mnist_model.pkl` als neue Modellregistrierung namens `mymodel`. Der `--assets-path` verweist auf einen Pfad in einem Experiment. In diesem Fall werden die Experiment- und Ausführungsinformationen aus der Datei `runoutput.json` geladen, die durch den Trainingsbefehl erstellt wurde. Die `-t registeredmodel.json` erstellt eine JSON-Datei, die auf das mit diesem Befehl erstellte neue registrierte Modell verweist und von anderen CLI-Befehlen verwendet wird, die mit registrierten Modellen arbeiten.

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Versionsverwaltung der Modelle

Beachten Sie die Versionsnummer, die für das Modell zurückgegeben wird. Die Version wird jedes Mal inkrementiert, wenn Sie ein neues Modell mit diesem Namen registrieren. Sie können z. B. das Modell herunterladen und über eine lokale Datei registrieren, indem Sie die folgenden Befehle ausführen:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Der erste Befehl lädt das registrierte Modell in das aktuelle Verzeichnis herunter. Der Dateiname lautet `sklearn_mnist_model.pkl`, d. h. es handelt sich um die Datei, auf die bei der Registrierung des Modells verwiesen wurde. Der zweite Befehl registriert das lokale Modell (`-p "sklearn_mnist_model.pkl"`) mit demselben Namen wie die vorherige Registrierung (`mymodel`). Dieses Mal listen die zurückgegebenen JSON-Daten die Version als 2 auf.

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Wenn Sie ein Modell bereitstellen möchten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Möglicherweise erhalten Sie die Warnung „Fehler bei der Überprüfung der Existenz von LocalWebservice“ oder „Fehler beim Erstellen des Docker-Clients“. Sie können dies ruhig ignorieren, da Sie keinen lokalen Webdienst bereitstellen.

Dieser Befehl stellt einen neuen Dienst namens `myservice` bereit, der die Version 1 des Modells verwendet, das Sie zuvor registriert haben.

Die Datei `inferenceConfig.yml` enthält Informationen zur Verwendung des Modells für Rückschlüsse. Sie verweist z. B. auf das Einstiegsskript (`score.py`) und die Softwareabhängigkeiten.

Weitere Informationen zur Struktur dieser Datei finden Sie unter [Rückschlusskonfigurationsschema](reference-azure-machine-learning-cli.md#inference-configuration-schema). Weitere Informationen zu Eingabeskripts finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

Die `aciDeploymentConfig.yml` beschreibt die zum Hosten des Dienstes verwendete Bereitstellungsumgebung. Die Bereitstellungskonfiguration hängt vom Computetyp ab, den Sie für die Bereitstellung verwenden. In diesem Fall wird eine Azure Container Instance verwendet. Weitere Informationen finden Sie unter [Bereitstellungskonfigurationsschema](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Es wird einige Minuten dauern, bis der Bereitstellungsprozess abgeschlossen ist.

> [!TIP]
> In diesem Beispiel wird Azure Container Instances verwendet. Bereitstellungen für Azure Container Instances erstellen automatisch die erforderliche Azure Container Instances-Ressource. Wenn Sie stattdessen für den Azure Kubernetes Service bereitstellen möchten, müssen Sie vorab einen AKS-Cluster erstellen und ihn als Teil des `az ml model deploy`-Befehls angeben. Ein Beispiel für die Bereitstellung in AKS finden Sie unter [Bereitstellen eines Modells in einem Azure Kubernetes Service-Cluster](how-to-deploy-azure-kubernetes-service.md).

Nach einigen Minuten werden Informationen ähnlich dem folgenden JSON-Code zurückgegeben:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Bewertungs-URI

Der von der Bereitstellung zurückgegebene `scoringUri` ist der REST-Endpunkt für ein als Webdienst bereitgestelltes Modell. Sie können diesen URI auch mit dem folgenden Befehl abrufen:

```azurecli-interactive
az ml service show -n myservice
```

Dieser Befehl gibt dasselbe JSON-Dokument zurück, einschließlich des `scoringUri`.

Der REST-Endpunkt kann verwendet werden, um Daten an den Dienst zu senden. Informationen zum Erstellen einer Clientanwendung, die Daten an den Dienst sendet, finden Sie unter [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](how-to-consume-web-service.md).

### <a name="send-data-to-the-service"></a>Senden von Daten an den Dienst

Obwohl Sie eine Clientanwendung erstellen können, um den Endpunkt aufzurufen, bietet die Machine Learning-CLI ein Hilfsprogramm, das als Testclient fungieren kann. Verwenden Sie den folgenden Befehl, um Daten in der Datei `testdata.json` an den Dienst zu senden:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Verwenden Sie alternativ den folgenden Befehl, wenn Sie PowerShell nutzen:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Die Antwort des Befehls ähnelt `[ 3 ]`.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

> [!IMPORTANT]
> Die von Ihnen erstellten Ressourcen können ggf. auch in anderen Azure Machine Learning-Tutorials und -Anleitungen verwendet werden.

### <a name="delete-deployed-service"></a>Löschen bereitgestellter Dienste

Wenn Sie planen, den Azure Machine Learning-Arbeitsbereich weiterhin zu nutzen, aber den bereitgestellten Dienst zur Senkung der Kosten entfernen möchten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml service delete -n myservice
```

Dieser Befehl gibt ein JSON-Dokument zurück, das den Namen des gelöschten Dienstes enthält. Es kann einige Minuten dauern, bis der Dienst gelöscht wird.

### <a name="delete-the-training-compute"></a>Löschen des Computeziels für das Training

Wenn Sie planen, den Azure Machine Learning-Arbeitsbereich weiterhin zu nutzen, aber das für das Training erstellte `cpu-cluster`-Computeziel entfernen möchten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Dieser Befehl gibt ein JSON-Dokument zurück, das die ID des gelöschten Computeziels enthält. Es kann einige Minuten dauern, bis das Computeziel gelöscht wird.

### <a name="delete-everything"></a>Alles löschen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit Ihnen keine Zusatzkosten entstehen.

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle in diesem Dokument erstellten Azure-Ressourcen zu löschen. Ersetzen Sie `<resource-group-name>` durch den Namen der Ressourcengruppe, die Sie zuvor erstellt haben:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Azure Machine Learning-Tutorial haben Sie die Machine Learning-CLI für die folgenden Aufgaben verwendet:

> [!div class="checklist"]
> * Installieren der Erweiterung für maschinelles Lernen
> * Erstellen eines Azure Machine Learning-Arbeitsbereichs
> * Erstellen der zum Trainieren des Modells verwendeten Computeressource
> * Definieren und Registrieren des Datasets zum Trainieren des Modells
> * Starten einer Trainingsausführung
> * Registrieren und Herunterladen eines Modells
> * Bereitstellen des Modells als Webdienst
> * Bewerten von Daten mithilfe des Webdiensts

Weitere Informationen zum Verwenden der Befehlszeilenschnittstelle (CLI) finden Sie unter [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).