---
title: Docker-Container
titleSuffix: Language Understanding - Azure Cognitive Services
description: Der LUIS-Container lädt Ihre trainierte oder veröffentlichte App in einen Docker-Container und ermöglicht den Zugriff auf die Abfragevorhersagen von den API-Endpunkten des Containers.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 68ff6a156e0d159816b184452f1f945cbce65216
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052026"
---
# <a name="install-and-run-luis-docker-containers"></a>Installieren und Ausführen von Docker-Containern für LUIS
 
Der LUIS-Container (Language Understanding) lädt Ihr trainiertes oder veröffentlichtes Language Understanding-Modell (auch als [LUIS-App](https://www.luis.ai) bezeichnet) in einen Docker-Container und ermöglicht den Zugriff auf die Abfragevorhersagen von den API-Endpunkten des Containers. Sie können Abfrageprotokolle vom Container erfassen und wieder in die Language Understanding-App hochladen, um die Vorhersagegenauigkeit der App zu verbessern.

Das folgende Video veranschaulicht die Verwendung dieses Containers.

[![Containerdemo für Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um den LUIS-Container auszuführen, benötigen Sie Folgendes: 

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Azure `Cognitive Services`-Ressource und LUIS-[App-Paket](luis-how-to-start-new-app.md#export-app-for-containers)-Datei |Um den Container zu verwenden, benötigen Sie Folgendes:<br><br>* Eine Azure _Cognitive Services_-Ressource, den zugehörigen Abrechnungsschlüssel und den URI des Abrechnungsendpunkts. Beide Werte stehen auf der Übersichts- und auf der Schlüsselseite der Ressource zur Verfügung und werden zum Starten des Containers benötigt. Sie müssen, wie im folgenden Beispiel BILLING_ENDPOINT_URI dargestellt, dem Endpunkt-URI das `luis/v2.0`-Routing hinzufügen. <br>* Eine trainierte oder veröffentlichte App, die als eingebundene Eingabe für den Container mit der zugehörigen App-ID gepackt ist Sie können die verpackte Datei über das LUIS-Portal oder die Erstellungs-APIs abrufen. Wenn Sie das LUIS-App-Paket von den [Erstellungs-APIs](#authoring-apis-for-package-file) erhalten, benötigen Sie auch Ihren _Erstellungsschlüssel_.<br><br>Diese Anforderungen werden verwendet, um Befehlszeilenargumente an die folgenden Variablen zu übergeben:<br><br>**{AUTHORING_KEY}** : Dieser Schlüssel wird verwendet, um das App-Paket vom LUIS-Dienst in der Cloud abzurufen und die Abfrageprotokolle wieder in die Cloud hochzuladen. Das Format ist `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Diese ID wird verwendet, um die App auszuwählen. Das Format ist `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}** : Dieser Schlüssel wird verwendet, um den Container zu starten. Sie finden den Endpunktschlüssel an zwei Orten. Zum einen finden Sie ihn im Azure-Portal in der Liste der Schlüssel der _Cognitive Services_-Ressource. Zum anderen ist der Endpunktschlüssel auch im LUIS-Portal auf der Einstellungsseite für Schlüssel und Endpunkt verfügbar. Verwenden Sie nicht den Starterschlüssel.<br><br>**{BILLING_ENDPOINT}** : Ein Beispiel ist `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>[Erstellungs- und Endpunktschlüssel](luis-boundaries.md#key-limits) haben unterschiedliche Zwecke. Verwenden Sie sie nicht im Austausch. |

### <a name="authoring-apis-for-package-file"></a>Erstellungs-APIs für die Paketdatei

Erstellungs-APIs für Paket-Apps:

* [Veröffentlichte Paket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nicht veröffentlichte, nur trainierte Paket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

Dieser Container unterstützt Mindestwerte und empfohlene Werte für folgende Einstellungen:

|Container| Minimum | Empfohlen | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|1 Kern, 2 GB Arbeitsspeicher|1 Kern, 4 GB Arbeitsspeicher|20,40|

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* TPS: Transaktionen pro Sekunde

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage aus dem `mcr.microsoft.com/azure-cognitive-services/luis`-Repository herunterzuladen.

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage herunterzuladen.

Eine vollständige Beschreibung der verfügbaren Tags, wie das im vorherigen Befehl verwendete `latest`, finden Sie unter [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) im Docker-Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

![Prozess für die Verwendung von LUIS-Containern (Language Understanding)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportieren Sie das Paket](#export-packaged-app-from-luis) für den Container aus dem LUIS-Portal oder über LUIS-APIs.
1. Verschieben Sie die Paketdatei in das erforderliche **Eingabeverzeichnis** auf dem [Hostcomputer](#the-host-computer). Sie dürfen die LUIS-Paketdatei nicht umbenennen, ändern, überschreiben oder dekomprimieren.
1. [Führen Sie den Container aus](##run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Einstellungen für _Eingabebereitstellung_ und Abrechnung. Es sind noch weitere [Beispiele](luis-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 
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

Das Eingabebereitstellungsverzeichnis kann gleichzeitig die App-Versionen **Bereitstellung**, **Staging** und **Trainiert** enthalten. Alle Pakete werden eingebunden. 

|Pakettyp|Abfrageendpunkt-API|Abfrageverfügbarkeit|Format des Paketdateinamens|
|--|--|--|--|
|Trainiert|Get, Post|Nur Container|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Staging|Get, Post|Azure und Container|`{APPLICATION_ID}_STAGING.gz`|
|Bereitstellung|Get, Post|Azure und Container|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Sie dürfen die LUIS-Paketdateien nicht umbenennen, ändern, überschreiben oder dekomprimieren.

### <a name="packaging-prerequisites"></a>Voraussetzungen für das Packen

Vor dem Packen einer LUIS-Anwendung müssen Sie über Folgendes verfügen:

|Voraussetzungen für das Packen|Details|
|--|--|
|Azure _Cognitive Services_-Ressourceninstanz|Unterstützte Regionen<br><br>USA, Westen (```westus```)<br>Europa, Westen (```westeurope```)<br>Australien, Osten (```australiaeast```)|
|Trainierte oder veröffentlichte LUIS-App|Ohne [nicht unterstützte Abhängigkeiten](#unsupported-dependencies) |
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

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportieren des Pakets einer trainierten App im LUIS-Portal

Das Paket einer trainierten App steht in der Liste **Versionen** zur Verfügung. 

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
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platzhalter | Wert |
|-------------|-------|
|{APPLICATION_ID} | Die Anwendungs-ID der veröffentlichten LUIS-App. |
|{APPLICATION_ENVIRONMENT} | Die Umgebung der veröffentlichten LUIS-App. Verwenden Sie einen der folgenden Werte:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Der Erstellungsschlüssel des LUIS-Kontos für die veröffentlichte LUIS-App.<br/>Sie finden Ihren Erstellungsschlüssel im LUIS-Portal auf der Seite **Benutzereinstellungen**. |
|{AZURE_REGION} | Die entsprechende Azure-Region:<br/><br/>```westus```: USA, Westen<br/>```westeurope```: Europa, Westen<br/>```australiaeast```: Australien, Osten |

Verwenden Sie den folgenden cURL-Befehl, um das veröffentlichte Paket herunterzuladen. Verwenden Sie dabei Ihre eigenen Werte:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

Bei einer erfolgreichen Ausführung ist die Antwort eine LUIS-Paketdatei. Speichern Sie die Datei an dem Speicherort, der für die Eingabebereitstellung des Containers angegeben wurde. 

### <a name="export-trained-apps-package-from-api"></a>Exportieren des Pakets einer trainierten App über die API

Verwenden Sie die folgende REST-API-Methode, um eine LUIS-Anwendung zu packen, die Sie bereits [trainiert](luis-how-to-train.md) haben. Ersetzen Sie die entsprechenden Platzhalterwerte im API-Aufruf durch Ihre eigenen Werte. Verwenden Sie dazu die Tabelle unter der HTTP-Spezifikation.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platzhalter | Wert |
|-------------|-------|
|{APPLICATION_ID} | Die Anwendungs-ID der trainierten LUIS-Anwendung. |
|{APPLICATION_VERSION} | Die Anwendungsversion der trainierten LUIS-Anwendung. |
|{AUTHORING_KEY} | Der Erstellungsschlüssel des LUIS-Kontos für die veröffentlichte LUIS-App.<br/>Sie finden Ihren Erstellungsschlüssel im LUIS-Portal auf der Seite **Benutzereinstellungen**.  |
|{AZURE_REGION} | Die entsprechende Azure-Region:<br/><br/>```westus```: USA, Westen<br/>```westeurope```: Europa, Westen<br/>```australiaeast```: Australien, Osten |

Verwenden Sie den folgenden cURL-Befehl, um das trainierte Paket herunterzuladen:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

Bei einer erfolgreichen Ausführung ist die Antwort eine LUIS-Paketdatei. Speichern Sie die Datei an dem Speicherort, der für die Eingabebereitstellung des Containers angegeben wurde. 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Für den Befehl werden die folgenden Parameter verwendet:

| Platzhalter | Wert |
|-------------|-------|
|{ENDPOINT_KEY} | Dieser Schlüssel wird verwendet, um den Container zu starten. Verwenden Sie nicht den Starterschlüssel. |
|{BILLING_ENDPOINT} | Den Wert des Abrechnungsendpunkts finden Sie auf der Übersichtsseite von `Cognitive Services` im Azure-Portal. Sie müssen die `luis/v2.0`-Weiterleitung dem Endpunkt-URI wie im folgenden Beispiel hinzufügen: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.|

Ersetzen Sie im folgenden Beispiel für den Befehl `docker run` diese Parameter durch Ihre eigenen Werte. Führen Sie den Befehl in der Windows-Konsole aus.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

* In diesem Beispiel wird das Verzeichnis auf dem Laufwerk `c:` verwendet, um Berechtigungskonflikte in Windows zu vermeiden. Wenn Sie ein bestimmtes Verzeichnis als Eingabeverzeichnis verwenden möchten, müssen Sie dem Docker-Dienst möglicherweise die erforderliche Berechtigung gewähren. 
* Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.
* Wenn Sie ein anderes Betriebssystem verwenden, verwenden Sie beim Einbinden die richtige Konsole/das richtige Terminal, die richtige Ordnersyntax und das richtige Zeilenfortsetzungszeichen für Ihr System. In diesen Beispielen wird von einer Windows-Konsole mit dem Zeilenfortsetzungszeichen `^` ausgegangen. Da der Container ein Linux-Betriebssystem ist, verwendet die Zieleinbindung eine linuxartige Ordnersyntax.



Dieser Befehl:

* Führt einen Container über das LUIS-Containerimage aus
* Lädt die LUIS-App aus der Eingabebereitstellung unter „C:\input“ auf dem Containerhost
* Weist zwei CPU-Kerne und 4 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Speichert Container und LUIS-Protokolle in der Ausgabebereitstellung unter „C:\output“ auf dem Containerhost
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar. 

Es sind noch weitere [Beispiele](luis-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
> Der Wert ApiKey ist der **Schlüssel** von der Seite „Schlüssel und Endpunkte“ im LUIS-Portal. Er ist auch auf der Seite mit den Schlüsseln der Azure `Cognitive Services`-Ressourcen verfügbar.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Vom Container unterstützte Endpunkt-APIs

Sowohl V2- als auch [V3-Versionen (Vorschau)](luis-migration-api-v3.md) der API sind mit dem Container verfügbar. 

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit. Endpunkte für veröffentlichte (Staging oder Produktion) Apps nutzen eine _andere_ Route als Endpunkte für trainierte Apps. 

Verwenden Sie für Container-APIs den Host `https://localhost:5000`. 

|Pakettyp|Methode|Weiterleiten|Abfrageparameter|
|--|--|--|--|
|Veröffentlicht|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{App-ID}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|Trainiert|Get, Post|/luis/v2.0/apps/{App-ID}/versions/{Versions-ID}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

Die Abfrageparameter legen fest, was auf welche Weise in der Abfrageantwort zurückgegeben wird:

|Query parameter (Abfrageparameter)|Type|Zweck|
|--|--|--|
|`q`|Zeichenfolge|Die Äußerung des Benutzers.|
|`timezoneOffset`|number|Das „timeZoneOffset“ ermöglicht das [Ändern der Zeitzone](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity), die von der vordefinierten datetimeV2-Entität verwendet wird.|
|`verbose`|boolean|Gibt bei Festlegung auf TRUE alle Absichten und deren Bewertungen zurück. Der Standardwert ist FALSE, bei dem nur die Hauptabsicht zurückgegeben wird.|
|`staging`|boolean|Gibt bei Festlegung auf TRUE die Abfrage aus den Ergebnissen der Stagingumgebung zurück. |
|`log`|boolean|Protokolliert Abfragen, die später für [aktives Lernen](luis-how-to-review-endpoint-utterances.md) verwendet werden können. Der Standardwert ist TRUE.|

### <a name="query-published-app"></a>Abfragen veröffentlichter Apps

Ein Beispiel für einen cURL-Befehl zum Abfragen des Containers für eine veröffentlichte App lautet:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Für Abfragen an die **Stagingumgebung** ändern Sie den Parameter **staging** in der Abfragezeichenfolge in TRUE: 

`staging=true`

### <a name="query-trained-app"></a>Abfragen trainierter Apps

Ein Beispiel für einen cURL-Befehl zum Abfragen des Containers für eine trainierte App lautet: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Der Versionsname enthält maximal 10 Zeichen, die auch in einer URL zulässig wären. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importieren der Endpunktprotokolle für aktives Lernen

Wenn eine Ausgabebereitstellung für den LUIS-Container angegeben wird, werden Protokolldateien von App-Abfragen im Ausgabeverzeichnis gespeichert, wobei {INSTANCE_ID} die Container-ID ist. Das Abfrageprotokoll für die App enthält die Abfrage, die Antwort und die Zeitstempel für jede Vorhersageabfrage, die an den LUIS-Container gesendet wurde. 

Der folgende Pfad veranschaulicht die geschachtelte Verzeichnisstruktur für die Protokolldateien des Containers.
```
/output/luis/{INSTANCE_ID}/
```
 
Wählen Sie Ihre App im LUIS-Portal aus, und wählen Sie dann **Endpunktprotokolle importieren** aus, um diese Protokolle hochzuladen. 

![Importieren der Protokolldateien des Containers für aktives Lernen](./media/luis-container-how-to/upload-endpoint-log-files.png)

Nachdem Sie das Protokoll hochgeladen haben, [überprüfen Sie die Endpunktäußerungen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) im LUIS-Portal.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

Zum Herunterfahren des Containers drücken Sie in der Befehlszeilenumgebung, in der der Container ausgeführt wird, **STRG+C**.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](luis-container-configuration.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben. 

## <a name="billing"></a>Abrechnung

Der LUIS-Container sendet Abrechnungsinformationen an Azure und verwendet dafür eine entsprechende _Cognitive Services_-Ressource in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Unterstützte Abhängigkeiten für `latest`-Container

Der neueste auf der Microsoft Build 2019 veröffentlichte Container unterstützt Folgendes:

* Bing-Rechtschreibprüfung: Anforderungen an den Endpunkt der Abfragevorhersage mit Parametern des Typs `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` für Abfragezeichenfolgen. Im Tutorial [Bing-Rechtschreibprüfung V7](luis-tutorial-bing-spellcheck.md) erfahren Sie mehr. Wenn dieses Feature verwendet wird, sendet der Container die Äußerung an die Bing-Rechtschreibprüfung V7.
* [Neue vordefinierte Bereiche](luis-reference-prebuilt-domains.md): Zu diesen auf Unternehmen fokussierten Domänen gehören Entitäten, Beispieläußerungen und Muster. Erweitern Sie diese Domänen für Ihre eigenen Zwecke. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Nicht unterstützte Abhängigkeiten für `latest`-Container

Wenn Ihre LUIS-App nicht unterstützte Abhängigkeiten hat, ist der [Export für Container](#export-packaged-app-from-luis) erst möglich, nachdem Sie die nicht unterstützten Features entfernt haben. Wenn Sie den Export für Container versuchen, meldet das LUIS-Portal, dass Sie nicht unterstützte Features entfernen müssen.

Sie können eine LUIS-Anwendung verwenden, sofern diese **keine** der folgenden Abhängigkeiten enthält:

Nicht unterstützte App-Konfigurationen|Details|
|--|--|
|Nicht unterstützte Containerkulturen| Niederländisch (nl-NL)<br>Japanisch (ja-JP)<br>Deutsch wird nur ab [Tokenizer 1.0.1](luis-language-support.md#custom-tokenizer-versions) unterstützt.|
|Nicht unterstützte Entitäten für alle Kulturen|Vordefinierte [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase)-Entität für alle Kulturen|
|Nicht unterstützte Entitäten für die Kultur Englisch (en-US)|Vordefinierte [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2)-Entitäten|
|Sprachvorbereitung|Externe Abhängigkeiten werden im Container nicht unterstützt.|
|Stimmungsanalyse|Externe Abhängigkeiten werden im Container nicht unterstützt.|

<!--blogs/samples/video coures -->

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

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](luis-container-configuration.md).
* Unter [Problembehandlung ](troubleshooting.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit LUIS-Funktionen.
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
