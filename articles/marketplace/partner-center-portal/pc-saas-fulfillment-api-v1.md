---
title: SaaS Fulfillment APIs v1 | Azure Marketplace
description: Erläutert, wie Sie ein SaaS-Angebot im Azure Marketplace mit den zugehörigen Fulfillment-APIs der Version 1 erstellen und verwalten.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: 78162983601e9126bd34cb737e74783df982bacb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259354"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS Fulfillment APIs Version 1 (veraltet)

In diesem Artikel wird erklärt, wie Sie ein SaaS-Angebot mit APIs erstellen. Die aus REST-Methoden und Endpunkten bestehenden APIs sind für das Zulassen von Abonnements Ihres SaaS-Angebots erforderlich, wenn Sie den Verkauf über Azure ausgewählt haben.  

> [!WARNING]
> Diese erste Version der SaaS Fulfillment API ist veraltet. Verwenden Sie stattdessen [SaaS Fulfillment-API V2](./pc-saas-fulfillment-api-v2.md).  Diese erste Version der API wird derzeit nur für bestehende Herausgeber bereitgestellt. 

Die folgenden APIs zur Integration Ihres SaaS-Diensts in Azure werden bereitgestellt:

-   Beheben
-   Abonnieren
-   Convert
-   Abbestellen


## <a name="api-methods-and-endpoints"></a>API-Methoden und -Endpunkte

In den folgenden Abschnitten werden die API-Methoden und -Endpunkte beschrieben, die zum Aktivieren von Abonnements für ein SaaS-Angebot verfügbar sind.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace-API-Endpunkt und API-Version

Der Endpunkt für die Azure Marketplace-API ist `https://marketplaceapi.microsoft.com`.

