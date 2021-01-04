---
title: Hinzufügen von API-Connectors zu Self-Service-Registrierungsflows – Azure AD
description: Konfigurieren Sie eine Web-API zur Verwendung in einem Benutzerflow.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d121e6280b83265a742736f9b8dd3aee96a8b32e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351758"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Hinzufügen eines API-Connectors zu einem Benutzerflow

Um einen [API-Connector](api-connectors-overview.md) zu verwenden, erstellen Sie zunächst den API-Connector und aktivieren ihn anschließend in einem Benutzerflow.

## <a name="create-an-api-connector"></a>Erstellen eines API-Connectors

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Alle API-Connectors (Vorschau)** und dann **Neuer API-Connector** aus.

   ![Hinzufügen eines neuen API-Connectors](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Geben Sie einen Anzeigenamen für den Aufruf an, z. B. **Genehmigungsstatus überprüfen**.
6. Geben Sie die **Endpunkt-URL** für den API-Aufruf an.
7. Geben Sie die Authentifizierungsinformationen für die API an.

   - Derzeit wird nur die Standardauthentifizierung unterstützt. Wenn Sie eine API ohne Standardauthentifizierung für Entwicklungszwecke verwenden möchten, geben Sie einfach Dummywerte für **Benutzername** und **Kennwort** ein, die in der API ignoriert werden können. Zur Verwendung mit einer Azure-Funktion mit einem API-Schlüssel können Sie den Code als Abfrageparameter in der **Endpunkt-URL** einfügen (z. B. https []()://contoso.azurewebsites.net/api/endpoint <b>?code=0123456789</b>).

   ![Konfigurieren eines neuen API-Connectors](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Wählen Sie **Speichern** aus.

> [!IMPORTANT]
> Bisher mussten Sie konfigurieren, welche Benutzerattribute an die API gesendet („Zu sendende Ansprüche“) und von der API angenommen („Zu empfangende Ansprüche“) werden sollten. Jetzt werden standardmäßig alle Benutzerattribute gesendet, wenn sie einen Wert haben, und jedes Benutzerattribut kann von der API in einer „Fortsetzungsantwort“ zurückgegeben werden.

## <a name="the-request-sent-to-your-api"></a>An die API gesendete Anforderung
Ein API-Connector wird als **HTTP POST**-Anforderung dargestellt und sendet Benutzerattribute („Ansprüche“) als Schlüssel-Wert-Paare in einem JSON-Text. Attribute werden ähnlich wie [Microsoft Graph](/graph/api/resources/user#properties)-Benutzereigenschaften serialisiert. 

**Beispielanforderung**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

Nur Benutzereigenschaften und benutzerdefinierte Attribute, die unter **Azure Active Directory** > **Externe Identitäten** > **Benutzerdefinierte Benutzerattribute** aufgeführt sind, können in der Anforderung gesendet werden.

Benutzerdefinierte Attribute sind im Verzeichnis im Format **extension_\<extensions-app-id>_AttributName** vorhanden. Die API sollte den Empfang von Ansprüchen in diesem serialisierten Format erwarten. Weitere Informationen zu benutzerdefinierten Attributen finden Sie unter [Definieren von benutzerdefinierten Attributen für Self-Service-Registrierungsflows](user-flow-add-custom-attributes.md).

Außerdem wird der Anspruch **Gebietsschema der Benutzeroberfläche („ui_locales“)** standardmäßig in allen Anforderungen gesendet. Er gibt das Gebietsschema (oder auch mehrere Gebietsschemas) eines Benutzers an, wie es auf seinem Gerät konfiguriert ist. Dieses kann von der API verwendet werden, um internationalisierte Antworten zurückzugeben.

> [!IMPORTANT]
> Wenn ein zu sendender Anspruch zum Zeitpunkt des Aufrufs des API-Endpunkts keinen Wert enthält, wird er nicht an die API gesendet. Die API sollte so entworfen werden, dass sie explizit den erwarteten Wert überprüft.

> [!TIP] 
> Mit den Ansprüchen [**Identitäten („identities“)**](/graph/api/resources/objectidentity) und **E-Mail-Adresse („email“)** kann Ihre API einen Benutzer identifizieren, bevor er über ein Konto in Ihrem Mandanten verfügt. Der Anspruch „identities“ wird gesendet, wenn sich ein Benutzer mit einem Identitätsanbieter (z. B. Google oder Facebook) authentifiziert. Der Anspruch „email“ wird immer gesendet.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivieren des API-Connectors in einem Benutzerflow

Führen Sie die folgenden Schritte aus, um einem Benutzerflow für die Self-Service-Registrierung einen API-Connector hinzuzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Benutzerflows (Vorschau)** und dann den Benutzerflow aus, dem Sie den API-Connector hinzufügen möchten.
5. Wählen Sie **API-Connectors** und dann die API-Endpunkte aus, die bei den folgenden Schritten im Benutzerflow aufgerufen werden sollen:

   - **Nach Anmeldung bei einem Identitätsanbieter**
   - **Vor dem Erstellen des Benutzers**

   ![Hinzufügen von APIs zum Benutzerflow](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Wählen Sie **Speichern** aus.

## <a name="after-signing-in-with-an-identity-provider"></a>Nach Anmeldung bei einem Identitätsanbieter

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird unmittelbar nach der Authentifizierung des Benutzers bei einem Identitätsanbieter (Google, Facebook, Azure AD) aufgerufen. Dieser Schritt geht der **_Seite zur Attributsammlung_* _ voraus, die dem Benutzer zum Sammeln von Benutzerattributen angezeigt wird. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>In diesem Schritt an die API gesendete Beispielanforderung
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

Ein API-Connector in diesem Schritt des Registrierungsprozesses wird nach der Seite zur Attributsammlung aufgerufen, sofern vorhanden. Dieser Schritt wird stets aufgerufen, ehe ein Benutzerkonto in Azure AD erstellt wird. 

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
 "identities": [ //Sent for Google and Facebook identity providers
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
| \<builtInUserAttribute>                            | \<attribute-type> | Nein       | Werte können im Verzeichnis gespeichert werden, wenn sie als _ *zu empfangender Anspruch** in der API-Connector-Konfiguration und als **Benutzerattribute** für einen Benutzerflow ausgewählt sind. Werte können im Token zurückgegeben werden, wenn sie als **Anwendungsanspruch** ausgewählt sind.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nein       | Der zurückgegebene Anspruch muss `_<extensions-app-id>_` nicht enthalten. Werte werden im Verzeichnis gespeichert, wenn sie als **zu empfangender Anspruch** in der API-Connector-Konfiguration und als **Benutzerattribute** für einen Benutzerflow ausgewählt sind. Benutzerdefinierte Attribute können im Token nicht zurückgesendet werden. |

### <a name="example-of-a-blocking-response"></a>Beispiel für eine Blockierungsantwort

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parameter   | type   | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Ja      | Die Version der API.                                                    |
| action      | String | Ja      | Der Wert muss `ShowBlockPage` sein.                                              |
| userMessage | String | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |
| code        | String | Nein       | Fehlercode Kann zum Debuggen verwendet werden. Wird nicht für den Benutzer angezeigt. |

**Anzeige einer Blockierungsantwort für den Endbenutzer**

![Beispiel für eine Blockierungsseite](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Beispiel für eine Validierungsfehlerantwort

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parameter   | type    | Erforderlich | BESCHREIBUNG                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Ja      | Die Version der API.                                                    |
| action      | String  | Ja      | Der Wert muss `ValidationError` sein.                                           |
| status      | Integer | Ja      | Für eine Validierungsfehlerantwort muss der Wert `400` sein.                        |
| userMessage | String  | Ja      | Meldung, die für den Benutzer angezeigt wird.                                            |
| code        | String  | Nein       | Fehlercode Kann zum Debuggen verwendet werden. Wird nicht für den Benutzer angezeigt. |

**Anzeige einer Validierungsfehlerantwort für den Endbenutzer**

![Beispiel für eine Validierungsseite](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Bewährte Methoden und Problembehandlungen

### <a name="using-serverless-cloud-functions"></a>Verwenden von serverlosen Cloudfunktionen
Serverlose Funktionen, z. B. HTTP-Trigger in Azure Functions, bieten eine einfache Möglichkeit zum Erstellen von API-Endpunkten, die mit dem API-Connector verwendet werden. Sie können die serverlosen Cloudfunktionen [beispielsweise](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts) verwenden, um Validierungslogik auszuführen und Registrierungen auf bestimmte Domänen zu beschränken. Mit den serverlosen Cloudfunktionen können auch andere Web-APIs, Benutzerspeicher und weitere Clouddienste für komplexere Szenarien aufgerufen werden.

### <a name="best-practices"></a>Bewährte Methoden
Stellen Sie Folgendes sicher:
* Ihre API entspricht den API-Anforderungs- und -Antwortverträgen, wie dies weiter oben beschrieben ist. 
* Die **Endpunkt-URL** des API-Connectors verweist auf den richtigen API-Endpunkt.
* In Ihrer API wird explizit auf NULL-Werte der empfangenen Ansprüche geprüft.
* Ihre API antwortet so schnell wie möglich, um eine flüssige Darstellung für den Benutzer zu gewährleisten.
    * Wenn Sie eine serverlose Funktion oder einen skalierbaren Webdienst verwenden, nutzen Sie einen Hostingplan, mit dem die API im aktiven Zustand („wach“ oder „warm“) Für Azure Functions wird empfohlen, den [Premium-Plan](../../azure-functions/functions-scale.md#premium-plan) zu verwenden. 


### <a name="use-logging"></a>Verwenden von Protokollierung
Üblicherweise ist es nützlich, die von Ihrem Web-API-Dienst aktivierten Protokollierungstools, etwa [Application Insights](../../azure-functions/functions-monitoring.md), zu verwenden, um Ihre API auf unerwartete Fehlercodes, Ausnahmen und schlechte Leistung zu überwachen.
* Überwachen Sie auf HTTP-Statuscodes, die weder HTTP 200 noch 400 sind.
* Der HTTP-Statuscode 401 oder 403 kennzeichnet in der Regel, dass ein Problem mit Ihrer Authentifizierung vorliegt. Überprüfen Sie die Authentifizierungsschicht Ihrer API und die entsprechende Konfiguration im API-Connector.
* Verwenden Sie in der Entwicklung ggf. eine aggressivere Protokollierungsebene (z. B. „trace“ oder „debug“).
* Überwachen Sie Ihre API hinsichtlich langer Antwortzeiten.

## <a name="next-steps"></a>Nächste Schritte
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Erfahren Sie, wie Sie [der Self-Service-Registrierung einen benutzerdefinierten Genehmigungsworkflow hinzufügen](self-service-sign-up-add-approvals.md).
- Erste Schritte mit den [Azure Functions-Schnellstartbeispielen](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->