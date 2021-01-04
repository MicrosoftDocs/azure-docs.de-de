---
title: Bereitstellen eines Machine Learning-Modells in Azure Functions mit Azure Cache for Redis
description: In diesem Artikel stellen Sie ein Modell aus Azure Machine Learning als Funktions-App in Azure Functions mithilfe einer Azure Cache for Redis-Instanz bereit. Azure Cache for Redis ist extrem leistungsfähig und skalierbar – gepaart mit einem Azure Machine Learning-Modell erzielen Sie niedrige Latenzzeiten und hohen Durchsatz in Ihrer Anwendung.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9731455edf0afbe4c0768ae40a51316ac71ad94
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537574"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Bereitstellen eines Machine Learning-Modells in Azure Functions mit Azure Cache for Redis 

In diesem Artikel stellen Sie ein Modell aus Azure Machine Learning als Funktions-App in Azure Functions mithilfe einer Azure Cache for Redis-Instanz bereit.  

Azure Cache for Redis ist extrem leistungsfähig und skalierbar – gepaart mit einem Azure Machine Learning-Modell erzielen Sie niedrige Latenzzeiten und hohen Durchsatz in Ihrer Anwendung. Einige Szenarien, in denen ein Cache besonders vorteilhaft ist, beziehen sich auf den Rückschluss der Daten und die eigentlichen Modellrückschlussergebnisse. In allen diesen Szenarien werden die Metadaten oder Ergebnisse im Arbeitsspeicher gespeichert, was zu einer Leistungssteigerung führt. 

> [!NOTE]
> Azure Machine Learning und Azure Functions sind zwar allgemein verfügbar, die Funktion zum Bereitstellen eines Modells aus dem Machine Learning-Dienst für Functions befindet sich jedoch in der Vorschauphase.  
>

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie im Artikel [Erstellen eines Arbeitsbereichs](../machine-learning/how-to-manage-workspace.md).
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* Ein in Ihrem Arbeitsbereich registriertes trainiertes Machine Learning-Modell. Falls Sie kein Modell besitzen, können Sie anhand der Informationen unter [Tutorial: Trainieren von Bildklassifikationsmodellen mit MNIST-Daten und Scikit-learn mithilfe von Azure Machine Learning](../machine-learning/tutorial-train-models-with-aml.md) ein Modell trainieren und registrieren.

> [!IMPORTANT]
> Bei den in diesem Artikel verwendeten Codeausschnitten wird davon ausgegangen, dass Sie die folgenden Variablen festgelegt haben:
>
> * `ws`: Ihr Azure Machine Learning-Arbeitsbereich
> * `model`: das registrierte Modell, das bereitgestellt wird
> * `inference_config`: die Rückschlusskonfiguration für das Modell
>
> Weitere Informationen zum Festlegen dieser Variablen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Erstellen einer Azure Cache for Redis-Instanz 
Sie können ein Machine Learning-Modell für Azure Functions mit jeder Cache-Instanz des Typs „Basic“, „Standard“ oder „Premium“ bereitstellen. Führen Sie zum Erstellen einer Cache-Instanz die folgenden Schritte aus:  

1. Navigieren Sie zur Homepage des Azure-Portals, oder öffnen Sie das Randleistenmenü, und wählen Sie dann **Ressource erstellen** aus. 
   
1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Auswählen von „Azure Cache for Redis“.":::
   
1. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Cache.
   
   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net* . | 
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Preisstufe** | Öffnen Sie die Dropdownliste, und wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

1. Wählen Sie die Registerkarte **Netzwerk** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Netzwerk** .

1. Wählen Sie auf der Registerkarte **Netzwerk** Ihre Konnektivitätsmethode aus.

1. Wählen Sie unten auf der Seite die Registerkarte **Weiter: Erweitert** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Erweitert** .

1. Aktivieren Sie auf der Registerkarte **Erweitert** für eine Basic- oder Standard-Cache-Instanz die Aktivierungsoption, wenn Sie einen TLS-fremden Port aktivieren möchten.

1. Konfigurieren Sie auf der Registerkarte **Advanced** für eine Premium-Cache-Instanz die Einstellungen für einen TLS-fremden Port, Clustering und Datenpersistenz.

1. Wählen Sie die Registerkarte **Weiter: Tags** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Tags** (Weiter: Tags) aus.

1. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten. 

1. Klicken Sie auf **Überprüfen und erstellen** . Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit. 

## <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung

Vor der Bereitstellung müssen Sie die Elemente definieren, die zum Ausführen des Modells als Webdienst erforderlich sind. Die folgende Liste beschreibt die wesentlichen für eine Bereitstellung erforderlichen Elemente:

