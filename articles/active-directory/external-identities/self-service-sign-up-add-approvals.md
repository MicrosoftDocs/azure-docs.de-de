---
title: Hinzufügen von benutzerdefinierten Genehmigungen zu Self-Service-Registrierungsflows – Azure AD
description: Hinzufügen von API-Connectors für benutzerdefinierte Genehmigungsworkflows in Self-Service-Registrierung externer Identitäten – Azure Active Directory (Azure AD)
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
ms.openlocfilehash: 82f0408b7e46493f6c3ec62d48a992e87f196f78
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860608"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Hinzufügen eines benutzerdefinierten Genehmigungsworkflows zur Self-Service-Registrierung

Mit [API-Connectors](api-connectors-overview.md) können Sie Ihre eigenen benutzerdefinierten Genehmigungsworkflows in die Self-Service-Registrierung integrieren, sodass Sie verwalten können, welche Gastbenutzerkonten in Ihrem Mandanten erstellt werden.

Dieser Artikel enthält ein Beispiel für die Integration in ein Genehmigungssystem. In diesem Beispiel sammelt der Benutzerflow der Self-Service-Registrierung während des Registrierungsvorgangs Benutzerdaten und übergibt sie an Ihr Genehmigungssystem. Anschließend kann das Genehmigungssystem folgende Aktionen ausführen:

- Automatisch den Benutzer genehmigen und Azure AD erlauben, das Benutzerkonto zu erstellen.
- Eine manuelle Überprüfung auslösen. Wenn die Anforderung genehmigt wird, verwendet das Genehmigungssystem Microsoft Graph zur Bereitstellung des Benutzerkontos. Das Genehmigungssystem kann den Benutzer auch darüber benachrichtigen, dass sein Konto erstellt wurde.

## <a name="register-an-application-for-your-approval-system"></a>Registrieren einer Anwendung für Ihr Genehmigungssystem

