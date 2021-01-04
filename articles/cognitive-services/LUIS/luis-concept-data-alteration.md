---
title: 'Ändern von Daten: LUIS'
description: Erfahren Sie, wie Daten vor der Vorhersage in Language Understanding Intelligent Service (LUIS) geändert werden können.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026009"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändern von Äußerungsdaten vor oder während der Vorhersage
LUIS bietet Möglichkeiten zum Bearbeiten einer Äußerung vor oder während der Vorhersage. Dazu gehören eine [Korrektur der Rechtschreibung](luis-tutorial-bing-spellcheck.md) und das Beheben von Zeitzonenproblemen für die vordefinierte Entität [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Korrigieren von Rechtschreibfehlern in Äußerungen


### <a name="v3-runtime"></a>V3-Runtime

Führen Sie eine Vorverarbeitung von Text zwecks Rechtschreibkorrektur durch, bevor Sie die Äußerungen an LUIS senden. Verwenden Sie Beispieläußerungen mit richtiger Schreibung, um sicherzustellen, dass Sie die richtigen Vorhersagen erhalten.

Verwenden Sie die [Bing-Rechtschreibprüfung](../bing-spell-check/overview.md), um Text vor dem Senden an LUIS zu korrigieren.

### <a name="prior-to-v3-runtime"></a>Vor der V3-Runtime

LUIS verwendet die [Bing-Rechtschreibprüfungs-API V7](../Bing-Spell-Check/overview.md) um Rechtschreibfehler in Äußerungen zu korrigieren. LUIS benötigt den diesem Dienst zugewiesenen Schlüssel. Erstellen Sie den Schlüssel, und fügen Sie den Schlüssel dann als Parameter der Abfragezeichenfolge an den [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) hinzu.

Der Endpunkt benötigt zwei Parameter, damit Rechtschreibkorrekturen funktionieren:

|Parameter|Wert|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing-Rechtschreibprüfungs-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) – Endpunktschlüssel|

Wenn die [Bing-Rechtschreibprüfungs-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) einen Fehler erkennt, werden die ursprüngliche Äußerung und die korrigierte Äußerung zusammen mit Vorhersagen vom Endpunkt zurückgegeben.

#### <a name="v2-prediction-endpoint-response"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Liste zulässiger Wörter
Die in LUIS verwendete Bing-Rechtschreibprüfungs-API unterstützt keine Liste mit Wörtern, die bei den Änderungen durch die Rechtschreibprüfung ignoriert werden sollen. Wenn Sie eine Liste für Wörter oder Akronyme benötigen, verarbeiten Sie die Äußerung im Client, bevor Sie die Äußerung zur Absichtsvorhersage an LUIS senden.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändern der Zeitzone von vordefinierten datetimeV2-Entitäten
Wenn eine LUIS-App die vordefinierte Entität [datetimeV2](luis-reference-prebuilt-datetimev2.md) verwendet, kann ein datetime-Wert in der Vorhersageantwort zurückgegeben werden. Der richtige datetime-Wert für die Rückgaben wird anhand der Zeitzone der Anforderung bestimmt. Wenn die Anforderung von einem Bot oder einer anderen zentralen Anwendung vor dem Empfang durch LUIS stammt, korrigieren Sie die von LUIS verwendete Zeitzone.

### <a name="v3-prediction-api-to-alter-timezone"></a>V3-Vorhersage-API zum Ändern der Zeitzone

In V3 bestimmt `datetimeReference` den Zeitzonenunterschied. Erfahren Sie mehr über [V3-Vorhersagen](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>V2-Vorhersage-API zum Ändern der Zeitzone
Die Zeitzone wird durch Hinzufügen der Zeitzone des Benutzers zum Endpunkt korrigiert. Dazu wird der zur API-Version passende `timezoneOffset`-Parameter verwendet. Der Wert des Parameters sollte die positive oder negative Zahl zum Ändern der Uhrzeit in Minuten sein.

#### <a name="v2-prediction-daylight-savings-example"></a>Sommerzeitbeispiel für die V2-Vorhersage
Wenn Sie den zurückgegebenen vordefinierten datetimeV2-Wert für die Anpassung an die Sommerzeit benötigen, sollten Sie den QueryString-Parameter mit einem +/-Wert in Minuten für die Abfrage an den [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) verwenden.

Hinzufügen von 60 Minuten:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Entfernen von 60 Minuten:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>Der C#-Code der V2-Vorhersage bestimmt den richtigen Parameterwert

Der folgende C#-Code verwendet die [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples)-Methode der [TimeZoneInfo](/dotnet/api/system.timezoneinfo)-Klasse, um den richtigen Wert für den Zeitzonenunterschied im Ausgang von der Systemzeit zu bestimmen:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Korrigieren Sie Rechtschreibfehler mithilfe dieses Tutorials.](luis-tutorial-bing-spellcheck.md)