---
title: API für lange Audioinhalte (Vorschau) – Spracherkennungsdienst
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
ms.openlocfilehash: 255cfe11f8601abc89a1d96f702f453c2af1ccbd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533059"
---
# <a name="long-audio-api-preview"></a>API für lange Audioinhalte (Vorschau)

Die API für lange Audioinhalte ist für asynchrone lange Sprachsynthesen konzipiert. Beispiele wären etwa Hörbücher, Zeitungsartikel und Dokumente. Diese API gibt keine synthetisierten Audiodaten in Echtzeit zurück, sondern erwartet, dass Sie die Antworten abrufen und die Ausgaben so verwenden, wie sie vom Dienst bereitgestellt werden. Im Gegensatz zur Text-to-Speech-API, die vom Speech SDK verwendet wird, kann die API für lange Audioinhalte synthetisierte Audiodaten erzeugen, die länger als 10 Minuten sind, wodurch sie ideal für Herausgeber und Plattformen für Audioinhalte geeignet ist.

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
> [!NOTE]
> Bei Chinesisch (Festland), Chinesisch (Hongkong SAR), Chinesisch (Taiwan), Japanisch und Koreanisch zählt jedes Wort als zwei Zeichen. 

## <a name="python-example"></a>Beispiel für Python

Dieser Abschnitt enthält Python-Beispiele, die die grundlegende Verwendung der Long Audio-API veranschaulichen. Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in eine Datei namens `voice_synthesis_client.py`.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Diese Bibliotheken werden verwendet, um Argumente zu analysieren, die HTTP-Anforderung zu erstellen und die REST-API für lange Audioinhalte (Sprachsynthese) aufzurufen.

### <a name="get-a-list-of-supported-voices"></a>Abrufen einer Liste der unterstützten Stimmen

Dieser Code ermöglicht es Ihnen, eine vollständige Liste der Stimmen für eine bestimmte Region bzw. für einen Endpunkt abzurufen, die bzw. den Sie verwenden können. Fügen Sie den Code zu `voice_synthesis_client.py` hinzu:

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Führen Sie das Skript mit dem Befehl `python voice_synthesis_client.py --voices -key <your_key> -region <region>` aus, und ersetzen Sie die folgenden Werte:

* Ersetzen Sie `<your_key>` durch Ihren Abonnementschlüssel für den Spracherkennungsdienst. Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<region>` durch die Region, in der Ihre Speech-Ressource erstellt wurde (Beispiel: `eastus` oder `westus`). Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.

Sie erhalten eine Ausgabe, die wie folgt aussieht:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Wenn der Parameter **PublicVoice** auf **True** festgelegt ist, wird eine öffentliche neuronale Stimme verwendet. Andernfalls wird eine benutzerdefinierte neuronale Stimme verwendet.

### <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Bereiten Sie eine Eingabetextdatei vor, entweder als Nur-Text oder als SSML-Text, und fügen Sie dann den folgenden Code in `voice_synthesis_client.py` hinzu:

> [!NOTE]
> 'concatenateResult' ist ein optionaler Parameter. Wenn dieser Parameter nicht festgelegt ist, wird für jeden Absatz eine Audioausgabe generiert. Indem Sie den Parameter festlegen, können Sie die Audiodateien in eine Ausgabe verketten. Standardmäßig ist die Audioausgabe auf riff-16khz-16bit-16bit-mono-pcm eingestellt. Weitere Informationen zu unterstützten Audioausgaben finden Sie unter [Audioausgabeformate](#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Führen Sie das Skript mit dem Befehl `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` aus, und ersetzen Sie die folgenden Werte:

* Ersetzen Sie `<your_key>` durch Ihren Abonnementschlüssel für den Spracherkennungsdienst. Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<region>` durch die Region, in der Ihre Speech-Ressource erstellt wurde (Beispiel: `eastus` oder `westus`). Diese Informationen sind auf der Registerkarte **Übersicht** für Ihre Ressource im [Azure-Portal](https://aka.ms/azureportal) verfügbar.
* Ersetzen Sie `<input>` durch den Pfad zu der Textdatei, die Sie für die Sprachsynthese vorbereitet haben.
* Ersetzen Sie `<locale>` durch das gewünschte Ausgabegebietsschema. Weitere Informationen finden Sie unter [Sprachunterstützung](language-support.md#neural-voices).
* Ersetzen Sie `<voice_guid>` durch die gewünschte Ausgabestimme. Verwenden Sie eine der Stimmen, die von Ihrem vorherigen Aufruf an den `/voicesynthesis/voices`-Endpunkt zurückgegeben wurden.

Sie erhalten eine Ausgabe, die wie folgt aussieht:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Das Ergebnis enthält den Eingabetext und die vom Dienst erzeugten Audioausgabedateien. Sie können diese Dateien in einem ZIP-Archiv herunterladen.

> [!NOTE]
> Wenn Sie über mehr als eine Eingabedatei verfügen, müssen Sie mehrere Anforderungen übermitteln. Es gelten jedoch einige Einschränkungen, die Sie beachten müssen. 
> * Der Client darf für jedes Azure-Abonnementkonto bis zu **5** Anforderungen pro Sekunde an den Server übermitteln. Wenn dieses Limit überschritten wird, wird der Fehlercode 429 (zu viele Anforderungen) an den Client gesendet. Reduzieren Sie die Menge an Anforderungen pro Sekunde.
> * Der Server darf bis zu **120** Anforderungen für jedes Azure-Abonnementkonto ausführen und in die Warteschlange einreihen. Wenn dieses Limit überschritten wird, gibt der Server den Fehlercode 429 (zu viele Anforderungen) zurück. Warten Sie, und senden Sie erst dann wieder neue Anforderungen, wenn einige Anforderungen abgeschlossen sind.

### <a name="remove-previous-requests"></a>Entfernen vorheriger Anforderungen

Der Dienst speichert bis zu **20.000** Anforderungen für jedes Azure-Abonnementkonto. Wenn die Anzahl Ihrer Anforderungen dieses Limit überschreitet, entfernen Sie vorherige Anforderungen, bevor Sie neue übermitteln. Wenn Sie vorhandene Anforderungen nicht entfernen, erhalten Sie eine Fehlermeldung.

Fügen Sie den folgenden Code zu `voice_synthesis_client.py` hinzu:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Führen Sie `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` aus, eine Liste Ihrer Syntheseanforderungen abzurufen. Eine Ausgabe wie die folgende sollte angezeigt werden:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Um eine Anforderung zu löschen, führen Sie `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` aus, und ersetzen Sie `<synthesis_id>` durch einen Anforderungs-ID-Wert, der von der vorherigen Anforderung zurückgegeben wurde.

> [!NOTE]
> Anforderungen mit dem Status „Wird ausgeführt“/„Wartend“ können nicht entfernt oder gelöscht werden.

Die abgeschlossene `voice_synthesis_client.py` ist auf [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) verfügbar.

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