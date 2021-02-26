---
title: Installieren und Ausführen von Docker-Containern für die APIs des Speech-Diensts
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Docker-Container für den Speech-Dienst, um Spracherkennung, Transkription, Generierung und mehr lokal durchzuführen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: Lokal, Docker, Container
ms.openlocfilehash: 7bebaf7558de8ec5c1fcca3c9a4526330da1d695
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575787"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Installieren und Ausführen von Docker-Containern für die APIs des Speech-Diensts 

Container ermöglichen es Ihnen, einige der Speech-Dienst-APIs in Ihrer eigenen Umgebung auszuführen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. In diesem Artikel erfahren Sie, wie Sie einen Speech-Container herunterladen, installieren und ausführen.

Mit Speech-Containern können Kunden eine Speech-basierte Anwendungsarchitektur erstellen, die sowohl für stabile Cloudfunktionen als auch für das Edge optimiert ist. Es sind verschiedene Container verfügbar, die denselben [Preis](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) wie die cloudbasierten Azure Speech-Dienste haben.


> [!IMPORTANT]
> Die folgenden Speech-Container sind jetzt allgemein verfügbar:
> * Standardspracherkennung
> * Benutzerdefinierte Spracherkennung
> * Standardsprachsynthese
> * Text-zu-Sprache (neuronal)
>
> Die folgenden Speech-Container sind in der geschlossenen Vorschau verfügbar:
> * Benutzerdefinierte Sprachsynthese
> * Speech-Sprachenerkennung 
>
> Wenn Sie die Speech-Container verwenden möchten, müssen Sie eine Onlineanfrage einreichen und diese genehmigen lassen. Weitere Informationen finden Sie weiter unten im Abschnitt **Anfordern der Genehmigung für die Containerausführung**.

