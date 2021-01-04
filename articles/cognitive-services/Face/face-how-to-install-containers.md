---
title: Installieren und Ausführen von Docker-Containern für die Gesichtserkennungs-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den Docker-Container für die Gesichtserkennungs-API, um menschliche Gesichter in Bildern zu erkennen und zu identifizieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: Lokal, Docker, Container, Identifizieren
ms.openlocfilehash: 0f6807f771510f85c5a20cfb2a160cfe1e8726a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000544"
---
# <a name="install-and-run-face-containers-preview"></a>Installieren und Ausführen von Containern für die Gesichtserkennung (Vorschauversion)

> [!IMPORTANT]
> Der Grenzwert für Benutzer von Gesichtserkennungscontainern wurde erreicht. Wir akzeptieren derzeit keine neuen Anträge für den Gesichtserkennungscontainer.

Die Gesichtserkennungs-API von Azure Cognitive Services bietet einen Linux-Docker-Container, der menschliche Gesichter in Bildern erkennt und analysiert. Sie identifiziert auch zugehörige Attribute wie Gesichtsmerkmale (z. B. Nasen und Augen), Geschlecht, Alter und andere vom Computer vorhergesagte Gesichtsmerkmale. Zusätzlich zur Erkennung kann die Gesichtserkennung anhand einer Zuverlässigkeitsbewertung überprüfen, ob zwei Gesichter auf demselben Bild oder auf verschiedenen Bildern identisch sind. Die Gesichtserkennung kann auch Gesichter anhand einer Datenbank vergleichen, um zu prüfen, ob bereits ein ähnliches oder identisches Gesicht vorhanden ist. Sie kann auch ähnliche Gesichter in Gruppen mit gemeinsamen Gesichtsmerkmalen organisieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des Containers für den Gesichtserkennungsdienst müssen die folgenden Voraussetzungen erfüllt sein.

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> Unter Windows muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie müssen über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen. Außerdem müssen Sie die grundlegenden `docker`-Befehle kennen.| 
|Gesichtserkennungsressource |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ **Gesichtserkennung** sowie den zugehörigen API-Schlüssel und Endpunkt-URI. Beide Werte stehen auf den Seiten **Übersicht** und **Schlüssel** der Ressource zur Verfügung. Sie sind zum Starten des Containers erforderlich.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für CPU-Kerne und Arbeitsspeicher beschrieben, die jedem Container für den Gesichtserkennungsdienst zugeordnet werden müssen.

| Container | Minimum | Empfohlen | Transaktionen pro Sekunde<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Gesicht | Ein Kern, 2 GB Arbeitsspeicher | Ein Kern, 4 GB Arbeitsspeicher |10, 20|

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* Transaktionen pro Sekunde (TPS)

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit dem Befehl „docker pull“

Es stehen Containerimages für den Gesichtserkennungsdienst zur Verfügung. 

| Container | Repository |
|-----------|------------|
| Gesicht | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker-Pullvorgang für den Container für die Gesichtserkennung

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](./face-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit „docker run“

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Genaue Informationen dazu, wie Sie die Werte `{ENDPOINT_URI}` und `{API_KEY}` abrufen, erhalten Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters).

Es sind [Beispiele](face-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die Gesichtserkennung auf der Grundlage des Containerimages aus
* Weist einen einzelnen CPU-Kern und 4 GB Arbeitsspeicher zu
* Macht TCP-Ports 5000 verfügbar und weist eine Pseudo-TTY-Verbindung für den Container zu
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar. 

Es sind noch weitere [Beispiele](./face-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet. Weitere Informationen finden Sie unter [Abrechnung](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. 

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](./face-resource-container-config.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der Container für den Gesichtserkennungsdienst sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ „Gesichtserkennung“ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für den Gesichtserkennungsdienst kennengelernt. Zusammenfassung:

* Containerimages werden aus Azure Container Registry heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für den Gesichtserkennungsdienst über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](face-resource-container-config.md).
* Weitere Informationen zum Erkennen und Identifizieren von Gesichtern finden Sie unter [Übersicht über die Gesichtserkennung](Overview.md).
* Informationen zu den vom Container unterstützten Methoden finden Sie unter [Gesichtserkennungs-API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Informationen zur Verwendung weiterer Cognitive Services-Container finden Sie unter [Cognitive Services-Container](../cognitive-services-container-support.md).
