---
title: Referenz für die Textübersetzungs-API Version 3.0
titlesuffix: Azure Cognitive Services
description: Referenzdokumentation für die Textübersetzungs-API Version 3.0
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 973d38413fa39fec1c50b5e9770b6114fa2c4c3d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387510"
---
# <a name="translator-text-api-v30"></a>Textübersetzungs-API Version 3.0

## <a name="whats-new"></a>Neuerungen

Version 3 der Textübersetzungs-API umfasst eine moderne JSON-basierte Web-API. Sie ermöglicht eine bessere Nutzung und Leistung, indem vorhandene Features zu einer geringeren Zahl von Vorgängen zusammengefasst und neue Features bereitgestellt werden.

 * Transliteration zur Konvertierung von Text in einer Sprache aus einem Skript in ein anderes Skript.
 * Übersetzung in mehrere Sprachen innerhalb einer Anforderung.
 * Spracherkennung, Übersetzung und Transliteration innerhalb einer Anforderung.
 * Wörterbuch zum Nachschlagen von Übersetzungsalternativen für einen Begriff und zum Ermitteln von Rückübersetzungen und Kontextbeispielen für die Begriffe.
 * Informativere Ergebnisse für die Spracherkennung.

## <a name="base-urls"></a>Basis-URLs

Der Microsoft Translator wird aus mehreren Rechenzentren unterstützt. Momentan befinden sie sich in 6 [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions):

* **Amerika**: USA, Westen 2 und USA, Westen-Mitte 
* **Asien-Pazifik:** Asien, Südosten und Südkorea, Süden
* **Europa:** Europa, Norden und Europa, Westen

Anforderungen an die Textübersetzungs-API von Microsoft werden in den meisten Fällen von dem Rechenzentrum bearbeitet, das dem Ursprungsort der Anforderung am nächsten liegt. Im Falle eines Rechenzentrumsausfalls kann die Anforderung außerhalb der Region weitergeleitet werden.

Um zu erzwingen, dass die Anforderung von einem bestimmten Rechenzentrum bearbeitet wird, ändern Sie den globalen Endpunkt in der API-Anforderung auf den gewünschten regionalen Endpunkt:

