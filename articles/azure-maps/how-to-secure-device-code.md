---
title: Sichern eines eingabebeschränkten Geräts mit Azure AD und Azure Maps-REST-APIs
titleSuffix: Azure Maps
description: Konfigurieren einer browserlosen Anwendung, die einmaliges Anmelden bei Azure AD und Aufrufe von Azure Maps-REST-APIs unterstützt.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895629"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Sichern eines eingabebeschränkten Geräts mit Azure AD und Azure Maps-REST-APIs

In diesem Handbuch wird erläutert, wie Sie öffentliche Anwendungen oder Geräte sichern, die geheime Schlüssel nicht sicher speichern oder keine Browsereingaben akzeptieren können. Diese Typen von Anwendungen fallen in die Kategorie IoT bzw. „Internet der Dinge“ (Internet of Things). Einige Beispiele für diese Anwendungen sind unter anderem: Smart TV-Geräte oder Anwendungen, die Sensordaten ausgeben. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Erstellen einer Anwendungsregistrierung in Azure AD

> [!NOTE]
> * **Erforderliche Lektüre:** [Szenario: Desktop-App, die Web-APIs aufruft](../active-directory/develop/scenario-desktop-overview.md)
> * Im folgenden Szenario wird der Gerätecodefluss verwendet, der keinen Webbrowser zum Abrufen eines Tokens umfasst.

Erstellen Sie die gerätebasierte Anwendung in Azure AD, um die Azure AD-Anmeldung zu aktivieren. Dieser Anwendung wird Zugriff auf Azure Maps-REST-APIs gewährt.

1. Wählen Sie im Azure-Portal in der Liste der Azure-Dienste die Optionen **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.  

    > [!div class="mx-imgBorder"]
    > ![App-Registrierung](./media/how-to-manage-authentication/app-registration.png)

2. Geben Sie einen **Namen** eine, wählen Sie **Nur Konten in diesem Organisationsverzeichnis**  als **Unterstützter Kontotyp** aus. Geben Sie in **Umleitungs-URI** den Wert **Öffentlicher Client/nativ (mobil und Desktop)** an, und fügen Sie dann dem Wert `https://login.microsoftonline.com/common/oauth2/nativeclient` hinzu. Weitere Detailinformationen finden Sie unter Azure AD [Desktop-App, die Web-APIs aufruft: App-Registrierung](../active-directory/develop/scenario-desktop-app-registration.md). **Registrieren** Sie dann die Anwendung.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen von App-Registrierungsdetails für Namen und Umleitungs-URI](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Navigieren Sie zu **Authentifizierung** , und aktivieren Sie **Anwendung als öffentlichen Client behandeln** . Hierdurch wird die Gerätecodeauthentifizierung mit Azure AD aktiviert.
    
    > [!div class="mx-imgBorder"]
    > ![Aktivieren der App-Registrierung als öffentlicher Client](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Navigieren Sie zu der Anwendung, um Azure Maps die delegierten API-Berechtigungen zuzuweisen. Wählen Sie dann **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Suchen Sie unter **Von meiner Organisation verwendete APIs** die Option **Azure Maps** , und wählen Sie sie aus.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen von App-API-Berechtigungen](./media/how-to-manage-authentication/app-permissions.png)

5. Aktivieren Sie das Kontrollkästchen neben **Auf Azure Maps zugreifen** , und wählen Sie dann **Berechtigungen hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Auswählen von App-API-Berechtigungen](./media/how-to-manage-authentication/select-app-permissions.png)

6. Konfigurieren Sie die rollenbasierte Azure-Zugriffssteuerung (Azure RBAC) für Benutzer oder Gruppen. Siehe [Gewähren des rollenbasierten Zugriffs für Benutzer in Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Fügen Sie Code zum Abrufen des Tokenflows in die Anwendung hinzu. Informationen zur Implementierung finden Sie unter [Gerätecodeflow](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Verweisen Sie beim Abrufen von Token auf den Bereich `user_impersonation`, der in den vorherigen Schritten ausgewählt wurde.

> [!Tip]
> Verwenden Sie den MSAL (Microsoft Authentication Library) für das Abrufen von Zugriffstoken. Siehe die Empfehlungen unter [Desktop-App, die Web-APIs aufruft: Codekonfiguration](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Verfassen Sie die HTTP-Anforderung mit dem abgerufenen Token aus Azure AD, und senden Sie die Anforderung mit einem gültigen HTTP-Client.

### <a name="sample-request"></a>Beispiel für eine Anforderung
Im Folgenden finden Sie einen Beispielanforderungstext zum Hochladen eines einfachen Geofence, der als Kreisgeometrie mit einem Mittelpunkt und einem Radius dargestellt wird.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Der folgende Beispielanforderungstext ist im GeoJSON-Format:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Beispiel für eine Antwort:

Headers:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Hauptteil:
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nächste Schritte

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)