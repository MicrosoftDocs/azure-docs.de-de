---
title: 'Schnellstart: Verwenden von cURL und REST zum Verwalten der Wissensdatenbank: QnA Maker'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Ihre Wissensdatenbank mit den REST-APIs erstellen, veröffentlichen und abfragen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 11/09/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 7592b9fb509f39504ad2399d0e939ceca1156221
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351094"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Schnellstart: Verwenden von cURL und REST zum Verwalten der Wissensdatenbank

In dieser Schnellstartanleitung wird Schritt für Schritt beschrieben, wie Sie Ihre Wissensdatenbank erstellen, veröffentlichen und abfragen. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../index.yml). Das Modell für die Wissensdatenbank wird im JSON-Code definiert, der im Text der API-Anforderung gesendet wird.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

* Aktuelle Version von [cURL](https://curl.haxx.se/). In den Schnellstartanleitungen werden mehrere Befehlszeilenschalter verwendet, die in der [cURL-Dokumentation](https://curl.haxx.se/docs/manpage.html) angegeben sind.
* Sie benötigen eine [QnA Maker-Ressource](../How-To/set-up-qnamaker-service-azure.md), um den Schlüssel und Ressourcennamen verwenden zu können. Sie haben den **Namen** der Ressource während der Ressourcenerstellung eingegeben. Der Schlüssel wurde anschließend für Sie erstellt. Der Ressourcenname wird als Unterdomäne für Ihren Endpunkt verwendet. Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Ressourcennamen abzurufen. Der Ressourcenname ist die erste Unterdomäne der Endpunkt-URL:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> In den folgenden Bash-Beispielen wird das Zeilenfortsetzungszeichen `\` verwendet. Verwenden Sie dieses Zeichen, falls in Ihrer Konsole oder Ihrem Terminal ein anderes Zeilenfortsetzungszeichen genutzt wird.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

* Aktuelle Version von [cURL](https://curl.haxx.se/). In den Schnellstartanleitungen werden mehrere Befehlszeilenschalter verwendet, die in der [cURL-Dokumentation](https://curl.haxx.se/docs/manpage.html) angegeben sind.
* Sie benötigen eine [QnA Maker-Ressource](../How-To/set-up-qnamaker-service-azure.md), um den Schlüssel und Ressourcennamen verwenden zu können. Sie haben den **Namen** der Ressource während der Ressourcenerstellung eingegeben. Der Schlüssel wurde anschließend für Sie erstellt. Der Ressourcenname wird als Unterdomäne für Ihren Endpunkt verwendet. Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und den Ressourcennamen abzurufen. Der Ressourcenname ist die erste Unterdomäne der Endpunkt-URL:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> In den folgenden Bash-Beispielen wird das Zeilenfortsetzungszeichen `\` verwendet. Verwenden Sie dieses Zeichen, falls in Ihrer Konsole oder Ihrem Terminal ein anderes Zeilenfortsetzungszeichen genutzt wird.

---

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Sie benötigen die folgenden Informationen, um mit den REST-APIs und mit cURL eine Wissensdatenbank zu erstellen:

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|JSON-Code zur Beschreibung der Wissensdatenbank|Parameter `-d`|[Beispiele](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) für JSON|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Die cURL-Antwort aus QnA Maker enthält die `operationId`, die benötigt wird, um den [Status des Vorgangs abzurufen](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)


Sie benötigen die folgenden Informationen, um mit den REST-APIs und mit cURL eine Wissensdatenbank zu erstellen:

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|JSON-Code zur Beschreibung der Wissensdatenbank|Parameter `-d`|[Beispiele](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) für JSON|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Die cURL-Antwort aus QnA Maker enthält die `operationId`, die benötigt wird, um den [Status des Vorgangs abzurufen](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Abrufen des Status eines Vorgangs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Da der Vorgang beim Erstellen einer Wissensdatenbank asynchron ist, enthält die Antwort Informationen zum Ermitteln des Status.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|Vorgangs-ID|URL-Route|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Vorgangs-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Die cURL-Antwort enthält den Status. Wenn der Status des Vorgangs „Erfolgreich“ lautet, enthält `resourceLocation` die Wissensdatenbank-ID.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```
# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)


Da der Vorgang beim Erstellen einer Wissensdatenbank asynchron ist, enthält die Antwort Informationen zum Ermitteln des Status.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|Vorgangs-ID|URL-Route|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Vorgangs-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Die cURL-Antwort enthält den Status. Wenn der Status des Vorgangs „Erfolgreich“ lautet, enthält `resourceLocation` die Wissensdatenbank-ID.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

---

## <a name="publish-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Sie benötigen Folgendes, bevor Sie die Wissensdatenbank abfragen können:
* Veröffentlichen einer Wissensdatenbank
* Abrufen des Laufzeitschlüssels für den Endpunkt

In diesem Schritt wird die Wissensdatenbank veröffentlicht. Das Abrufen des Laufzeitschlüssels für den Endpunkt ist eine [separate Aufgabe](#get-published-knowledge-bases-runtime-endpoint-key).

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Sie benötigen Folgendes, bevor Sie die Wissensdatenbank abfragen können:
* Veröffentlichen einer Wissensdatenbank
* Abrufen des Laufzeitschlüssels für den Endpunkt

In diesem Schritt wird die Wissensdatenbank veröffentlicht. Das Abrufen des Laufzeitschlüssels für den Endpunkt ist eine [separate Aufgabe](#get-published-knowledge-bases-runtime-endpoint-key).

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Abrufen des Laufzeitschlüssels für den Endpunkt für eine veröffentlichte Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Sie benötigen Folgendes, bevor Sie die Wissensdatenbank abfragen können:
* Veröffentlichen einer Wissensdatenbank
* Abrufen des Laufzeitschlüssels für den Endpunkt

In diesem Schritt wird der Laufzeitschlüssel für den Endpunkt abgerufen. Die Veröffentlichung der Wissensdatenbank ist eine [separate Aufgabe](#publish-knowledge-base).

Der Laufzeitschlüssel für den Endpunkt ist für alle Wissensdatenbanken gleich, die die QnA Maker-Ressource nutzen.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Die cURL-Antwort enthält die Laufzeitschlüssel für die Endpunkte. Verwenden Sie beim Abfragen nur einen der Schlüssel, um eine Antwort von der Wissensdatenbank zu erhalten.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Sie benötigen Folgendes, bevor Sie die Wissensdatenbank abfragen können:
* Veröffentlichen einer Wissensdatenbank
* Abrufen des Laufzeitschlüssels für den Endpunkt

In diesem Schritt wird der Laufzeitschlüssel für den Endpunkt abgerufen. Die Veröffentlichung der Wissensdatenbank ist eine [separate Aufgabe](#publish-knowledge-base).

Der Laufzeitschlüssel für den Endpunkt ist für alle Wissensdatenbanken gleich, die die QnA Maker-Ressource nutzen.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Die cURL-Antwort enthält die Laufzeitschlüssel für die Endpunkte. Verwenden Sie beim Abfragen nur einen der Schlüssel, um eine Antwort von der Wissensdatenbank zu erhalten.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>Abfragen einer Antwort von der veröffentlichten Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Das Abrufen einer Antwort aus der Wissensdatenbank erfolgt über eine andere Laufzeit als beim Verwalten der Wissensdatenbank. Da es sich um eine separate Laufzeit handelt, müssen Sie für die Authentifizierung einen Laufzeitschlüssel verwenden.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Laufzeitschlüssel|Parameter `-h` für den Header `Authorization`|Der Schlüssel ist Teil einer Zeichenfolge, die das Wort `Endpointkey ` enthält. Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON-Code zur Beschreibung der Abfrage|Parameter `-d`|[Anforderungstextparameter](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) und [JSON-Beispiele](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Eine erfolgreiche Antwort enthält die beste Antwort und die anderen Informationen, die von einer Clientanwendung, z. B. einem Chatbot, zum Anzeigen einer Antwort für den Benutzer benötigt werden.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Das Abrufen einer Antwort aus der Wissensdatenbank erfolgt über eine andere Laufzeit als beim Verwalten der Wissensdatenbank. Da es sich um eine separate Laufzeit handelt, müssen Sie für die Authentifizierung einen Laufzeitschlüssel verwenden.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON-Code zur Beschreibung der Abfrage|Parameter `-d`|[Anforderungstextparameter](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) und [JSON-Beispiele](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Eine erfolgreiche Antwort enthält die beste Antwort und die anderen Informationen, die von einer Clientanwendung, z. B. einem Chatbot, zum Anzeigen einer Antwort für den Benutzer benötigt werden.


---

## <a name="delete-knowledge-base"></a>Löschen einer Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Löschen Sie die Wissensdatenbank, wenn Sie die Nutzung beendet haben.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Löschen Sie die Wissensdatenbank, wenn Sie die Nutzung beendet haben.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|QnA Maker-Ressourcenname|URL|Wird zum Erstellen der URL verwendet.|
|QnA Maker-Ressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim QnA Maker-Dienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

---

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Referenzdokumentation zur [Erstellung](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)
* Referenzdokumentation zur [Laufzeit](/rest/api/cognitiveservices/qnamaker4.0/runtime)
* [Bash-Beispielskripts mit cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)