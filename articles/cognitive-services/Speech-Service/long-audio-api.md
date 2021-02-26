---
title: 'API für lange Audioinhalte: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie die API für lange Audioinhalte für die asynchrone Synthese von langen Sprachsynthesen konzipiert ist.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: e28bd5b5caca259201758f0c633b2120a411f422
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007447"
---
# <a name="long-audio-api"></a>API für lange Audioinhalte

Die API für lange Audioinhalte ist für asynchrone lange Sprachsynthesen konzipiert. Beispiele wären etwa Hörbücher, Zeitungsartikel und Dokumente. Diese API gibt keine synthetisierten Audiodaten in Echtzeit zurück, sondern erwartet, dass Sie die Antworten abrufen und die Ausgaben so verwenden, wie sie vom Dienst bereitgestellt werden. Im Gegensatz zur API für die Sprachsynthese, die vom Speech SDK verwendet wird, kann die API für lange Audioinhalte synthetisierte Audiodaten erzeugen, die länger als 10 Minuten sind, wodurch sie ideal für Herausgeber und Plattformen für Audioinhalte geeignet ist, um lange Audioinhalte wie Hörbücher in einem Batch zu erstellen.

Weitere Vorteile der API für lange Audioinhalte:

* Die vom Dienst zurückgegebene Stimmensynthese verwendet die besten neuronalen Stimmen.
* Es muss kein Stimmendpunkt bereitgestellt werden, da Stimmen in einem nicht echtzeitbasierten Batchmodus synthetisiert werden.