* Ein __Eingabeskript__ . Dieses Skript akzeptiert Anforderungen, bewertet die Anforderung mithilfe des Modells und gibt die Ergebnisse zurück.

    > [!IMPORTANT]
    > Das Eingabeskript ist spezifisch für Ihr Modell. Es muss das Format der Daten der eingehenden Anforderung, das Format der im Modell erwarteten Daten und das Format der an Clients zurückgegebenen Daten erkennen.
    >
    > Wenn die Anforderungsdaten in einem Format vorliegen, das in Ihrem Modell nicht verwendet werden kann, können sie im Skript in ein akzeptables Format umgewandelt werden. Außerdem kann die Antwort im Skript umgewandelt werden, bevor sie an den Client zurückgegeben wird.
    >
    > Beim Packen für Functions wird die Eingabe standardmäßig als Text behandelt. Wenn Sie die Rohbytes der Eingabe verarbeiten möchten (z. B. für Blob-Trigger), müssen Sie [AMLRequest zum Akzeptieren von Rohdaten](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data) verwenden.

Stellen Sie für die Funktion „run“ sicher, dass eine Verbindung mit einem Redis-Endpunkt hergestellt wird.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Weitere Informationen zum Einstiegsskript finden Sie unter [Definieren von Bewertungscode](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script).

* **Abhängigkeiten** , z. B. Hilfsprogramme oder Python/Conda-Pakete, die zum Ausführen des Eingabeskripts oder Modells erforderlich sind

Diese Entitäten werden in einer __Rückschlusskonfiguration__ gekapselt. Die Rückschlusskonfiguration verweist auf das Eingabeskript und andere Abhängigkeiten.

> [!IMPORTANT]
> Beim Erstellen einer Rückschlusskonfiguration zur Verwendung mit Azure Functions müssen Sie ein [Environment](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py)-Objekt verwenden. Beachten Sie, dass Sie beim Definieren einer benutzerdefinierten Umgebung azureml-defaults mit Version > = 1.0.45 als Pip-Abhängigkeit hinzufügen müssen. Dieses Paket enthält die erforderlichen Funktionen zum Hosten des Modells als Webdienst. Im folgenden Beispiel wird veranschaulicht, wie ein Environment-Objekt erstellt und mit einer Rückschlusskonfiguration verwendet wird:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](../machine-learning/how-to-use-environments.md).

Weitere Informationen zur Rückschlusskonfiguration finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Bei der Bereitstellung in Functions müssen Sie keine __Bereitstellungskonfiguration__ erstellen.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installieren des SDK-Vorschaupakets für Functions-Unterstützung

Zum Erstellen von Paketen für Azure Functions müssen Sie das SDK-Vorschaupaket installieren.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Erstellen des Images

Verwenden Sie zum Erstellen des in Azure Functions bereitgestellten Docker-Images [azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) oder die spezifische Paketfunktion für den Trigger, den Sie verwenden möchten. Der folgende Codeausschnitt veranschaulicht, wie ein neues Paket mit einem HTTP-Trigger aus der Modell- und Rückschlusskonfiguration erstellt wird:

> [!NOTE]
> Im Codeausschnitt wird davon ausgegangen, dass `model` ein registriertes Modell und `inference_config` die Konfiguration für die Rückschlussumgebung enthält. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Bei `show_output=True` wird die Ausgabe des Docker-Buildprozesses angezeigt. Nach Abschluss des Prozesses wurde das Image in der Azure Container Registry-Instanz für Ihren Arbeitsbereich erstellt. Nachdem das Image erstellt wurde, wird der Speicherort in Ihrer Instanz von Azure Container Registry angezeigt. Der zurückgegebene Speicherort weist das Format `<acrinstance>.azurecr.io/package@sha256:<imagename>` auf.

