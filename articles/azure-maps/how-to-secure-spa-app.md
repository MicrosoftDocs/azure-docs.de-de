---
title: Schützen einer Single-Page-Webanwendung mit nicht interaktiver Anmeldung
titleSuffix: Azure Maps
description: Konfigurieren einer Single-Page-Webanwendung mit nicht interaktiver rollenbasierter Zugriffssteuerung von Azure (Azure RBAC) und Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: c39104912c99b199d38cf489bb61d64e83b89286
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895595"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Schützen einer Single-Page-Webanwendung mit nicht interaktiver Anmeldung

Die folgende Anleitung bezieht sich auf eine Anwendung, die Azure Active Directory (Azure AD) verwendet, um ein Zugriffstoken für Azure Maps-Anwendungen bereitzustellen, wenn sich der Benutzer nicht bei Azure AD anmelden kann. Dieser Flow erfordert das Hosting eines Webdiensts, der so gesichert sein muss, dass nur die Single-Page-Webanwendung darauf zugreifen kann. Es gibt mehrere Implementierungen, mit denen die Authentifizierung bei Azure AD erreicht werden kann. Diese Anleitung nutzt das Produkt Azure Functions, um Zugriffstoken abzurufen.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps kann Zugriffstoken aus Benutzeranmeldungs-/interaktiven Flows unterstützen. Interaktive Flows ermöglichen einen eingeschränkteren Bereich der Zugriffssperrung und Geheimnisverwaltung.

## <a name="create-azure-function"></a>Azure-Funktion erstellen

Erstellen Sie eine gesicherte Webdienstanwendung, die für die Authentifizierung bei Azure AD zuständig ist. 

1. Erstellen Sie eine Funktion im Azure-Portal. Weitere Informationen finden Sie unter [Erstellen einer Azure-Funktion](../azure-functions/functions-create-first-azure-function.md).

2. Konfigurieren Sie die CORS-Richtlinie für die Azure-Funktion so, dass Sie die Single-Page-Webanwendung darauf zugreifen kann. Dadurch werden Browserclients auf die zulässigen Ursprünge Ihrer Webanwendung gesichert. Siehe [Hinzufügen der CORS-Funktion](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Fügen Sie der Azure-Funktion eine systemseitig zugewiesene Identität hinzu](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity), um das Erstellen eines Dienstprinzipals für die Authentifizierung bei Azure AD zu ermöglichen.  

4. Gewähren Sie der systemseitig zugewiesenen Identität rollenbasierten Zugriff auf das Azure Maps-Konto. Details hierzu finden Sie unter [Gewähren des rollenbasierten Zugriffs](#grant-role-based-access).

5. Schreiben Sie Code für die Azure-Funktion, mit dem sie Azure Maps-Zugriffstoken mittels einer systemseitig zugewiesenen Identität mit einem der unterstützten Mechanismen oder dem REST-Protokoll abruft. Siehe [Abrufen von Token für Azure-Ressourcen](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity).

    Beispiel für ein REST-Protokoll:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Beispiel für eine Antwort:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Konfigurieren der Sicherheit für den HttpTrigger der Azure-Funktion

   * [Erstellen eines Funktionszugriffsschlüssels](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * [Sichern Sie den HTTP-Endpunkt](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) für die in Produktion befindliche Azure-Funktion.
   
7. Konfigurieren Sie das Azure Maps Web SDK der Webanwendung. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Gewähren von rollenbasiertem Zugriff

Sie gewähren die *rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)* , indem Sie die systemseitig zugewiesene Identität einer oder mehreren Azure-Rollendefinitionen zuweisen. Informationen über die für Azure Maps verfügbaren Rollendefinitionen für die rollenbasierte Zugriffssteuerung von Azure finden Sie unter **Zugriffssteuerung (IAM)** . Wählen Sie **Rollen** aus, und suchen Sie dann nach Rollen, die mit *Azure Maps* beginnen.

1. Navigieren Sie zu Ihrem **Azure Maps-Konto** . Wählen Sie **Zugriffssteuerung (IAM)**  > **Rollenzuweisungen** aus.

    > [!div class="mx-imgBorder"]
    > ![Gewähren von Zugriff mithilfe von Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Wählen Sie auf der Registerkarte **Rollenzuweisungen** unter **Rolle** eine integrierte Azure Maps-Rollendefinition aus, z. B. **Azure Maps-Datenleser** oder **Azure Maps-Datenmitwirkender** . Wählen Sie unter **Zugriff zuweisen zu** die Option **Funktions-App** aus. Wählen Sie den Prinzipal nach Namen aus. Klicken Sie dann auf **Speichern** .

   * Lesen Sie die Details zum [Hinzufügen oder Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Integrierte Azure Maps-Rollendefinitionen bieten einen sehr umfangreichen Autorisierungszugriff auf viele Azure Maps-REST-APIs. Informationen, wie Sie den Zugriff auf APIs auf ein Mindestmaß beschränken, finden Sie unter [Erstellen einer benutzerdefinierten Rollendefinition und Zuweisen der systemseitig zugewiesenen Identität](../role-based-access-control/custom-roles.md) zur benutzerdefinierten Rollendefinition. Dadurch wird die geringste Berechtigung aktiviert, die für den Zugriff der Anwendung auf Azure Maps erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Single-Page-Webanwendungsszenario:
> [!div class="nextstepaction"]
> [Einzelseitenanwendung](../active-directory/develop/scenario-spa-overview.md)

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Erkunden Sie weitere Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)