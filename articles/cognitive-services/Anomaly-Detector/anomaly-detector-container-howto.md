---
title: Installieren und Ausführen von Docker-Containern für die Anomalieerkennungs-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Algorithmen der Anomalieerkennungs-API, um mithilfe eines Docker-Containers lokal Anomalien in Ihren Daten zu finden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbullwin
ms.custom: cog-serv-seo-aug-2020
keywords: lokal, Docker, Container, Streaming, Algorithmen
ms.openlocfilehash: 911eb993ea5bb3dcce63057efc2d56d91d5a136b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364377"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>Installieren und Ausführen von Docker-Containern für die Anomalieerkennungs-API 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Mithilfe von Containern können Sie die Anomalieerkennungs-API in Ihrer eigenen Umgebung verwenden. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. In diesem Artikel erfahren Sie, wie Sie einen Container für die Anomalieerkennung herunterladen, installieren und ausführen.

Die Anomalieerkennung bietet einen einzelnen Docker-Container für die lokale Verwendung der API. Verwenden Sie den Container für Folgendes:
* Verwenden der Algorithmen der Anomalieerkennung für Ihre Daten
* Überwachen von Streamingdaten und Erkennen von Anomalien in Echtzeit, wenn sie auftreten
* Erkennen von Anomalien für das ganze Dataset als Batch 
* Erkennen von Trendänderungspunkten in Ihrem Dataset als Batch
* Anpassen der Sensitivität des Algorithmus für die Anomalieerkennung, damit er besser auf Ihre Daten ausgerichtet ist

Ausführliche Informationen zur API finden Sie unter:
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden des Containers für die Anomalieerkennung müssen die folgenden Voraussetzungen erfüllt sein:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.|
|Anomalieerkennungsressource |Um diese Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ _Anomalieerkennung_ , um den entsprechenden API-Schlüssel und den URI des Endpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf der Übersichts- und auf der Schlüsselseite der **Anomalieerkennung** zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](../../iot-edge/index.yml). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für CPU-Kerne und Arbeitsspeicher beschrieben, die jedem Container für die Anomalieerkennung zugeordnet werden müssen.

| Abfragen pro Sekunde (QPS) | Minimum | Empfohlen |
|-----------|---------|-------------|
| 10 QPS | Vier Kerne, 1 GB Arbeitsspeicher | Acht Kerne, 2 GB Arbeitsspeicher |
| 20 QPS | Acht Kerne, 2 GB Arbeitsspeicher | 16 Kerne, 4 GB Arbeitsspeicher |

Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage herunterzuladen.

| Container | Repository |
|-----------|------------|
| cognitive-services-anomaly-detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Ausführen von „docker pull“ für den Container für die Anomalieerkennung

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](anomaly-detector-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Genaue Informationen dazu, wie Sie die Werte `{ENDPOINT_URI}` und `{API_KEY}` abrufen, erhalten Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters).

Es sind [Beispiele](anomaly-detector-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
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
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Führen Sie den zweiten Container an Port 5001 aus.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Jeder weitere Container sollte an einem anderen Port ausgeführt werden.

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.

Verwenden Sie für Container-APIs den Host http://localhost:5000.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](anomaly-detector-container-configuration.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der Container für die Anomalieerkennung sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Anomalieerkennung_ in Ihrem Azure-Konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Anomalieerkennung kennengelernt. Zusammenfassung:

* Die Anomalieerkennung stellt einen Linux-Container für Docker bereit, der die Anomalieerkennung kapselt. Sie kann auf Batches oder Streams ausgeführt werden und bietet Bereichsrückschlüsse sowie eine Optimierung der Genauigkeit.
* Containerimages werden aus einer privaten Azure Container Registry-Instanz für Container heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Anomalieerkennung über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. die analysierten Zeitreihendaten) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](anomaly-detector-container-configuration.md).
* [Bereitstellen eines Containers für die Anomalieerkennung in Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)