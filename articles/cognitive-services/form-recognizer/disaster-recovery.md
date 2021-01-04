---
title: Anleitung zur Notfallwiederherstellung für die Azure-Formularerkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die API zum Kopieren von Modellen nutzen können, um Ihre Ressourcen für die Formularerkennung zu sichern.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 168dcf994d00ba1cb2070665ada5a55cf86cfa4a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359811"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Sichern und Wiederherstellen Ihrer Modelle für die Formularerkennung

Wenn Sie eine Ressource für die Formularerkennung im Azure-Portal erstellen, geben Sie eine Region an. Ab diesem Zeitpunkt bleiben Ihre Ressource und alle zugehörigen Vorgänge mit der jeweiligen Azure-Serverregion verknüpft. Es ist selten, aber nicht unmöglich, dass ein Netzwerkproblem auftritt, das eine ganze Region betrifft. Wenn Ihre Lösung immer verfügbar sein muss, dann sollten Sie sie so konzipieren, dass entweder ein Failover in eine andere Region durchgeführt oder die Workload auf zwei oder mehr Regionen verteilt wird. Beide Ansätze setzen voraus, dass mindestens zwei Ressourcen für die Formularerkennung in unterschiedlichen Regionen vorliegen und dass es möglich ist, [benutzerdefinierte Modelle](./quickstarts/curl-train-extract.md) regionsübergreifend zu synchronisieren.

Die Kopier-API unterstützt dieses Szenario, indem sie es Ihnen ermöglicht, benutzerdefinierte Modelle von einem Formularerkennungskonto in andere Konten zu kopieren, die in einer beliebigen unterstützten geografischen Region vorliegen können. Der vorliegende Leitfaden zeigt, wie Sie die REST-API zum Kopieren mit cURL verwenden. Sie können auch einen HTTP-Anforderungsdienst wie Postman verwenden, um die Anforderungen auszugeben.

## <a name="business-scenarios"></a>Geschäftsszenarien

Wenn Ihre App oder Ihr Unternehmen von der Verwendung eines benutzerdefinierten Modells für die Formularerkennung abhängt, empfehlen wir Ihnen, Ihr Modell in ein anderes Konto für die Formularerkennung zu kopieren, das sich in einer anderen Region befindet. Im Falle eines regionalen Ausfalls können Sie auf Ihr Modell in der Region zugreifen, in die das Modell kopiert wurde.

##  <a name="prerequisites"></a>Voraussetzungen

1. Zwei Azure-Ressourcen für die Formularerkennung in unterschiedlichen Azure-Regionen. Wenn Sie über keine solchen Ressourcen verfügen, navigieren Sie zum Azure-Portal, und <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Erstellen einer neuen Formularerkennungsressource" target="_blank">erstellen Sie eine neue Formularerkennungsressource<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Abonnementschlüssel, Endpunkt-URL und Abonnement-ID Ihrer Formularerkennungsressource. Sie finden diese Werte im Azure-Portal auf der Registerkarte **Übersicht** für die Ressource.


## <a name="copy-api-overview"></a>Übersicht über die Kopier-API

Der Vorgang zum Kopieren eines benutzerdefinierten Modells umfasst die folgenden Schritte:

1. Zuerst senden Sie eine Autorisierungsanforderung für den Kopiervorgang an die Zielressource – d. h. die Ressource, die das kopierte Modell erhalten soll. Als Antwort erhalten Sie die URL des neu erstellten Zielmodells, das die kopierten Daten erhalten wird.
1. Als Nächstes senden Sie die Kopieranforderung an die Quellressource – also an die Ressource, die das zu kopierende Modell enthält. Als Antwort erhalten Sie eine URL, mit der Sie den Fortschritt des Vorgangs nachverfolgen können.
1. Sie verwenden die Anmeldeinformationen für Ihre Quellressource zum Abfragen der URL zum Fortschritt, bis der Vorgang erfolgreich abgeschlossen wurde. Sie können auch die ID des neuen Modells in der Zielressource abfragen, um den Status des neuen Modells abzurufen.