> [!NOTE]
> Pakete für Functions unterstützen derzeit HTTP-Trigger, Blob-Trigger und Service Bus-Trigger. Weitere Informationen zu Triggern finden Sie unter [Azure Functions-Bindungen](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Speichern Sie die Speicherortinformationen, da sie beim Bereitstellen des Images verwendet werden.

## <a name="deploy-image-as-a-web-app"></a>Bereitstellen eines Images als Web-App

1. Verwenden Sie den folgenden Befehl, um die Anmeldeinformationen für die Azure Container Registry-Instanz zu erhalten, die das Image enthält. Ersetzen Sie `<myacr>` durch den zuvor von `package.location` zurückgegebenen Wert: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Dokument:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Speichern Sie die Werte für __Benutzername__ und eines der __Kennwörter__ .

1. Wenn Sie noch nicht über eine Ressourcengruppe oder einen App Service-Plan für die Bereitstellung des Diensts verfügen, veranschaulichen die folgenden Befehle das Erstellen dieser beiden Elemente:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In diesem Beispiel wird der _Linux Basic_ -Tarif (`--sku B1`) verwendet.

    > [!IMPORTANT]
    > Die von Azure Machine Learning erstellten Images verwenden Linux, daher müssen Sie den Parameter `--is-linux` verwenden.

1. Erstellen Sie das Speicherkonto, das für die Speicherung von Webaufträgen verwendet werden soll, und rufen Sie dessen Verbindungszeichenfolge ab. Ersetzen Sie `<webjobStorage>` durch den Namen, den Sie verwenden möchten.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Verwenden Sie den folgenden Befehl, um die Funktions-App zu erstellen. Ersetzen Sie `<app-name>` durch den Namen, den Sie verwenden möchten. Ersetzen Sie `<acrinstance>` und `<imagename>` durch die zuvor von `package.location` zurückgegebenen Werte. Ersetzen Sie `<webjobStorage>` durch den Namen des Speicherkontos aus dem vorherigen Schritt:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > An diesem Punkt wurde die Funktions-App erstellt. Da Sie jedoch nicht die Verbindungszeichenfolge für den HTTP-Trigger oder Anmeldeinformationen für die Azure Container Registry-Instanz mit dem Image angegeben haben, ist die Funktions-App nicht aktiv. Im nächsten Schritt geben Sie die Verbindungszeichenfolge und die Authentifizierungsinformationen für die Containerregistrierung an. 

1. Verwenden Sie den folgenden Befehl, um die für den Zugriff auf die Containerregistrierung erforderlichen Anmeldeinformationen für die Funktions-App bereitzustellen. Ersetzen Sie `<app-name>` durch den Namen der Funktions-App. Ersetzen Sie `<acrinstance>` und `<imagetag>` durch die Werte aus dem AZ CLI-Aufruf im vorherigen Schritt. Ersetzen Sie `<username>` und `<password>` durch die zuvor abgerufenen ACR-Anmeldeinformationen:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Dieser Befehl gibt Informationen ähnlich dem folgenden JSON-Dokument zurück:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Zu diesem Zeitpunkt beginnt die Funktions-App mit dem Laden des Images.

> [!IMPORTANT]
> Es kann einige Minuten dauern, bis das Image geladen wurde. Sie können den Fortschritt im Azure-Portal überwachen.

## <a name="test-azure-function-http-trigger"></a>Testen eines Azure Functions-HTTP-Triggers 

Wir führen jetzt den Azure Functions-HTTP-Trigger aus und testen ihn.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Functions-App.
1. Wählen Sie unter „Developer“ die Option **Programmieren und testen** aus. 
1. Wählen Sie auf der rechten Seite die Registerkarte **Eingabe** aus. 
1. Klicken Sie auf die Schaltfläche **Ausführen** , um den Azure Functions-HTTP-Trigger zu testen. 

Sie haben nun ein Modell aus Azure Machine Learning als Funktions-App in Azure Functions mithilfe einer Azure Cache for Redis-Instanz bereitgestellt. Weitere Informationen zu Azure Cache for Redis erhalten Sie anhand der Links im Abschnitt unten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten Tutorial fortfahren möchten, können Sie die in dieser Schnellstartanleitung erstellten Ressourcen beibehalten und wiederverwenden.

Wenn Sie den Schnellstart nicht mehr benötigen, können Sie die in dieser Schnellstartanleitung erstellten Azure-Ressourcen löschen, um das Anfallen von Kosten zu vermeiden. 

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Beim Löschen einer Ressourcengruppe werden alle darin enthaltenen Ressourcen unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln über das jeweilige Blatt löschen, statt die Ressourcengruppe zu löschen.

### <a name="to-delete-a-resource-group"></a>So löschen Sie eine Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie anschließend **Ressourcengruppen** aus.

2. Geben Sie im Feld **Nach Name filtern...** den Namen Ihrer Ressourcengruppe ein. Wählen Sie in Ihrer Ressourcengruppe in der Ergebnisliste **...** und anschließend **Ressourcengruppe löschen** aus.

Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte 

* Erfahren Sie mehr über [Azure Cache for Redis](./cache-overview.md).
* Erfahren Sie in der Dokumentation zu [Functions](../azure-functions/functions-create-function-linux-custom-image.md), wie Sie Ihre Funktions-App konfigurieren.
* [API-Referenz](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 
* Erstellen einer [Python-App, die Azure Cache for Redis verwendet](./cache-python-get-started.md)