Die aktuelle API-Version ist `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Abonnement auflösen

Eine POST-Aktion beim Auflösen des Endpunkts ermöglicht Benutzern das Auflösen eines Marketplace-Tokens in eine permanente Ressourcen-ID.  Die Ressourcen-ID ist der eindeutige Bezeichner des SAAS-Abonnements. 

Wenn ein Benutzer zur Website eines ISV weitergeleitet wird, enthält die URL in den Abfrageparametern ein Token. Es wird erwartet, dass der ISV dieses Token verwendet und eine Anforderung zu dessen Auflösung stellt. Die Antwort enthält die eindeutige ID des SAAS-Abonnements, den Namen, die Angebots-ID und den Plan für die Ressource. Dieses Token ist nur eine Stunde gültig.

*Anforderung*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parametername** |     **Beschreibung**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  Die Version des für diese Anforderung zu verwendenden Vorgangs.   |
|  |  |


*Header*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
| x-ms-correlationid | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieses Feld setzt alle Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite in Beziehung. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| Inhaltstyp       | Ja          | `application/json`                                        |
| authorization      | Ja          | JWT-Bearertoken (JSON Web Token)                    |
| x-ms-marketplace-token| Ja| Der Tokenabfrageparameter in der URL, wenn der Benutzer von Azure zur Website des SaaS-ISV umgeleitet wird. **Hinweis:** Dieses Token ist nur eine Stunde lang gültig. Führen Sie zusätzlich eine URL-Decodierung des Tokenwerts im Browser durch, bevor Sie ihn verwenden.|
|  |  |  |
  

*Antworttext*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Parametername** | **Datentyp** | **Beschreibung**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Zeichenfolge        | ID des SaaS-Abonnements.          |
| subscriptionName| Zeichenfolge| Name des SaaS-Abonnements, das vom Benutzer beim Abonnieren des SaaS-Diensts in Azure festgelegt wurde.|
| OfferId            | Zeichenfolge        | Angebots-ID, die der Benutzer abonniert hat. |
| planId             | Zeichenfolge        | Tarif-ID, die der Benutzer abonniert hat.  |
|  |  |  |


*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token erfolgreich aufgelöst.                                                            |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder eine ungültige API-Version wurde angegeben. Fehler beim Auflösen des Tokens, da das Token entweder falsch formatiert oder abgelaufen ist (nach dem Generieren ist das Token nur eine Stunde lang gültig). |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                                 |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                                |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen, versuchen Sie es später erneut.                                        |
|  |  |  |


*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben; andernfalls ist dieser Wert die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Diese ID wird für alle Abstimmungen verwendet. |
| Retry-After        | Nein           | Dieser Wert wird nur für die Antwort 429 festgelegt.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Abonnieren

Der Abonnementendpunkt ermöglicht Benutzern das Starten des Abonnements eines SaaS-Diensts für einen bestimmten Tarif und das Aktivieren der Abrechnung im Commerce-System.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Eindeutige ID des SaaS-Abonnements, die nach dem Auflösen des Tokens über die API-Auflösung abgerufen wird.                              |
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

|  **Headerschlüssel**        | **Erforderlich** |  **Beschreibung**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nein         | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| x-ms-correlationid     |   Nein         | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| If-Match/If-None-Match |   Nein         |   Starker ETag-Wert des Validierungssteuerelements.                                                          |
| Inhaltstyp           |   Ja        |    `application/json`                                                                   |
|  authorization         |   Ja        |    JWT-Bearertoken (JSON Web Token)                                               |
| x-ms-marketplace-session-mode| Nein | Flag zum Aktivieren des Probelaufmodus beim Abonnieren eines SaaS-Angebots. Wenn es festgelegt ist, wird das Abonnement nicht belastet. Nützlich für ISV-Testszenarien. Legen Sie es auf **„dryrun“** fest.|
|  |  |  |

*Text*

``` json
{
    "lanId": "",
}
```

| **Elementname** | **Datentyp** | **Beschreibung**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Erforderliche) Zeichenfolge        | Tarif-ID des SaaS-Diensts, den der Benutzer abonniert.  |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivierung des SaaS-Abonnements für einen bestimmten Tarif erhalten.                   |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder der Text der JSON-Datei ist falsch formatiert. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                   |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden                                  |
| 409                  | `Conflict`           | Für das Abonnement wird derzeit ein anderer Vorgang ausgeführt.                     |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                  |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen, versuchen Sie es später erneut.                          |
|  |  |  |

Verfolgen Sie für die Antwort 202 den Status des Anforderungsvorgangs am Header „Operation-Location“ nach. Die Authentifizierung ist identisch mit anderen Marketplace-APIs.

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben; andernfalls ist dieser Wert die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Ja          | Intervall, in dem der Client den Status überprüfen kann.                                                       |
| Operation-Location | Ja          | Link zu einer Ressource, um den Vorgangsstatus abzurufen.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Endpunkt der Tarifänderung

Der Änderungsendpunkt ermöglicht dem Benutzer das Konvertieren seines derzeit abonnierten Tarifs in einen neuen Tarif.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID des SaaS-Abonnements.                              |
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

| **Headerschlüssel**          | **Erforderlich** | **Beschreibung**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client. Es wird eine GUID empfohlen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.   |
| x-ms-correlationid      | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| If-Match /If-None-Match | Nein           | Starker ETag-Wert des Validierungssteuerelements.                              |
| Inhaltstyp            | Ja          | `application/json`                                        |
| authorization           | Ja          | JWT-Bearertoken (JSON Web Token)                    |
|  |  |  |

*Text*

```json
{
    "planId": ""
}
```

|  **Elementname** |  **Datentyp**  | **Beschreibung**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Erforderliche) Zeichenfolge         | Tarif-ID des SaaS-Diensts, den der Benutzer abonniert.          |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivierung des SaaS-Abonnements für einen bestimmten Tarif erhalten.                   |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder der Text der JSON-Datei ist falsch formatiert. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                   |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden                                  |
| 409                  | `Conflict`           | Für das Abonnement wird derzeit ein anderer Vorgang ausgeführt.                     |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                  |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen, versuchen Sie es später erneut.                          |
|  |  |  |

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben; andernfalls ist dieser Wert die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Ja          | Intervall, in dem der Client den Status überprüfen kann.                                                       |
| Operation-Location | Ja          | Link zu einer Ressource, um den Vorgangsstatus abzurufen.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Löschen eines Abonnements

Die DELETE-Aktion am Abonnementendpunkt ermöglicht einem Benutzer das Löschen eines Abonnements mit einer bestimmten ID.

*Anforderung*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID des SaaS-Abonnements.                              |
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client. Es wird eine GUID empfohlen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.                                                           |
| x-ms-correlationid | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| authorization      | Ja          | JWT-Bearertoken (JSON Web Token)                    |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivierung des SaaS-Abonnements für einen bestimmten Tarif erhalten.                   |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder der Text der JSON-Datei ist falsch formatiert. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                   |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden                                  |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                  |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen. Versuchen Sie es später noch mal.                          |
|  |  |  |

Verfolgen Sie für die Antwort 202 den Status des Anforderungsvorgangs am Header „Operation-Location“ nach. Die Authentifizierung ist identisch mit anderen Marketplace-APIs.

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben, andernfalls ist dies die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Ja          | Intervall, in dem der Client den Status überprüfen kann.                                                       |
| Operation-Location | Ja          | Link zu einer Ressource, um den Vorgangsstatus abzurufen.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Vorgangsstatus abrufen

Dieser Endpunkt ermöglicht dem Benutzer das Nachverfolgen des Status eines ausgelösten asynchronen Vorgangs (Abonnieren/Abbestellen/Tarif ändern).

*Anforderung*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Eindeutige ID für den ausgelösten Vorgang.                |
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client. Es wird eine GUID empfohlen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.   |
| x-ms-correlationid | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
| authorization      | Ja          | JWT-Bearertoken (JSON Web Token)                    |
|  |  |  | 

*Antworttext*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametername** | **Datentyp** | **Beschreibung**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Zeichenfolge        | ID des Vorgangs.                                                                      |
| status             | Enum          | Vorgangsstatus, einer der folgenden Werte: `In Progress`, `Succeeded`, oder `Failed`.          |
| resourceLocation   | Zeichenfolge        | Link zum erstellten oder geänderten Abonnement. Hilft dem Client beim Abrufen des aktualisierten Status nach dem Vorgang. Dieser Wert wird für `Unsubscribe`-Vorgänge nicht festgelegt. |
| created            | DateTime      | Erstellungszeitpunkt (UTC) des Vorgangs.                                                           |
| lastModified       | DateTime      | Letzte Aktualisierung des Vorgangs in UTC.                                                      |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Die GET-Anforderung wurde erfolgreich aufgelöst, und der Text enthält die Antwort.    |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder eine ungültige API-Version wurde angegeben. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                      |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden.                                     |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                     |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen, versuchen Sie es später erneut.                             |
|  |  |  |

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben, andernfalls ist dies die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Ja          | Intervall, in dem der Client den Status überprüfen kann.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Abonnement abrufen

Die GET-Aktion am Abonnementendpunkt ermöglicht einem Benutzer das Abrufen eines Abonnements mit einem bestimmten Ressourcenbezeichner.

*Anforderung*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID des SaaS-Abonnements.                              |
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.                                                           |
| x-ms-correlationid | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| authorization      | Ja          | JWT-Bearertoken (JSON Web Token)                                                                    |
|  |  |  |

*Antworttext*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametername**     | **Datentyp** | **Beschreibung**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Zeichenfolge        | ID der SaaS-Abonnementsressource in Azure.    |
| offerId                | Zeichenfolge        | Angebots-ID, die der Benutzer abonniert hat.         |
| planId                 | Zeichenfolge        | Tarif-ID, die der Benutzer abonniert hat.          |
| saasSubscriptionName   | Zeichenfolge        | Name des SaaS-Abonnements.                |
| saasSubscriptionStatus | Enum          | Vorgangsstatus  Einer der folgenden:  <br/> - `Subscribed`: Das Abonnement ist aktiv.  <br/> - `Pending`: Die Ressource wurde vom Benutzer erstellt, vom ISV jedoch nicht aktiviert.   <br/> - `Unsubscribed`: Der Benutzer hat das Abonnement gekündigt.   <br/> - `Suspended`: Der Benutzer hat das Abonnement angehalten.   <br/> - `Deactivated`:  Das Azure-Abonnement wurde angehalten.  |
| created                | DateTime      | Zeitstempelwert der Abonnementerstellung in UTC. |
| lastModified           | DateTime      | Zeitstempelwert der Abonnementänderung in UTC. |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Die GET-Anforderung wurde erfolgreich aufgelöst, und der Text enthält die Antwort.    |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder eine ungültige API-Version wurde angegeben. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                      |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden                                     |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                     |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen, versuchen Sie es später erneut.                             |
|  |  |  |

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben, andernfalls ist dies die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Nein           | Intervall, in dem der Client den Status überprüfen kann.                                                       |
| eTag               | Ja          | Link zu einer Ressource, um den Vorgangsstatus abzurufen.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Abonnements abrufen

Die GET-Aktion am Abonnementendpunkt ermöglicht einem Benutzer das Abrufen aller Abonnements für alle Angebote des ISV.

*Anforderung*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parametername**  | **Beschreibung**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | Die Version des für diese Anforderung zu verwendenden Vorgangs. |
|  |  |

*Header*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nein           | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client. Es wird eine GUID empfohlen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.             |
| x-ms-correlationid | Nein           | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Wert dient zum Korrelieren aller Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| authorization      | Ja          | JWT-Bearertoken (JSON Web Token)                    |
|  |  |  |

*Antworttext*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametername**     | **Datentyp** | **Beschreibung**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Zeichenfolge        | ID der SaaS-Abonnementsressource in Azure    |
| offerId                | Zeichenfolge        | Angebots-ID, die der Benutzer abonniert hat         |
| planId                 | Zeichenfolge        | Tarif-ID, die der Benutzer abonniert hat          |
| saasSubscriptionName   | Zeichenfolge        | Name des SaaS-Abonnements                |
| saasSubscriptionStatus | Enum          | Vorgangsstatus  Einer der folgenden:  <br/> - `Subscribed`: Das Abonnement ist aktiv.  <br/> - `Pending`: Die Ressource wurde vom Benutzer erstellt, vom ISV jedoch nicht aktiviert.   <br/> - `Unsubscribed`: Der Benutzer hat das Abonnement gekündigt.   <br/> - `Suspended`: Der Benutzer hat das Abonnement angehalten.   <br/> - `Deactivated`:  Das Azure-Abonnement wurde angehalten.  |
| created                | DateTime      | Zeitstempelwert der Abonnementerstellung in UTC |
| lastModified           | DateTime      | Zeitstempelwert der Abonnementänderung in UTC |
|  |  |  |

*Antwortcodes*

| **HTTP-Statuscode** | **Fehlercode**     | **Beschreibung**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Die GET-Anforderung wurde erfolgreich aufgelöst, und der Text enthält die Antwort.    |
| 400                  | `BadRequest`         | Entweder fehlen erforderliche Header, oder eine ungültige API-Version wurde angegeben. |
| 403                  | `Forbidden`          | Der Aufrufer ist zum Durchführen dieses Vorgangs nicht autorisiert.                      |
| 404                  | `NotFound`           | Abonnement mit der angegebenen ID nicht gefunden                                     |
| 429                  | `RequestThrottleId`  | Der Dienst ist mit der Verarbeitung von Anforderungen ausgelastet, versuchen Sie es später erneut.                     |
| 503                  | `ServiceUnavailable` | Der Dienst ist vorübergehend ausgefallen. Versuchen Sie es später noch mal.                             |
|  |  |  |

*Antwortheader*

| **Headerschlüssel**     | **Erforderlich** | **Beschreibung**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Vom Client empfangene Anforderungs-ID.                                                                   |
| x-ms-correlationid | Ja          | Korrelations-ID, wenn vom Client übergeben, andernfalls ist dies die Serverkorrelations-ID.                   |
| x-ms-activityid    | Ja          | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet. |
| Retry-After        | Nein           | Intervall, in dem der Client den Status überprüfen kann.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS-Webhook

Ein SaaS-Webhook wird verwendet, um Änderungen proaktiv an den SaaS-Dienst zu melden. Diese POST-API muss nicht authentifiziert werden und wird vom Microsoft-Dienst aufgerufen. Der SaaS-Dienst muss die API-Vorgänge zur Überprüfung und Autorisierung aufrufen, bevor er auf die Webhookbenachrichtigung reagieren kann. 

*Text*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Parametername**     | **Datentyp** | **Beschreibung**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Zeichenfolge       | Eindeutige ID für den ausgelösten Vorgang.                |
| activityId   | Zeichenfolge        | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Dienst. Dieser Wert wird für alle Abstimmungen verwendet.               |
| subscriptionId                     | Zeichenfolge        | ID der SaaS-Abonnementsressource in Azure.    |
| offerId                | Zeichenfolge        | Angebots-ID, die der Benutzer abonniert hat. Nur mit der Aktion „Update“ bereitgestellt.        |
| publisherId                | Zeichenfolge        | Herausgeber-ID des SaaS-Angebots         |
| planId                 | Zeichenfolge        | Tarif-ID, die der Benutzer abonniert hat. Nur mit der Aktion „Update“ bereitgestellt.          |
| action                 | Zeichenfolge        | Die Aktion, die diese Benachrichtigung auslöst. Mögliche Werte: Activate, Delete, Suspend, Reinstate, Update          |
| timeStamp                 | Zeichenfolge        | Zeitstempelwert in UTC, als diese Benachrichtigung ausgelöst wurde.          |
|  |  |  |


## <a name="next-steps"></a>Nächste Schritte

Entwicklern steht außerdem das programmgesteuerte Abrufen und Bearbeiten von Workloads, Angeboten und Herausgeberprofilen mit den [Cloud-Partnerportal-REST-APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) zur Verfügung.
