---
title: Behandeln von Bereitstellungsproblemen bei Remotewebdiensten
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie allgemeine Docker-Bereitstellungsfehler mit Azure Kubernetes Service und Azure Container Instances umgehen, lösen und beheben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 92cd70e864ae0490ce3f9e7435d9518241f93c8e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031503"
---
# <a name="troubleshoot-model-deployment"></a>Behandeln von Problemen bei der Modellimplementierung

Erfahren Sie, wie Sie die häufigsten Fehler bei Docker-Remotebereitstellungen mit Azure Container Instances (ACI) und Azure Kubernetes Service (AKS) mit Azure Machine Learning behandeln, beheben oder umgehen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.
* Das [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* Die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Schritte für die Docker-Bereitstellung von Machine Learning-Modellen

Wenn Sie ein Modell für nicht lokales Compute in Azure Machine Learning bereitstellen, passiert Folgendes:

1. Das Dockerfile, das Sie in Ihrer Rückschlusskonfiguration (InferenceConfig) in Ihrem Umgebungsobjekt (Environments) angegeben haben, wird zusammen mit dem Inhalt Ihres Quellverzeichnisses an die Cloud gesendet.
1. Falls in Ihrer Containerregistrierung kein zuvor erstelltes Image verfügbar ist, wird in der Cloud ein neues Docker-Image erstellt und in der Standardcontainerregistrierung Ihres Arbeitsbereichs gespeichert.
1. Das Docker-Image aus Ihrer Containerregistrierung wird in Ihr Computeziel heruntergeladen.
1. Der Standardblobspeicher Ihres Arbeitsbereichs wird in Ihr Computeziel eingebunden, sodass Sie auf registrierte Modelle zugreifen können.
1. Ihr Webserver wird initialisiert. Hierzu wird die Funktion `init()` Ihres Eingabeskripts ausgeführt.
1. Wenn bei Ihrem bereitgestellten Modell eine Anforderung eingeht, wird diese durch die Funktion `run()` verarbeitet.

Der Hauptunterschied bei der Verwendung einer lokalen Bereitstellung besteht darin, dass das Containerimage auf Ihrem lokalen Computer erstellt wird. Daher muss bei einer lokalen Bereitstellung Docker installiert sein.

Wenn Sie mit diesen grundlegenden Schritten vertraut sind, können Sie besser nachvollziehen, wo Fehler auftreten.

## <a name="get-deployment-logs"></a>Abrufen von Bereitstellungsprotokollen

Das Abrufen Ihrer Bereitstellungsprotokolle ist der erste Schritt beim Debuggen von Fehlern. Führen Sie zunächst die [hier](how-to-deploy-and-where.md#connect-to-your-workspace) beschriebenen Schritte aus, um eine Verbindung mit Ihrem Arbeitsbereich herzustellen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Gehen Sie wie folgt vor, um die Protokolle aus einem bereitgestellten Webdienst abzurufen:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Wenn Sie beispielsweise über ein Objekt vom Typ `azureml.core.Workspace` namens `ws` verfügen, können Sie die folgenden Schritte ausführen:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Lokales Debuggen

Wenn bei der Bereitstellung eines Modells für ACI oder AKS Probleme auftreten, versuchen Sie, es als lokalen Webdienst bereitzustellen. Das Verwenden eines lokalen Webdiensts erleichtert die Problembehandlung. Informationen zur lokalen Problembehandlung bei einer Bereitstellung finden Sie im Artikel zur [lokalen Problembehandlung](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Planen des Containers nicht möglich

Beim Bereitstellen eines Diensts für ein Azure Kubernetes Service-Computeziel versucht Azure Machine Learning, den Dienst mit der angeforderten Menge von Ressourcen zu planen. Wenn nach 5 Minuten keine Knoten mit der entsprechenden Menge von verfügbaren Ressourcen im Cluster vorhanden sind, schlägt die Bereitstellung fehl. Die Fehlermeldung ist `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`. Sie können diesen Fehler beheben, indem Sie entweder weitere Knoten hinzufügen, die SKU Ihrer Knoten oder die Ressourcenanforderungen für Ihren Dienst ändern. 

In der Fehlermeldung ist in der Regel angegeben, von welcher Ressource mehr benötigt wird. Wenn beispielsweise eine Fehlermeldung angezeigt wird, in der `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` (0/3 Knoten sind verfügbar: 3 unzureichend nvidia.com/gpu) steht, bedeutet dies, dass der Dienst GPUs benötigt und drei Knoten im Cluster vorhanden sind, die nicht über verfügbare GPUs verfügen. Dieses Problem kann durch Hinzufügen weiterer Knoten behoben werden, wenn Sie eine GPU-SKU verwenden, oder durch Wechseln zu einer GPU-fähigen SKU, wenn nicht. Alternativ können Sie Ihre Umgebung so ändern, dass keine GPUs erforderlich sind.  

## <a name="service-launch-fails"></a>Fehler beim Starten des Diensts

Nach der erfolgreichen Erstellung des Images versucht das System, mithilfe Ihrer Bereitstellungskonfiguration einen Container zu starten. Als Teil des Container-Startprozesses wird die `init()`-Funktion in Ihrem Bereitstellungsskript vom System aufgerufen. Wenn in der `init()`-Funktion nicht abgefangene Ausnahmen auftreten, ist in der Fehlermeldung möglicherweise ein **CrashLoopBackOff**-Fehler zu finden.

Nutzen Sie die Informationen im Artikel [Untersuchen des Docker-Protokolls](how-to-troubleshoot-deployment-local.md#dockerlog).

## <a name="function-fails-get_model_path"></a>Fehler bei der Funktion: get_model_path()

Oftmals wird in der `init()`-Funktion im Bewertungsskript die Funktion [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) aufgerufen, um eine Modelldatei oder einen Ordner mit Modelldateien im Container zu finden. Wenn die Datei oder der Ordner für das Modell nicht gefunden werden kann, tritt bei der Funktion ein Fehler auf. Die einfachste Möglichkeit zum Debuggen dieses Fehlers besteht darin, den unten dargestellten Python-Code in der Containershell auszuführen:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

In diesem Beispiel wird der lokale Pfad (relativ zu `/var/azureml-app`) in dem Container ausgegeben, unter dem Ihr Bewertungsskript die Modelldatei oder den Modellordner erwartet. Dann können Sie überprüfen, ob sich die Datei oder der Ordner wirklich dort befinden, wo sie erwartet werden.

Das Festlegen der Protokollierungsstufe auf DEBUG kann dazu führen, dass zusätzliche Informationen protokolliert werden, was bei der Identifizierung des Fehlers nützlich sein kann.

## <a name="function-fails-runinput_data"></a>Fehler bei der Funktion: run(input_data)

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

## <a name="http-status-code-502"></a>HTTP-Statuscode 502

Der Statuscode 502 gibt an, dass der Dienst eine Ausnahme ausgelöst hat oder in der `run()`-Methode der Datei „score.py“ abgestürzt ist. Verwenden Sie die Informationen in diesem Artikel, um die Datei zu debuggen.

## <a name="http-status-code-503"></a>HTTP-Statuscode 503

Azure Kubernetes Service-Bereitstellungen unterstützen die automatische Skalierung, mit der das Hinzufügen von Replikaten ermöglicht wird, um zusätzliche Last zu unterstützen. Die Autoskalierung wurde für die Behandlung **gradueller** Änderungen der Auslastung konzipiert. Wenn Sie große Spitzen bei den Anforderungen pro Sekunde erhalten, erhalten Clients möglicherweise den HTTP-Statuscode 503. Obwohl die Autoskalierung schnell reagiert, nimmt die Erstellung zusätzlicher Container in AKS beträchtliche Zeit in Anspruch.

Skalierungsentscheidungen werden auf der Grundlage der Auslastung der aktuellen Containerreplikate getroffen. Zur Ermittlung der aktuellen Auslastung wird die Anzahl ausgelasteter Replikate (Replikate, die eine Anforderung verarbeiten) durch die Gesamtanzahl aktueller Replikate geteilt. Übersteigt dieser Wert `autoscale_target_utilization`, werden weitere Replikate erstellt. Ist der Wert niedriger, wird die Replikatanzahl verringert. Entscheidungen zum Hinzufügen von Replikaten sind eifrig und schnell (ungefähr 1 Sekunde). Die Entscheidung, Replikate zu entfernen, erfolgt zurückhaltend (etwa 1 Minute). Der Auslastungsgrad der Autoskalierung ist standardmäßig auf **70 %** festgelegt, was bedeutet, dass der Dienst Spitzen bei den Anforderungen pro Sekunde (RPS) von **bis zu 30 %** verarbeiten kann.

Es gibt zwei Möglichkeiten, die beim Verhindern des Statuscodes 503 helfen können:

> [!TIP]
> Diese beiden Ansätze können einzeln oder in Kombination verwendet werden.

* Ändern Sie den Auslastungsgrad für die Erstellung neuer Replikate durch die automatische Skalierung. Sie können den Auslastungsgrad anpassen, indem Sie einen niedrigeren Wert für `autoscale_target_utilization` festlegen.

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

Weitere Informationen zum Festlegen von `autoscale_target_utilization`, `autoscale_max_replicas` und `autoscale_min_replicas` finden Sie in der Modulreferenz zu [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py).

## <a name="http-status-code-504"></a>HTTP-Statuscode 504

Der Statuscode 504 gibt an, dass für die Anforderung ein Timeout aufgetreten ist. Das Standardtimeout beträgt 1 Minute.

Sie können das Timeout erhöhen oder versuchen, den Dienst zu beschleunigen, indem Sie unnötige Aufrufe in „score.py“ entfernen. Wenn das Problem durch diese Aktionen nicht behoben werden kann, debuggen Sie die Datei „score.py“ mithilfe der Informationen in diesem Artikel. Der Code kann sich in einem nicht reaktionsfähigen Zustand oder in einer Endlosschleife befinden.

## <a name="advanced-debugging"></a>Erweitertes Debuggen

Sie müssen den in der Modellimplementierung enthaltenen Python-Code ggf. interaktiv debuggen. Dies ist beispielsweise der Fall, wenn das Einstiegsskript fehlschlägt und der Grund nicht durch zusätzliche Protokollierung ermittelt werden kann. Mit Visual Studio Code und debugpy können Sie an den Code anfügen, der im Docker-Container ausgeführt wird.

Weitere Informationen finden Sie im [Leitfaden „Interaktives Debuggen in VS Code“](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Benutzerforum zur Modellimplementierung](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung finden Sie hier:

* [Bereitstellung: wie und wo?](how-to-deploy-and-where.md)
* [Tutorial: Trainieren und Bereitstellen von Modellen](tutorial-train-models-with-aml.md)
* [Lokales Ausführen und Debuggen von Experimenten](./how-to-debug-visual-studio-code.md)