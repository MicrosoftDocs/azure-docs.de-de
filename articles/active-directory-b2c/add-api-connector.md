---
title: Hinzufügen von API-Connectors zu Benutzerflows (Vorschauversion)
description: Konfigurieren Sie einen API-Connector, der in einem Benutzerflow verwendet werden soll.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: b63db3d02b471a577586ecd54f56caa59af504d6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805511"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Hinzufügen eines API-Connectors zu einem Benutzerflow für die Registrierung (Vorschauversion)

> [!IMPORTANT]
> API-Connectors für die Registrierung sind ein Feature der öffentlichen Vorschauversion von Azure AD B2C. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um einen [API-Connector](api-connectors-overview.md) zu verwenden, erstellen Sie zunächst den API-Connector und aktivieren ihn anschließend in einem Benutzerflow.

## <a name="create-an-api-connector"></a>Erstellen eines API-Connectors

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus.
4. Wählen Sie **API-Connectors (Vorschau)** und dann **Neuer API-Connector** aus.

   ![Hinzufügen eines neuen API-Connectors](./media/add-api-connector/api-connector-new.png)

5. Geben Sie einen Anzeigenamen für den Aufruf an, beispielsweise **Benutzerinformationen überprüfen**.
6. Geben Sie die **Endpunkt-URL** für den API-Aufruf an.
7. Geben Sie die Authentifizierungsinformationen für die API an.

   - Derzeit wird nur die Standardauthentifizierung unterstützt. Wenn Sie eine API ohne Standardauthentifizierung für Entwicklungszwecke verwenden möchten, geben Sie einfach Dummywerte für **Benutzername** und **Kennwort** ein, die in der API ignoriert werden können. Sie können den Code zur Verwendung mit einer Azure-Funktion mit einem API-Schlüssel als Abfrageparameter in der **Endpunkt-URL** einfügen (z. B. `https://contoso.azurewebsites.net/api/endpoint?code=0123456789`).

   ![Konfigurieren eines neuen API-Connectors](./media/add-api-connector/api-connector-config.png)
8. Wählen Sie **Speichern** aus.