> [!CAUTION]
> Die Kopier-API unterstützt derzeit keine Modell-IDs für [zusammengesetzte benutzerdefinierte Modelle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose). Das Zusammensetzen von Modellen ist eine Previewfunktion in der Vorschauversion v2.1-preview.2. 

## <a name="generate-copy-authorization-request"></a>Generieren einer Autorisierungsanforderung für den Kopiervorgang

Mit der folgenden HTTP-Anforderung wird eine Autorisierung für den Kopiervorgang von Ihrer Zielressource abgerufen. Sie müssen den Endpunkt und den Schlüssel Ihrer Zielressource als Header eingeben.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Sie erhalten eine `201\Created`-Antwort mit einem `modelId`-Wert im Textkörper. Diese Zeichenfolge ist die ID des neu erstellten (leeren) Modells. Die API benötigt das `accessToken` zum Kopieren der Daten in diese Ressource, und der `expirationDateTimeTicks`-Wert repräsentiert den Ablauf des Tokens. Speichern Sie sämtliche dieser Werte an einem sicheren Speicherort.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Starten des Kopiervorgangs

Mit der folgenden HTTP-Anforderung wird der Kopiervorgang für die Quellressource gestartet. Sie müssen den Endpunkt und den Schlüssel Ihrer Ressource als Header eingeben. Beachten Sie, dass die Anforderungs-URL die Modell-ID des Quellmodells enthält, das Sie kopieren möchten.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Der Textkörper Ihrer Anforderung muss das folgende Format aufweisen. Sie müssen die Ressourcen-ID und den Regionsnamen für Ihre Zielressource eingeben. Sie finden Ihre Ressourcen-ID auf der Registerkarte **Eigenschaften** Ihrer Ressource im Azure-Portal. Den Namen der Region finden Sie auf der Registerkarte **Keys and endpoint** (Schlüssel und Endpunkt). Außerdem benötigen Sie die Modell-ID, das Zugriffstoken und Ablaufwert aus dem vorherigen Schritt.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> Die Kopier-API unterstützt das Feature [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) auf transparente Weise. Dies erfordert keine besondere Behandlung, aber beachten Sie, dass Sie beim Kopieren zwischen einer unverschlüsselten Ressource und einer verschlüsselten Ressource den Anforderungsheader `x-ms-forms-copy-degrade: true` einfügen müssen. Wenn dieser Header nicht enthalten ist, tritt beim Kopiervorgang ein Fehler auf und es wird ein `DataProtectionTransformServiceError` zurückgegeben.

Sie erhalten eine `202\Accepted`-Antwort mit einem Operation-Location-Header. Dieser Wert ist die URL, mit der Sie den Fortschritt des Vorgangs nachverfolgen. Kopieren Sie die URL für den nächsten Schritt an einen temporären Speicherort.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Häufige Fehler

|Fehler|Lösung|
|:--|:--|
| 400/Ungültige Anforderung mit `"code:" "1002"` | Zeigt einen Validierungsfehler oder eine schlecht formulierte Kopieranforderung an. Zu den häufigen Problemen gehören: a) Ungültige oder geänderte `copyAuthorization`-Nutzlast. b) Abgelaufener Wert für `expirationDateTimeTicks`-Token (`copyAuhtorization`-Nutzlast ist 24 Stunden lang gültig). c) Ungültige oder nicht unterstützte `targetResourceRegion`. d) Ungültige oder fehlerhafte `targetResourceId`-Zeichenfolge.
|

## <a name="track-copy-progress"></a>Nachverfolgen des Kopierfortschritts

Verfolgen Sie den Fortschritt des Vorgangs, indem Sie die API **Get Copy Model Result** für den Quellressourcenendpunkt abrufen.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Die Antwort richtet sich nach dem Status des Vorgangs. Suchen Sie im JSON-Textkörper nach dem Feld `"status"`. Wenn Sie diesen API-Aufruf in einem Skript automatisieren, empfehlen wir, den Vorgang einmal pro Sekunde abzufragen.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Häufige Fehler

