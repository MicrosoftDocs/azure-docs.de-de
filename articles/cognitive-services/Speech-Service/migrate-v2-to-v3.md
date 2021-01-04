---
title: 'Migration von Version 2 zu Version 3 der REST-API: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Dieses Dokument hilft Entwicklern bei der Migration von Code von Version 2 zu Version 3 der REST-API für die Spracherkennung der Speech-Dienste.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: c5bc00ecf5e4c8ae440ce6610e9be8c8f77ed666
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862206"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migration von Version 2.0 zu Version 3.0 der REST-API

Im Vergleich zur Version 2 ist die Version 3 der REST-API der Speech-Dienste für die Spracherkennung zuverlässiger, einfacher zu verwenden und konsistenter mit APIs für ähnliche Dienste. Die meisten Teams können innerhalb von ein oder zwei Tagen von Version 2 zu Version 3 migrieren.

## <a name="forward-compatibility"></a>Aufwärtskompatibilität

Alle Entitäten aus Version 2 können auch in der API der Version 3 unter derselben Identität gefunden werden. Wenn sich das Schema eines Ergebnisses geändert hat (z. B. bei Transkriptionen), verwendet das Ergebnis eines GET-Aufrufs in der Version 3 der API das Schema der Version 3. Das Ergebnis eines GET-Aufrufs in der Version 2 der API verwendet dasselbe Schema der Version 2. Neu erstellte Entitäten der Version 3 sind **nicht** in Ergebnissen von APIs der Version 2 verfügbar.

## <a name="breaking-changes"></a>Aktuelle Änderungen

Die Liste der Breaking Changes wurde nach dem Umfang der zur Anpassung erforderlichen Änderungen sortiert. Nur einige wenige Änderungen erfordern nicht triviale Änderungen im Aufrufcode. Die meisten Änderungen erfordern lediglich eine Änderung der Elementnamen.

### <a name="host-name-changes"></a>Änderungen des Hostnamens

Die Endpunkthostnamen haben sich von `{region}.cris.ai` zu `{region}.api.cognitive.microsoft.com` geändert. Pfade zu den neuen Endpunkten enthalten nicht mehr `api/`, da es Teil des Hostnamens ist. Das [Swagger-Dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) listet gültige Regionen und Pfade auf.

### <a name="identity-of-an-entity"></a>Identität einer Entität

Die Eigenschaft `id` ist jetzt `self`. In Version 2 musste ein Benutzer der API wissen, wie unsere Pfade für die API erstellt werden. Dies war nicht erweiterbar und erforderte vom Benutzer unnötige Arbeit. Die Eigenschaft `id` (UUID) wird ersetzt durch `self` (Zeichenfolge), die der Position der Entität (URL) entspricht. Der Wert ist zwischen all Ihren Entitäten nach wie vor eindeutig. Wenn `id` als Zeichenfolge in Ihrem Code gespeichert ist, reicht eine Umbenennung aus, um das neue Schema zu unterstützen. Sie können jetzt den `self`-Inhalt als URL für die REST-Aufrufe `GET`, `PATCH` und `DELETE` für Ihre Entität verwenden.

Wenn die Entität über zusätzliche Funktionen verfügt, die über andere Pfade verfügbar sind, werden diese unter `links` aufgelistet. Das folgende Beispiel für die Transkription zeigt eine separate Methode, um den Inhalt der Transkription mit `GET` abzurufen:

**Transkription der Version 2:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**Transkription der Version 3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Abhängig von der Implementierung Ihres Codes reicht es möglicherweise nicht aus, die Eigenschaft umzubenennen. Wir empfehlen, die zurückgegebenen `self`- und `links`-Werte als Ziel-URLs Ihrer REST-Aufrufe zu verwenden, anstatt Pfade in Ihrem Client zu generieren. Durch die Verwendung der zurückgegebenen URLs können Sie sicher sein, dass zukünftige Änderungen der Pfade Ihren Clientcode nicht beeinträchtigen.

### <a name="working-with-collections-of-entities"></a>Arbeiten mit Auflistungen von Entitäten

