---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 8f7798e684a0a144cfe5285a0dd926a3b440934a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557333"
---
Die Einträge im `inferenceconfig.json`-Dokument werden den Parametern für die [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)-Klasse zugeordnet. In der folgenden Tabelle wird die Zuordnung zwischen den Entitäten im JSON-Dokument und den Parametern für die Methode beschrieben:

| JSON-Entität | Methodenparameter | BESCHREIBUNG |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pfad zu einer lokalen Datei, die den Code zum Ausführen für das Image enthält. |
| `sourceDirectory` | `source_directory` | Optional. Pfad zu Ordnern, die alle Dateien zum Erstellen des Bilds enthalten, wodurch der Zugriff auf Dateien in diesem Ordner oder Unterordner vereinfacht wird. Sie können einen vollständigen Ordner von Ihrem lokalen Computer als Abhängigkeiten für den Webdienst hochladen. Hinweis: Ihre „entry_script“-, „conda_file“- und „extra_docker_file_steps“-Pfad sind relative Pfade zum „source_directory“-Pfad. |
| `environment` | `environment` | Optional.  Die [Umgebung](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) für Azure Machine Learning.|

Sie können die vollständigen Spezifikationen einer Azure Machine Learning-[Umgebung](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) in die Rückschlusskonfigurationsdatei aufnehmen. Wenn diese Umgebung in Ihrem Arbeitsbereich nicht vorhanden ist, erstellt Azure Machine Learning sie. Andernfalls aktualisiert Azure Machine Learning die Umgebung bei Bedarf. Nachfolgend finden Sie ein JSON-Beispiel:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Sie können auch eine vorhandene Azure Machine Learning-[Umgebung](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) in getrennten CLI-Parametern verwenden und den Schlüssel „Environment“ aus der Rückschlusskonfigurationsdatei entfernen. Verwenden Sie „-e“ für den Umgebungsnamen und „--ev“ für die Umgebungsversion. Wenn Sie „--ev“ nicht angeben, wird die neueste Version verwendet. Hier sehen Sie ein Beispiel für eine Rückschlusskonfigurationsdatei:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Der folgende Befehl veranschaulicht, wie Sie ein Modell mithilfe der vorherigen Rückschlusskonfigurationsdatei (namens „myInferenceConfig.json“) bereitstellen. 

Außerdem wird die neueste Version einer vorhandenen Azure Machine Learning-[Umgebung](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (namens „AzureML-Minimal“) verwendet.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```