| Container | Features | Neueste Version |
|--|--|--|
| Spracherkennung | Analysiert die Stimmung und transkribiert kontinuierliche Echtzeitsprach- oder Batchaudioaufzeichnungen mit Zwischenergebnissen.  | 2.9.0 |
| Benutzerdefinierte Spracherkennung | Verwendet ein benutzerdefiniertes Modell aus dem [Custom Speech-Portal](https://speech.microsoft.com/customspeech) und transkribiert kontinuierliche Echtzeitsprach- oder Batchaudioaufzeichnungen in Text mit Zwischenergebnissen. | 2.9.0 |
| Text-zu-Sprache | Konvertiert Text in natürlich klingende Sprache mit Nur-Text-Eingaben oder SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese). | 1.11.0 |
| Benutzerdefinierte Sprachsynthese | Verwendet ein benutzerdefiniertes Modell aus dem [Custom Voice-Portal](https://aka.ms/custom-voice-portal) und konvertiert Text in natürlich klingende Sprache mit Nur-Text-Eingaben oder SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese). | 1.11.0 |
| Speech-Sprachenerkennung | Erkennen der in Audiodateien gesprochenen Sprache | 1.0 |
| Text-zu-Sprache (neuronal) | Konvertiert Text mithilfe von Deep Neural Network-Technologie in natürlich klingende Sprache, die eine natürlichere synthetische Sprache ermöglicht. | 1.3.0 |

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung von Speech-Containern müssen folgende Voraussetzungen erfüllt sein:

| Erforderlich | Zweck |
|--|--|
| Docker-Engine | Die Docker-Engine muss auf einem [Hostcomputer](#the-host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. <br><br> **Unter Windows** muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.<br><br> |
| Kenntnisse zu Docker | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden `docker`-Befehle kennen. |
| Speech-Ressource | Um diese Container zu verwenden, benötigen Sie Folgendes:<br><br>Eine Azure-Ressource vom Typ _Speech_, um den entsprechenden API-Schlüssel und den URI des Endpunkts zu erhalten. Beide Werte stehen im Azure-Portal auf den Seiten „Übersicht“ und „Schlüssel“ von **Speech** zur Verfügung. Beide sind zum Starten des Containers erforderlich.<br><br>**{API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel auf der Seite **Schlüssel**<br><br>**{ENDPOINT_URI}** : Der Endpunkt, der auf der Seite **Übersicht** angegeben ist |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Der Hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Unterstützung von Advanced Vector Extensions

Der **Host** ist der Computer, auf dem der Docker-Container ausgeführt wird. Der Host *muss* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) unterstützen. Sie können die AVX2-Unterstützung auf Linux-Hosts mit dem folgenden Befehl überprüfen:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Der Hostcomputer *muss* AVX2 unterstützen. Ohne AVX2-Unterstützung funktioniert der Container *nicht* ordnungsgemäß.

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

Die folgende Tabelle beschreibt die minimale und empfohlene Zuordnung von Ressourcen für jeden Speech-Container.

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
| Spracherkennung | 2 Kerne, 2 GB Arbeitsspeicher | 4 Kerne, 4 GB Arbeitsspeicher |
| Benutzerdefinierte Spracherkennung | 2 Kerne, 2 GB Arbeitsspeicher | 4 Kerne, 4 GB Arbeitsspeicher |
| Text-zu-Sprache | Ein Kern, 2 GB Arbeitsspeicher | 2 Kerne, 3 GB Arbeitsspeicher |
| Benutzerdefinierte Sprachsynthese | Ein Kern, 2 GB Arbeitsspeicher | 2 Kerne, 3 GB Arbeitsspeicher |
| Speech-Sprachenerkennung | 1 Kern, 1 GB Arbeitsspeicher | 1 Kern, 1 GB Arbeitsspeicher |
| Text-zu-Sprache (neuronal) | 6 Kerne, 12 GB Arbeitsspeicher | 8 Kerne, 16 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

> [!NOTE]
> Die Mindestanforderungen und Empfehlungen basieren auf Docker-Grenzwerten, *nicht* auf den Ressourcen des Hostcomputers. Spracherkennungscontainer ordnen beispielsweise Teile des Arbeitsspeichers eines großen Sprachmodells zu, und es wird *empfohlen*, dass die gesamte Datei in den Arbeitsspeicher passt (was zusätzlich 4 bis 6 GB ausmacht). Außerdem dauert die erste Ausführung des Containers unter Umständen länger, da Modelle in den Arbeitsspeicher ausgelagert werden.

## <a name="request-approval-to-the-run-the-container"></a>Anfordern der Genehmigung für die Containerausführung

Füllen Sie das [Anforderungsformular](https://aka.ms/csgate) aus, und übermitteln Sie es, um Zugriff auf den Container anzufordern. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Containerimages für Speech stehen in der folgenden Container Registry zur Verfügung:

# <a name="speech-to-text"></a>[Spracherkennung](#tab/stt)

| Container | Repository |
|-----------|------------|
| Spracherkennung | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Benutzerdefinierte Spracherkennung](#tab/cstt)

| Container | Repository |
|-----------|------------|
| Benutzerdefinierte Spracherkennung | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Sprachsynthese](#tab/tts)

| Container | Repository |
|-----------|------------|
| Text-zu-Sprache | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Text-zu-Sprache (neuronal)](#tab/ntts)

| Container | Repository |
|-----------|------------|
| Text-zu-Sprache (neuronal) | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Benutzerdefinierte Sprachsynthese](#tab/ctts)

| Container | Repository |
|-----------|------------|
| Benutzerdefinierte Sprachsynthese | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Speech-Sprachenerkennung](#tab/lid)

> [!TIP]
> Um die nützlichsten Ergebnisse zu erhalten, empfehlen wir, den Container für Speech-Spracherkennung mit den Containern für Sprache-in-Text oder die benutzerdefinierte Spracherkennung zu verwenden. 

| Container | Repository |
|-----------|------------|
| Speech-Sprachenerkennung | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-Pullvorgang für die Speech-Container

# <a name="speech-to-text"></a>[Spracherkennung](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-Pullvorgang für den Spracherkennungscontainer

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> Das Tag `latest` pullt das Gebietsschema `en-US`. Weitere Gebietsschemas finden Sie unter [Gebietsschemas für die Spracherkennung](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Gebietsschemas für die Spracherkennung

Alle Tags, mit Ausnahme von `latest`, haben das folgende Format und beachten die Groß-/Kleinschreibung:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Das folgende Tag ist ein Beispiel für das Format:

```
2.6.0-amd64-en-us
```

Informationen zu allen unterstützten Gebietsschemas des Containers **Spracherkennung** finden Sie unter [Imagetags für Spracherkennung](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Benutzerdefinierte Spracherkennung](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-Pullvorgang für den benutzerdefinierten Spracherkennungscontainer

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> Die Werte für `locale` und `voice` für benutzerdefinierte Speech-Container werden durch das benutzerdefinierte Modell bestimmt, das vom Container erfasst wird.

# <a name="text-to-speech"></a>[Sprachsynthese](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-Pullvorgang für den Sprachsynthesecontainer

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> Das Tag `latest` pullt das Gebietsschema `en-US` und die Stimme `ariarus`. Weitere Gebietsschemas finden Sie unter [Gebietsschemas für die Sprachsynthese](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Gebietsschemas für die Sprachsynthese

Alle Tags, mit Ausnahme von `latest`, haben das folgende Format und beachten die Groß-/Kleinschreibung:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Das folgende Tag ist ein Beispiel für das Format:

```
1.8.0-amd64-en-us-ariarus
```

Informationen zu allen unterstützten Gebietsschemas und den entsprechenden Stimmen des Containers **Sprachsynthese** finden Sie unter [Imagetags für Sprachsynthese](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Beim Erstellen einer HTTP POST-Anforderung für die *Sprachsynthese* erfordert die Meldung der [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) ein `voice`-Element mit einem `name`-Attribut. Der Wert ist das entsprechende Gebietsschema des Containers und die Stimme, auch bekannt als „[Kurzname](language-support.md#standard-voices)“. Das Tag `latest` beispielsweise weist den Sprachnamen `en-US-AriaRUS` auf.

# <a name="neural-text-to-speech"></a>[Text-zu-Sprache (neuronal)](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-Pullvorgang für den „Text-zu-Sprache (neuronal)“-Container

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> Das Tag `latest` pullt das Gebietsschema `en-US` und die Stimme `arianeural`. Weitere Gebietsschemas finden Sie unter [Gebietsschemas für „Text-zu-Sprache (neuronal)“](#neural-text-to-speech-locales).

#### <a name="neural-text-to-speech-locales"></a>Gebietsschemas für „Text-zu-Sprache (neuronal)“

Alle Tags, mit Ausnahme von `latest`, haben das folgende Format und beachten die Groß-/Kleinschreibung:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Das folgende Tag ist ein Beispiel für das Format:

```
1.3.0-amd64-en-us-arianeural
```

Informationen zu allen unterstützten Gebietsschemas und den entsprechenden Stimmen des Containers **Text-zu-Sprache (neuronal)** finden Sie unter [Imagetags für „Text-zu-Sprache (neuronal)“](../containers/container-image-tags.md#neural-text-to-speech).

> [!IMPORTANT]
> Beim Erstellen einer HTTP POST-Anforderung für *Text-zu-Sprache (neuronal)* erfordert die Meldung der [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) ein `voice`-Element mit einem `name`-Attribut. Der Wert ist das entsprechende Gebietsschema des Containers und die Stimme, auch bekannt als „[Kurzname](language-support.md#neural-voices)“. Das Tag `latest` beispielsweise weist den Sprachnamen `en-US-AriaNeural` auf.

# <a name="custom-text-to-speech"></a>[Benutzerdefinierte Sprachsynthese](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-Pullvorgang für den benutzerdefinierten Sprachsynthesecontainer

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> Die Werte für `locale` und `voice` für benutzerdefinierte Speech-Container werden durch das benutzerdefinierte Modell bestimmt, das vom Container erfasst wird.

# <a name="speech-language-detection"></a>[Speech-Sprachenerkennung](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker-Pullvorgang für den Speech-Sprachenerkennungscontainer

Verwenden Sie den [Docker-Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Verwenden des Containers

Wenn sich der Container auf dem [Hostcomputer](#the-host-computer) befindet, können Sie über den folgenden Prozess mit dem Container arbeiten.

1. [Führen Sie den Container aus](#run-the-container-with-docker-run), und verwenden Sie dabei die erforderlichen Abrechnungseinstellungen. Es sind noch weitere [Beispiele](speech-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.
1. [Fragen Sie den Vorhersageendpunkt des Containers ab.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um den Container auszuführen. Genaue Informationen dazu, wie Sie die Werte `{Endpoint_URI}` und `{API_Key}` abrufen, erhalten Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters). Es sind auch weitere [Beispiele](speech-container-configuration.md#example-docker-run-commands) für den Befehl `docker run` verfügbar.

# <a name="speech-to-text"></a>[Spracherkennung](#tab/stt)

Zum Ausführen des Containers für die standardmäßige *Spracherkennung* führen Sie den folgenden `docker run`-Befehl aus.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die *Spracherkennung* aus dem Containerimage aus.
* Ordnet 4 CPU-Kerne und 4 GB Arbeitsspeicher zu.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

> [!NOTE]
> Container unterstützen komprimierte Audioeingaben für das Speech SDK mithilfe von GStreamer.
> Befolgen Sie zum Installieren von GStreamer in einem Container die Linux-Anweisungen für GStreamer im Artikel [Verwenden von per Codec komprimierter Audioeingabe mit dem Speech SDK](how-to-use-codec-compressed-audio-input-streams.md).

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarisierung für die Spracherkennungsausgabe
Die Diarisierung ist standardmäßig aktiviert. Verwenden Sie `diarize_speech_config.set_service_property`, um eine Diarisierung in Ihrer Antwort zu erhalten.

1. Legen Sie das Ausgabeformat des Ausdrucks auf `Detailed` fest.
2. Legen Sie den Modus der Diarisierung fest. Unterstützte Modi: `Identity` und `Anonymous`.
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> Der Modus „Identität“ gibt `"SpeakerId": "Customer"` oder `"SpeakerId": "Agent"` zurück.
> Der Modus „Anonym“ gibt `"SpeakerId": "Speaker 1"` oder `"SpeakerId": "Speaker 2"` zurück.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analysieren der Stimmung bei der Spracherkennungsausgabe 
Ab Version v2.6.0 des Spracherkennungs-Containers sollten Sie den TextAnalytics 3.0 API-Endpunkt anstelle des Vorschauendpunkts verwenden. Beispiel:
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Die Textanalyse `v3.0`-API ist nicht mit Textanalyse `v3.0-preview.1` abwärtskompatibel. Um die Unterstützung für die neueste Standpunktanalysefunktion zu erhalten, verwenden Sie `v2.6.0` des Spracherkennungs-Containerimages und Textanalyse `v3.0`.

Ab v2.2.0 des Spracherkennungscontainers können Sie die [API für die Standpunktanalyse v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) in der Ausgabe aufrufen. Um die Standpunktanalyse aufrufen zu können, benötigen Sie einen Textanalyse-API-Ressourcenendpunkt. Beispiel: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Wenn Sie auf einen Textanalyseendpunkt in der Cloud zugreifen, benötigen Sie einen Schlüssel. Wenn Sie die Textanalyse lokal ausführen, müssen Sie diesen möglicherweise nicht bereitstellen.

Der Schlüssel und der Endpunkt werden wie im folgenden Beispiel als Argumente an den Speech-Container übergeben.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Dieser Befehl:

* Führt dieselben Schritte aus wie der obige Befehl.
* Speichert einen Textanalyse-API-Endpunkt und einen Schlüssel zum Senden von Standpunktanalyseanforderungen. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Phraselist v2 für die Spracherkennungsausgabe 

Seit v2.6.0 des Spracherkennungscontainers können Sie die Ausgabe mit eigenen Ausdrücken abrufen – entweder den gesamten Satz oder Ausdrücke in der Satzmitte. Beispielsweise *der große Mann* im folgenden Satz:

* „Dies ist ein Satz **der große Mann** dies ist ein weiterer Satz“.

Zum Konfigurieren einer Ausdruckliste müssen Sie Ihre eigenen Ausdrücke beim Vornehmen des Aufrufs hinzufügen. Zum Beispiel:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)

```

Wenn Sie mehrere Ausdrücke hinzufügen möchten, rufen Sie `.addPhrase()` für jeden Ausdruck auf, um ihn der Ausdrucksliste hinzuzufügen. 


# <a name="custom-speech-to-text"></a>[Benutzerdefinierte Spracherkennung](#tab/cstt)

Der Container für *benutzerdefinierte Spracherkennung* basiert auf einem benutzerdefinierten Sprachmodell. Das benutzerdefinierte Modell muss über das [Custom Speech-Portal](https://speech.microsoft.com/customspeech)[trainiert](how-to-custom-speech-train-model.md) worden sein.

Die **Modell-ID** für Custom Speech ist zur Ausführung des Containers erforderlich. Sie finden diese auf der Seite **Training** des Custom Speech-Portals. Navigieren Sie im Custom Speech-Portal zur Seite **Training**, und wählen Sie das Modell aus.
<br>

![Seite „Training“ im Custom Speech-Portal](media/custom-speech/custom-speech-model-training.png)

Rufen Sie die **Modell-ID** ab, um diese als Argument für den `ModelId`-Parameter des `docker run`-Befehls zu verwenden.
<br>

![Details zum benutzerdefinierten Sprachmodell](media/custom-speech/custom-speech-model-details.png)

Die folgende Tabelle zeigt die verschiedenen `docker run`-Parameter und die entsprechenden Beschreibungen:

| Parameter | BESCHREIBUNG |
|---------|---------|
| `{VOLUME_MOUNT}` | Die [Volumebereitstellung](https://docs.docker.com/storage/volumes/) des Hostcomputers, die Docker zum dauerhaften Speichern des benutzerdefinierten Modells verwendet. Beispiel: *C:\CustomSpeech*, wobei sich *Laufwerk „C“* auf dem Hostcomputer befindet. |
| `{MODEL_ID}` | Die **Modell-ID** für Custom Speech von der Seite **Training** des Custom Speech-Portals. |
| `{ENDPOINT_URI}` | Der Endpunkt ist zur Messung und Abrechnung erforderlich. Weitere Informationen finden Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters). |
| `{API_KEY}` | Der API-Schlüssel ist erforderlich. Weitere Informationen finden Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters). |

Zum Ausführen des Containers für die *benutzerdefinierte Spracherkennung* führen Sie den folgenden `docker run`-Befehl aus:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die *benutzerdefinierte Spracherkennung* aus dem Containerimage aus.
* Ordnet 4 CPU-Kerne und 4 GB Arbeitsspeicher zu.
* Lädt das Modell für die *benutzerdefinierte Spracherkennung* aus der Volumebereitstellung für die Eingabe, z. B. *C:\CustomSpeech*.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Lädt das Modell anhand der `ModelId` herunter (sofern diese in der Volumebereitstellung nicht gefunden wird).
* Wenn das benutzerdefinierte Modell zuvor bereits heruntergeladen wurde, wird die `ModelId` ignoriert.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Basismodell-Download in den benutzerdefinierten Spracherkennungscontainer  
Seit v2.6.0 des benutzerdefinierten Spracherkennungscontainers können Sie die verfügbaren Informationen zum Basismodell mithilfe der Option `BaseModelLocale=<locale>` abrufen. Mit dieser Option erhalten Sie eine Liste der verfügbaren Basismodelle für das betreffende Gebietsschema unter Ihrem Abrechnungskonto. Zum Beispiel:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die *benutzerdefinierte Spracherkennung* aus dem Containerimage aus.
* Überprüfen Sie die verfügbaren Basismodelle des Zielgebietsschemas, und geben Sie sie zurück.

In der Ausgabe erhalten Sie eine Liste der Basismodelle mit den Informationen Gebietsschema, Modell-ID und Datum/-uhrzeit der Erstellung. Sie können die Modell-ID verwenden, um das bevorzugte spezifische Basismodell herunterzuladen und zu verwenden. Zum Beispiel:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Benutzerdefinierte Aussprache für den benutzerdefinierten Sprachcontainer 
Seit v2.5.0 des benutzerdefinierten Spracherkennungscontainers können Sie benutzerdefinierte Ausspracheergebnisse in der Ausgabe erhalten. Dazu müssen Sie lediglich Ihre eigenen benutzerdefinierten Ausspracheregeln in Ihrem benutzerdefinierten Modell einrichten und das Modell in einem benutzerdefinierten Spracherkennungscontainer einbinden.


# <a name="text-to-speech"></a>[Sprachsynthese](#tab/tts)

Zum Ausführen des Containers für die standardmäßige *Sprachsynthese* führen Sie den folgenden `docker run`-Befehl aus.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die standardmäßige *Sprachsynthese* aus dem Containerimage aus.
* Ordnet 1 CPU-Kern und 2 GB Arbeitsspeicher zu.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

# <a name="neural-text-to-speech"></a>[Text-zu-Sprache (neuronal)](#tab/ntts)

Zum Ausführen des Containers für *Text-zu-Sprache (neuronal)* führen Sie den folgenden `docker run`-Befehl aus.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für *Text-zu-Sprache (neuronal)* aus dem Containerimage aus.
* Belegt 6 CPU-Kerne und 12 GB Arbeitsspeicher.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

# <a name="custom-text-to-speech"></a>[Benutzerdefinierte Sprachsynthese](#tab/ctts)

Der Container für die *benutzerdefinierte Sprachsynthese* basiert auf einem benutzerdefinierten Sprachmodell. Das benutzerdefinierte Modell muss über das [Custom Voice-Portal](https://aka.ms/custom-voice-portal)[trainiert](how-to-custom-voice-create-voice.md) worden sein. Die **Modell-ID** für Custom Voice ist zur Ausführung des Containers erforderlich. Sie finden diese auf der Seite **Training** des Custom Voice-Portals. Navigieren Sie im Custom Voice-Portal zur Seite **Training**, und wählen Sie das Modell aus.
<br>

![Seite „Training“ im Custom Voice-Portal](media/custom-voice/custom-voice-model-training.png)

Rufen Sie die **Modell-ID** ab, um diese als Argument für den `ModelId`-Parameter des docker run-Befehls zu verwenden.
<br>

![Details des Custom Voice-Modells](media/custom-voice/custom-voice-model-details.png)

Die folgende Tabelle zeigt die verschiedenen `docker run`-Parameter und die entsprechenden Beschreibungen:

| Parameter | BESCHREIBUNG |
|---------|---------|
| `{VOLUME_MOUNT}` | Die [Volumebereitstellung](https://docs.docker.com/storage/volumes/) des Hostcomputers, die Docker zum dauerhaften Speichern des benutzerdefinierten Modells verwendet. Beispiel: *C:\CustomSpeech*, wobei sich *Laufwerk „C“* auf dem Hostcomputer befindet. |
| `{MODEL_ID}` | Die **Modell-ID** für Custom Speech von der Seite **Training** des Custom Voice-Portals. |
| `{ENDPOINT_URI}` | Der Endpunkt ist zur Messung und Abrechnung erforderlich. Weitere Informationen finden Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters). |
| `{API_KEY}` | Der API-Schlüssel ist erforderlich. Weitere Informationen finden Sie unter [Ermitteln erforderlicher Parameter](#gathering-required-parameters). |

Zum Ausführen des Containers für die *benutzerdefinierte Sprachsynthese* führen Sie den folgenden `docker run`-Befehl aus:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen Container für die *benutzerdefinierte Sprachsynthese* aus dem Containerimage aus.
* Ordnet 1 CPU-Kern und 2 GB Arbeitsspeicher zu.
* Lädt das Modell für die *benutzerdefinierte Sprachsynthese* aus der Volumebereitstellung für die Eingabe, z. B. *C:\CustomVoice*.
* Macht den TCP-Port 5000 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Lädt das Modell anhand der `ModelId` herunter (sofern diese in der Volumebereitstellung nicht gefunden wird).
* Wenn das benutzerdefinierte Modell zuvor bereits heruntergeladen wurde, wird die `ModelId` ignoriert.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

# <a name="speech-language-detection"></a>[Speech-Sprachenerkennung](#tab/lid)

Führen Sie zum Ausführen des Containers für die *Speech-Sprachenerkennung* den folgenden `docker run`-Befehl aus.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl: 

* Führt einen Speech-Sprachenerkennungscontainer aus dem Containerimage aus. Gegenwärtig werden Ihnen für die Ausführung dieses Images keine Gebühren berechnet.
* Ordnet 1 CPU-Kern und 1 GB Arbeitsspeicher zu.
* Macht den TCP-Port 5003 verfügbar und ordnet eine Pseudo-TTY-Verbindung für den Container zu.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

Wenn Sie nur Anforderungen der Speech-Sprachenerkennung senden, müssen Sie den Wert `phraseDetection` des Speech-Clients auf `None` festlegen.  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Wenn Sie diesen Container mit dem Spracherkennungscontainer ausführen möchten, können Sie [dieses Docker-Image](https://hub.docker.com/r/antsu/on-prem-client) verwenden. Verwenden Sie nach dem Starten der beiden Container diesen docker run-Befehl, um `speech-to-text-with-languagedetection-client` auszuführen.

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Eine höhere Anzahl gleichzeitiger Aufrufe kann sich auf Zuverlässigkeit und Wartezeit auswirken. Für die Sprachenerkennung empfiehlt es sich, maximal 4 gleichzeitige Aufrufe mit 1 CPU und 1 GB Arbeitsspeicher zu verwenden. Für Hosts mit 2 CPUs und 2 GB Arbeitsspeicher werden maximal 6 gleichzeitige Aufrufe empfohlen.

***

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

> [!NOTE]
> Verwenden Sie eine eindeutige Portnummer, wenn Sie mehrere Container verwenden.

| Container | SDK-Host-URL | Protocol |
|--|--|--|
| Standardmäßige und benutzerdefinierte Spracherkennung | `ws://localhost:5000` | WS |
| Sprachsynthese (einschließlich standardmäßige, benutzerdefinierte und neuronale Sprachsynthese), Speech-Sprachenerkennung | `http://localhost:5000` | HTTP |

Weitere Informationen zur Verwendung der Protokolle WSS und HTTPS finden Sie unter [Containersicherheit](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Spracherkennung (standardmäßig und benutzerdefiniert)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analysieren von Stimmungen

Wenn Sie Ihre Anmeldeinformationen für die Textanalyse-API [in den Container](#analyze-sentiment-on-the-speech-to-text-output) eingegeben haben, können Sie das Speech SDK verwenden, um Spracherkennungsanforderungen mit Standpunktanalyse zu senden. Sie können die API-Antworten so konfigurieren, dass sie entweder ein *einfaches* oder *detailliertes* Format verwenden.
> [!NOTE]
> Bei v1.13 des Speech Service Python SDK ist ein Problem mit der Standpunktanalyse bekannt. Verwenden Sie v1.12.x oder früher, wenn Sie eine Standpunktanalyse im Speech Service Python SDK verwenden.

# <a name="simple-format"></a>[Einfaches Format](#tab/simple-format)

Fügen Sie `"Sentiment"` als Wert für `Simple.Extensions` hinzu, um den Speech-Client für die Verwendung eines einfachen Formats zu konfigurieren. Wenn Sie eine bestimmte Modellversion für die Textanalyse auswählen möchten, ersetzen Sie `'latest'` in der `speechcontext-phraseDetection.sentimentAnalysis.modelversion`-Eigenschaftskonfiguration.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` gibt das Stimmungsergebnis auf der Stammebene der Antwort zurück.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Detailliertes Format](#tab/detailed-format)

Fügen Sie `"Sentiment"` als Wert für `Detailed.Extensions`, `Detailed.Options` oder beides hinzu, um den Speech-Client für die Verwendung eines detaillierten Formats zu konfigurieren. Wenn Sie eine bestimmte Modellversion für die Textanalyse auswählen möchten, ersetzen Sie `'latest'` in der `speechcontext-phraseDetection.sentimentAnalysis.modelversion`-Eigenschaftskonfiguration.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` stellt das Stimmungsergebnis auf der Stammebene der Antwort dar. `Detailed.Options` liefert das Ergebnis auf der `NBest`-Ebene der Antwort. Sie können separat oder zusammen verwendet werden.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Wenn Sie die Standpunktanalyse vollständig deaktivieren möchten, fügen Sie `sentimentanalysis.enabled` den Wert `false` hinzu.

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Sprachsynthese (standardmäßig, neuronal und benutzerdefiniert)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Ausführen mehrerer Container auf dem gleichen Host

Wenn Sie beabsichtigen, mehrere Container mit offengelegten Ports auszuführen, stellen Sie sicher, dass jeder Container mit einem anderen offengelegten Port ausgeführt wird. Führen Sie beispielsweise den ersten Container an Port 5000 und den zweiten Container an Port 5001 aus.

Sie können diesen Container und einen anderen Azure Cognitive Services-Container zusammen auf dem Host ausführen. Sie können auch mehrere Container desselben Cognitive Services-Containers ausführen.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Beim Starten oder Ausführen des Containers können Probleme auftreten. Verwenden Sie eine [Bereitstellung](speech-container-configuration.md#mount-settings) für die Ausgabe, und aktivieren Sie die Protokollierung. Auf diese Weise kann der Container Protokolldateien generieren, die bei der Behebung von Problemen helfen.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der Speech-Container sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ *Speech* in Ihrem Azure-Konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](speech-container-configuration.md).

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Speech-Containern kennengelernt. Zusammenfassung:

* Speech stellt vier Linux-Container für Docker bereit, die verschiedene Funktionen kapseln:
  * *Spracherkennung*
  * *Benutzerdefinierte Spracherkennung*
  * *Sprachsynthese*
  * *Benutzerdefinierte Sprachsynthese*
  * *Text-zu-Sprache (neuronal)*
  * *Speech-Sprachenerkennung*
* Containerimages werden aus der Containerregistrierung in Azure heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Geben Sie den Host-URI des Containers an, unabhängig davon, ob Sie die Rest-API (nur Sprachsynthese) oder das SDK (Spracherkennung oder Sprachsynthese) verwenden. 
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
>  Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z.B. das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich unter [Konfigurieren von Containern](speech-container-configuration.md) über Konfigurationseinstellungen.
* Erfahren Sie, wie Sie [Container für den Speech-Dienst mit Kubernetes und Helm verwenden](speech-container-howto-on-premises.md).
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)