Sie müssen Ihr Genehmigungssystem als Anwendung in Ihrem Azure AD-Mandanten registrieren, damit es sich mit Azure AD authentifizieren und über die Berechtigung zum Erstellen von Benutzern verfügen kann. Erfahren Sie mehr über [Grundlagen zu Authentifizierung und Autorisierung für Microsoft Graph](/graph/auth/auth-concepts).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im linken Menü **App-Registrierungen** und dann **Neue Registrierung** aus.
4. Geben Sie einen **Namen** für die Anwendung ein, z. B. _Registrierungsgenehmigungen_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Wählen Sie **Registrieren**. Sie können bei anderen Feldern die Standardwerte beibehalten.

   ![Screenshot: Hervorgehobene Schaltfläche „Registrieren“](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. Wählen Sie unter **Verwalten** im linken Menü **API-Berechtigungen** und dann **Berechtigung hinzufügen** aus.
7. Wählen Sie auf der Seite **API-Berechtigungen anfordern** die Option **Microsoft Graph** aus, und wählen Sie **Anwendungsberechtigungen** aus.
8. Erweitern Sie unter **Berechtigungen auswählen** den Eintrag **Benutzer**, und aktivieren Sie dann das Kontrollkästchen **User.ReadWrite.All**. Diese Berechtigung ermöglicht dem Genehmigungssystem, den Benutzer nach der Genehmigung zu erstellen. Klicken Sie dann auf **Berechtigungen hinzufügen**.

   ![Seite für das Registrieren einer Anwendung](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Wählen Sie auf der Seite **API-Berechtigungen** die Option **Administratorzustimmung für (Ihr Mandantenname) erteilen** und dann **Ja**.
10. Wählen Sie im linken Menü unter **Verwalten** die Option **Zertifikate & Geheimnisse** und anschließend **Neuer geheimer Clientschlüssel** aus.
11. Geben Sie eine **Beschreibung** für das Geheimnis ein, z. B. _Genehmigungen für geheimen Clientschlüssel_, und wählen Sie den Zeitraum bis zum Ablauf des geheimen Clientschlüssels mit einem Eintrag in **Läuft ab**. Wählen Sie anschließend **Hinzufügen**.
12. Kopieren Sie den Wert des geheimen Clientschlüssels.

    ![Kopieren des geheimen Clientschlüssels für die Verwendung im Genehmigungssystem](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Konfigurieren Sie das Genehmigungssystem für die Verwendung der **Anwendungs-ID** als Client-ID und des **geheimen Clientschlüssels**, den Sie für die Authentifizierung mit Azure AD generiert haben.

## <a name="create-the-api-connectors"></a>Erstellen der API-Connectors

Als nächstes [erstellen Sie die API-Connectors](self-service-sign-up-add-api-connector.md#create-an-api-connector) für ihren Self-Service-Registrierungs-Benutzerflow. Ihre Genehmigungssystem-API benötigt zwei Connectors und entsprechende Endpunkte, wie im folgenden Beispiel gezeigt. Diese API-Connectors führen folgende Aktionen aus:

- **Genehmigungsstatus überprüfen**. Senden Sie sofort nach der Anmeldung eines Benutzers mit einem Identitätsanbieter einen Aufruf an das Genehmigungssystem, um zu überprüfen, ob der Benutzer über eine Genehmigungsanforderung verfügt oder bereits abgelehnt wurde. Wenn Ihr Genehmigungssystem nur automatische Genehmigungsentscheidungen durchführt, ist dieser API-Connector möglicherweise nicht erforderlich. Beispiel für API-Connector „Genehmigungsstatus überprüfen“.

  ![Genehmigungsstatus überprüfen – API-Connector-Konfiguration](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Genehmigung anfordern**: Senden Sie einen Aufruf an das Genehmigungssystem, nachdem ein Benutzer die Attributsammlungseite abgeschlossen hat, aber bevor das Benutzerkonto erstellt wird, um die Genehmigung anzufordern. Die Genehmigungsanforderung kann automatisch erteilt oder manuell überprüft werden. Beispiel für API-Conncector „Genehmigung anfordern“. 

  ![Genehmigung anfordern – API-Connector-Konfiguration](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Befolgen Sie zum Erstellen dieser Connectors die Schritte in [Erstellen eines API-Connectors](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Aktivieren der API-Connectors in einem Benutzerflow

Nun fügen Sie die API-Connectors mit folgenden Schritten einem Self-Service-Registrierungs-Benutzerflow hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Benutzerflows (Vorschau)** aus und dann den Benutzerflow, für den Sie den API-Connector aktivieren möchten.
5. Wählen Sie mit folgenden Schritte **API-Connectors** aus und dann die API-Endpunkte, die Sie im Benutzerflow aufrufen möchten:

   - **Nach der Anmeldung mit einem Identitätsanbieter**: Wählen Sie den API-Connector für den Genehmigungsstatus aus, z. B. _Genehmigungsstatus überprüfen_.
   - **Vor dem Erstellen des Benutzers**: Wählen Sie den API-Connector für Ihre Genehmigungsanforderung aus, z. B. _Genehmigung anfordern_.

   ![Hinzufügen von APIs zum Benutzerflow](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Wählen Sie **Speichern** aus.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Steuern des Registrierungsflows mit API-Antworten

Das Genehmigungssystem kann seine Antworten verwenden, wenn es zum Steuern des Registrierungsflows aufgerufen wird. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Anforderung und Antworten für den API-Connector „Genehmigungsstatus überprüfen“

Beispiel für die Anforderung, die von der API über den API-Connector „Genehmigungsstatus überprüfen“ empfangen wurde:

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

Die genauen Ansprüche, die an die API gesendet werden, hängen von den Informationen ab, die vom Identitätsanbieter bereitgestellt werden. „Email“ wird immer gesendet.

#### <a name="continuation-response-for-check-approval-status"></a>Fortsetzungsantwort für „Genehmigungsstatus überprüfen“

Der **Genehmigungsstatus überprüfen**-API-Endpunkt sollte unter folgender Voraussetzung eine Fortsetzungsantwort zurückgeben:

- Der Benutzer hat zuvor keine Genehmigung angefordert.

Beispiel für eine Fortsetzungsantwort:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Blockierungsantwort für „Genehmigungsstatus überprüfen“

Der **Genehmigungsstatus überprüfen**-API-Endpunkt sollte unter folgender Voraussetzung eine Blockierungsantwort zurückgeben:

- Die Benutzergenehmigung steht aus.
- Der Benutzer wurde abgelehnt und er sollte die Genehmigung nicht erneut anfordern dürfen.

Nachfolgend finden Sie Beispiele für Blockierungsantworten:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Anforderung und Antworten für den API-Connector „Genehmigung anfordern“

Beispiel für eine HTTP-Anforderung, die von der API über den API-Connector „Genehmigung anfordern“ empfangen wurde:

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

#### <a name="continuation-response-for-request-approval"></a>Fortsetzungsantwort für „Genehmigung anfordern“

Der **Genehmigung anfordern**-API-Endpunkt sollte unter folgenden Voraussetzungen eine Fortsetzungsantwort zurückgeben:

- Der Benutzer kann **_automatisch genehmigt_** werden.

Beispiel für eine Fortsetzungsantwort:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Bei Empfang einer Fortsetzungsantwort erstellt Azure AD ein Benutzerkonto und leitet den Benutzer an die Anwendung weiter.

#### <a name="blocking-response-for-request-approval"></a>Blockierungsantwort für „Genehmigung anfordern“

Der **Genehmigung anfordern**-API-Endpunkt sollte unter folgenden Voraussetzungen eine Blockierungsantwort zurückgeben:

- Eine Benutzergenehmigungsanforderung wurde erstellt und steht nun aus.
- Eine Benutzergenehmigungsanforderung wurde automatisch abgelehnt.

Nachfolgend finden Sie Beispiele für Blockierungsantworten:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

Die `userMessage` in der Antwort wird dem Benutzer angezeigt, z. B.:

![Beispielseite für ausstehende Genehmigung](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Erstellung eines Benutzerkontos nach manueller Genehmigung

Nach dem Erhalt der manuellen Genehmigung erstellt das benutzerdefinierte Genehmigungssystem mit [Microsoft Graph](/graph/use-the-api) ein [Benutzerkonto](/graph/azuread-users-concept-overview). Wie das Genehmigungssystem das Benutzerkonto bereitstellt, hängt von dem Identitätsanbieter ab, der vom Benutzer verwendet wurde.

### <a name="for-a-federated-google-or-facebook-user"></a>Für einen verbundenen Google- oder Facebook-Benutzer

> [!IMPORTANT]
> Das Genehmigungssystem sollte explizit prüfen, ob `identities`, `identities[0]` und `identities[0].issuer` vorhanden sind und `identities[0].issuer` „facebook“ oder „google“ ist, um diese Methode zu verwenden.

Wenn sich Ihr Benutzer mit einem Google- oder Facebook-Konto angemeldet hat, können Sie die [Benutzererstellungs-API](/graph/api/user-post-users?tabs=http) verwenden.

1. Das Genehmigungssystem verwendet die HTTP-Anforderung aus dem Benutzerflow.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Das Genehmigungssystem verwendet Microsoft Graph, um ein Benutzerkonto zu erstellen.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parameter                                           | Erforderlich | BESCHREIBUNG                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Ja      | Kann generiert werden, indem in dem an die API gesendeten `email`-Anspruch das `@`-Zeichen durch `_` ersetzt wird und er `#EXT@<tenant-name>.onmicrosoft.com` vorangestellt wird. |
| accountEnabled                                      | Ja      | Muss auf `true` festgelegt sein.                                                                                                                                                 |
| mail                                                | Ja      | Entspricht dem an die API gesendeten `email`-Anspruch.                                                                                                               |
| userType                                            | Ja      | Muss `Guest`lauten. Weist diesen Benutzer als Gastbenutzer aus.                                                                                                                 |
| Identitäten                                          | Ja      | Die Verbundidentitätsinformationen.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | Nein       | Andere integrierte Attribute wie `displayName`, `city` und andere. Parameternamen sind identisch mit den vom API-Connector gesendeten Parametern.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | Nein       | Benutzerdefinierte Attribute über den Benutzer. Parameternamen sind identisch mit den vom API-Connector gesendeten Parametern.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>Für einen verbundenen Azure Active Directory-Benutzer

Wenn ein Benutzer sich mit einem verbundenen Azure Active Directory-Konto anmeldet, müssen Sie den Benutzer mit der [Einladungs-API](/graph/api/invitation-post) erstellen und ihm dann optional mit der [Benutzeraktualisierungs-API](/graph/api/user-update) weitere Attribute zuweisen.

1. Das Genehmigungssystem empfängt die HTTP-Anforderung aus dem Benutzerflow.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Das Genehmigungssystem erstellt die Einladung mithilfe der vom API-Connector bereitgestellten `email`.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Beispielanwort:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. Das Genehmigungssystem aktualisiert mit der ID des eingeladenen Benutzers das Konto des Benutzers mit den gesammelten Benutzerattributen (optional).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit unseren [Azure Functions-Schnellstartbeispielen](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Untersuchen Sie das [Beispiel für eine Self-Service-Registrierung für Gastbenutzer mit manueller Genehmigung](code-samples-self-service-sign-up.md#custom-approval-workflows).
