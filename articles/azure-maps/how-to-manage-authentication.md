---
title: Verwalten der Authentifizierung in Azure Maps | Microsoft-Dokumentation
description: Sie können über das Azure-Portal die Authentifizierung in Azure Maps verwalten.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 617adbcda70799aa07248945bbc27f9d95aa77a3
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952571"
---
# <a name="manage-authentication-in-azure-maps"></a>Verwalten der Authentifizierung in Azure Maps

Nachdem Sie ein Azure Maps-Konto erstellt haben, werden eine Client-ID und Schlüssel erstellt, um entweder die Authentifizierung über Azure Active Directory (Azure AD) oder Shared Key zu unterstützen.

## <a name="view-authentication-details"></a>Anzeigen von Authentifizierungsdetails

Sie können Ihre Authentifizierungsdetails für das Azure-Portal anzeigen. Wechseln Sie zu Ihrem Konto, und klicken Sie im Menü **Einstellungen** auf **Authentifizierung**.

![Authentifizierungsdetails](./media/how-to-manage-authentication/how-to-view-auth.png)

 Weitere Informationen finden Sie unter [Authentifizierung mit Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Einrichten der Azure AD-App-Registrierung

Nachdem Sie ein Azure Maps-Konto erstellt haben, müssen Sie eine Verknüpfung zwischen Ihrem Azure AD-Mandanten und der Azure Maps-Ressource erstellen.

1. Öffnen Sie das Azure AD-Blatt, und erstellen Sie eine App-Registrierung. Geben Sie einen Namen für die Registrierung ein. Geben Sie im Feld **Anmelde-URL** die URL für die Homepage der Web-App/API ein (z. B. https:\//localhost/). Wenn Sie bereits über eine registrierte App verfügen, fahren Sie mit Schritt 2 fort.

    ![App-Registrierung](./media/how-to-manage-authentication/app-registration.png)

    ![App-Registrierungsdetails](./media/how-to-manage-authentication/app-create.png)

2. Navigieren Sie unter **App-Registrierungen** zur Anwendung, und klicken Sie auf **Einstellungen**, um Azure Maps die delegierten API-Berechtigungen zuzuweisen.  Wählen Sie **Erforderliche Berechtigungen** aus, und klicken Sie dann auf **Hinzufügen**. Suchen Sie unter **API auswählen** nach **Azure Maps**, wählen Sie die Option aus, und klicken Sie dann auf **Auswählen**.

    ![App-API-Berechtigungen](./media/how-to-manage-authentication/app-permissions.png)

3. Wählen Sie unter **Berechtigungen auswählen** die Berechtigung **Access Azure Maps** (Auf Azure Maps zugreifen) aus, und klicken Sie dann auf **Auswählen**.

    ![Auswählen von App-API-Berechtigungen](./media/how-to-manage-authentication/select-app-permissions.png)

4. Führen Sie je nach Ihrer Authentifizierungsmethode Schritt a oder b aus.

    1. Wenn Ihre Anwendung die Benutzertokenauthentifizierung mit dem Azure Maps Web SDK nutzt, aktivieren Sie `oauthEnableImplicitFlow`, indem Sie die Option im Abschnitt „Manifest“ der Detailseite Ihrer App-Registrierung auf TRUE festlegen.
    
       ![App-Manifest](./media/how-to-manage-authentication/app-manifest.png)

    2. Wenn Ihre Anwendung Server/Anwendung-Authentifizierung verwendet, wechseln Sie innerhalb der App-Registrierung zum Blatt **Schlüssel**, und erstellen Sie entweder ein Kennwort, oder laden Sie ein öffentliches Schlüsselzertifikat in die App-Registrierung hoch. Wenn Sie ein Kennwort erstellen, nachdem Sie auf **Speichern** geklickt haben, kopieren Sie das Kennwort zur späteren Verwendung und bewahren Sie es sicher auf. Dieses Kennwort benötigen Sie zum Abrufen von Token von Azure AD.

       ![App-Schlüssel](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Gewähren von RBAC in Azure Maps

Nachdem Sie ein Azure Maps-Konto mit Ihrem Azure AD-Mandanten verknüpfen können Sie die Zugriffssteuerung gewähren, indem Sie einem Benutzer oder einer Anwendung Azure Maps-Zugriffssteuerungsrollen zuweisen.

1. Wechseln Sie zur **Zugriffssteuerung (IAM)**, klicken Sie auf **Rollenzuweisungen**, und wählen Sie dann **Rollenzuweisung hinzufügen** aus.

    ![Gewähren von RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** unter **Rolle** die Rolle **Azure Maps Date Reader (Preview)** aus. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus. Wählen Sie unter **Auswählen** den Benutzer oder die Anwendung aus. Wählen Sie **Speichern** aus.

    ![Rollenzuweisung hinzufügen](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Anzeigen verfügbarer Azure Maps-RBAC-Rollen

Öffnen Sie die **Zugriffssteuerung (IAM)**, klicken Sie auf **Rollen**, und suchen Sie dann nach Rollen, die mit **Azure Maps** beginnen, um Rollen für die rollenbasierte Zugriffssteuerung für Azure Maps anzuzeigen. Hierbei handelt es sich um die Rollen, denen Sie den Zugriff gewähren können.

![Anzeigen verfügbarer Rollen](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Anzeigen der rollenbasierten Zugriffssteuerung von Azure Maps

Mit der rollenbasierten Zugriffssteuerung kann die Zugriffssteuerung genau abgestimmt werden.

Öffnen Sie die **Zugriffssteuerung (IAM)**, klicken Sie auf **Rollenzuweisungen**, und filtern Sie dann nach **Azure Maps**, um Benutzer und Apps anzuzeigen, denen die rollenbasierte Zugriffssteuerung für Azure Maps gewährt wurde.

![Benutzer und Apps mit gewährter rollenbasierter Zugriffssteuerung anzeigen](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Anfordern von Token für Azure Maps

Nachdem Sie Ihre App registriert und mit Azure Maps verknüpft haben, können Sie Zugriffstoken anfordern.

* Wenn Ihre Anwendung die Benutzertokenauthentifizierung mit dem Azure Maps Web SDK nutzt, müssen Sie Ihre HTML-Seite mit der Azure Maps-Client-ID und der Azure AD-App-ID konfigurieren.

* Wenn Ihre Anwendung die Server/Anwendung-Authentifizierung nutzt, müssen Sie ein Token vom Azure AD-Anmeldungsendpunkt `https://login.microsoftonline.com` mit der Azure AD-Ressourcen-ID `https://atlas.microsoft.com/`, der Azure Maps-Client-ID, der Azure AD-App-ID und dem Azure AD-App-Registrierungskennwort bzw. -Zertifikat konfigurieren.

Weitere Informationen zum Anfordern von Zugriffstoken aus Azure AD für Benutzer und Dienstprinzipale finden Sie unter [Was ist Authentifizierung?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure AD-Authentifizierung und Azure Maps-Web-SDK finden Sie unter [Verwenden des Azure Maps-Kartensteuerelements](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Erfahren Sie, wie Sie die API-Nutzungsmetriken für Ihr Azure Maps-Konto anzeigen:
> [!div class="nextstepaction"] 
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)