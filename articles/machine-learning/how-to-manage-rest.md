---
title: Verwenden von REST zum Verwalten von ML-Ressourcen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie REST-APIs zum Erstellen, Ausführen und Löschen von Azure Machine Learning-Ressourcen, z. B. einen Arbeitsbereich, oder zum Registrieren von Modellen verwenden.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 09a0580adbe6d51e4de811a57ee17203d65a2435
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316910"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Erstellen, Ausführen und Löschen von Azure ML-Ressourcen mithilfe von REST



Es gibt verschiedene Möglichkeiten zur Verwaltung Ihrer Azure ML-Ressourcen. Sie können das [-Portal](https://portal.azure.com/), die [Befehlszeilenschnittstelle (CLI)](/cli/azure/?preserve-view=true&view=azure-cli-latest) oder das [Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) verwenden. Sie können auch die REST-API verwenden. Die REST-API verwendet HTTP-Verben in einer Standardmethode zum Erstellen, Abrufen, Aktualisieren und Löschen von Ressourcen. Die REST-API funktioniert mit jeder Sprache oder jedem Tool, die bzw. das HTTP-Anforderungen ausführen kann. Durch die unkomplizierte Struktur von REST sind diese APIs häufig eine gute Wahl in Skriptumgebungen sowie für MLOps-Automatisierung. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Abrufen eines Autorisierungstokens
> * Erstellen einer ordnungsgemäß formatierten REST-Anforderung mithilfe der Dienstprinzipalauthentifizierung
> * Verwenden von GET-Anforderungen zum Abrufen von Informationen zu den hierarchischen Ressourcen von Azure ML
> * Verwenden von PUT- und POST-Anforderungen zum Erstellen und Ändern von Ressourcen
> * Verwenden von DELETE-Anforderungen zum Bereinigen von Ressourcen 
> * Verwenden schlüsselbasierter Autorisierung zum Bewerten von bereitgestellten Modellen

## <a name="prerequisites"></a>Voraussetzungen

- Ein **Azure-Abonnement** , für das Sie über Administratorrechte verfügen. Wenn Sie nicht über ein solches Abonnement verfügen, testen Sie das [kostenlose oder kostenpflichtige persönliche Abonnement](https://aka.ms/AMLFree).
- Ein [Azure Machine Learning-Arbeitsbereich](./how-to-manage-workspace.md).
- Administrative REST-Anforderungen verwenden Dienstprinzipalauthentifizierung. Führen Sie die Schritte in [Einrichten von Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](./how-to-setup-authentication.md#service-principal-authentication) zum Erstellen eines Dienstprinzipals in Ihrem Arbeitsbereich aus.
- Das **curl** -Hilfsprogramm. Das **curl** -Programm ist im [Windows-Subsystem für Linux](/windows/wsl/install-win10) oder jeder beliebigen UNIX-Distribution verfügbar. In PowerShell ist **curl** ein Alias für **Invoke-WebRequest** , und `curl -d "key=val" -X POST uri` wird zu `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Abrufen eines Tokens für die Dienstprinzipalauthentifizierung

Administrative REST-Anforderungen werden mit einem impliziten OAuth2-Flow authentifiziert. Dieser Authentifizierungsflow verwendet ein Token, das vom Dienstprinzipal Ihres Abonnements bereitgestellt wird. Um dieses Token abzurufen, benötigen Sie Folgendes:

- Ihre Mandanten-ID (identifiziert die Organisation, zu der Ihr Abonnement gehört)
- Ihre Client-ID (die dem erstellten Token zugeordnet wird)
- Ihr geheimer Clientschlüssel (den Sie schützen sollten)

Sie sollten diese Werte von der Antwort auf die Erstellung Ihres Dienstprinzipals erhalten. Das Abrufen dieser Werte wird in [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](./how-to-setup-authentication.md#service-principal-authentication) erläutert. Wenn Sie das Abonnement Ihres Unternehmens verwenden, verfügen Sie möglicherweise nicht über die Berechtigung zum Erstellen eines Dienstprinzipals. In diesem Fall sollten Sie ein [kostenloses oder kostenpflichtiges persönliches Abonnement](https://aka.ms/AMLFree) verwenden.

So rufen Sie ein Token ab:

1. Öffnen Sie ein Terminalfenster.
1. Geben Sie den folgenden Code in der Befehlszeile ein:
1. Ersetzen Sie `{your-tenant-id}`, `{your-client-id}`, und `{your-client-secret}` durch Ihre eigenen Werte. In diesem Artikel sind Zeichenfolgen, die von geschweiften Klammern umgeben sind, Variablen, die Sie durch Ihre entsprechenden eigenen Werte ersetzen müssen.
1. Führen Sie den folgenden Befehl aus:

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Die Antwort sollte ein Zugriffstoken bereitstellen, das eine Stunde lang gültig ist:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Notieren Sie sich das Token, da Sie es verwenden, um alle nachfolgenden administrativen Anforderungen zu authentifizieren. Hierzu legen Sie einen Autorisierungsheader in allen Anforderungen fest:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Beachten Sie, dass der Wert mit der Zeichenfolge „Bearer“ beginnt, einschließlich eines einzelnen Leerzeichens, bevor Sie das Token hinzufügen.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Abrufen einer Liste aller Ressourcengruppen, die Ihrem Abonnement zugeordnet sind

Um eine Liste aller Ressourcengruppen abzurufen, die Ihrem Abonnement zugeordnet sind, führen Sie Folgendes aus:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

In Azure werden viele REST-APIs veröffentlicht. Jeder Dienstanbieter aktualisiert seine API in seinem eigenen Rhythmus, ohne jedoch vorhandene Programme funktionsunfähig zu machen. Der Dienstanbieter verwendet das `api-version`-Argument, um Kompatibilität zu gewährleisten. Das `api-version`-Argument variiert von Dienst zu Dienst. Für Machine Learning Service ist die aktuelle API-Version beispielsweise `2019-11-01`. Für Speicherkonten ist dies `2019-06-01`. Für Schlüsseltresore ist dies `2019-09-01`. Alle REST-Aufrufe sollten das `api-version`-Argument auf den erwarteten Wert festlegen. Sie können sich auf die Syntax und Semantik der angegebenen Version verlassen, auch wenn die API weiterentwickelt wird. Wenn Sie eine Anforderung ohne das `api-version`-Argument an einen Anbieter senden, enthält die Antwort eine von Menschen lesbare Liste unterstützter Werte. 

Der Aufruf oben führt zu einer komprimierten JSON-Antwort der folgenden Form: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Drilldown in Arbeitsbereiche und deren Ressourcen

Um den Satz von Arbeitsbereichen in einer Ressourcengruppe abzurufen, führen Sie Folgendes aus, und ersetzen dabei `{your-subscription-id}`, `{your-resource-group}` und `{your-access-token}`: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Sie erhalten erneut eine JSON-Liste, die dieses Mal eine Liste enthält, in der jedes Element einen Arbeitsbereich beschreibt:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Zum Arbeiten mit Ressourcen in einem Arbeitsbereich wechseln Sie vom allgemeinen **management.azure.com** -Server zu einem REST-API-Server, der für den Speicherort des Arbeitsbereichs spezifisch ist. Beachten Sie den Wert des `discoveryUrl`-Schlüssels in der JSON-Antwort oben. Wenn Sie diese URL mit GET abrufen, erhalten Sie eine Antwort ähnlich der folgenden:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Der Wert der `api`-Antwort ist die URL des Servers, den Sie für zusätzliche Anforderungen verwenden. Zum Auflisten von Experimenten senden Sie beispielsweise den folgenden Befehl. Ersetzen Sie `regional-api-server` durch den Wert der `api`-Antwort (z. B. `centralus.api.azureml.ms`). Ersetzen Sie außerdem `your-subscription-id`, `your-resource-group`, `your-workspace-name` und `your-access-token` wie üblich:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Zum Abrufen registrierter Modelle in Ihrem Arbeitsbereich senden Sie auf ähnliche Weise Folgendes:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Beachten Sie, dass der Pfad zum Auflisten von Experimenten mit `history/v1.0` beginnt, während der Pfad mit `modelmanagement/v1.0` zum Auflisten von Modellen beginnt. Die REST-API ist in verschiedene Betriebsgruppen unterteilt, die jeweils über einen eindeutigen Pfad verfügen. 

|Bereich|`Path`|
|-|-|
|Artifacts|/rest/api/azureml|
|Datenspeicher|/azure/machine-learning/how-to-access-data|
|Hyperparameteroptimierung|hyperdrive/v1.0/|
|Modelle|modelmanagement/v1.0/|
|Ausführungsverlauf|execution/v1.0/ and history/v1.0/|

Sie können die REST-API unter Verwendung des allgemeinen Musters untersuchen:

|URL-Komponente|Beispiel|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/{your-subscription-id}/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Erstellen und Ändern von Ressourcen mit PUT- und POST-Anforderungen

Zusätzlich zum Abrufen von Ressourcen mit dem GET-Verb unterstützt die REST-API die Erstellung aller Ressourcen, die zum Trainieren, Bereitstellen und Überwachen von ML-Lösungen erforderlich sind. 

Zum Trainieren und Ausführen von ML-Modellen sind Computeressourcen erforderlich. Sie können die Computeressourcen eines Arbeitsbereichs folgendermaßen auflisten: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Um eine benannte Computeressource zu erstellen oder zu überschreiben, verwenden Sie eine PUT-Anforderung. Ersetzen Sie im Folgenden neben den nun vertrauten Ersetzungen von `your-subscription-id`, `your-resource-group`, `your-workspace-name` und `your-access-token` auch `your-compute-name` sowie die Werte für `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName` und `adminUserPassword`. Der folgende Befehl erstellt eine dedizierte Standard_D1 mit einem Knoten (eine grundlegende CPU-Computeressource), die nach 30 Minuten herunterskaliert wird, wie in der Referenz unter [Machine Learning Compute: Erstellen oder Aktualisieren des SDK-Verweises](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate) angegeben:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> In Windows-Terminals müssen Sie die doppelten Anführungszeichen beim Senden von JSON-Daten möglicherweise mit Escapezeichen versehen. Das heißt, Text wie `"location"` wird zu `\"location\"`. 

Eine erfolgreiche Anforderung erhält eine `201 Created`-Antwort, aber beachten Sie, dass diese Antwort einfach nur bedeutet, dass der Bereitstellungsvorgang begonnen hat. Sie müssen eine Abfrage (oder das Portal) verwenden, um den erfolgreichen Abschluss zu bestätigen.

### <a name="create-an-experimental-run"></a>Erstellen einer experimentellen Ausführung

Um einen Testlauf innerhalb eines Experiments zu starten, benötigen Sie einen ZIP-Ordner mit Ihrem Trainingsskript und zugehörige Dateien sowie eine JSON-Ausführungsdefinitionsdatei. Der ZIP-Ordner muss über die Datei mit den Python-Einträgen in seinem Stammverzeichnis verfügen. Zippen Sie als Beispiel ein triviales Python-Programm wie das folgende in einen Ordner namens **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Speichern Sie diesen nächsten Codeausschnitt als **definition.json**. Vergewissern Sie sich, dass der Wert von „Script“ mit dem Namen der soeben gezippten Python-Datei übereinstimmt. Vergewissern Sie sich, dass der Wert von „Target“ mit dem Namen einer verfügbaren Computeressource übereinstimmt. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Stellen Sie diese Dateien mit `multipart/form-data`-Inhalt auf dem Server bereit:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Eine erfolgreiche POST-Anforderung generiert den Status `200 OK` mit einem Antworttext, der den Bezeichner der erstellten Ausführung enthält:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Sie können eine Ausführung mit dem REST-ful-Muster überwachen, das Ihnen nun vertraut sein sollte:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Löschen von Ressourcen, die nicht mehr benötigt werden

Einige, aber nicht alle Ressourcen unterstützen das DELETE-Verb. Überprüfen Sie die [API-Referenz](/rest/api/azureml/), bevor Sie für Löschanwendungsfälle die REST-API verwenden. Zum Löschen eines Modells können Sie beispielsweise Folgendes verwenden:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Verwenden von REST zum Bewerten eines bereitgestellten Modells

Obwohl es möglich ist, ein Modell so bereitzustellen, dass es mit einem Dienstprinzipal authentifiziert wird, verwenden die meisten Bereitstellungen, die mit einem Client zusammenarbeiten, schlüsselbasierte Authentifizierung. Den entsprechenden Schlüssel finden Sie auf der Seite der Bereitstellung auf der Registerkarte **Endpunkte** von Studio. Am gleichen Speicherort wird der Bewertungs-URI Ihres Endpunkts angezeigt. Die Eingaben des Modells müssen als JSON-Array mit dem Namen `data` modelliert werden:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Erstellen eines Arbeitsbereichs mithilfe von REST 

Jeder Azure ML-Arbeitsbereich weist eine Abhängigkeit von vier anderen Azure-Ressourcen auf: von einer Containerregistrierung mit aktivierter Verwaltung, einem Schlüsseltresor, einer Application Insights-Ressource und einem Speicherkonto. Sie können erst einen Arbeitsbereich erstellen, wenn diese Ressourcen vorhanden sind. Weitere Informationen zum Erstellen dieser Ressourcen finden Sie in der REST-API-Referenz.

Um einen Arbeitsbereich zu erstellen, senden Sie einen ähnlichen Befehl wie den folgenden mit PUT an `management.azure.com`. Auch wenn dieser Aufruf erfordert, dass Sie eine große Anzahl von Variablen festlegen, ist er strukturell identisch mit anderen Aufrufen, die in diesem Artikel erläutert wurden. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Sie sollten eine `202 Accepted`-Antwort erhalten und in den zurückgegebenen Headern einen `Location`-URI. Sie können diesen URI für Informationen zur Bereitstellung mit GET abrufen, einschließlich hilfreicher Debuginformationen, wenn ein Problem mit einer ihrer abhängigen Ressourcen vorliegt (wenn Sie beispielsweise vergessen haben, Administratorzugriff für Ihre Containerregistrierung zu aktivieren). 

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Verschieben des Arbeitsbereichs

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

### <a name="deleting-the-azure-container-registry"></a>Löschen der Azure Container Registry

Der Azure Machine Learning-Arbeitsbereich verwendet für einige Operationen die Azure Container Registry (ACR). Es wird automatisch eine ACR-Instanz erstellt, wenn erstmals eine erforderlich ist.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erkunden der vollständigen [AzureML REST-API-Referenz](/rest/api/azureml/).
- Lernen Sie, wie Sie den Designer zum [Prognostizieren von Automobilpreisen mit dem Designer](./tutorial-designer-automobile-price-train-score.md) verwenden.
- Erkunden von [Azure Machine Learning mit Jupyter-Notebooks](..//machine-learning/samples-notebooks.md).