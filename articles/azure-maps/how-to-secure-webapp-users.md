---
title: Schützen einer Webanwendung mit interaktiver einmaliger Anmeldung
titleSuffix: Azure Maps
description: Erfahren Sie, wie Sie eine Webanwendung konfigurieren, die einmaliges Anmelden über Azure AD mit dem Azure Maps Web SDK unter Verwendung des Protokolls OpenID Connect unterstützt.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895580"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Schützen einer Webanwendung mit Benutzeranmeldung

Der folgende Leitfaden bezieht sich auf eine Anwendung, die auf Webservern gehostet wird, mehrere Geschäftsszenarien verwaltet und auf Webservern bereitgestellt wird. Die Anwendung hat die Anforderung, geschützte Ressourcen bereitzustellen, die nur für Benutzer von Azure AD geschützt sind. Das Ziel des Szenarios besteht darin, die Webanwendung für die Authentifizierung bei Azure AD zu aktivieren und Azure Maps-REST-APIs im Auftrag des Benutzers aufzurufen.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Erstellen einer Anwendungsregistrierung in Azure AD

Sie müssen die Webanwendung in Azure AD erstellen, damit Benutzer sich anmelden können. Die Webanwendung delegiert anschließend den Benutzerzugriff an Azure Maps-REST-APIs.

1. Wählen Sie im Azure-Portal in der Liste der Azure-Dienste die Optionen **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.  

    > [!div class="mx-imgBorder"]
    > ![App-Registrierung](./media/how-to-manage-authentication/app-registration.png)

2. Geben Sie einen **Namen** ein, und wählen Sie einen **Supportkontotyp** aus. Geben Sie einen Umleitungs-URI an, der die URL darstellt, für die Azure AD das Token ausstellt, und die URL ist, unter der das Kartensteuerelement gehostet wird. Weitere Informationen finden Sie im Azure AD-[Szenario: Web-App, die Benutzer anmeldet](../active-directory/develop/scenario-web-app-sign-user-overview.md). Führen Sie die angegebenen Schritte im Azure AD-Szenario aus.  

3. Sobald die Registrierung der Anwendung abgeschlossen ist, bestätigen Sie, dass Benutzer sich bei der Anwendung anmelden können. Wenn die Anmeldung funktioniert, kann der Anwendung ein delegierter Zugriff auf die REST-APIs von Azure Maps gewährt werden.
    
4.  Navigieren Sie zu der Anwendung, um Azure Maps die delegierten API-Berechtigungen zuzuweisen. Wählen Sie dann **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Suchen Sie unter **Von meiner Organisation verwendete APIs** die Option **Azure Maps** , und wählen Sie sie aus.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen von App-API-Berechtigungen](./media/how-to-manage-authentication/app-permissions.png)

5. Aktivieren Sie das Kontrollkästchen neben **Auf Azure Maps zugreifen** , und wählen Sie dann **Berechtigungen hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Auswählen von App-API-Berechtigungen](./media/how-to-manage-authentication/select-app-permissions.png)

6. Aktivieren Sie die Webanwendung für das Aufrufen der REST-APIs von Azure Maps, indem Sie die App-Registrierung mit einem Anwendungsgeheimnis konfigurieren. Detaillierte Anweisungen finden Sie unter [Eine Web-App, die Web-APIs aufruft: App-Registrierung](../active-directory/develop/scenario-web-app-call-api-app-registration.md). Ein Geheimnis ist erforderlich, um sich bei Azure AD im Namen des Benutzers zu authentifizieren. Das Registrierungszertifikat oder Geheimnis der App sollte in einem sicheren Speicher aufbewahrt werden, damit die Web-App zur Authentifizierung bei Azure AD abgerufen werden kann. 
   
   * Wenn für die Anwendung bereits eine Azure AD-App-Registrierung und ein Geheimnis konfiguriert wurde, kann dieser Schritt übersprungen werden.

> [!Tip]
> Wenn die Anwendung in einer Azure-Umgebung gehostet wird, empfehlen wir, [verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) und eine Azure Key Vault-Instanz zu verwenden, um auf Geheimnisse zuzugreifen. Dies geschieht durch [Beziehen eines Zugriffstokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) für den Zugriff auf Geheimnisse oder Zertifikate in Azure Key Vault. Informationen zum Herstellen einer Verbindung mit Azure Key Vault zum Abrufen von Geheimnissen finden Sie im [Tutorial zum Herstellen einer Verbindung mithilfe einer verwalteten Identität](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Implementieren Sie einen sicheren Token-Endpunkt für das Azure Maps Web SDK, um auf ein Token zuzugreifen. 
   
   * Ein Beispiel eines Tokencontrollers finden Sie unter [Azure Maps: Azure AD-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Informationen zu einer Nicht-AspNetCore- oder anderen Implementierung finden Sie unter [Beziehen des Tokens für die App](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) in der Azure AD-Dokumentation.
   * Der geschützte Token-Endpunkt ist dafür zuständig, dem authentifizierten und autorisierten Benutzer ein Zugriffstoken für den Aufruf der REST-APIs von Azure Maps zurückzugeben.

8. Konfigurieren Sie die rollenbasierte Azure-Zugriffssteuerung (Azure RBAC) für Benutzer oder Gruppen. Details hierzu finden Sie unter [Erteilen des rollenbasierten Zugriffs für Benutzer](#grant-role-based-access-for-users-to-azure-maps).

9. Konfigurieren Sie die Webanwendungsseite mit dem Azure Maps Web SDK für den Zugriff auf den sicheren Token-Endpunkt. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Webanwendungsszenario:
> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps: Beispiele für Azure AD-Web-Apps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)