|BESCHREIBUNG|Region|Basis-URL|
|:--|:--|:--|
|Azure|Global|  api.cognitive.microsofttranslator.com|
|Azure|Nordamerika|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asien-Pazifik|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Abonnieren Sie die Textübersetzungs-API oder [Cognitive Services-Multi-Service](https://azure.microsoft.com/pricing/details/cognitive-services/) in Microsoft Cognitive Services, und verwenden Sie Ihren Abonnementschlüssel (im Azure-Portal) für die Authentifizierung. 

Es gibt drei Header, mit denen Sie Ihr Abonnement authentifizieren können. Diese Tabelle beschreibt, wie jeder verwendet wird:

|Header|BESCHREIBUNG|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Verwendung mit Cognitive Services-Abonnement, wenn Sie Ihren geheimen Schlüssel übergeben*.<br/>Der Wert ist der geheime Azure-Schlüssel für Ihr Abonnement für die Textübersetzungs-API.|
|Autorisierung|*Verwendung mit dem Cognitive Services-Abonnement, wenn Sie ein Authentifizierungstoken übergeben.*<br/>Der Wert ist das Bearertoken: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Verwendung mit dem Multi-Service-Abonnement von Cognitive Services, wenn Sie einen geheimen Multi-Service-Schlüssel übergeben.*<br/>Der Wert ist die Region des Multi-Service-Abonnements. Dieser Wert ist optional, wenn kein Multi-Service-Abonnement verwendet wird.|

###  <a name="secret-key"></a>Geheimer Schlüssel
Die erste Option ist die Authentifizierung mithilfe des Headers `Ocp-Apim-Subscription-Key`. Fügen Sie einfach den `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>`-Header zur Ihrer Anforderung hinzu.

### <a name="authorization-token"></a>Autorisierungstoken
Alternativ können Sie Ihren geheimen Schlüssel für ein Zugriffstoken austauschen. Dieses Token ist in jeder Anforderung als `Authorization`-Header enthalten. Senden Sie eine `POST`-Anforderung an die folgende URL, um ein Autorisierungstoken zu erhalten:

| Environment     | Authentifizierungsdienst-URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Hier sind Beispielanforderungen zum Abrufen eines Tokens über einen geheimen Schlüssel angegeben:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Eine erfolgreiche Anforderung gibt das codierte Zugriffstoken im Antworttext im Nur-Text-Format zurück. Das gültige Token wird während der Autorisierung als Bearertoken an den Translator-Dienst übergeben.

```
Authorization: Bearer <Base64-access_token>
```

Ein Authentifizierungstoken ist zehn Minuten lang gültig. Das Token sollte wiederverwendet werden, wenn die Translator-APIs mehrfach aufgerufen werden. Wenn Ihr Programm aber über längere Zeit Anforderungen an die Translator-API sendet, muss das Programm regelmäßig (z.B. alle acht Minuten) ein neues Zugriffstoken anfordern.

### <a name="multi-service-subscription"></a>Abonnement für mehrere Dienste

Die letzte Authentifizierungsoption ist die Verwendung des Multi-Service-Abonnements von Cognitive Services. Auf diese Weise können Sie einen einzigen geheimen Schlüssel verwenden, um Anforderungen für mehrere Dienste zu authentifizieren. 

Wenn Sie einen geheimen Multi-Service-Schlüssel verwenden, müssen Sie Ihrer Anforderung zwei Authentifizierungsheader hinzufügen. Der erste übergibt den geheimen Schlüssel, der zweite gibt die Region an, die Ihrem Abonnement zugeordnet ist. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Die Region ist für das Abonnement der Multi-Service-Text-API erforderlich. Die von Ihnen ausgewählte Region ist die einzige Region, die Sie für die Textübersetzung verwenden können, wenn Sie den Multi-Service-Abonnementschlüssel verwenden, und muss sich um die gleiche Region handeln, die Sie bei der Registrierung für Ihr Multi-Service-Abonnement über das Azure-Portal ausgewählt haben.

Diese Regionen sind verfügbar: `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` und `westus2`.

Wenn Sie den geheimen Schlüssel in der Abfragezeichenfolge mit dem Parameter `Subscription-Key` übergeben, dann müssen Sie die Region mit dem Abfrageparameter `Subscription-Region` angeben.

Wenn Sie ein Bearertoken verwenden, müssen Sie das Token vom Regionsendpunkt `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` beziehen.


## <a name="errors"></a>Errors

Eine Standardfehlerantwort ist ein JSON-Objekt mit einem Name-Wert-Paar mit dem Namen `error`. Der Wert ist außerdem ein JSON-Objekt mit Eigenschaften:

  * `code`: Ein vom Server definierter Fehlercode.

  * `message`: Eine Zeichenfolge als für Menschen lesbare Darstellung des Fehlers.

Ein Kunde mit einer kostenlose Testversion des Abonnements erhält beispielsweise den folgenden Fehler, nachdem das Kontingent für den Free-Tarif erschöpft ist:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes sind:

| Code | BESCHREIBUNG |
|:----|:-----|
| 400000| Eine der Anforderungseingaben ist ungültig.|
| 400001| Der scope-Parameter ist ungültig.|
| 400002| Der category-Parameter ist ungültig.|
| 400003| Ein Sprachenspezifizierer fehlt oder ist ungültig.|
| 400004| Ein Zielskriptspezifizierer („To script“) fehlt oder ist ungültig.|
| 400005| Ein Eingabetext fehlt oder ist ungültig.|
| 400006| Die Kombination von Sprache und Skript ist ungültig.|
| 400018| Ein Quellskriptspezifizierer („From script“) fehlt oder ist ungültig.|
| 400019| Eine der angegebenen Sprachen wird nicht unterstützt.|
| 400020| Eines der Elemente im Array des Eingabetexts ist ungültig.|
| 400021| Der API-Versionsparameter fehlt oder ist ungültig.|
| 400023| Eines der angegebenen Sprachpaare wird nicht unterstützt.|
| 400035| Die Ausgangssprache (Feld „From“) ist ungültig.|
| 400036| Der Zielsprache (Feld „To“) fehlt oder ist ungültig.|
| 400042| Eine der angegebenen Optionen (Feld „Optionen“) ist ungültig.|
| 400043| Die Clientablaufverfolgungs-ID (Feld „ClientTraceId“ oder X-ClientTranceId-Header) fehlt oder ist ungültig.|
| 400050| Der Eingabetext ist zu lang. Informieren Sie sich unter [Anforderungsgrenzwerte](../request-limits.md).|
| 400064| Der translation-Parameter fehlt oder ist ungültig.|
| 400070| Die Anzahl der Zielskripts (ToScript-Parameter) entspricht nicht der Anzahl von Zielsprachen (To-Parameter).|
| 400071| Der Wert ist für TextType ungültig.|
| 400072| Das Array mit dem Eingabetext enthält zu viele Elemente.|
| 400073| Der script-Parameter ist ungültig.|
| 400074| Der Anforderungstext ist kein gültiger JSON-Code.|
| 400075| Die Kombination aus Sprachpaar und Kategorie ist ungültig.|
| 400077| Die maximale Anforderungsgröße wurde überschritten. Informieren Sie sich unter [Anforderungsgrenzwerte](../request-limits.md).|
| 400079| Das für die Übersetzung zwischen Ausgangs- und Zielsprache angeforderte benutzerdefinierte System ist nicht vorhanden.|
| 401000| Die Anforderung wurde nicht autorisiert, da die Anmeldeinformationen fehlen oder ungültig sind.|
| 401015| „Die angegebenen Anmeldeinformationen gelten für die SAPI. Diese Anforderung erfordert Anmeldeinformationen für die Text-API. Verwenden Sie ein Abonnement für die Textübersetzungs-API.“|
| 403000| Der Vorgang ist nicht zulässig.|
| 403001| Der Vorgang ist nicht zulässig, da das kostenlose Kontingent für das Abonnement überschritten wurde.|
| 405000| Die Anforderungsmethode wird für die angeforderte Ressource nicht unterstützt.|
| 408001| Das angeforderte Übersetzungssystem wird vorbereitet. Versuchen Sie es in einigen Minuten erneut.|
| 408002| Anforderungstimeout beim Warten auf den eingehenden Datenstrom. Der Client hat innerhalb des Zeitraums, in dem der Server vorbereitet war, zu warten, keine Anforderung erzeugt. Der Client kann die Anforderung ohne Änderungen zu einem späteren Zeitpunkt wiederholen.|
| 415000| Der Content-Type-Header fehlt oder ist ungültig.|
| 429000, 429001, 429002| Der Server hat die Anforderung abgelehnt, da der Client die Anforderungsgrenzwerte überschritten hat.|
| 500000| Ein unerwarteter Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn, und geben Sie dabei Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Antwortheader X-RequestId und Clientbezeichner aus dem Anforderungsheader X-ClientTraceId.|
| 503000| Service is temporarily unavailable. (Der Dienst ist vorübergehend nicht verfügbar.) Versuchen Sie es erneut. Wenn der Fehler weiterhin besteht, melden Sie ihn, und geben Sie dabei Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Antwortheader X-RequestId und Clientbezeichner aus dem Anforderungsheader X-ClientTraceId.|

