---
title: Leitfaden zur Problembehandlung bei der Bereitstellung
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie gängige Docker-Bereitstellungsfehler mit AKS und ACI mithilfe von Azure Machine Learning Service umgehen, lösen und beheben können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 3730e4a0bfa05e6606e50b9bbd9d9152e2488954
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851718"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI

Erfahren Sie, wie Sie die häufigsten Docker-Bereitstellungsfehler mit Azure Container Instances (ACI) und Azure Kubernetes Service (AKS) mit Azure Machine Learning Service umgehen oder beheben können.

Bei der Bereitstellung eines Modells im Azure Machine Learning-Dienst führt das System eine Reihe von Aufgaben aus. Dies sind die Aufgaben bei der Bereitstellung:

1. Registrieren des Modells in der Modellregistrierung des Arbeitsbereichs.

2. Erstellen eines Docker-Images, einschließlich:
    1. Herunterladen des registrierten Modells aus der Registry. 
    2. Erstellen eines Dockerfiles mit einer Python-Umgebung auf der Grundlage der Abhängigkeiten, die Sie in der YAML-Datei der Umgebung angeben.
    3. Hinzufügen Ihrer Modelldateien und des Bewertungsskripts, das Sie im Dockerfile bereitstellen.
    4. Erstellen eines neuen Docker-Images mithilfe des Dockerfiles.
    5. Registrieren des Docker-Images bei der Azure Container Registry, die dem Arbeitsbereich zugeordnet ist.

    > [!IMPORTANT]
    > Abhängig von Ihrem Code kann die Imageerstellung automatisch ohne Ihre Eingabe erfolgen.

3. Bereitstellen des Docker-Images im ACI-Dienst (Azure Container Instance) oder in AKS (Azure Kubernetes Service).

4. Starten eines neuen Containers (oder mehrerer Container) in ACI oder AKS. 

