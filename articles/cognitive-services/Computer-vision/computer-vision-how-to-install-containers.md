---
title: Installieren und Ausführen von Containern – maschinelles Sehen
titlesuffix: Azure Cognitive Services
description: Informationen zum Herunterladen, Installieren und Ausführen von Containern für maschinelles Sehen in diesem Schritt-für-Schritt-Tutorial.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 06/10/2019
ms.author: IEvangelist
ms.custom: seodec18
ms.openlocfilehash: 49e3af546ffcf5be47c73bfe633223a4d968d887
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060924"
---
# <a name="install-and-run-recognize-text-containers"></a>Installieren und Ausführen von Texterkennungscontainern

Der Texterkennungsteil des maschinelles Sehens ist auch als Docker-Container verfügbar. Damit können Sie gedruckten Text in Bildern von verschiedensten Objekten mit unterschiedlichen Oberflächen und Hintergründen erkennen und extrahieren. Hierzu zählen z.B. Belege, Poster, und Visitenkarten.  
> [!IMPORTANT]
> Der Texterkennungscontainer funktioniert derzeit nur für Englisch.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des Containers für die Texterkennung müssen Sie die folgenden Voraussetzungen erfüllen:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Azure `Cognitive Services`-Ressource |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure _Cognitive Services_-Ressource, den zugehörigen Abrechnungsschlüssel und den URI des Abrechnungsendpunkts. Beide Werte stehen auf der Übersichts- und auf der Schlüsselseite der Ressource zur Verfügung und werden zum Starten des Containers benötigt. Sie müssen, wie im folgenden Beispiel BILLING_ENDPOINT_URI dargestellt, dem Endpunkt-URI das `vision/v2.0`-Routing hinzufügen. <br><br>**{BILLING_KEY}** : Der Ressourcenschlüssel.<br><br>**{BILLING_ENDPOINT_URI}** : Der Endpunkt-URI. Beispiel: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Anfordern des Zugriffs auf die private Containerregistrierung

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für CPU-Kerne und Arbeitsspeicher beschrieben, die jedem Container für die Texterkennung zugeordnet werden.

| Container | Minimum | Empfohlen |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Texterkennung|1 Kern, 8 GB Arbeitsspeicher, 0,5 TPS|2 Kerne, 8 GB Arbeitsspeicher, 1 TPS|0,5, 1|

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* TPS: Transaktionen pro Sekunde

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Es stehen Containerimages für die Texterkennung zur Verfügung. 

| Container | Repository |
|-----------|------------|
|Texterkennung | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage herunterzuladen.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-Pullvorgang für den Container für die Texterkennung

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](computer-vision-resource-container-config.md) für den Befehl `docker run` verfügbar. 
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Für den Befehl werden die folgenden Parameter verwendet:

| Platzhalter | Wert |
|-------------|-------|
|{BILLING_KEY} | Dieser Schlüssel wird zum Starten des Containers verwendet und ist auf der Schlüsselseite von Azure `Cognitive Services` verfügbar.  |
|{BILLING_ENDPOINT_URI} | Der Wert für den URI des Abrechnungsendpunkts. Ein Beispiel ist `https://westus.api.cognitive.microsoft.com/vision/v2.0`.|

Sie müssen, wie im folgenden Beispiel BILLING_ENDPOINT_URI dargestellt, dem Endpunkt-URI das `vision/v2.0`-Routing hinzufügen.

Ersetzen Sie im folgenden Beispiel für den Befehl `docker run` diese Parameter durch Ihre eigenen Werte.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Dieser Befehl:

* Führt einen Erkennungscontainer auf der Grundlage des Containerimages aus
* Weist einen einzelnen CPU-Kern und 4 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar. 

Es sind noch weitere [Beispiele](./computer-vision-resource-container-config.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. 

Verwenden Sie für Container-APIs den Host `https://localhost:5000`.

### <a name="asynchronous-text-recognition"></a>Asynchrone Texterkennung

Sie können die Vorgänge `POST /vision/v2.0/recognizeText` und `GET /vision/v2.0/textOperations/*{id}*` kombiniert verwenden, um gedruckten Text in einem Bild asynchron erkennen, ähnlich wie der Dienst für maschinelles Sehen die entsprechenden REST-Vorgänge verwendet. Der Texterkennungscontainer erkennt zurzeit nur gedruckten Text, keinen handgeschriebenen Text, daher wird der Parameter `mode`, der normalerweise für den Vorgang des Diensts für maschinelles Sehen angegeben wird, vom Texterkennungscontainer ignoriert.

### <a name="synchronous-text-recognition"></a>Synchrone Texterkennung

Mit dem Vorgang `POST /vision/v2.0/recognizeTextDirect` können Sie gedruckten Text in einem Bild synchron erkennen. Da dieser Vorgang synchron ist, entspricht der Anforderungstext für diesen Vorgang dem des Vorgangs `POST /vision/v2.0/recognizeText`, aber der Antworttext für diesen Vorgang ist identisch mit dem des Vorgangs `GET /vision/v2.0/textOperations/*{id}*`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](./computer-vision-resource-container-config.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben. 


## <a name="billing"></a>Abrechnung

Der Container für die Texterkennung sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Texterkennung_ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](./computer-vision-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Texterkennung kennengelernt. Zusammenfassung:

* Die Texterkennung stellt einen Linux-Container für Docker bereit, der die Texterkennung kapselt.
* Containerimages werden aus Microsoft Container Registry (MCR) in Azure heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Texterkennung über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (etwa das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](computer-vision-resource-container-config.md).
* Lesen Sie [Übersicht über maschinelles Sehen](Home.md), um weitere Informationen zur Erkennung von gedrucktem und handschriftlichem Text zu erhalten.  
* Details zu den vom Container unterstützten Methoden finden Sie unter [Maschinelles Sehen-API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Unter [Häufig gestellte Fragen (FAQ)](FAQ.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit der Funktionalität des maschinellen Sehens.
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