> [!NOTE]
> Die API für lange Audioinhalte unterstützt jetzt sowohl [öffentliche neuronale Stimmen](./language-support.md#neural-voices) als auch [benutzerdefinierte neuronale Stimmen](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Workflow

Wenn Sie die API für lange Audioinhalte verwenden, senden Sie typischerweise eine Textdatei oder zu synthetisierende Dateien, fragen den Status ab und können die Audioausgabe herunterladen, wenn der Status „Erfolgreich“ ist.

Dieses Diagramm bietet eine allgemeine Übersicht über den Workflow.

![Diagramm zum Workflow der API für lange Audioinhalte](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Vorbereiten der Inhalte für die Synthese

Stellen Sie bei der Vorbereitung Ihrer Textdatei Folgendes sicher:

* Es handelt sich entweder um Nur-Text (.txt) oder SSML-Text (.txt).
* Die Verschlüsselung ist [UTF-8 mit Bytereihenfolge-Marke (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom).
* Es handelt sich um eine einzelne Datei, nicht um ein ZIP-Archiv.
* Sie enthält mehr als 400 Zeichen für Nur-Text oder 400 [abrechenbare Zeichen](./text-to-speech.md#pricing-note) für SSML-Text und weniger als 10.000 Absätze.
  * Bei Nur-Text wird jeder Absatz durch Drücken der **EINGABETASTE** getrennt – [Beispiel für die Eingabe von Nur-Text](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) anzeigen
  * Bei SSML-Text wird jede SSML-Komponente als Absatz betrachtet. SSML-Elemente sollen durch verschiedene Absätze getrennt werden – [Beispiel für die Eingabe von SSML-Text](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) anzeigen.

## <a name="python-example"></a>Beispiel für Python

Dieser Abschnitt enthält Python-Beispiele, die die grundlegende Verwendung der Long Audio-API veranschaulichen. Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in eine Datei namens `long_audio_synthesis_client.py`.

```python
import json
import ntpath
import requests
```

Diese Bibliotheken werden verwendet, um die HTTP-Anforderung zu erstellen und die REST-API für die Synthese für lange Audioinhalte (Sprachsynthese) aufzurufen.

### <a name="get-a-list-of-supported-voices"></a>Abrufen einer Liste der unterstützten Stimmen

Um eine Liste der unterstützten Stimmen zu erhalten, senden Sie eine GET-Anforderung an `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices`.


Dieser Code ermöglicht es Ihnen, eine vollständige Liste der Stimmen für eine bestimmte Region bzw. für einen Endpunkt abzurufen, die bzw. den Sie verwenden können.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Ersetzen Sie die folgenden Werte:

* Ersetzen Sie `<your_key>` durch Ihren Abonnementschlüssel für den Spracherkennungsdienst. Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<region>` durch die Region, in der Ihre Speech-Ressource erstellt wurde (Beispiel: `eastus` oder `westus`). Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.

Sie erhalten eine Ausgabe, die wie folgt aussieht:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Wenn **properties.publicAvailable** den Wert **true** (wahr) ergibt, ist die Stimme eine öffentliche neuronale Stimme. Andernfalls ist es eine benutzerdefinierte neuronale Stimme.

### <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Bereiten Sie eine Eingabetextdatei vor, entweder als Nur-Text oder als SSML-Text, und fügen Sie dann den folgenden Code in `long_audio_synthesis_client.py` hinzu:

> [!NOTE]
> `concatenateResult` ist ein optionaler Parameter. Wenn dieser Parameter nicht festgelegt ist, wird für jeden Absatz eine Audioausgabe generiert. Indem Sie den Parameter festlegen, können Sie die Audiodateien in eine Ausgabe verketten. 
> `outputFormat` ist ebenfalls optional. Standardmäßig ist die Audioausgabe auf riff-16khz-16bit-16bit-mono-pcm eingestellt. Weitere Informationen zu unterstützten Audioausgabeformaten finden Sie unter [Audioausgabeformate](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Ersetzen Sie die folgenden Werte:

* Ersetzen Sie `<your_key>` durch Ihren Abonnementschlüssel für den Spracherkennungsdienst. Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<region>` durch die Region, in der Ihre Speech-Ressource erstellt wurde (Beispiel: `eastus` oder `westus`). Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<input_file_path>` durch den Pfad zu der Textdatei, die Sie für die Sprachsynthese vorbereitet haben.
* Ersetzen Sie `<locale>` durch das gewünschte Ausgabegebietsschema. Weitere Informationen finden Sie unter [Sprachunterstützung](language-support.md#neural-voices).

Verwenden Sie eine der Stimmen, die von Ihrem vorherigen Aufruf an den `/voices`-Endpunkt zurückgegeben wurden.

* Wenn Sie die öffentliche neuronale Stimme verwenden, ersetzen Sie `<voice_name>` durch die gewünschte Ausgabestimme.
* Um eine benutzerdefinierte neuronale Stimme zu verwenden, ersetzen Sie die Variable `voice_identities` durch Folgendes, und ersetzen Sie `<voice_id>` durch die `id` Ihrer benutzerdefinierten neuronalen Stimme.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Sie erhalten eine Ausgabe, die wie folgt aussieht:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Wenn Sie über mehr als eine Eingabedatei verfügen, müssen Sie mehrere Anforderungen übermitteln. Es gelten jedoch einige Einschränkungen, die Sie beachten müssen.
> * Der Client darf für jedes Azure-Abonnementkonto bis zu **5** Anforderungen pro Sekunde an den Server übermitteln. Wenn dieses Limit überschritten wird, wird der Fehlercode 429 (zu viele Anforderungen) an den Client gesendet. Verringern Sie die Anforderungen pro Sekunde.
> * Der Server darf bis zu **120** Anforderungen für jedes Azure-Abonnementkonto ausführen und in die Warteschlange einreihen. Wenn dieses Limit überschritten wird, gibt der Server den Fehlercode 429 (zu viele Anforderungen) zurück. Warten Sie, und senden Sie erst dann wieder neue Anforderungen, wenn einige Anforderungen abgeschlossen sind.

Die URL in der Ausgabe kann zum Abrufen des Status der Anforderung verwendet werden.

### <a name="get-information-of-a-submitted-request"></a>Abrufen von Informationen zu einer übermittelten Anforderung

Um den Status einer übermittelten Syntheseanforderung zu erhalten, senden Sie einfach eine GET-Anforderung an die URL, die im vorherigen Schritt zurückgegeben wurde.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
Die Ausgabe sieht dann wie folgt aus:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Aus der Eigenschaft `status` können Sie den Status dieser Anforderung lesen. Die Anforderung beginnt mit dem Status `NotStarted`, wechselt dann zu `Running` und wird schließlich zu `Succeeded` oder `Failed`. Sie können eine Schleife verwenden, um diese API abzufragen, bis der Status zu `Succeeded` wechselt.

### <a name="download-audio-result"></a>Herunterladen von Audioergebnissen

Wenn eine Syntheseanforderung erfolgreich durchgeführt wurde, können Sie das Audioergebnis durch Aufrufen der API GET `/files` herunterladen.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Ersetzen Sie `<request_id>` durch die ID der Anforderung, von der Sie das Ergebnis herunterladen möchten. Sie befindet sich in der Antwort des vorherigen Schritts.

Die Ausgabe sieht dann wie folgt aus:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Die Ausgabe enthält Informationen von zwei Dateien. Die Ausgabe mit `"kind": "LongAudioSynthesisScript"` ist das übermittelte Eingabeskript. Die andere Ausgabe mit `"kind": "LongAudioSynthesisResult"` ist das Ergebnis dieser Anforderung.
Das Ergebnis ist eine ZIP-Datei, die die erzeugten Audioausgabedateien zusammen mit einer Kopie des Eingabetexts enthält.

Beide Dateien können über die URL in ihrer `links.contentUrl`-Eigenschaft heruntergeladen werden.

### <a name="get-all-synthesis-requests"></a>Abrufen aller Syntheseanforderungen

Mit dem folgenden Code können Sie eine Liste aller übermittelten Anforderungen abrufen:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Die Ausgabe sieht dann wie folgt aus:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

Die `values`-Eigenschaft enthält eine Liste der Syntheseanforderungen. Die Liste ist paginiert, mit einer maximalen Seitengröße von 100. Wenn mehr als 100 Anforderungen vorliegen, wird eine `"@nextLink"`-Eigenschaft bereitgestellt, um die nächste Seite der paginierten Liste abzurufen.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Sie können auch die Seitengröße und die Sprunganzahl anpassen, indem Sie `skip` und `top` im URL-Parameter angeben.

### <a name="remove-previous-requests"></a>Entfernen vorheriger Anforderungen

Der Dienst speichert bis zu **20.000** Anforderungen für jedes Azure-Abonnementkonto. Wenn die Anzahl Ihrer Anforderungen dieses Limit überschreitet, entfernen Sie vorherige Anforderungen, bevor Sie neue übermitteln. Wenn Sie vorhandene Anforderungen nicht entfernen, erhalten Sie eine Fehlermeldung.

Der folgende Code zeigt, wie Sie eine bestimmte Syntheseanforderung entfernen können.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Wenn die Anforderung erfolgreich entfernt wurde, lautet der Antwortstatuscode „HTTP 204“ (Kein Inhalt).

```console
response.status_code: 204
```

> [!NOTE]
> Anforderungen mit dem Status `NotStarted` oder `Running` können nicht entfernt oder gelöscht werden.

Die abgeschlossene `long_audio_synthesis_client.py` ist auf [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) verfügbar.

## <a name="http-status-codes"></a>HTTP-Statuscodes

In der folgenden Tabelle werden die HTTP-Antwortcodes und -Nachrichten von der REST-API ausführlich erläutert.

| API | HTTP-Statuscode | BESCHREIBUNG | Lösung |
|-----|------------------|-------------|----------|
| Erstellen | 400 | Die Stimmensynthese ist in dieser Region nicht aktiviert. | Verwenden Sie einen Speech-Abonnementschlüssel mit einer unterstützten Region. |
|        | 400 | Nur das **Standard**-Speech-Abonnement für diese Region ist gültig. | Ändern Sie den Speech-Abonnementschlüssel in den Tarif „Standard“. |
|        | 400 | Für das Azure-Konto wurde das Anforderungslimit von 20.000 Anforderungen überschritten. Entfernen Sie einige Anforderungen, bevor Sie neue Anforderungen übermitteln. | Der Server speichert für jedes Azure-Konto bis zu 20.000 Anforderungen. Löschen Sie einige Anforderungen, bevor Sie neue Anforderungen übermitteln. |
|        | 400 | Das folgende Modell kann nicht in der Stimmensynthese verwendet werden: {modelID}. | Stellen Sie sicher, dass der Zustand von {modelID} korrekt ist. |
|        | 400 | Die Region für die Anforderung entspricht nicht der Region für das Modell: {modelID}. | Stellen Sie sicher, dass die Region von {modelID} der Region der Anforderung entspricht. |
|        | 400 | Die Textdatei wird von der Stimmensynthese nur mit UTF-8-Codierung und Bytereihenfolge-Marke unterstützt. | Stellen Sie sicher, dass die Eingabedatei über UTF-8-Codierung und Bytereihenfolge-Marke verfügen. |
|        | 400 | In der Stimmensyntheseanforderung sind nur gültige SSML-Eingaben zulässig. | Stellen Sie sicher, dass die SSML-Eingabeausdrücke korrekt sind. |
|        | 400 | Der Stimmname „{voiceName}“ wurde in der Eingabedatei nicht gefunden. | Der SSML-Stimmname der Eingabe ist nicht auf die Modell-ID abgestimmt. |
|        | 400 | Die Anzahl der Absätze in der Eingabedatei muss weniger als 10.000 Zeichen betragen. | Stellen Sie sicher, dass die Anzahl der Absätze in der Datei weniger als 10.000 beträgt. |
|        | 400 | Die Eingabedatei muss mehr als 400 Zeichen enthalten. | Stellen Sie sicher, dass die Eingabedatei mehr als 400 Zeichen enthält. |
|        | 404 | Das in der Stimmensynthesedefinition deklarierte Modell wurde nicht gefunden: {modelID}. | Stellen Sie sicher, dass die Modell-ID ({modelID}) korrekt ist. |
|        | 429 | Der Grenzwert für die aktive Stimmensynthese wurde überschritten. Warten Sie, bis einige Anforderungen abgeschlossen wurden. | Der Server kann für jedes Azure-Konto bis zu 120 Anforderungen ausführen und in die Warteschlange einreihen. Warten Sie, und senden Sie erst dann wieder neue Anforderungen, wenn einige Anforderungen abgeschlossen wurden. |
| All       | 429 | Zu viele Anforderungen. | Der Client kann für jedes Azure-Konto pro Sekunde bis zu fünf Anforderungen an den Server übermitteln. Verringern Sie die Anforderungen pro Sekunde. |
| Löschen    | 400 | Die Stimmensyntheseaufgabe wird noch verwendet. | Nur Anforderungen mit dem Status **Abgeschlossen** oder **Fehler** können gelöscht werden. |
| GetByID   | 404 | Die angegebene Entität wurde nicht gefunden. | Stellen Sie sicher, dass die Synthese-ID korrekt ist. |

## <a name="regions-and-endpoints"></a>Regionen und Endpunkte

Die API für lange Audioinhalte ist in mehreren Regionen mit eindeutigen Endpunkten verfügbar:

| Region | Endpunkt |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indien, Mitte | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asien, Südosten | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| UK, Süden | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa, Westen | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Audioausgabeformate

Wir unterstützen flexible Audioausgabeformate. Sie können Audioausgaben pro Absatz generieren oder mit dem Parameter „concatenateResult“ zu einer einzelnen Ausgabe verketten. Die folgenden Audioausgabeformate werden von der API für lange Audioinhalte unterstützt:

> [!NOTE]
> Das Standardaudioformat ist riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="sample-code"></a>Beispielcode
Beispielcode für die API für lange Audioinhalte finden Sie auf GitHub.

* [Beispielcode: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Beispielcode: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Beispielcode: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
