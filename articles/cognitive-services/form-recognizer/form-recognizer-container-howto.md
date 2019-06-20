---
title: Installieren und Ausführen eines Containers – Formularerkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie den Container für die Formularerkennung verwenden, um Formular- und Tabellendaten zu analysieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 1c9e68f643f27f70190b5847225692d554cc5480
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475446"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installieren und Ausführen eines Containers für die Formularerkennung
Die Formularerkennung wendet die Machine Learning-Technologie an, um Schlüssel-Wert-Paare und Tabellen aus Formularen zu identifizieren und zu extrahieren. Diesen werden Werte und Tabelleneinträge zugeordnet, und dann werden strukturierte Daten ausgegeben, die die Beziehungen in der Originaldatei enthalten. Sie können Ihr benutzerdefiniertes Formularerkennungsmodell mithilfe einer einfachen REST-API aufrufen, um die Komplexität zu reduzieren und es einfach in Ihren Prozess zur Workflowautomatisierung oder eine andere Anwendung zu integrieren. Es werden nur fünf Dokumente (oder ein leeres Formular) benötigt, sodass Sie schnell, präzise und auf Ihre spezifischen Inhalte zugeschnittene Ergebnisse ohne komplizierte manuelle Eingriffe oder umfangreiche datenwissenschaftliche Kenntnisse erzielen können. Es sind keine Datenbeschriftungen oder Datenanmerkungen erforderlich.

