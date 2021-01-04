---
title: Installieren und Ausführen von Docker-Containern für LUIS
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den LUIS-Container, um Ihre trainierte oder veröffentlichte App zu laden, und erhalten Sie lokalen Zugriff auf ihre Vorhersagen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
keywords: Lokal, Docker, Container
ms.openlocfilehash: 778fe388ae3db68d836384299a8a1c7c06e31f41
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001801"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Installieren und Ausführen von Docker-Containern für LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Mithilfe von Containern können Sie LUIS in Ihrer eigenen Umgebung verwenden. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. In diesem Artikel erfahren Sie, wie Sie einen LUIS-Container herunterladen, installieren und ausführen.

Der LUIS-Container (Language Understanding) lädt Ihr trainiertes oder veröffentlichtes Language Understanding-Modell. Als [LUIS-App](https://www.luis.ai) ermöglicht der Docker-Container den Zugriff auf die Abfragevorhersagen von den API-Endpunkten des Containers. Sie können Abfrageprotokolle vom Container erfassen und wieder in die Language Understanding-App hochladen, um die Vorhersagegenauigkeit der App zu verbessern.

Das folgende Video veranschaulicht die Verwendung dieses Containers.

[![Containerdemo für Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie für die Ausführung des LUIS-Containers die folgenden Voraussetzungen:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.|
|Azure `Cognitive Services`-Ressource und LUIS-[App-Paket](luis-how-to-start-new-app.md)-Datei |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>* Eine Azure _Cognitive Services_-Ressource, den zugehörigen Abrechnungsschlüssel und den URI des Abrechnungsendpunkts. Beide Werte stehen auf der Übersichts- und auf der Schlüsselseite der Ressource zur Verfügung und werden zum Starten des Containers benötigt. <br>* Eine trainierte oder veröffentlichte App, die als eingebundene Eingabe für den Container mit der zugehörigen App-ID gepackt ist Sie können die verpackte Datei über das LUIS-Portal oder die Erstellungs-APIs abrufen. Wenn Sie das LUIS-App-Paket von den [Erstellungs-APIs](#authoring-apis-for-package-file) erhalten, benötigen Sie auch Ihren _Erstellungsschlüssel_.<br><br>Diese Anforderungen werden verwendet, um Befehlszeilenargumente an die folgenden Variablen zu übergeben:<br><br>**{AUTHORING_KEY}** : Dieser Schlüssel wird verwendet, um das App-Paket vom LUIS-Dienst in der Cloud abzurufen und die Abfrageprotokolle wieder in die Cloud hochzuladen. Das Format ist `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}** : Diese ID wird verwendet, um die App auszuwählen. Das Format ist `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Dieser Schlüssel wird verwendet, um den Container zu starten. Sie finden den Endpunktschlüssel an zwei Orten. Zum einen finden Sie ihn im Azure-Portal in der Liste der Schlüssel der _Cognitive Services_-Ressource. Zum anderen ist der Endpunktschlüssel auch im LUIS-Portal auf der Einstellungsseite für Schlüssel und Endpunkt verfügbar. Verwenden Sie nicht den Starterschlüssel.<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Übersichtsseite angegeben ist.<br><br>[Erstellungs- und Endpunktschlüssel](luis-limits.md#key-limits) haben unterschiedliche Zwecke. Verwenden Sie sie nicht im Austausch. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Erstellungs-APIs für die Paketdatei

Erstellungs-APIs für Paket-Apps:

* [Veröffentlichte Paket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nicht veröffentlichte, nur trainierte Paket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der nachfolgenden Tabelle sind Mindestwerte und empfohlene Werte für den Containerhost aufgeführt. Ihre Anforderungen ändern sich möglicherweise mit dem Datenverkehrsvolumen.

|Container| Minimum | Empfohlen | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|Ein Kern, 2 GB Arbeitsspeicher|Ein Kern, 4 GB Arbeitsspeicher|20, 40|

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* TPS: Transaktionen pro Sekunde

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage aus dem `mcr.microsoft.com/azure-cognitive-services/language/luis`-Repository herunterzuladen.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Eine vollständige Beschreibung der verfügbaren Tags, wie das im vorherigen Befehl verwendete `latest`, finden Sie unter [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) im Docker-Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

![Prozess für die Verwendung von LUIS-Containern (Language Understanding)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportieren Sie das Paket](#export-packaged-app-from-luis) für den Container aus dem LUIS-Portal oder über LUIS-APIs.
1. Verschieben Sie die Paketdatei in das erforderliche **Eingabeverzeichnis** auf dem [Hostcomputer](#the-host-computer). Sie dürfen die LUIS-Paketdatei nicht umbenennen, ändern, überschreiben oder dekomprimieren.
1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Einstellungen für _Eingabebereitstellung_ und Abrechnung. Es sind noch weitere [Beispiele](luis-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.
1. [Fragen Sie den Vorhersageendpunkt des Containers ab](#query-the-containers-prediction-endpoint).
1. Wenn Sie mit dem Container fertig sind, [importieren Sie die Endpunktprotokolle](#import-the-endpoint-logs-for-active-learning) aus der Ausgabebereitstellung in das LUIS-Portal und [beenden](#stop-the-container) den Container.
1. Nutzen Sie im LUIS-Portal das [aktive Lernen](luis-how-to-review-endpoint-utterances.md) auf der Seite mit den **Äußerungen des Überprüfungsendpunkts**, um die App zu verbessern.

Die im Container ausgeführte App kann nicht geändert werden. Um die App im Container zu ändern, müssen Sie sie über das [LUIS-Portal](https://www.luis.ai) oder die [LUIS-Erstellungs-APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) im LUIS-Dienst ändern. Trainieren und/oder veröffentlichen Sie sie anschließend, laden Sie ein neues Paket herunter, und führen Sie den Container erneut aus.

Die LUIS-App im Container kann nicht zurück in den LUIS-Dienst exportiert werden. Nur die Abfrageprotokolle können hochgeladen werden.

## <a name="export-packaged-app-from-luis"></a>Exportieren gepackter Apps aus LUIS

Der LUIS-Container benötigt eine trainierte oder veröffentlichte LUIS-App, um auf Vorhersageabfragen zu Benutzeräußerungen zu antworten. Um die LUIS-App zu erhalten, verwenden Sie entweder die trainierte oder die veröffentlichte Paket-API.

Der Standardspeicherort ist das Unterverzeichnis `input` – relativ zum Ausführungsort des `docker run`-Befehls.

Platzieren Sie die Paketdatei in einem Verzeichnis, und verweisen Sie beim Ausführen des Docker-Containers auf dieses Verzeichnis als die Eingabebereitstellung.

### <a name="package-types"></a>Pakettypen

Das Eingabebereitstellungsverzeichnis kann gleichzeitig die App-Versionen **Bereitstellung**, **Staging** und **Mit Versionsangabe** enthalten. Alle Pakete werden eingebunden.

|Pakettyp|Abfrageendpunkt-API|Abfrageverfügbarkeit|Format des Paketdateinamens|
|--|--|--|--|
|Mit Versionsangabe|GET, POST|Nur Container|`{APP_ID}_v{APP_VERSION}.gz`|
|Staging|GET, POST|Azure und Container|`{APP_ID}_STAGING.gz`|
|Bereitstellung|GET, POST|Azure und Container|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Sie dürfen die LUIS-Paketdateien nicht umbenennen, ändern, überschreiben oder dekomprimieren.

### <a name="packaging-prerequisites"></a>Voraussetzungen für das Packen

Vor dem Packen einer LUIS-Anwendung müssen Sie über Folgendes verfügen:

|Voraussetzungen für das Packen|Details|
|--|--|
|Azure _Cognitive Services_-Ressourceninstanz|Unterstützte Regionen<br><br>USA, Westen (`westus`)<br>Europa, Westen (`westeurope`)<br>Australien, Osten (`australiaeast`)|
|Trainierte oder veröffentlichte LUIS-App|Ohne [nicht unterstützte Abhängigkeiten][unsupported-dependencies] |
|Zugriff auf das Dateisystem des [Hostcomputers](#the-host-computer) |Der Hostcomputer muss eine [Eingabebereitstellung](luis-container-configuration.md#mount-settings) zulassen.|

### <a name="export-app-package-from-luis-portal"></a>Exportieren von App-Paketen im LUIS-Portal

Das [LUIS-Portal](https://www.luis.ai) bietet die Möglichkeit, das trainierte oder veröffentlichte App-Paket zu exportieren.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportieren des Pakets einer veröffentlichten App im LUIS-Portal

Das Paket einer veröffentlichten App steht in der Liste **Meine Apps** zur Verfügung.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an.
1. Aktivieren Sie in der Liste das Kontrollkästchen links neben dem App-Namen.
1. Wählen Sie auf der kontextbezogenen Symbolleiste über der Liste die Option **Exportieren** aus.
1. Wählen Sie **Export for container (GZIP)** (Für Container exportieren (GZIP)) aus.
1. Wählen Sie die Umgebung des **Produktionsslots** oder des **Stagingslots** aus.
1. Das Paket wird über den Browser heruntergeladen.

![Exportieren des veröffentlichten Pakets für den Container über das Menü „Exportieren“ auf der App-Seite](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportieren des Pakets einer App mit Versionsangabe im LUIS-Portal

Das Paket einer App mit Versionsangabe steht in der Liste **Versionen** zur Verfügung.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an.
1. Wählen Sie die App in der Liste aus.
1. Wählen Sie auf der Navigationsleiste der App **Verwalten** aus.
1. Wählen Sie auf der linken Navigationsleiste **Versionen** aus.
1. Aktivieren Sie in der Liste das Kontrollkästchen links neben dem Versionsnamen.
1. Wählen Sie auf der kontextbezogenen Symbolleiste über der Liste die Option **Exportieren** aus.
1. Wählen Sie **Export for container (GZIP)** (Für Container exportieren (GZIP)) aus.
1. Das Paket wird über den Browser heruntergeladen.

![Exportieren des trainierten Pakets für den Container über das Menü „Exportieren“ auf der Versionsseite](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Exportieren des Pakets einer veröffentlichten App über die API

Verwenden Sie die folgende REST-API-Methode, um eine LUIS-App zu packen, die Sie bereits [veröffentlicht](luis-how-to-publish-app.md) haben. Ersetzen Sie die entsprechenden Platzhalterwerte im API-Aufruf durch Ihre eigenen Werte. Verwenden Sie dazu die Tabelle unter der HTTP-Spezifikation.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platzhalter | Wert |
|-------------|-------|
| **{APP_ID}** | Die Anwendungs-ID der veröffentlichten LUIS-App. |
| **{SLOT_NAME}** | Die Umgebung der veröffentlichten LUIS-App. Verwenden Sie einen der folgenden Werte:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Der Erstellungsschlüssel des LUIS-Kontos für die veröffentlichte LUIS-App.<br/>Sie finden Ihren Erstellungsschlüssel im LUIS-Portal auf der Seite **Benutzereinstellungen**. |
| **{AZURE_REGION}** | Die entsprechende Azure-Region:<br/><br/>`westus`: USA, Westen<br/>`westeurope`: Europa, Westen<br/>`australiaeast`: Australien, Osten |

Weitere Informationen zum Herunterladen des veröffentlichten Pakets finden Sie in der [API-Dokumentation][download-published-package]. Beim erfolgreichen Herunterladen ist die Antwort eine LUIS-Paketdatei. Speichern Sie die Datei an dem Speicherort, der für die Eingabebereitstellung des Containers angegeben wurde.

### <a name="export-versioned-apps-package-from-api"></a>Exportieren des Pakets einer App mit Versionsangabe über die API

Verwenden Sie die folgende REST-API-Methode, um eine LUIS-Anwendung zu packen, die Sie bereits [trainiert](luis-how-to-train.md) haben. Ersetzen Sie die entsprechenden Platzhalterwerte im API-Aufruf durch Ihre eigenen Werte. Verwenden Sie dazu die Tabelle unter der HTTP-Spezifikation.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platzhalter | Wert |
|-------------|-------|
| **{APP_ID}** | Die Anwendungs-ID der trainierten LUIS-App. |
| **{APP_VERSION}** | Die Anwendungsversion der trainierten LUIS-App. |
| **{AUTHORING_KEY}** | Der Erstellungsschlüssel des LUIS-Kontos für die veröffentlichte LUIS-App.<br/>Sie finden Ihren Erstellungsschlüssel im LUIS-Portal auf der Seite **Benutzereinstellungen**. |
| **{AZURE_REGION}** | Die entsprechende Azure-Region:<br/><br/>`westus`: USA, Westen<br/>`westeurope`: Europa, Westen<br/>`australiaeast`: Australien, Osten |

Weitere Informationen zum Herunterladen des Pakets mit Versionsangabe finden Sie in der [API-Dokumentation][download-versioned-package]. Beim erfolgreichen Herunterladen ist die Antwort eine LUIS-Paketdatei. Speichern Sie die Datei an dem Speicherort, der für die Eingabebereitstellung des Containers angegeben wurde.

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Genaue Informationen dazu, wie Sie die Werte `{ENDPOINT_URI}` und `{API_KEY}` abrufen, erhalten Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters).

Es sind [Beispiele](luis-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* In diesem Beispiel wird das Verzeichnis auf dem Laufwerk `C:` verwendet, um Berechtigungskonflikte in Windows zu vermeiden. Wenn Sie ein bestimmtes Verzeichnis als Eingabeverzeichnis verwenden möchten, müssen Sie dem Docker-Dienst möglicherweise die erforderliche Berechtigung gewähren.
* Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern vertraut sind.
* Wenn Sie ein anderes Betriebssystem verwenden, verwenden Sie beim Einbinden die richtige Konsole/das richtige Terminal, die richtige Ordnersyntax und das richtige Zeilenfortsetzungszeichen für Ihr System. In diesen Beispielen wird von einer Windows-Konsole mit dem Zeilenfortsetzungszeichen `^` ausgegangen. Da der Container ein Linux-Betriebssystem ist, verwendet die Zieleinbindung eine linuxartige Ordnersyntax.

Dieser Befehl:

* Führt einen Container über das LUIS-Containerimage aus
* Lädt die LUIS-App aus der Eingabebereitstellung unter *C:\input* auf dem Containerhost
* Weist zwei CPU-Kerne und 4 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Speichert Container und LUIS-Protokolle in der Ausgabebereitstellung unter *C:\output* auf dem Containerhost
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

Es sind noch weitere [Beispiele](luis-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Seite **Azure-Ressourcen** im LUIS-Portal. Er ist auch auf der Seite mit den Schlüsseln der Azure `Cognitive Services`-Ressourcen verfügbar.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Vom Container unterstützte Endpunkt-APIs

Sowohl die V2- als auch [V3](luis-migration-api-v3.md)-Version der API ist mit dem Container verfügbar.

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. Endpunkte für veröffentlichte Apps (Staging oder Produktion) nutzen eine _andere_ Route als Endpunkte für Apps mit Versionsangabe.

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.

# <a name="v3-prediction-endpoint"></a>[V3-Vorhersageendpunkt](#tab/v3)

|Pakettyp|HTTP-Verb|Route|Abfrageparameter|
|--|--|--|--|
|Veröffentlicht|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Mit Versionsangabe|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Die Abfrageparameter legen fest, was auf welche Weise in der Abfrageantwort zurückgegeben wird:

|Query parameter (Abfrageparameter)|type|Zweck|
|--|--|--|
|`query`|Zeichenfolge|Die Äußerung des Benutzers.|
|`verbose`|boolean|Ein boolescher Wert, der angibt, ob alle Metadaten für die vorhergesagten Modelle zurückgegeben werden sollen. Der Standardwert ist "false".|
|`log`|boolean|Protokolliert Abfragen, die später für [aktives Lernen](luis-how-to-review-endpoint-utterances.md) verwendet werden können. Der Standardwert ist "false".|
|`show-all-intents`|boolean|Ein boolescher Wert, der angibt, ob alle Absichten zurückgegeben werden sollen oder nur die Absicht mit der höchsten Bewertung. Der Standardwert ist "false".|

# <a name="v2-prediction-endpoint"></a>[V2-Vorhersageendpunkt](#tab/v2)

|Pakettyp|HTTP-Verb|Route|Abfrageparameter|
|--|--|--|--|
|Veröffentlicht|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Mit Versionsangabe|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Die Abfrageparameter legen fest, was auf welche Weise in der Abfrageantwort zurückgegeben wird:

|Query parameter (Abfrageparameter)|type|Zweck|
|--|--|--|
|`q`|Zeichenfolge|Die Äußerung des Benutzers.|
|`timezoneOffset`|number|Das „timeZoneOffset“ ermöglicht das [Ändern der Zeitzone](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity), die von der vordefinierten datetimeV2-Entität verwendet wird.|
|`verbose`|boolean|Gibt bei Festlegung auf TRUE alle Absichten und deren Bewertungen zurück. Der Standardwert ist FALSE, bei dem nur die Hauptabsicht zurückgegeben wird.|
|`staging`|boolean|Gibt bei Festlegung auf TRUE die Abfrage aus den Ergebnissen der Stagingumgebung zurück. |
|`log`|boolean|Protokolliert Abfragen, die später für [aktives Lernen](luis-how-to-review-endpoint-utterances.md) verwendet werden können. Der Standardwert ist "true".|

**_

### <a name="query-the-luis-app"></a>Abfragen der LUIS-App

Ein Beispiel für einen cURL-Befehl zum Abfragen des Containers für eine veröffentlichte App lautet:

# <a name="v3-prediction-endpoint"></a>[V3-Vorhersageendpunkt](#tab/v3)

Verwenden Sie die folgende API, um ein Modell in einem Slot abzufragen:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Ersetzen Sie zum Abfragen der _ *Stagingumgebung** `production` in der Route durch `staging`:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Verwenden Sie die folgende API, um ein Modell mit Versionsangabe abzufragen:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2-Vorhersageendpunkt](#tab/v2)

Verwenden Sie die folgende API, um ein Modell in einem Slot abzufragen:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Für Abfragen an die **Stagingumgebung** ändern Sie den Parameter **staging** in der Abfragezeichenfolge in TRUE:

`staging=true`

Verwenden Sie die folgende API, um ein Modell mit Versionsangabe abzufragen:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Der Versionsname enthält maximal 10 Zeichen, die auch in einer URL zulässig wären.

**_

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importieren der Endpunktprotokolle für aktives Lernen

Wenn eine Ausgabebereitstellung für den LUIS-Container angegeben wurde, werden Protokolldateien von App-Abfragen im Ausgabeverzeichnis gespeichert, wobei `{INSTANCE_ID}` die Container-ID ist. Das Abfrageprotokoll für die App enthält die Abfrage, die Antwort und die Zeitstempel für jede Vorhersageabfrage, die an den LUIS-Container gesendet wurde.

Der folgende Pfad veranschaulicht die geschachtelte Verzeichnisstruktur für die Protokolldateien des Containers.
```
/output/luis/{INSTANCE_ID}/
```

Wählen Sie Ihre App im LUIS-Portal aus, und klicken Sie dann auf _ *Endpunktprotokolle importieren**, um diese Protokolle hochzuladen.

![Importieren der Protokolldateien des Containers für aktives Lernen](./media/luis-container-how-to/upload-endpoint-log-files.png)

Nachdem Sie das Protokoll hochgeladen haben, [überprüfen Sie die Endpunktäußerungen](./luis-concept-review-endpoint-utterances.md) im LUIS-Portal.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

Zum Herunterfahren des Containers drücken Sie in der Befehlszeilenumgebung, in der der Container ausgeführt wird, **STRG+C**.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](luis-container-configuration.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der LUIS-Container sendet Abrechnungsinformationen an Azure und verwendet dafür eine entsprechende _Cognitive Services_-Ressource in Ihrem Azure-Konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von LUIS-Containern (Language Understanding) kennengelernt. Zusammenfassung:

* Language Understanding (LUIS) bietet einen Linux-Container für Docker, der Vorhersagen für Endpunktabfragen zu Äußerungen bereitstellt.
* Containerimages werden aus Microsoft Container Registry (MCR) heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können die REST-API verwenden, um die Containerendpunkte abzufragen, indem Sie den Host-URI des Containers angeben.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (etwa das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie [Konfigurieren von Containern](luis-container-configuration.md), um sich über Konfigurationseinstellungen zu informieren.
* Informationen zu bekannten Funktionseinschränkungen finden Sie unter [Installieren und Ausführen von Docker-Containern für LUIS](luis-container-limitations.md).
* Unter [Problembehandlung ](troubleshooting.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit LUIS-Funktionen.
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container