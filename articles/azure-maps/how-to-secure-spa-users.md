---
title: Schützen einer Single-Page-Webanwendung mit Benutzeranmeldung
titleSuffix: Azure Maps
description: Konfigurieren einer Single-Page-Anwendung, die einmaliges Anmelden bei Azure AD mit Azure Maps Web SDK unterstützt.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895612"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Schützen einer Single-Page-Webanwendung mit Benutzeranmeldung

Die folgende Anleitung bezieht sich auf eine Anwendung, die auf einem Inhaltsserver gehostet wird oder nur minimale Webserverabhängigkeiten besitzt. Die Anwendung bietet nur Azure AD-Benutzern geschützte Ressourcen. Das Ziel des Szenarios besteht darin, die Webanwendung für die Authentifizierung bei Azure AD zu aktivieren und Azure Maps-REST-APIs im Auftrag des Benutzers aufzurufen.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Erstellen einer Anwendungsregistrierung in Azure AD

Erstellen Sie die Webanwendung in Azure AD, damit Benutzer sich anmelden können. Die Webanwendung delegiert den Benutzerzugriff an Azure Maps-REST-APIs.

1. Wählen Sie im Azure-Portal in der Liste der Azure-Dienste die Optionen **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.  

    > [!div class="mx-imgBorder"]
    > ![App-Registrierung](./media/how-to-manage-authentication/app-registration.png)

2. Geben Sie einen **Namen** ein, wählen Sie einen **Supportkontotyp** aus, geben Sie einen Umleitungs-URI an, der die URL darstellt, für die Azure AD das Token ausstellt, und die URL ist, unter der das Kartensteuerelement gehostet wird. Ein ausführliches Beispiel finden Sie unter [Azure Maps Azure AD: Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Klicken Sie anschließend auf **Registrieren** .  

3. Navigieren Sie zu der Anwendung, um Azure Maps die delegierten API-Berechtigungen zuzuweisen. Wählen Sie dann unter **App-Registrierungen** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Suchen Sie unter **Von meiner Organisation verwendete APIs** die Option **Azure Maps** , und wählen Sie sie aus.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen von App-API-Berechtigungen](./media/how-to-manage-authentication/app-permissions.png)

4. Aktivieren Sie das Kontrollkästchen neben **Auf Azure Maps zugreifen** , und wählen Sie dann **Berechtigungen hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Auswählen von App-API-Berechtigungen](./media/how-to-manage-authentication/select-app-permissions.png)

5. `oauth2AllowImplicitFlow`aktivieren. Um diese Einstellung zu aktivieren, legen Sie im Abschnitt **Manifest** Ihrer App-Registrierung `oauth2AllowImplicitFlow` auf `true` fest.

6. Kopieren Sie die Azure AD-App-ID und die Azure AD-Mandanten-ID aus der App-Registrierung, um Sie im Web SDK zu verwenden. Fügen Sie die Azure AD-App-Registrierungsdetails und die `x-ms-client-id` aus dem Azure Map-Konto dem Web SDK hinzu.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Konfigurieren Sie die rollenbasierte Azure-Zugriffssteuerung (Azure RBAC) für Benutzer oder Gruppen. Informationen zum [Aktivieren von Azure RBAC finden Sie in den folgenden Abschnitten](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Single-Page-Webanwendungsszenario:
> [!div class="nextstepaction"]
> [Einzelseitenanwendung](../active-directory/develop/scenario-spa-overview.md)

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)