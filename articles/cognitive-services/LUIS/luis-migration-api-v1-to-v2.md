---
title: Migration von API-Version 1 zu API-Version 2
titleSuffix: Azure Cognitive Services
description: Die Endpunkt- und Erstellungs-APIs der Version 1 von Language Understanding sind veraltet. In dieser Anleitung erfahren Sie, wie Sie zu den Endpunkt- und Erstellungs-APIs der Version 2 migrieren.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018751"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Anleitung zur Migration von API-Version 1 zu API-Version 2 für LUIS-Apps
Die [Endpunkt](https://aka.ms/v1-endpoint-api-docs)- und [Erstellungs](https://aka.ms/v1-authoring-api-docs)-APIs der Version 1 sind veraltet. In diesem Handbuch erfahren Sie, wie Sie zu den [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356)- und [Erstellungs](https://go.microsoft.com/fwlink/?linkid=2092087)-APIs der Version 2 migrieren.

## <a name="new-azure-regions"></a>Neue Azure-Regionen
LUIS stellt neue [Regionen](./luis-reference-regions.md) für die LUIS-APIs bereit. LUIS bietet ein eigenes Portal für Regionsgruppen. Die Anwendung muss in derselben Region erstellt werden, die Sie abfragen möchten. Regionen werden von Anwendungen nicht automatisch migriert. Sie exportieren die App aus einer Region und importieren sie dann in eine andere, damit sie in einer neuen Region verfügbar ist.

## <a name="authoring-route-changes"></a>Erstellen von Routenänderungen
Die Route der Erstellungs-API wurde von der Route **prog** zur Route **api** geändert.


| version | Route |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Endpunkt-Routenänderungen
Die Endpunkt-API weist neue Abfragezeichenfolgen-Parameter sowie eine andere Antwort auf. Wenn das verbose-Flag auf TRUE festgelegt ist, werden alle Absichten unabhängig von ihrer Bewertung, zusätzlich zur höchstbewerteten Absicht topScoringIntent in einem Array mit dem Namen „intents“ zurückgegeben.

| version | GET-Route |
|--|--|
|1|/luis/v1/application?ID={App-ID}&q={q}|
|2|/luis/v2.0/apps/{App-ID}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&Bing-Rechtschreibprüfungs-Abonnementschlüssel][&log]|


Erfolgsantwort v1-Endpunkt:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

Erfolgsantwort v2-Endpunkt:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Schlüsselverwaltung nicht mehr in der API
Die Abonnementendpunkt-Schlüssel-APIs sind veraltet und geben den Fehler „410 GONE“ zurück.

| version | Route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{Abonnementschlüssel}|

Azure-[Endpunktschlüssel](luis-how-to-azure-subscription.md) werden im Azure-Portal generiert. Sie Weisen den Schlüssel auf der Seite **[Veröffentlichen](luis-how-to-azure-subscription.md)** einer LUIS-App zu. Sie müssen den tatsächlichen Schlüsselwert nicht kennen. LUIS verwendet für die Zuweisung den Namen des Abonnements.

## <a name="new-versioning-route"></a>Neue Versionsverwaltungsroute
Die v2-Modell ist nun in einer [Version](luis-how-to-manage-versions.md) enthalten. Ein Versionsname umfasst 10 Zeichen in der Route. Die Standardversion ist 0.1.

| version | Route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{App-ID}/entities|
|2|/luis/**api**/v2.0/apps/{App-ID}/**versions**/{Versions-ID}/entities|

## <a name="metadata-renamed"></a>Metadaten umbenannt
Mehrere APIs, die LUIS Metadaten zurückgeben, weisen neue Namen auf.

| v1-Routenname | v2-Routenname |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>„Beispiel“ in „Vorschlag“ umbenannt
LUIS schlägt Äußerungen von vorhandenen [Endpunktäußerungen](luis-how-to-review-endpoint-utterances.md) vor, die das Modell verbessern können. In der vorherigen Version wurde dies als **Beispiel** bezeichnet. In der neuen Version wurde diese Bezeichnung von „Beispiel“ in **Vorschlag** geändert. Auf der LUIS-Website wird dies **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Endpunktäußerungen überprüfen) genannt.

| version | Route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{App-ID}/entities/{Entitäts-ID}/**sample**|
|1|/luis/v1.0/**prog**/apps/{App-ID}/intents/{Absichts-ID}/**sample**|
|2|/luis/**api**/v2.0/apps/{App-ID}/**versions**/{Versions-ID}/entities/{Entitäts-ID}/**suggest**|
|2|/luis/**api**/v2.0/apps/{App-ID}/**versions**/{Versions-ID}/intents/{Absichts-ID}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>Erstellen einer App aus vordefinierten Domänen
[Vordefinierte Domänen](./howto-add-prebuilt-models.md) stellen ein vordefiniertes Domänenmodell bereit. Mit vordefinierten Domänen können Sie Ihre LUIS-Anwendung schnell für gebräuchliche Domänen entwickeln. Diese API ermöglicht es Ihnen, eine neue App basierend auf einer vordefinierten Domäne zu erstellen. Die Antwort ist die neue App-ID.

|v2-Route|Verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |get, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{Kultur}  |get|

## <a name="importing-1x-app-into-2x"></a>Importieren einer 1.x-App in 2.x
Der JSON-Code der exportierten 1.x-App enthält einige Bereiche, die Sie vor dem Importieren in [LUIS][LUIS] 2.0 ändern müssen.

### <a name="prebuilt-entities"></a>Vordefinierte Entitäten
Die [vordefinierten Entitäten](./howto-add-prebuilt-models.md) wurden geändert. Stellen Sie sicher, dass Sie die vordefinierten Entitäten von v2 verwenden. Dazu gehört die Verwendung von [datetimeV2](luis-reference-prebuilt-datetimev2.md) anstelle von datetime.

### <a name="actions"></a>Aktionen
Die actions-Eigenschaft ist nicht mehr gültig. Sie sollte leer sein.

### <a name="labeled-utterances"></a>Bezeichnete Äußerungen
In v1 konnten bezeichnete Äußerungen Leerzeichen am Anfang oder Ende des Worts oder Ausdrucks enthalten. Die Leerzeichen wurden entfernt.

## <a name="common-reasons-for-http-response-status-codes"></a>Häufige Ursachen für HTTP-Antwortstatuscodes
Weitere Informationen finden Sie unter [LUIS-API-Antwortcodes](luis-reference-response-codes.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die v2-API-Dokumentation zum Aktualisieren vorhandener REST-Aufrufe an die LUIS-[Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356)- und [Erstellungs](https://go.microsoft.com/fwlink/?linkid=2092087)-APIs.

[LUIS]: ./luis-reference-regions.md