## <a name="the-request-sent-to-your-api"></a>An die API gesendete Anforderung
Ein API-Connector wird als **HTTP POST**-Anforderung dargestellt und sendet Benutzerattribute („Ansprüche“) als Schlüssel-Wert-Paare in einem JSON-Text. Attribute werden ähnlich wie [Microsoft Graph](/graph/api/resources/user#properties)-Benutzereigenschaften serialisiert. 

**Beispielanforderung**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Nur Benutzereigenschaften und benutzerdefinierte Attribute, die unter **Azure AD B2C** > **Benutzerattribute** aufgeführt sind, können in der Anforderung gesendet werden.

Benutzerdefinierte Attribute sind im Verzeichnis im Format **extension_\<extensions-app-id>_CustomAttribute** vorhanden. Die API sollte den Empfang von Ansprüchen in diesem serialisierten Format erwarten. Weitere Informationen zu benutzerdefinierten Attributen finden Sie unter [Definieren benutzerdefinierter Attribute in Azure Active Directory B2C](user-flow-custom-attributes.md).

Außerdem wird der Anspruch **Gebietsschema der Benutzeroberfläche („ui_locales“)** standardmäßig in allen Anforderungen gesendet. Er gibt das Gebietsschema (oder auch mehrere Gebietsschemas) eines Benutzers an, wie es auf seinem Gerät konfiguriert ist. Dieses kann von der API verwendet werden, um internationalisierte Antworten zurückzugeben.

> [!IMPORTANT]
> Wenn ein Anspruch zum Zeitpunkt des Aufrufs des API-Endpunkts keinen Wert enthält, wird der Anspruch nicht an die API gesendet. Ihre API sollte so entworfen sein, dass explizit geprüft wird, ob ein Anspruch in der Anforderung enthalten ist. Fehlt der Anspruch, sollte eine entsprechende Verarbeitung erfolgen.

> [!TIP] 
> Mit den Ansprüchen [**Identitäten („identities“)**](/graph/api/resources/objectidentity) und **E-Mail-Adresse („email“)** kann Ihre API einen Benutzer identifizieren, bevor er über ein Konto in Ihrem Mandanten verfügt. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivieren des API-Connectors in einem Benutzerflow

Führen Sie die folgenden Schritte aus, um einem Benutzerflow für die Registrierung einen API-Connector hinzuzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus.
4. Wählen Sie **Benutzerflows** und dann den Benutzerflow aus, dem Sie den API-Connector hinzufügen möchten.
5. Wählen Sie **API-Connectors** und dann die API-Endpunkte aus, die bei den folgenden Schritten im Benutzerflow aufgerufen werden sollen:

   - **Nach Anmeldung bei einem Identitätsanbieter**
   - **Vor dem Erstellen des Benutzers**

   ![Hinzufügen von APIs zum Benutzerflow](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Wählen Sie **Speichern** aus.

## <a name="after-signing-in-with-an-identity-provider"></a>Nach Anmeldung bei einem Identitätsanbieter

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird unmittelbar nach der Authentifizierung des Benutzers bei einem Identitätsanbieter (etwa Google, Facebook und Azure AD) aufgerufen. Dieser Schritt geht der ***Seite zur Attributsammlung***  voraus, die dem Benutzer zum Sammeln von Benutzerattributen angezeigt wird. Dieser Schritt wird nicht aufgerufen, wenn sich ein Benutzer mit einem lokalen Konto registriert.

### <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Die genauen Ansprüche, die an die API gesendet werden, hängen von den Informationen ab, die vom Identitätsanbieter bereitgestellt werden. Der Anspruch „email“ wird immer gesendet.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Von der Web-API in diesem Schritt erwartete Antworttypen

Wenn die Web-API während eines Benutzerflows eine HTTP-Anforderung von Azure AD empfängt, kann sie folgende Antworten zurückgeben:

- Fortsetzungsantwort
- Blockierungsantwort

#### <a name="continuation-response"></a>Fortsetzungsantwort

Eine Fortsetzungsantwort gibt an, dass der Benutzerflow mit dem nächsten Schritt fortgesetzt werden soll: Attributerfassungsseite.

In einer Fortsetzungsantwort kann die API Ansprüche zurückgeben. Wenn ein Anspruch von der API zurückgegeben wird, passiert mit dem Anspruch Folgendes:

- Das Eingabefeld auf der Attributerfassungsseite wird vorab aufgefüllt.

Sehen Sie sich ein Beispiel für eine [Fortsetzungsantwort](#example-of-a-continuation-response) an.

#### <a name="blocking-response"></a>Blockierungsantwort

Eine Blockierungsantwort beendet den Benutzerflow. Sie kann von der API absichtlich ausgegeben werden, um die Fortsetzung des Benutzerflows zu beenden, indem für den Benutzer eine Blockierungsseite angezeigt wird. Auf der Blockierungsseite wird die von der API angegebene `userMessage` angezeigt.

Sehen Sie sich ein Beispiel für eine [Blockierungsantwort](#example-of-a-blocking-response) an.

## <a name="before-creating-the-user"></a>Vor dem Erstellen des Benutzers

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird nach der Seite zur Attributsammlung aufgerufen, sofern vorhanden. Dieser Schritt wird immer aufgerufen, bevor ein Benutzerkonto erstellt wird.

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Die genauen Ansprüche, die an die API gesendet werden, hängen von den Informationen ab, die vom Benutzer gesammelt oder vom Identitätsanbieter bereitgestellt werden.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Von der Web-API in diesem Schritt erwartete Antworttypen

Wenn die Web-API während eines Benutzerflows eine HTTP-Anforderung von Azure AD empfängt, kann sie folgende Antworten zurückgeben:

- Fortsetzungsantwort
- Blockierungsantwort
- Überprüfungsantwort

#### <a name="continuation-response"></a>Fortsetzungsantwort

Eine Fortsetzungsantwort gibt an, dass der Benutzerflow mit dem nächsten Schritt fortgesetzt werden soll: Erstellen des Benutzers im Verzeichnis.

In einer Fortsetzungsantwort kann die API Ansprüche zurückgeben. Wenn ein Anspruch von der API zurückgegeben wird, passiert mit dem Anspruch Folgendes:

- Alle Werte, die dem Anspruch über die Attributerfassungsseite bereits zugewiesen wurden, werden überschrieben.

Sehen Sie sich ein Beispiel für eine [Fortsetzungsantwort](#example-of-a-continuation-response) an.

#### <a name="blocking-response"></a>Blockierungsantwort
Eine Blockierungsantwort beendet den Benutzerflow. Sie kann von der API absichtlich ausgegeben werden, um die Fortsetzung des Benutzerflows zu beenden, indem für den Benutzer eine Blockierungsseite angezeigt wird. Auf der Blockierungsseite wird die von der API angegebene `userMessage` angezeigt.

Sehen Sie sich ein Beispiel für eine [Blockierungsantwort](#example-of-a-blocking-response) an.

### <a name="validation-error-response"></a>Validierungsfehlerantwort
 Wenn die API eine Validierungsfehlerantwort ausgibt, wird der Benutzerflow auf der Attributsammlungsseite angehalten, und dem Benutzer wird eine `userMessage` angezeigt. Der Benutzer kann dann das Formular bearbeiten und erneut senden. Diese Antwort kann für die Eingabevalidierung verwendet werden.

Sehen Sie sich ein Beispiel für eine [Validierungsfehlerantwort](#example-of-a-validation-error-response) an.

## <a name="example-responses"></a>Beispielantworten

### <a name="example-of-a-continuation-response"></a>Beispiel für eine Fortsetzungsantwort

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | type              | Erforderlich | BESCHREIBUNG                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | Ja      | Die Version der API.                                                                                                                                                                                                                                                                |
| action                                             | String            | Ja      | Der Wert muss `Continue` sein.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nein       | Zurückgegebene Werte können Werte überschreiben, die von einem Benutzer gesammelt wurden. Diese können auch im Token zurückgegeben werden, wenn sie als **Anwendungsanspruch** ausgewählt sind.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nein       | Der Anspruch muss `_<extensions-app-id>_` nicht enthalten. Zurückgegebene Werte können Werte überschreiben, die von einem Benutzer gesammelt wurden. Diese können auch im Token zurückgegeben werden, wenn sie als **Anwendungsanspruch** ausgewählt sind.  |

### <a name="example-of-a-blocking-response"></a>Beispiel für eine Blockierungsantwort

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parameter   | type   | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Ja      | Die Version der API.                                                    |
| action      | String | Ja      | Der Wert muss `ShowBlockPage` sein.                                              |
| userMessage | String | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |

**Anzeige einer Blockierungsantwort für den Endbenutzer**

![Beispiel für eine Blockierungsseite](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Beispiel für eine Validierungsfehlerantwort



```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parameter   | type    | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Ja      | Die Version der API.                                                    |
| action      | String  | Ja      | Der Wert muss `ValidationError` sein.                                           |
| status      | Integer | Ja      | Für eine Validierungsfehlerantwort muss der Wert `400` sein.                        |
| userMessage | String  | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |

> [!NOTE]
> Der HTTP-Statuscode muss „400“ lauten, und der Antworttext muss den Wert „status“ enthalten.

**Anzeige einer Validierungsfehlerantwort für den Endbenutzer**

![Beispiel für eine Validierungsseite](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Bewährte Methoden und Problembehandlungen

### <a name="using-serverless-cloud-functions"></a>Verwenden von serverlosen Cloudfunktionen
Serverlose Funktionen, z. B. HTTP-Trigger in Azure Functions, bieten eine einfache Möglichkeit zum Erstellen von API-Endpunkten, die mit dem API-Connector verwendet werden. Sie können die serverlosen Cloudfunktionen [beispielsweise](code-samples.md#api-connectors) verwenden, um Validierungslogik auszuführen und Registrierungen auf bestimmte E-Mail-Domänen zu beschränken. Mit den serverlosen Cloudfunktionen können auch andere Web-APIs, Benutzerspeicher und weitere Clouddienste für komplexere Szenarien aufgerufen werden.

### <a name="best-practices"></a>Bewährte Methoden
Stellen Sie Folgendes sicher:
* Ihre API entspricht den API-Anforderungs- und -Antwortverträgen, wie dies weiter oben beschrieben ist. 
* Die **Endpunkt-URL** des API-Connectors verweist auf den richtigen API-Endpunkt.
* In Ihrer API wird explizit auf NULL-Werte der empfangenen Ansprüche geprüft.
* Ihre API antwortet so schnell wie möglich, um eine flüssige Darstellung für den Benutzer zu gewährleisten.
    * Wenn Sie eine serverlose Funktion oder einen skalierbaren Webdienst verwenden, nutzen Sie einen Hostingplan, mit dem die API im aktiven Zustand („wach“ oder „warm“) gehalten wird. Für Azure Functions wird empfohlen, den [Premium-Plan](../azure-functions/functions-scale.md) zu verwenden.


### <a name="use-logging"></a>Verwenden von Protokollierung
Üblicherweise ist es nützlich, die von Ihrem Web-API-Dienst aktivierten Protokollierungstools, etwa [Application Insights](../azure-functions/functions-monitoring.md), zu verwenden, um Ihre API auf unerwartete Fehlercodes, Ausnahmen und schlechte Leistung zu überwachen.
* Überwachen Sie auf HTTP-Statuscodes, die weder HTTP 200 noch 400 sind.
* Der HTTP-Statuscode 401 oder 403 kennzeichnet in der Regel, dass ein Problem mit Ihrer Authentifizierung vorliegt. Überprüfen Sie die Authentifizierungsschicht Ihrer API und die entsprechende Konfiguration im API-Connector.
* Verwenden Sie in der Entwicklung ggf. eine aggressivere Protokollierungsebene (z. B. „trace“ oder „debug“).
* Überwachen Sie Ihre API hinsichtlich langer Antwortzeiten.

## <a name="next-steps"></a>Nächste Schritte
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
- Erste Schritte mit den [Beispielen](code-samples.md#api-connectors)
