---
title: 'Messungsdienst-APIs: Kommerzieller Microsoft-Marketplace'
description: Mit der API für Nutzungsereignisse können Sie Nutzungsereignisse für SaaS-Angebote in Microsoft AppSource und Azure Marketplace ausgeben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436061"
---
# <a name="marketplace-metered-billing-apis"></a>APIs für getaktete Abrechnung im Marketplace

Die APIs für getaktete Abrechnung sollten verwendet werden, wenn der Herausgeber benutzerdefinierte Messungsdimensionen für ein Angebot erstellt, das im Partner Center veröffentlicht werden soll. Für alle erworbenen Angebote, die über einen oder mehrere Pläne mit benutzerdefinierten Dimensionen zur Ausgabe von Nutzungsereignissen verfügen, ist eine Integration in die APIs für getaktete Abrechnung erforderlich.

Weitere Informationen zum Erstellen benutzerdefinierter Messungsdimensionen für SaaS finden Sie unter [Getaktete SaaS-Abrechnung](saas-metered-billing.md).

Weitere Informationen zum Erstellen benutzerdefinierter Messungsdimensionen für ein Azure-Anwendungsangebot mit einem Plan für verwaltete Apps finden Sie unter [Konfigurieren der Details der Einrichtung Ihres Azure-Anwendungsangebots](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>Hinweis zum Erzwingen von TLS 1.2

Die TLS-Version 1.2 wird als Mindestversion für die HTTPS-Kommunikation erzwungen. Stellen Sie sicher, dass Sie diese TLS-Version in Ihrem Code verwenden. TLS-Version 1.0 und 1.1 sind veraltet, und Verbindungsversuche werden abgelehnt.

## <a name="metered-billing-single-usage-event"></a>Einzelnes Nutzungsereignis für getaktete Abrechnung

Die API für Nutzungsereignisse sollte vom Herausgeber aufgerufen werden, um Nutzungsereignisse für eine aktive Ressource (abonniert) für den vom jeweiligen Kunden erworbenen Plan auszugeben. Das Nutzungsereignis wird für jede benutzerdefinierte Dimension des Plans, die vom Herausgeber bei Veröffentlichung des Angebots definiert wurde, separat ausgegeben.

Für jede Stunde eines Kalendertags kann nur ein Nutzungsereignis ausgegeben werden. Sie können beispielsweise heute um 8:15 Uhr ein Nutzungsereignis ausgeben. Wenn dieses Ereignis akzeptiert wird, wird das nächste Nutzungsereignis heute ab 9:00 Uhr akzeptiert. Wenn Sie ein zusätzliches Ereignis heute zwischen 8:15 und 8:59:59 senden, wird es als Duplikat zurückgewiesen. Sie sollten alle Einheiten sammeln, die in einer Stunde verwendet werden, und diese dann in einem einzelnen Ereignis ausgeben.

Für jede Stunde eines Kalendertags kann pro Ressource nur ein Nutzungsereignis ausgegeben werden. Wenn mehr als eine Einheit in einer Stunde verwendet wird, sammeln Sie alle in dieser Stunde verwendeten Einheiten, und geben Sie diese dann in einem einzelnen Ereignis aus. Nutzungsereignisse können nur für die letzten 24 Stunden ausgegeben werden. Wenn Sie ein Nutzungsereignis zu einem Zeitpunkt zwischen 8:00 Uhr und 8:59:59 Uhr ausgeben (und es akzeptiert wird) und ein zusätzliches Ereignis für denselben Tag zwischen 8:00 und 8:59:59 senden, wird es als Duplikat zurückgewiesen.

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Abfrageparameter:*

| Parameter | Empfehlung          |
| ---------- | ---------------------- |
| `ApiVersion` | Verwenden Sie 2018-08-31. |
| | |

*Anforderungsheader:*

| Inhaltstyp       | Verwenden Sie `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` | Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`   | Ein eindeutiges Zugriffstoken, das den ISV identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es an folgender Stelle beschrieben ist: <br> <ul> <li> Für SaaS unter [Abrufen des Tokens mit HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post) </li> <li> Für verwaltete Anwendung unter [Authentifizierungsstrategien](./marketplace-metering-service-authentication.md) </li> </ul> |
| | |

*Beispiel für Anforderungstext:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` hat für SaaS-Apps und für verwaltete Apps, die benutzerdefinierte Verbrauchseinheiten ausgeben, eine andere Bedeutung. 

Bei von Azure-Anwendungen verwalteten App-Plänen ist `resourceId` die `resource group Id` der verwalteten App. Ein Beispielskript zum Abrufen der ID finden Sie unter [Verwenden des Tokens für von Azure verwaltete Identitäten](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Bei SaaS-Angeboten ist `resourceId` die SaaS-Abonnement-ID. Weitere Informationen zu SaaS-Abonnements finden Sie unter [Auflisten von Abonnements](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Antworten

Code: 200<br>
OK. Die Nutzungsausgabe wurde auf Microsoft-Seite zur weiteren Verarbeitung und Abrechnung akzeptiert und aufgezeichnet.

Beispiel einer Antwortnutzlast: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Code: 400 <br>
Ungültige Anforderung.

* Es wurden fehlende oder ungültige Anforderungsdaten bereitgestellt.
* `effectiveStartTime` liegt mehr als 24 Stunden in der Vergangenheit. Das Ereignis ist abgelaufen.
* Das SaaS-Abonnement weist nicht den Status „Abonniert“ auf.

Beispiel einer Antwortnutzlast: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Code: 403<br>

Unzulässig. Das Autorisierungstoken wurde nicht angegeben, ist ungültig oder abgelaufen.  Möglicherweise versucht die Anforderung auch, auf ein Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Code: 409<br>
Konflikt. Es wurde bereits erfolgreich ein Nutzungsereignis für die angegebene Ressourcen-ID sowie Datum und Stunde der Nutzung gemeldet.

Beispiel einer Antwortnutzlast: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Batchnutzungsereignis für getaktete Abrechnung

Mit der API für Batchnutzungsereignisse können Sie Nutzungsereignisse gleichzeitig für mehr als eine erworbene Ressource ausgeben. Außerdem können Sie mehrere Nutzungsereignisse für dieselbe Ressource ausgeben, solange diese für unterschiedliche Kalenderstunden gelten. Die maximale Anzahl von Ereignissen in einem einzelnen Batch beträgt 25.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Abfrageparameter:*

| Parameter  | Empfehlung     |
| ---------- | -------------------- |
| `ApiVersion` | Verwenden Sie 2018-08-31. |

*Anforderungsheader:*

| Inhaltstyp       | Verwenden Sie `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` | Eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`      | Ein eindeutiges Zugriffstoken, das den ISV identifiziert, der diesen API-Aufruf sendet. Das Format ist `Bearer <access_token>`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es an folgender Stelle beschrieben ist: <br> <ul> <li> Für SaaS unter [Abrufen des Tokens mit HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post) </li> <li> Für verwaltete Anwendung unter [Authentifizierungsstrategien](./marketplace-metering-service-authentication.md) </li> </ul> |
| | |


*Beispiel für Anforderungstext:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` hat für SaaS-Apps und für verwaltete Apps, die benutzerdefinierte Verbrauchseinheiten ausgeben, eine andere Bedeutung. 

Bei von Azure-Anwendungen verwalteten App-Plänen ist `resourceId` die `resource group Id` der verwalteten App. Ein Beispielskript zum Abrufen der ID finden Sie unter [Verwenden des Tokens für von Azure verwaltete Identitäten](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Bei SaaS-Angeboten ist `resourceId` die SaaS-Abonnement-ID. Weitere Informationen zu SaaS-Abonnements finden Sie unter [Auflisten von Abonnements](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Antworten

Code: 200<br>
OK. Die Batchnutzungsausgabe wurde auf Microsoft-Seite zur weiteren Verarbeitung und Abrechnung akzeptiert und aufgezeichnet. Die Antwortliste wird mit dem Status für jedes einzelne Ereignis im Batch zurückgegeben. Sie sollten die Antwortnutzlast durchlaufen, um die Antworten für jedes einzelne Nutzungsereignis zu verstehen, das als Teil des Batchereignisses gesendet wird.

Beispiel einer Antwortnutzlast: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Beschreibung des Statuscodes, der in der `BatchUsageEvent`-API-Antwort angegeben ist:

| Statuscode  | BESCHREIBUNG |
| ---------- | -------------------- |
| `Accepted` | Akzeptiert: |
| `Expired` | Abgelaufene Nutzung. |
| `Duplicate` | Doppelte Nutzung angegeben. |
| `Error` | Fehlercode |
| `ResourceNotFound` | Die angegebene Nutzungsressource ist ungültig. |
| `ResourceNotAuthorized` | Sie sind nicht autorisiert, die Nutzung für diese Ressource anzugeben. |
| `InvalidDimension` | Die Dimension, für die die Nutzung übergeben wird, ist für dieses Angebot bzw. diesen Plan ungültig. |
| `InvalidQuantity` | Die übergebene Menge ist kleiner oder gleich 0 (null). |
| `BadArgument` | Die Eingabe fehlt oder ist falsch formatiert. |

Code: 400<br>
Ungültige Anforderung. Der Batch enthielt mehr als 25 Nutzungsereignisse.

Code: 403<br>
Unzulässig. Das Autorisierungstoken wurde nicht angegeben, ist ungültig oder abgelaufen.  Möglicherweise versucht die Anforderung auch, auf ein Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

## <a name="development-and-testing-best-practices"></a>Bewährte Methoden für Entwicklung und Test

Um die Ausgabe benutzerdefinierter Verbrauchseinheiten zu testen, implementieren Sie die Integration in die Messungs-API, und erstellen Sie einen Plan für Ihr veröffentlichtes SaaS-Angebot mit benutzerdefinierten Dimensionen, die mit einem Preis pro Einheit von 0 (null) definiert sind. Veröffentlichen Sie dann dieses Angebot als Vorschauversion, sodass nur eingeschränkte Benutzer auf die Integration zugreifen und diese testen können.

Sie können auch einen privaten Plan für ein vorhandenes Liveangebot verwenden, um den Zugriff auf diesen Plan während des Tests auf eine begrenzte Zielgruppe zu beschränken.

## <a name="get-support"></a>Support

Befolgen Sie die Anweisungen unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md), um die Supportoptionen für Herausgeber zu verstehen und ein Supportticket für Microsoft zu öffnen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Messungsdienst-APIs finden Sie unter [Häufig gestellte Fragen zu Marketplace-Messungsdienst-APIs](./marketplace-metering-service-apis-faq.md).
