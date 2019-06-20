---
title: Installieren von Speech-Containern
titleSuffix: Azure Cognitive Services
description: Installieren Sie Speech-Container, und führen Sie sie aus. Die Spracherkennung wandelt Audiodatenströme in Echtzeit in Text um, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. Die Sprachsynthese konvertiert Eingabetext in menschenähnliche synthetische Sprache.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: 763e7bc9298eee1ab602968360bbc79a58243e5b
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752444"
---
# <a name="install-and-run-speech-service-containers"></a>Installieren und Ausführen von Containern für den Speech-Dienst

Mit Speech-Containern können Kunden eine Speech-basierte Anwendungsarchitektur erstellen, die sowohl von widerstandsfähigen Cloudfunktionen als auch von der Edgeposition profitieren kann. 

Die beiden Sprachcontainer sind **Spracherkennung** und **Sprachsynthese**. 

|Funktion|Features|Neueste Version|
|-|-|--|
|Spracherkennung| <li>Transkribiert kontinuierliche Echtzeitsprache oder Batchaudioaufzeichnungen in Text mit Zwischenergebnissen.|1.1.1|
|Sprachsynthese| <li>Konvertiert Text in natürlich klingende Sprache. mit Nur-Text-Eingabe und SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese). |1.1.0|

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung von Speech-Containern müssen folgende Voraussetzungen erfüllt sein:

|Erforderlich|Zweck|
|--|--|
|Docker-Engine| Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br>|
|Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen.| 
|Speech-Ressource |Um diese Container verwenden zu können, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ _Speech_, um den entsprechenden Abrechnungsschlüssel und den URI des Abrechnungsendpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf der Übersichts- und auf der Schlüsselseite für **Speech** zur Verfügung und werden zum Starten des Containers benötigt.<br><br>**{BILLING_KEY}** : Der Ressourcenschlüssel.<br><br>**{BILLING_ENDPOINT_URI}** : Der Endpunkt-URI. Beispiel: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Anfordern des Zugriffs auf die Containerregistrierung

