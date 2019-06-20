---
title: Installieren und Ausführen von Containern – Anomalieerkennung
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den erweiterten Algorithmus der Anomalieerkennungs-API, um Anomalien in Zeitreihendaten zu identifizieren.
services: cognitive-services
author: IEvangelist
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 0369e27e7a260c50fc4de690dd3261421356799a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073325"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Installieren und Ausführen von Containern für die Anomalieerkennung

Die Anomalieerkennung umfasst die folgenden Container: 

|Funktion|Features|
|-|-|
|Anomalieerkennung| <li> Erkennt Anomalien in Echtzeit <li> Erkennt Anomalien für das ganze Dataset als Batch <li> Leitet den normalen zu erwartenden Bereich Ihrer Daten ab <li> Unterstützt die Anpassung der Anomalieerkennung an Ihre Daten |

Ausführliche Informationen zu den APIs finden Sie unter:
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden des Containers für die Anomalieerkennung müssen die folgenden Voraussetzungen erfüllt sein:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Anomalieerkennungsressource |Um diese Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ _Anomalieerkennung_, um den entsprechenden Abrechnungsschlüssel und den URI des Abrechnungsendpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf der Übersichts- und auf der Schlüsselseite der Anomalieerkennung zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{BILLING_KEY}** : Der Ressourcenschlüssel.<br><br>**{BILLING_ENDPOINT_URI}** : Der Endpunkt-URI. Beispiel: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Anfordern des Zugriffs auf die Containerregistrierung

Sie müssen zuerst das [Formular zum Anfordern von Containern für die Anomalieerkennung](https://aka.ms/adcontainer) ausfüllen und übermitteln, um Zugriff auf den Container anzufordern.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für CPU-Kerne und Arbeitsspeicher beschrieben, die jedem Container für die Anomalieerkennung zugeordnet werden müssen.

| Abfragen pro Sekunde (QPS) | Minimum | Empfohlen |
|-----------|---------|-------------|
| 10 QPS | 4 Kerne, 1 GB Arbeitsspeicher | 8 Kerne, 2 GB Arbeitsspeicher |
| 20 QPS | 8 Kerne, 2 GB Arbeitsspeicher | 16 Kerne, 4 GB Arbeitsspeicher |

Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage herunterzuladen.

| Container | Repository |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Ausführen von „docker pull“ für den Container für die Anomalieerkennung

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](anomaly-detector-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um einen der drei Container auszuführen. Für den Befehl werden die folgenden Parameter verwendet:

| Platzhalter | Wert |
|-------------|-------|
|{BILLING_KEY} | Dieser Schlüssel wird zum Starten des Containers verwendet und steht im Azure-Portal auf der Schlüsselseite der Anomalieerkennung zur Verfügung.  |
|{BILLING_ENDPOINT_URI} | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Übersichtsseite der Anomalieerkennung.|

Ersetzen Sie im folgenden Beispiel für den Befehl `docker run` diese Parameter durch Ihre eigenen Werte.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Dieser Befehl:

* Führt einen Container für die Anomalieerkennung auf der Grundlage des Containerimages aus
* Weist einen einzelnen CPU-Kern und 4 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Ausführen mehrerer Container auf dem gleichen Host

Wenn Sie beabsichtigen, mehrere Container mit offengelegten Ports auszuführen, stellen Sie sicher, dass jeder Container mit einem anderen Port ausgeführt wird. Führen Sie beispielsweise den ersten Container an Port 5000 und den zweiten Container an Port 5001 aus.

Ersetzen Sie `<container-registry>` und `<container-name>` durch die Werte der von Ihnen verwendeten Container. Dabei muss es sich nicht um die gleichen Container handeln. Sie können den Container für die Anomalieerkennung und den LUIS-Container zusammen auf dem Host ausführen, oder Sie können mehrere Container für die Anomalieerkennung ausführen. 

Führen Sie den ersten Container an Port 5000 aus. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Führen Sie den zweiten Container an Port 5001 aus.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Jeder weitere Container sollte an einem anderen Port ausgeführt werden. 

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. 

Verwenden Sie für Container-APIs den Host https://localhost:5000.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](anomaly-detector-container-configuration.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben. 

## <a name="billing"></a>Abrechnung

Der Container für die Anomalieerkennung sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Anomalieerkennung_ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Anomalieerkennung kennengelernt. Zusammenfassung:

* Die Anomalieerkennung stellt einen Linux-Container für Docker bereit, der die Anomalieerkennung kapselt. Sie kann auf Batches oder Streams ausgeführt werden und bietet Bereichsrückschlüsse sowie eine Optimierung der Genauigkeit.
* Containerimages werden aus einer privaten Azure Container Registry-Instanz für Containervorschauversionen heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Anomalieerkennung über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. die analysierten Zeitreihendaten) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](anomaly-detector-container-configuration.md).
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