Zuvor gab die API der Version 2 alle verfügbaren Entitäten in einem Ergebnis zurück. Um eine differenziertere Steuerung der erwarteten Antwortgröße der Version 3 zu ermöglichen, sind alle Auflistungsergebnisse paginiert. Sie haben die Kontrolle über die Anzahl der zurückgegebenen Entitäten und den Startoffset der Seite. Dieses Verhalten erleichtert es, die Runtime des Antwortprozessors vorherzusagen.

Die grundlegende Form der Antwort ist für alle Auflistungen gleich:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

Die Eigenschaft `values` enthält eine Teilmenge der verfügbaren Auflistungsentitäten. Die Anzahl und der Offset können mithilfe der Abfrageparameter `skip` und `top` gesteuert werden. Wenn `@nextLink` nicht `null` ist, stehen mehr Daten zur Verfügung und der nächste Datenbatch kann durch einen GET-Aufruf für `$.@nextLink` abgerufen werden.

Diese Änderung erfordert den Aufruf von `GET` für die Auflistung in einer Schleife, bis alle Elemente zurückgegeben wurden.

### <a name="creating-transcriptions"></a>Erstellen von Transkriptionen

Eine detaillierte Beschreibung zum Erstellen von Transkriptionsbatches finden Sie in [Vorgehensweise bei der Batchtranskription](./batch-transcription.md).

Mit der Transkriptions-API der Version 3 können Sie bestimmte Transkriptionsoptionen explizit festlegen. Alle (optionalen) Konfigurationseigenschaften können jetzt in der `properties`-Eigenschaft festgelegt werden.
Version 3 unterstützt auch mehrere Eingabedateien, sodass eine Liste von URLs erforderlich ist und nicht eine einzelne URL wie in Version 2. Der v2-Eigenschaftsname `recordingsUrl` ist jetzt `contentUrls` in Version 3. Die Funktionalität der Stimmungsanalyse in Transkriptionen wurde in Version 3 entfernt. Optionen für die Stimmungsanalyse finden Sie unter Microsoft Cognitive Service-[Textanalyse](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/).

Die neue Eigenschaft `timeToLive` unter `properties` kann dazu beitragen, die bestehenden abgeschlossenen Entitäten zu löschen. `timeToLive` gibt eine Dauer an, nach der eine abgeschlossene Entität automatisch gelöscht wird. Legen Sie einen hohen Wert fest (z. B. `PT12H`), wenn die Entitäten kontinuierlich nachverfolgt, verbraucht und gelöscht werden und daher normalerweise lange vor Ablauf von 12 Stunden verarbeitet werden.

**POST-Anforderungstext der v2-Transkription:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**POST-Anforderungstext der v3-Transkription:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Format der v3-Transkriptionsergebnisse