|Funktion|Features|
|-|-|
|Formularerkennung| <li>Verarbeitet Dateien vom Typ PDF, PNG und JPG.<li>Trainiert benutzerdefinierte Modelle mit mindestens fünf Formularen mit demselben Layout. <li>Extrahiert Schlüssel-Wert-Paare und Tabelleninformationen. <li>Verwendet die Maschinelles Sehen-API von Cognitive Service (Texterkennung) zum Erkennen und Extrahieren von gedrucktem Text aus Bildern in Formularen.<li>Erfordert keine Anmerkungen oder Beschriftungen.|

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung von Containern für die Formularerkennung müssen Sie die folgenden Voraussetzungen erfüllen:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.|
|Azure-Befehlszeilenschnittstelle| Sie müssen Sie die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) auf Ihrem Host installieren.|
|Maschinelles Sehen-API-Ressource| Um gescannte Dokumente und Bilder zu verarbeiten, wird eine **Maschinelles Sehen-Ressource** benötigt. Sie können entweder als Azure-Ressource (REST-API oder SDK) oder als [Container](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text` auf das Feature **Texterkennung** zugreifen. Die üblichen Gebühren werden abgerechnet. <br><br>Sie müssen sowohl den Schlüssel- als auch den Abrechnungsendpunkt für Ihre spezifische Maschinelles Sehen-Ressource (Azure-Cloud oder Cognitive Services-Container) übergeben. Verwenden Sie diesen Schlüssel- und Abrechnungsendpunkt als {COMPUTER_VISION_API_KEY} und {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Wenn Sie den **`cognitive-services-recognize-text`-Container** verwenden, stellen Sie Folgendes sicher:<br><br>*Ihr Maschinelles Sehen-Schlüssel für den Formularerkennungscontainer ist der Schlüssel, der im Maschinelles Sehen-Befehl `docker run` für den `cognitive-services-recognize-text`-Container angegeben ist.<br>*Ihr Abrechnungsendpunkt ist der Endpunkt des Containers, z.B. `https://localhost:5000`. Wenn Sie sowohl den Maschinelles Sehen- als auch den Formularerkennungscontainer gemeinsam auf demselben Host verwenden, können nicht beide mit dem Standardport `5000` gestartet werden.  |  
|Formularerkennungsressource |Um diese Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ _Formularerkennung_, um den entsprechenden Abrechnungsschlüssel und den URI des Abrechnungsendpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf der Übersichtsseite und auf der Schlüsselseite der **Formularerkennung** zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{BILLING_KEY}** : Der Ressourcenschlüssel.<br><br>**{BILLING_ENDPOINT_URI}** : Der Endpunkt-URI. Beispiel: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Anfordern des Zugriffs auf die Containerregistrierung

Sie müssen zuerst das [Formular zum Anfordern von Cognitive Services-Containern für Formularerkennung](https://aka.ms/FormRecognizerRequestAccess) ausfüllen und senden, um Zugriff auf den Container anzufordern. Damit werden Sie auch für maschinelles Sehen registriert. Sie müssen den Zugriff auf maschinelles Sehen nicht separat anfordern. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für CPU-Kerne und Arbeitsspeicher beschrieben, die jedem Container für die Formularerkennung zugeordnet werden.

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 Kerne, 4 GB Arbeitsspeicher | 4 Kerne, 8 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* TPS: Transaktionen pro Sekunde

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

> [!Note]
> Die Mindestanforderungen und empfohlenen Werte basieren auf Docker-Grenzwerten und *nicht* den Ressourcen des Hostcomputers.

## <a name="get-the-container-image-with-docker-pull-command"></a>Abrufen des Containerimages mit dem Befehl „docker pull“

Es stehen Containerimages für die Formularerkennung zur Verfügung.

| Container | Repository |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Wenn Sie anstelle des Dienst zur Formularerkennung den [Container](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text` verwenden möchten, verwenden Sie den Befehl `docker pull` unbedingt mit dem richtigen Containernamen: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker-Pullvorgang für den Container für die Formularerkennung

#### <a name="form-recognizer"></a>Formularerkennung

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen, aber nicht verwendeten Abrechnungseinstellungen. Es sind noch weitere [Beispiele](form-recognizer-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um einen der drei Container auszuführen. Für den Befehl werden die folgenden Parameter verwendet:

| Platzhalter | Wert |
|-------------|-------|
|{BILLING_KEY} | Dieser Schlüssel wird zum Starten des Containers verwendet und steht im Azure-Portal auf der Schlüsselseite der Formularerkennung zur Verfügung.  |
|{BILLING_ENDPOINT_URI} | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Übersichtsseite der Formularerkennung.|
|{COMPUTER_VISION_API_KEY}| Der Schlüssel ist im Azure-Portal auf der Schlüsselseite der Maschinelles Sehen-API verfügbar.|
|{COMPUTER_VISION_ENDPOINT_URI}|Der Abrechnungsendpunkt. Wenn Sie eine cloudbasierte Ressource für maschinelles Sehen verwenden, ist der URI-Wert im Azure-Portal auf der Übersichtsseite der Maschinelles Sehen-API verfügbar. Bei Verwendung eines `cognitive-services-recognize-text`-Containers verwenden Sie die URL des Abrechnungsendpunkts, die im Befehl `docker run` an den Container übergeben wurde.|

Ersetzen Sie im folgenden Beispiel für den Befehl `docker run` diese Parameter durch Ihre eigenen Werte.

### <a name="form-recognizer"></a>Formularerkennung

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Dieser Befehl:

* Führt einen Formularerkennungscontainer auf der Grundlage des Containerimages aus
* Weist zwei CPU-Kerne und 8 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.
* Stellt dem Container ein „/input“- und ein „/output“-Volume bereit

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Ausführen separater Container als separate Docker-Ausführungsbefehle

Für die lokal auf dem gleichen Host gehostete Kombination aus Formularerkennung und Texterkennung folgen zwei Docker CLI-Beispielbefehle.

Führen Sie den ersten Container an Port 5000 aus. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Führen Sie den zweiten Container an Port 5001 aus.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Jeder weitere Container sollte an einem anderen Port ausgeführt werden. 

### <a name="run-separate-containers-with-docker-compose"></a>Ausführen von separaten Containern mit Docker Compose

Für die lokal auf dem gleichen Host gehostete Kombination aus Formularerkennung und Texterkennung folgt eine Docker Compose YAML-Beispieldatei. Die Texterkennung `{COMPUTER_VISION_API_KEY}` muss für die Container `formrecognizer` und `ocr` gleich sein. `{COMPUTER_VISION_ENDPOINT_URI}` wird nur im `ocr`-Container verwendet, da der `formrecognizer`-Container den `ocr`-Namen und -Port verwendet. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` sowie `FormRecognizer:ComputerVisionApiKey` und `FormRecognizer:ComputerVisionEndpointUri` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

|Container|Endpunkt|
|--|--|
|Formularerkennung|http://localhost:5000


### <a name="form-recognizer"></a>Formularerkennung

Der Container bietet websocketbasierte Abfrageendpunkt-APIs, auf die über die [Dokumentation des Formularerkennungsdienste-SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) zugegriffen wird.

Das Formularerkennungs-SDK verwendet standardmäßig die Onlinedienste. Um den Container zu verwenden, müssen Sie die Initialisierungsmethode ändern. Weitere Informationen finden Sie in den folgenden Beispielen.

#### <a name="for-c"></a>C#

Ändern Sie den folgenden Azure-Cloudinitialisierungsaufruf

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

in den folgenden Aufruf mit dem Containerendpunkt:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python

Ändern Sie den folgenden Azure-Cloudinitialisierungsaufruf

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

zu diesem Aufruf unter Verwendung des Containerendpunkts:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formularerkennung

Die vom Container bereitgestellten REST-Endpunkt-APIs finden Sie [hier](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container ausführen, werden mithilfe von **stdout** und **stderr** Informationen ausgegeben, die hilfreich sind, um Probleme beim Starten oder Ausführen des Containers zu beheben.

## <a name="billing"></a>Abrechnung

Der Container für die Formularerkennung sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Formularerkennung_ in Ihrem Azure-Konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](form-recognizer-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Formularerkennung kennengelernt. Zusammenfassung:

* Die Formularerkennung stellt einen Linux-Container für Docker bereit.
* Containerimages werden aus der privaten Containerregistrierung in Azure heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in einem Container für die Formularerkennung über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
>  Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z.B. das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](form-recognizer-container-configuration.md).
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