Weitere Informationen über diesen Prozess finden Sie in der Einführung zur [Modellverwaltung](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Voraussetzungen

Beim Auftreten eines Problems besteht der erste Schritt darin, die (zuvor beschriebene) Bereitstellungsaufgabe in einzelne Schritte aufzuschlüsseln, um das Problem zu isolieren.

Die Aufschlüsselung der Bereitstellung in Aufgaben ist hilfreich, wenn Sie die [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-)- oder [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)-API verwenden, da beide Funktionen die oben genannten Schritte als einzelne Aktion ausführen. Im Normalfall sind diese APIs praktisch, bei der Problembehandlung ist es jedoch hilfreich, die Schritte aufzuschlüsseln, indem Sie sie durch die unten angegebenen API-Aufrufe ersetzen.

1. Registrieren des Modells. Hier finden Sie Beispielcode dazu:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Erstellen des Images. Hier finden Sie Beispielcode dazu:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Bereitstellen des Images als Dienst. Hier finden Sie Beispielcode dazu:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Nachdem Sie den Bereitstellungsvorgang in einzelne Aufgaben aufgeschlüsselt haben, können wir uns einige der häufigsten Fehler ansehen.

## <a name="image-building-fails"></a>Fehler bei der Image-Erstellung

Wenn das Docker-Image nicht erstellt werden kann, tritt beim Aufruf von [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) oder [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) ein Fehler auf, mit einigen Fehlermeldungen, die Anhaltspunkte bieten können. Ferner finden Sie weitere Details zu den Fehlern in den Erstellungsprotokollen des Images. Unten sehen Sie etwas Beispielcode, aus dem Sie ersehen können, wie Sie den URI des Image-Erstellungsprotokolls ermitteln können.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

Der URI des Imageprotokolls ist eine SAS-URL, die auf eine Protokolldatei verweist, die in Ihrem Azure Blob Storage gespeichert ist. Kopieren Sie einfach den URI, und fügen Sie ihn in ein Browserfenster ein, dann können Sie die Protokolldatei herunterladen und anzeigen.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault-Zugriffsrichtlinie und Azure Resource Manager-Vorlagen

Die Imageerstellung kann auch aufgrund eines Problems mit der Zugriffsrichtlinie für den Azure Key Vault fehlschlagen. Diese Situation kann eintreten, wenn Sie eine Azure Resource Manager-Vorlage mehrmals zum Erstellen des Arbeitsbereichs und der zugehörigen Ressourcen (einschließlich Azure Key Vault) verwenden. Dies ist beispielsweise der Fall, wenn Sie die Vorlage mehrere Male mit den gleichen Parametern in einer Continuous Integration- und Deployment-Pipeline verwenden.

Die meisten Vorgänge zum Erstellen von Ressourcen mit Vorlagen sind idempotent, Key Vault löscht die Zugriffsrichtlinien jedoch jedes Mal, wenn die Vorlage verwendet wird. Das Löschen der Zugriffsrichtlinien unterbricht den Zugriff auf den Schlüsseltresor für vorhandene Arbeitsbereiche, die ihn verwenden. Diese Bedingung führt zu Fehlern, wenn Sie versuchen, neue Images zu erstellen. Im Folgenden finden Sie Beispiele für Fehler, die angezeigt werden können:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK:__
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Folgende Ansätze werden empfohlen, um dieses Problem zu umgehen:

* Stellen Sie die Vorlage nicht mehrmals mit den gleichen Parametern bereit, oder löschen Sie die vorhandenen Ressourcen, bevor Sie die Vorlage verwenden, um sie neu zu erstellen.
* Untersuchen Sie die Key Vault-Zugriffsrichtlinien, und verwenden Sie sie dann zum Festlegen der `accessPolicies`-Eigenschaft der Vorlage.
* Überprüfen Sie, ob die Key Vault-Ressource bereits vorhanden ist. Wenn dies der Fall ist, erstellen Sie sie nicht mithilfe der Vorlage neu. Fügen Sie beispielsweise einen Parameter hinzu, mit dem Sie die Erstellung der Key Vault-Ressource deaktivieren können, wenn sie bereits vorhanden ist.

## <a name="debug-locally"></a>Lokales Debuggen

Wenn bei der Bereitstellung eines Modells für ACI oder AKS Probleme auftreten, versuchen Sie, es als lokalen Webdienst bereitzustellen. Das Verwenden eines lokalen Webdiensts erleichtert die Problembehandlung. Das Docker-Image mit dem Modell wird heruntergeladen und auf dem lokalen System gestartet.

> [!IMPORTANT]
> Bereitstellungen lokaler Webdienste erfordern eine funktionierende Installation von Docker auf Ihrem lokalen System. Docker muss ausgeführt werden, bevor Sie einen lokalen Webdienst bereitstellen. Informationen zum Installieren und Verwenden von Docker finden Sie unter [https://www.docker.com/](https://www.docker.com/).

> [!WARNING]
> Bereitstellungen lokaler Webdienste werden nicht für Produktionsszenarien unterstützt.

Ändern Sie zum lokalen Bereitstellen Ihren Code so, dass `LocalWebservice.deploy_configuration()` zum Erstellen einer Bereitstellungskonfiguration verwendet wird. Verwenden Sie dann `Model.deploy()`, um den Dienst bereitzustellen. Im folgenden Beispiel wird ein (in der `model`-Variable enthaltenes) Modell als lokaler Webdienst bereitgestellt:

```python
from azureml.core.model import InferenceConfig
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   execution_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

An diesem Punkt können Sie mit dem Dienst wie gewohnt arbeiten. Der folgende Code zeigt beispielsweise, wie Daten an den Dienst gesendet werden:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Aktualisieren des Diensts

Beim lokalen Testen müssen Sie möglicherweise die `score.py`-Datei aktualisieren, um die Protokollierung hinzuzufügen oder ggf. zu versuchen, alle Probleme zu behandeln, die Sie ermittelt haben. Zum erneuten Laden von Änderungen an der `score.py`-Datei verwenden Sie `reload()`. Der folgende Code lädt z.B. das Skript für den Dienst neu und sendet dann Daten an ihn. Die Daten werden mithilfe der aktualisierten `score.py`-Datei bewertet:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Das Skript wird aus dem Speicherort erneut geladen, der durch das vom Dienst verwendete `InferenceConfig`-Objekt angegeben wird.

Um das Modell, Conda-Abhängigkeiten oder eine Bereitstellungskonfiguration zu ändern, verwenden Sie [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Das folgende Beispiel aktualisiert das vom Dienst verwendete Modell:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Löschen des Diensts

Verwenden Sie zum Löschen des Diensts [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Untersuchen des Docker-Protokolls

Das Dienstobjekt erlaubt die Ausgabe von detaillierten Protokollnachrichten der Docker-Engine. Sie können das Protokoll für ACI, AKS und lokale Bereitstellungen anzeigen. Die Ausgabe der Protokolle wird im folgenden Beispiel veranschaulicht.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Fehler beim Starten des Diensts

Nach der erfolgreichen Erstellung des Images versucht das System, mithilfe Ihrer Bereitstellungskonfiguration einen Container zu starten. Als Teil des Container-Startprozesses wird die `init()`-Funktion in Ihrem Bereitstellungsskript vom System aufgerufen. Wenn in der `init()`-Funktion nicht abgefangene Ausnahmen auftreten, ist in der Fehlermeldung möglicherweise ein **CrashLoopBackOff**-Fehler zu finden.

Verwenden Sie die Informationen im Abschnitt [Untersuchen des Docker-Protokolls](#dockerlog), um die Protokolle überprüfen.

## <a name="function-fails-getmodelpath"></a>Fehler bei der Funktion: get_model_path()

Oftmals wird in der `init()`-Funktion im Bewertungsskript die Funktion [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) aufgerufen, um eine Modelldatei oder einen Ordner mit Modelldateien im Container zu finden. Wenn die Datei oder der Ordner für das Modell nicht gefunden werden kann, tritt bei der Funktion ein Fehler auf. Die einfachste Möglichkeit zum Debuggen dieses Fehlers besteht darin, den unten dargestellten Python-Code in der Containershell auszuführen:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

In diesem Beispiel wird der lokale Pfad (relativ zu `/var/azureml-app`) in dem Container ausgegeben, unter dem Ihr Bewertungsskript die Modelldatei oder den Modellordner erwartet. Dann können Sie überprüfen, ob sich die Datei oder der Ordner wirklich dort befinden, wo sie erwartet werden.

Das Festlegen der Protokollierungsstufe auf DEBUG kann dazu führen, dass zusätzliche Informationen protokolliert werden, was bei der Identifizierung des Fehlers nützlich sein kann.

## <a name="function-fails-runinputdata"></a>Fehler bei der Funktion: run(input_data)

Wenn der Dienst erfolgreich bereitgestellt wurde, aber beim Veröffentlichen von Daten am Bewertungsendpunkt abstürzt, können Sie Ihrer `run(input_data)`-Funktion eine Anweisung zum Abfangen von Fehlern hinzufügen, damit sie stattdessen eine detaillierte Fehlermeldung zurückgibt. Beispiel: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Hinweis**: Die Rückgabe von Fehlermeldungen aus dem Aufruf `run(input_data)` sollte nur zu Debugzwecken erfolgen. Aus Sicherheitsgründen sollten Sie in einer Produktionsumgebung keine Fehlermeldungen auf diese Weise zurückgeben.

## <a name="http-status-code-503"></a>HTTP-Statuscode 503

Azure Kubernetes Service-Bereitstellungen unterstützen die automatische Skalierung, mit der das Hinzufügen von Replikaten ermöglicht wird, um zusätzliche Last zu unterstützen. Die Autoskalierung wurde jedoch für die Behandlung **stetiger** Veränderungen der Last konzipiert. Wenn Sie große Spitzen bei den Anforderungen pro Sekunde erhalten, erhalten Clients möglicherweise den HTTP-Statuscode 503.

Es gibt zwei Möglichkeiten, die beim Verhindern des Statuscodes 503 helfen können:

* Ändern Sie den Auslastungsgrad für die Erstellung neuer Replikate durch die automatische Skalierung.
    
    Der Auslastungsgrad der automatischen Skalierung ist standardmäßig auf 70 % festgelegt, was bedeutet, dass der Dienst Spitzen bei den Anforderungen pro Sekunde (RPS) von bis zu 30 % verarbeiten kann. Sie können den Auslastungsgrad anpassen, indem Sie einen niedrigeren Wert für `autoscale_target_utilization` festlegen.

    > [!IMPORTANT]
    > Durch diese Änderung werden Replikate *nicht schneller* erstellt. Stattdessen werden sie mit einem niedrigeren Schwellenwert für die Auslastung erstellt. Anstatt abzuwarten, bis der Dienst zu 70 % ausgelastet ist, werden Replikate schon bei 30 % Auslastung erstellt, wenn Sie den Wert in 30 % ändern.
    
    Wenn der Webdienst bereits die derzeit maximale Anzahl von Replikaten verwendet und Sie weiterhin den Statuscode 503 erhalten, erhöhen Sie den `autoscale_max_replicas`-Wert, um die maximale Anzahl der Replikate zu erhöhen.

* Ändern Sie die Mindestanzahl der Replikate. Indem Sie die Mindestanzahl der Replikate erhöhen, wird ein größerer Pool für die Verarbeitung eingehender Spitzen bereitgestellt.

    Legen Sie einen höheren Wert für `autoscale_min_replicas` fest, um die Mindestanzahl der Replikate zu erhöhen. Sie können die erforderliche Anzahl von Replikaten berechnen, indem Sie den folgenden Code verwenden und die Werte durch die Werten Ihres Projekts ersetzen:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Wenn Anforderungsspitzen eingehen, die die neue Mindestanzahl von Replikaten überschreiten, erhalten Sie möglicherweise wieder den Statuscode 503. Wenn sich der Datenverkehr Ihres Diensts beispielsweise erhöht, müssen Sie die Mindestanzahl von Replikaten möglicherweise erhöhen.

Weitere Informationen zum Festlegen von `autoscale_target_utilization`, `autoscale_max_replicas` und `autoscale_min_replicas` finden Sie in der Modulreferenz zu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung finden Sie hier:

* [Bereitstellung: wie und wo?](how-to-deploy-and-where.md)
* [Tutorial: Trainieren und Bereitstellen von Modellen](tutorial-train-models-with-aml.md)