Das Schema der Transkriptionsergebnisse hat sich geringfügig geändert, um es an die von Echtzeitendpunkten erstellten Transkriptionen anzupassen. Eine ausführliche Beschreibung des neuen Formats finden Sie unter [Vorgehensweise bei der Batchtranskription](./batch-transcription.md). Das Schema des Ergebnisses ist in unserem [GitHub-Beispielrepository](https://aka.ms/csspeech/samples) unter `samples/batch/transcriptionresult_v3.schema.json` veröffentlicht.

Eigenschaftsnamen weisen jetzt die gemischte Groß-/Kleinschreibung auf und die Werte für `channel` und `speaker` verwenden jetzt ganzzahlige Typen. Das Format für die Dauer verwendet jetzt die in ISO 8601 beschriebene Struktur, die der in anderen Azure APIs verwendeten Formatierung der Dauer entspricht.

Beispiel eines Transkriptionsergebnisses der Version 3. Die Unterschiede sind in den Kommentaren beschrieben.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>Abrufen des Inhalts von Entitäten und der Ergebnisse

In Version 2 wurden die Links zu den Eingabe- oder Ergebnisdateien mit den restlichen Metadaten der Entität inline gestellt. Als eine Verbesserung in Version 3 gibt es eine klare Trennung zwischen den Metadaten der Entitäten (die von einem GET-Aufruf für `$.self` zurückgegeben werden) sowie den Details und Anmeldeinformationen für den Zugriff auf die Ergebnisdateien. Diese Trennung trägt zum Schutz der Kundendaten bei und ermöglicht eine differenziertere Kontrolle über die Gültigkeitsdauer der Anmeldeinformationen.

In Version 3 enthalten `links` eine Untereigenschaft namens `files` für den Fall, dass die Entität Daten (Datasets, Transkriptionen, Endpunkte oder Auswertungen) verfügbar macht. Ein GET-Aufruf für `$.links.files` gibt eine Liste von Dateien und eine SAS-URL für den Zugriff auf den Inhalt der einzelnen Dateien zurück. Um die Gültigkeitsdauer der SAS-URLs zu steuern, kann der Abfrageparameter `sasValidityInSeconds` zur Angabe der Lebensdauer verwendet werden.

**Transkription der Version 2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**Transkription der Version 3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Ein GET-Aufruf für `$.links.files` würde zu folgendem führen:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

Die Eigenschaft `kind` gibt das Format des Inhalts der Datei an. Bei Transkriptionen sind die Dateien der Art `TranscriptionReport` die Zusammenfassung des Auftrags und die Dateien der Art `Transcription` sind das Ergebnis des Auftrags selbst.

### <a name="customizing-models"></a>Anpassen von Modellen

Vor Version 3 gab es eine Unterscheidung zwischen einem _Akustikmodell_ und einem _Sprachmodell_, wenn ein Modell trainiert wurde. Diese Unterscheidung führte dazu, dass bei der Erstellung von Endpunkten oder Transkriptionen mehrere Modelle angegeben werden mussten. Um diesen Prozess für einen Aufrufer zu vereinfachen, haben wir die Unterschiede entfernt und alles vom Inhalt der Datasets abhängig gemacht, die für das Modelltraining verwendet werden. Mit dieser Änderung unterstützt die Modellerstellung jetzt gemischte Datasets (Sprachdaten und Akustikdaten). Endpunkte und Transkriptionen erfordern jetzt nur noch ein Modell.

Mit dieser Änderung wurde die Notwendigkeit eines `kind` im `POST`-Vorgang entfernt und das `datasets[]`-Array kann jetzt mehrere Datasets gleicher oder gemischter Art enthalten.

Die Akustikdaten werden beim Sprachtraining automatisch intern verwendet, um die Ergebnisse eines trainierten Modells zu verbessern. Im Allgemeinen liefern Modelle, die mit der API der Version 3 erstellt wurden, genauere Ergebnisse als Modelle, die mit der API der Version 2 erstellt wurden.

### <a name="retrieving-base-and-custom-models"></a>Abrufen von Basis- und benutzerdefinierten Modellen

In Version 3 wurden die Auflistungen der „Basismodelle“ von den kundeneigenen „angepassten Modellen“ getrennt, um das Abrufen der verfügbaren Modelle zu vereinfachen. Die beiden Routen sind jetzt `GET /speechtotext/v3.0/models/base` und `GET /speechtotext/v3.0/models/`.

In Version 2 wurden alle Modelle zusammen in einer einzelnen Antwort zurückgegeben.

### <a name="name-of-an-entity"></a>Name einer Entität

Die Eigenschaft `name` ist jetzt `displayName`. Dies entspricht anderen Azure-APIs, die keine Identitätseigenschaften angeben. Der Wert dieser Eigenschaft darf nicht eindeutig sein und kann nach der Erstellung der Entität mit einem `PATCH`-Vorgang geändert werden.

**Transkription der Version 2:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**Transkription der Version 3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Zugreifen auf referenzierte Entitäten

In Version 2 wurden referenzierte Entitäten immer unterstrichen, z. B. die verwendeten Modelle eines Endpunkts. Die Schachtelung von Entitäten führte zu umfangreichen Antworten, und die Consumer nutzten die geschachtelten Inhalte nur selten. Um den Umfang der Antwort zu verringern und die Leistung zu verbessern, werden die referenzierten Entitäten in der Antwort nicht mehr aufgeführt. Stattdessen wird ein Verweis auf die andere Entität angezeigt, der direkt für einen nachfolgenden `GET`-Aufruf (ebenfalls eine URL) verwendet werden kann, der dasselbe Muster wie der `self`-Link aufweist.

**Transkription der Version 2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**Transkription der Version 3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Wenn Sie die Details eines referenzierten Modells nutzen müssen (wie im obigen Beispiel gezeigt), rufen Sie einfach ein GET für `$.model.self` auf.

### <a name="retrieving-endpoint-logs"></a>Abrufen von Endpunktprotokollen

Version 2 des Diensts unterstützte die Protokollierung von Endpunktergebnissen. Um die Ergebnisse eines Endpunkts mit Version 2 abzurufen, würden Sie einen „Datenexport“ durchführen, der eine Momentaufnahme der durch einen Zeitbereich definierten Ergebnisse darstellt. Das Exportieren von Datenbatches war unflexibel. Die API der Version 3 ermöglicht den Zugriff auf jede einzelne Datei und gestattet deren Iteration.

**Ein erfolgreich ausgeführter Endpunkt der Version 3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Antwort von GET `$.links.logs`:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Die Paginierung für Endpunktprotokolle funktioniert ähnlich wie bei allen anderen Auflistungen, außer dass kein Offset angegeben werden kann. Aufgrund der großen Menge an verfügbaren Daten wird die Paginierung vom Server bestimmt.

In Version 3 kann jedes Endpunktprotokoll einzeln gelöscht werden, indem ein `DELETE`-Vorgang für den `self` einer Datei ausgegeben oder aber `DELETE` für `$.links.logs` verwendet wird. Um ein Enddatum anzugeben, kann der Anforderung der Abfrageparameter `endDate` hinzugefügt werden.

### <a name="using-custom-properties"></a>Verwenden von benutzerdefinierten Eigenschaften

Zur besseren Trennung der benutzerdefinierten Eigenschaften von den optionalen Konfigurationseigenschaften befinden sich jetzt alle explizit benannten Eigenschaften in der `properties`-Eigenschaft und alle von den aufrufenden Funktionen definierten Eigenschaften in der `customProperties`-Eigenschaft.

**Transkriptionsentität der Version 2:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**Transkriptionsentität der Version 3:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Diese Änderung ermöglicht es Ihnen auch, die richtigen Typen für alle explizit benannten Eigenschaften unter `properties` zu verwenden. (z. B. „boolean“ anstelle von „string“).

### <a name="response-headers"></a>Antwortheader

Version 3 gibt bei `POST`-Anforderungen nicht mehr den `Operation-Location`-Header zusätzlich zum `Location`-Header zurück. Der Wert beider Header in Version 2 war derselbe. Jetzt wird nur `Location` zurückgegeben.

Da die neue API-Version jetzt von Azure API Management (APIM) verwaltet wird, sind die mit der Drosselung zusammenhängenden Header `X-RateLimit-Limit`, `X-RateLimit-Remaining` und `X-RateLimit-Reset` nicht in den Antwortheadern enthalten.

### <a name="accuracy-tests"></a>Genauigkeitsprüfungen

Genauigkeitsprüfungen wurden in Auswertungen umbenannt, da der neue Name besser beschreibt, was sie darstellen. Die neuen Pfade sind: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`.

## <a name="next-steps"></a>Nächste Schritte

Überprüfen Sie alle Features dieser häufig verwendeten REST-APIs, die von Speech-Diensten bereitgestellt werden:

* [Spracherkennungs-REST-API](rest-speech-to-text.md)
* [Swagger-Dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) für Version 3 der REST-API
* Beispielcode zum Ausführen von Batchtranskriptionen finden Sie im [GitHub-Beispielrepository](https://aka.ms/csspeech/samples) im Unterverzeichnis `samples/batch`.
