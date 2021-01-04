---
title: Paketmodelle
titleSuffix: Azure Machine Learning
description: Packen Sie ein Modell. Modelle können entweder als Docker-Image gepackt werden, das Sie anschließend herunterladen können, oder Sie können eine Dockerfile erstellen und diese zur Erstellung des Images verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312634"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Verpacken eines registrierten Modells mit Docker

In diesem Artikel wird gezeigt, wie ein registriertes Azure Machine Learning-Modell mit Docker verpackt werden kann.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Modell in Ihrem Machine Learning-Arbeitsbereich trainiert und registriert haben. [In diesem Tutorial](how-to-train-scikit-learn.md) erfahren Sie, wie Sie ein Scikit-learn-Modell trainieren und registrieren können.


## <a name="package-models"></a>Paketmodelle

In einigen Fällen möchten Sie möglicherweise ein Docker-Image erstellen, ohne das Modell bereitzustellen (beispielsweise wenn Sie [in Azure App Service bereitstellen](how-to-deploy-app-service.md) möchten). Oder Sie möchten das Image herunterladen und in einer lokalen Docker-Installation ausführen. Es könnte sogar vorkommen, dass Sie die für die Imageerstellung verwendeten Dateien herunterladen, untersuchen und ändern und das Image dann manuell erstellen möchten.

Diese Schritte können Sie bei der Modellpaketerstellung ausführen. Dabei werden alle Ressourcen, die zum Hosten eines Modells als Webdienst benötigt werden, in einem Paket zusammengefasst, und Sie erhalten die Möglichkeit, entweder ein vollständig erstelltes Docker-Image oder die zum Erstellen eines solchen Images erforderlichen Dateien herunterzuladen. Es gibt zwei Möglichkeiten, die Modellpaketerstellung zu verwenden:

**Herunterladen eines Modellpakets:** Laden Sie ein Docker-Image herunter, in dem das Modell und die anderen Dateien enthalten sind, die dazu erforderlich sind, es als Webdienst zu hosten.

**Generieren eines Dockerfile:** Laden Sie das Dockerfile, das Modell, das Eingabeskript und andere Ressourcen herunter, die zum Erstellen eines Docker-Images benötigt werden. Sie können dann die Dateien untersuchen oder Änderungen daran vornehmen, bevor Sie das Image lokal erstellen.

Beide Pakete können verwendet werden, um ein lokales Docker-Image abzurufen.

> [!TIP]
> Das Erstellen eines Pakets ist mit dem Bereitstellen eines Modells vergleichbar. Sie verwenden ein registriertes Modell und eine Rückschlusskonfiguration.

> [!IMPORTANT]
> Damit Sie ein vollständig erstelltes Image herunterladen oder ein Image lokal erstellen können, muss [Docker](https://www.docker.com) in Ihrer Entwicklungsumgebung installiert sein.

### <a name="download-a-packaged-model"></a>Herunterladen eines Modellpakets

Im folgenden Beispiel wird ein Image erstellt, das in der Azure-Containerregistrierung für Ihren Arbeitsbereich registriert wird:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nachdem Sie ein Paket erstellt haben, können Sie `package.pull()` verwenden, um das Image per Pullvorgang in Ihre lokale Docker-Umgebung zu übertragen. In der Ausgabe dieses Befehls wird der Name des Images angezeigt. Beispiel: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Nachdem Sie das Modell heruntergeladen haben, verwenden Sie den `docker images`-Befehl, um die lokalen Images aufzulisten:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Um einen lokalen Container auf Basis dieses Images zu starten, verwenden Sie den folgenden Befehl, um einen benannten Container aus der Shell oder Befehlszeile zu starten. Ersetzen Sie den `<imageid>`-Wert durch die Image-ID, die vom Befehl `docker images` zurückgegeben wurde.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Mit diesem Befehl wird die aktuelle Version des Images mit dem Namen `myimage` gestartet. Der Befehl ordnet den lokalen Port 6789 dem Port des Containers zu, an dem der Webdienst lauscht (5001). Außerdem weist der Befehl dem Container den Namen `mycontainer` zu, wodurch ein Beenden des Containers vereinfacht wird. Nachdem der Container gestartet wurde, können Sie Anforderungen an `http://localhost:6789/score` senden.

### <a name="generate-a-dockerfile-and-dependencies"></a>Generieren eines Dockerfile und Generieren von Abhängigkeiten

Im folgenden Beispiel wird gezeigt, wie Sie das Dockerfile, das Modell und andere Ressourcen herunterladen, die erforderlich sind, um ein Image lokal zu erstellen. Mit dem Parameter `generate_dockerfile=True` ist angegeben, dass Sie kein vollständig erstelltes Image, sondern die Dateien herunterladen möchten.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Mit diesem Code werden die Dateien heruntergeladen, die zum Erstellen des Images im Verzeichnis `imagefiles` benötigt werden. Das Dockerfile, das zu den gespeicherten Dateien gehört, verweist auf ein Basisimage in einer Azure-Containerregistrierung. Wenn Sie das Image in Ihrer lokalen Docker-Installation erstellen, müssen Sie die Adresse, den Benutzernamen und das Kennwort verwenden, um die Authentifizierung bei der Registrierung vorzunehmen. Führen Sie die folgenden Schritte aus, um das Image durch Verwenden einer lokalen Docker-Installation zu erstellen:

1. Verwenden Sie in einer Shell- oder Befehlszeilensitzung den folgenden Befehl, um für Docker die Authentifizierung bei Azure-Containerregistrierung durchzuführen. Ersetzen Sie `<address>`, `<username>` und `<password>` durch die Werte, die mit `package.get_container_registry()` abgerufen wurden.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Verwenden Sie den folgenden Befehl, um das Image zu erstellen. Ersetzen Sie `<imagefiles>` durch den Pfad des Verzeichnisses, in dem die Dateien mit `package.save()` gespeichert wurden.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Mit diesem Befehl wird der Imagename auf `myimage` festgelegt.

Verwenden Sie den Befehl `docker images`, um zu überprüfen, ob das Image erstellt wurde. In der Liste sollte das Image `myimage` angezeigt werden:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Verwenden Sie den folgenden Befehl, um basierend auf diesem Image einen neuen Container zu starten:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Mit diesem Befehl wird die aktuelle Version des Images mit dem Namen `myimage` gestartet. Der Befehl ordnet den lokalen Port 6789 dem Port des Containers zu, an dem der Webdienst lauscht (5001). Außerdem weist der Befehl dem Container den Namen `mycontainer` zu, wodurch ein Beenden des Containers vereinfacht wird. Nachdem der Container gestartet wurde, können Sie Anforderungen an `http://localhost:6789/score` senden.

### <a name="example-client-to-test-the-local-container"></a>Beispielclient zum Testen des lokalen Containers

Der folgende Code ist ein Beispiel für einen Python-Client, der mit dem Container verwendet werden kann:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Beispielclients in anderen Programmiersprachen finden Sie unter [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Beenden des Docker-Containers

Verwenden Sie zum Beenden des Containers den folgenden Befehl über eine andere Shell- oder Befehlszeileninstanz:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Erstellen von Clientanwendungen zum Nutzen von Webdiensten](how-to-consume-web-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
* [Erstellen von Ereigniswarnungen und Triggern für Modellbereitstellungen](how-to-use-event-grid.md)