Sie müssen zuerst das [Formular zum Anfordern von Cognitive Services-Speech-Containern](https://aka.ms/speechcontainerspreview/) ausfüllen und übermitteln, um Zugriff auf den Container anzufordern. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Unterstützung von Advanced Vector Extensions

Der **Host** ist der Computer, auf dem der Docker-Container ausgeführt wird. Der Host muss [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) unterstützen. Auf Linux-Hosts kann diese Unterstützung mithilfe des folgenden Befehls überprüft werden: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die Mindestanforderungen und empfohlenen Werte für die CPU-Kerne und den Arbeitsspeicher für die einzelnen Speech-Container beschrieben:

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | Zwei Kerne<br>2 GB Arbeitsspeicher  | Vier Kerne<br>4 GB Arbeitsspeicher  |
|cognitive-services-text-to-speech | Ein Kern, 0,5 GB Arbeitsspeicher| Zwei Kerne, 1 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.


Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

**Hinweis:** Die Mindestanforderungen und Empfehlungen basieren auf Docker-Grenzwerten, *nicht* auf den Ressourcen des Hostcomputers. Spracherkennungscontainer ordnen beispielsweise Teile des Arbeitsspeichers eines großen Sprachmodells zu, und es wird _empfohlen_, dass die gesamte Datei in den Arbeitsspeicher passt (was zusätzlich 4 bis 6 GB ausmacht). Außerdem dauert die erste Ausführung des Containers unter Umständen länger, da Modelle in den Arbeitsspeicher ausgelagert werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Für Speech stehen folgende Containerimages zur Verfügung: 

| Container | Repository |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Das Sprachgebietsschema ist im Containertag enthalten.

Das Tag `latest` pullt das Gebietsschema `en-us` und die Stimme `jessarus`. 

#### <a name="speech-to-text-locales"></a>Gebietsschemas für die Spracherkennung

Mit Ausnahme von `latest` liegen alle Tags im folgenden Format vor, wobei `<culture>` den Gebietsschemacontainer angibt:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Das folgende Tag ist ein Beispiel für das Format:

```
1.0.0-amd64-en-us-preview
```

Die folgende Tabelle enthält die unterstützten Gebietsschemas für die **Spracherkennung** in der Version 1.1.1 des Containers:

|Sprachgebietsschema|`Tags`|
|--|--|
|Chinesisch|`zh-cn`|
|Englisch |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Französisch |`fr-ca`<br>`fr-fr`|
|Deutsch|`de-de`|
|Italienisch|`it-it`|
|Japanisch|`ja-jp`|
|Koreanisch|`ko-kr`|
|Portugiesisch|`pt-br`|
|Spanisch|`es-es`<br>`es-mx`|


#### <a name="text-to-speech-locales"></a>Gebietsschemas für die Sprachsynthese

Mit Ausnahme von `latest` liegen alle Tags im folgenden Format vor, wobei `<culture>` das Gebietsschema und `<voice>` die Stimme des Containers angibt:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Das folgende Tag ist ein Beispiel für das Format:

```
1.0.0-amd64-en-us-jessarus-preview
```

Die folgende Tabelle enthält die unterstützten Gebietsschemas für die **Sprachsynthese** in der Version 1.1.0 des Containers:

|Sprachgebietsschema|`Tags`|Unterstützte Stimmen|
|--|--|--|
|Chinesisch|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Englisch |`en-au`|catherine<br>hayleyrus|
|Englisch |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Englisch |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Englisch |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Französisch|`fr-ca`|caroline<br>harmonierus|
|Französisch|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Deutsch|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italienisch|`it-it`|cosimo-apollo<br>luciarus|
|Japanisch|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Koreanisch|`ko-kr`|heamirus|
|Portugiesisch|`pt-br`|daniel-apollo<br>heloisarus|
|Spanisch|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Spanisch|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker-Pullvorgang für die Speech-Container

#### <a name="speech-to-text"></a>Spracherkennung

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Text-zu-Sprache

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen, aber nicht verwendeten Abrechnungseinstellungen. Es sind noch weitere [Beispiele](speech-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar. 
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um einen der drei Container auszuführen. Für den Befehl werden die folgenden Parameter verwendet:

**Während der Vorschau** müssen die Abrechnungseinstellungen zwar gültig sein, um den Container starten zu können, die Nutzung wird Ihnen jedoch nicht in Rechnung gestellt.

| Platzhalter | Wert |
|-------------|-------|
|{BILLING_KEY} | Dieser Schlüssel wird zum Starten des Containers verwendet und steht im Azure-Portal auf der Schlüsselseite für Speech zur Verfügung.  |
|{BILLING_ENDPOINT_URI} | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Speech-Übersichtsseite.|

Ersetzen Sie im folgenden Beispiel für den Befehl `docker run` diese Parameter durch Ihre eigenen Werte.

### <a name="text-to-speech"></a>Text-zu-Sprache

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Spracherkennung

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Dieser Befehl:

* Führt einen Speech-Container auf der Grundlage des Containerimages aus
* Ordnet zwei CPU-Kerne und 2 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar. 

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

|Container|Endpunkt|
|--|--|
|Spracherkennung|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Text-zu-Sprache|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Spracherkennung

Der Container bietet websocketbasierte Abfrageendpunkt-APIs, auf die über das [Speech SDK](index.yml) zugegriffen wird.

Standardmäßig verwendet das Speech SDK Online-Speech-Dienste. Um den Container verwenden zu können, müssen Sie die Initialisierungsmethode ändern. Weitere Informationen finden Sie in den folgenden Beispielen.

#### <a name="for-c"></a>C#

Ändern Sie den folgenden Azure-Cloudinitialisierungsaufruf

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

in den folgenden Aufruf mit dem Containerendpunkt:

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python

Ändern Sie den folgenden Azure-Cloudinitialisierungsaufruf

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

in den folgenden Aufruf mit dem Containerendpunkt:

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Text-zu-Sprache

Die vom Container bereitgestellten REST-Endpunkt-APIs finden Sie [hier](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech). Beispiele finden Sie [hier](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container ausführen, werden mithilfe von **stdout** und **stderr** Informationen ausgegeben, die hilfreich sind, um Probleme beim Starten oder Ausführen des Containers zu beheben. 

## <a name="billing"></a>Abrechnung

Der Speech-Container sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Speech_ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Speech-Containern kennengelernt. Zusammenfassung:

* Speech stellt zur Kapselung von Spracherkennung und Sprachsynthese zwei Linux-Container für Docker bereit.
* Containerimages werden aus der privaten Containerregistrierung in Azure heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Speech-Containern über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
>  Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z.B. das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](speech-container-configuration.md).
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
