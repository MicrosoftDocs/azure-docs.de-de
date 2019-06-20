---
title: Registrieren einer SaaS-Anwendung | Azure Marketplace
description: In diesem Artikel wird die Registrierung einer SaaS-Anwendung über das Azure-Portal erläutert.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: abe499c081c73d42e712d5872e121262a1a50197
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259384"
---
# <a name="register-a-saas-application"></a>Registrieren einer SaaS-Anwendung

In diesem Artikel wird die Registrierung einer SaaS-Anwendung über das [Microsoft Azure-Portal](https://portal.azure.com/) erläutert.  Nach der erfolgreichen Registrierung erhalten Sie ein Azure Active Directory-Sicherheitstoken (Azure AD), über das Sie auf die SaaS-Fulfillment-APIs zugreifen können.  Weitere Informationen zu Azure AD finden Sie unter [Was ist Authentifizierung?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="service-to-service-authentication-flow"></a>Ablauf der Dienst-zu-Dienst-Authentifizierung

Das folgende Diagramm zeigt den Ablauf des Abonnements eines neuen Kunden und die Verwendung dieser APIs:

![SaaS-Angebot: API-Ablauf](./media/saas-offer-publish-api-flow-v1.png)

Azure legt keine Einschränkungen für die Authentifizierung fest, die die SaaS-Lösung für ihre Endbenutzer verfügbar macht. Die Authentifizierung über die SaaS-Fulfillment-APIs erfolgt jedoch mit einem Azure AD-Sicherheitstoken, das normalerweise durch die Registrierung der SaaS-App im Azure-Portal abgerufen wird. 


## <a name="register-an-azure-ad-secured-app"></a>Registrieren einer über Azure AD geschützten App

Jede Anwendung muss zunächst in einem Azure AD-Mandanten registriert werden, um die Funktionen von Azure AD nutzen zu können. Dieser Registrierungsvorgang umfasst das Angeben von Details zu Ihrer Anwendung in Azure AD. Beispielsweise muss die URL für den Speicherort angegeben werden, die URL, an die nach der Authentifizierung eines Benutzers Antworten gesendet werden sollen, der URI zum Identifizieren der App usw.  Führen Sie die folgenden Schritte aus, um mit dem Azure-Portal eine neue Anwendung zu registrieren:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2.  Wenn Sie unter Ihrem Konto mehrere Zugriffsmöglichkeiten haben, können Sie oben rechts auf Ihr Konto klicken und Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten festlegen.
3.  Klicken Sie im linken Navigationsbereich auf den **Azure Active Directory**-Dienst und dann auf **App-Registrierungen** und **Registrierung einer neuen Anwendung**.

    ![SaaS: AD-App-Registrierungen](./media/saas-offer-app-registration-v1.png)

4.  Geben Sie auf der Seite „Erstellen“ die Registrierungsinformationen\' für Ihre Anwendung ein:
    -   **Name**: Geben Sie einen aussagekräftigen Anwendungsnamen ein.
    -   **Anwendungstyp**: 
        - Wählen Sie für [Clientanwendungen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), die lokal auf dem Gerät installiert sind, die Option **Nativ** aus. Diese Einstellung wird für öffentliche [native OAuth-Clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) verwendet.
        - Wählen Sie die Option **Web-App/API** für [Clientanwendungen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) und [Ressourcen-/API-Anwendungen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) aus, die auf einem sicheren Server installiert sind. Diese Einstellung wird für vertrauliche OAuth-[Webclients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) und für öffentliche [Clients auf Basis von Benutzer-Agents](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) verwendet.
        Außerdem kann dieselbe Anwendung sowohl einen Client als auch eine Ressource/API verfügbar machen.
    -   **Anmelde-URL**: Geben Sie für Web-Apps/API-Anwendungen die Basis-URL Ihrer App an. **http://localhost:31544** kann beispielsweise die URL für eine Web-App sein, die auf einem lokalen Computer ausgeführt wird. Benutzer können sich mit dieser URL dann bei einer Webclientanwendung anmelden.
    -   **Umleitungs-URI**: Geben Sie für native Anwendungen den URI an, der von Azure AD zum Zurückgeben von Tokenantworten verwendet wird. Geben Sie einen für Ihre Anwendung spezifischen Wert ein, z.B. **http://MyFirstAADApp** .

        ![SaaS: AD-App-Registrierungen](./media/saas-offer-app-registration-v1-2.png)

        Spezifische Beispiele für Webanwendungen oder native Anwendungen finden Sie in den Schnellstart-Einrichtungsanleitungen, die im Abschnitt *Erste Schritte* des [Azure AD-Entwicklerleitfadens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) verfügbar sind.

5.  Klicken Sie auf **Erstellen**, wenn Sie fertig sind. Azure AD weist Ihrer Anwendung eine eindeutige *Anwendungs-ID* zu, und Sie gelangen auf die Hauptseite für die Registrierung Ihrer Anwendung. Je nachdem, ob es sich bei Ihrer Anwendung um eine Web- oder eine systemeigene Anwendung handelt, werden jeweils andere Optionen zum Hinzufügen weiterer Funktionen zu Ihrer Anwendung bereitgestellt.

>[!Note]
>Die neu registrierte Anwendung wird standardmäßig so konfiguriert, dass sich nur Benutzer desselben Mandanten an Ihrer Anwendung anmelden können.


## <a name="using-the-azure-ad-security-token"></a>Verwenden des Azure AD-Sicherheitstokens

Nach der Registrierung der Anwendung können Sie programmgesteuert ein Azure AD-Sicherheitstoken anfordern.  Es wird erwartet, dass der Herausgeber dieses Token verwendet und eine Anforderung zu dessen Auflösung stellt.  Bei Verwendung der verschiedenen Fulfillment-APIs ist der Tokenabfrageparameter in der URL enthalten, wenn der Benutzer von Azure zur SaaS-Website umgeleitet wird.  Dieses Token ist nur eine Stunde lang gültig.  Zusätzlich sollten Sie eine URL-Decodierung des Tokenwerts im Browser durchführen, bevor Sie ihn verwenden.

Weitere Informationen zu diesen Token finden Sie unter [Azure Active Directory-Zugriffstoken](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Abrufen eines Tokens basierend auf der Azure AD-App

HTTP-Methode

`GET`

*Anforderungs-URL*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*URI-Parameter*

|  **Parametername**  | **Erforderlich**  | **Beschreibung**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Mandanten-ID der registrierten AAD-Anwendung   |
|  |  |  |


*Anforderungsheader*

|  **Headername**  | **Erforderlich** |  **Beschreibung**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Typ     | True         | Der Anforderung zugeordneter Inhaltstyp. Standardwert: `application/x-www-form-urlencoded`.  |
|  |  |  |


*Anforderungstext*

| **Eigenschaftenname**   | **Erforderlich** |  **Beschreibung**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Gewährungstyp. Standardwert: `client_credentials`.                    |
|  Client_id          | True         |  Der Azure AD-App zugeordneter Client-/App-Bezeichner.                  |
|  client_secret      | True         |  Der Azure AD-App zugeordnetes Kennwort.                               |
|  Resource           | True         |  Zielressource, für die das Token angefordert wird. Standardwert: `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Antwort*

|  **Name**  | **Typ**       |  **Beschreibung**    |
| ---------- | -------------  | ------------------- |
| 200 – OK    | TokenResponse  | Anforderung erfolgreich   |
|  |  |  |

*TokenResponse*

Beispiel für Antworttoken:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Nächste Schritte

In der über Azure AD geschützten App kann jetzt [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md) verwendet werden.