|Fehler|Lösung|
|:--|:--|
|"errors":[{"code":"AuthorizationError",<br>"message":"Autorisierungsfehler aufgrund <br>fehlender oder ungültiger Autorisierungsansprüche."}]   | Tritt auf, wenn die `copyAuthorization`-Nutzlast oder der Inhalt gegenüber dem geändert wird, was von der `copyAuthorization`-API zurückgegeben wurde. Stellen Sie sicher, dass die Nutzlast exakt denselben Inhalt hat, der vom früheren `copyAuthorization`-Aufruf zurückgegeben wurde.|
|"errors":[{"code":"AuthorizationError",<br>"message":"Die Metadaten für die Autorisierung <br>konnten nicht abgerufen werden. Wenn dieses Problem weiterhin besteht, verwenden Sie ein anderes <br>Zielmodell, in das Sie kopieren können."}] | Gibt an, dass die `copyAuthorization`-Nutzlast mit einer Kopieranforderung wiederverwendet wird. Eine erfolgreiche Kopieranforderung lässt keine weiteren Anforderungen zu, die dieselbe `copyAuthorization`-Nutzlast verwenden. Wenn Sie einen separaten Fehler (wie die unten aufgeführten) auslösen und anschließend die Kopie mit derselben Autorisierungsnutzlast erneut versuchen, wird dieser Fehler ausgelöst. Die Lösung besteht darin, eine neue `copyAuthorization`-Nutzlast zu generieren und dann die Kopieranforderung neu zu erstellen.|
|"errors":[{"code":"DataProtectionTransformServiceError",<br>"message":"Anforderungen zur Datenübertragung sind nicht zulässig, <br>da sie zu einem weniger sicheren Datenschutzsystem herabgestuft werden. Einzelheiten entnehmen Sie der Dokumentation, <br>oder wenden Sie sich an Ihren Dienstadministrator."}]    | Tritt auf, wenn zwischen einer für `AEK` aktivierten Ressource und einer nicht für `AEK` aktivierten Ressource kopiert wird. Um das Kopieren des verschlüsselten Modells zum Ziel als „unverschlüsselt“ zu gestatten, geben Sie den `x-ms-forms-copy-degrade: true`-Header mit der Kopieranforderung an.|
|"errors":[{"code":"ResourceResolverError",<br>"message":"Konnte keine Informationen für Cognitive-Ressource mit der ID '...' abrufen. Stellen Sie sicher, dass die Ressource gültig und in der angegebenen Region 'westus2' vorhanden ist."}] | Gibt an, dass die mit `targetResourceId` angegebene Azure-Ressource keine gültige Cognitive-Ressource oder nicht vorhanden ist. Überprüfen Sie die Kopieranforderung, und stellen Sie sie erneut aus, um dieses Problem zu beheben.|


### <a name="optional-track-the-target-model-id"></a>[Optional] Nachverfolgen der Zielmodell-ID 

Sie können auch mit der API **Get Custom Model** den Status des Vorgangs abrufen, indem Sie das Zielmodell abfragen. Rufen Sie diese API unter Verwendung der Zielmodell-ID ab, die Sie im ersten Schritt kopiert haben.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Im Antworttext werden Informationen zum Modell angezeigt. Überprüfen Sie den Status des Modells im Feld `"status"`.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>cURL-Beispielcode

Die folgenden Codeausschnitte verwenden cURL, um die in den vorherigen Schritten beschriebenen API-Aufrufe auszuführen. Sie müssen die Modell-IDs und die Abonnementinformationen für Ihre eigenen Ressourcen eingeben.

### <a name="generate-copy-authorization-request"></a>Generieren einer Autorisierungsanforderung für den Kopiervorgang

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Starten des Kopiervorgangs

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Nachverfolgen des Kopierfortschritts

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie Sie die Kopier-API zum Sichern Ihrer benutzerdefinierten Modelle in einer sekundären Formularerkennungsressource verwenden. Sehen Sie sich als Nächstes die API-Referenzdokumentation an, um sich über weitere Möglichkeiten bei Verwendung der Formularerkennung zu informieren.
* [Referenzdokumentation zur Rest-API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
