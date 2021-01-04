---
title: Sichern einer Azure API Management-API mithilfe von Azure Active Directory B2C
description: Erfahren Sie, wie Sie von Azure Active Directory B2C ausgestellte Zugriffstoken zum Sichern eines Azure API Management-API-Endpunkts verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953487"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Sichern einer Azure API Management-API mit Azure AD B2C

Erfahren Sie, wie Sie den Zugriff auf Ihre APIM-API (Azure API Management) auf Clients beschränken, die sich mit Azure Active Directory B2C (Azure AD B2C) authentifiziert haben. Führen Sie die in diesem Artikel beschriebenen Schritte aus, um eine eingehende Richtlinie in APIM zu erstellen und zu testen, die den Zugriff auf nur die Anforderungen einschränkt, die ein gültiges von Azure AD B2C ausgestelltes Zugriffstoken enthalten.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die folgenden Ressourcen, bevor Sie mit den Schritten in diesem Artikel fortfahren können:

* [Azure AD B2C-Mandant](tutorial-create-tenant.md)
* In Ihrem Mandanten [registrierte Anwendung](tutorial-register-applications.md)
* Im Mandanten [erstellte Benutzerabläufe](tutorial-create-user-flows.md)
* Eine in Azure API Management [veröffentlichte API](../api-management/import-and-publish.md)
* [Postman](https://www.getpostman.com/) zum Testen des gesicherten Zugriffs (optional)

## <a name="get-azure-ad-b2c-application-id"></a>Abrufen einer Azure AD B2C-Anwendungs-ID

Wenn Sie eine API in Azure API Management mit Azure AD B2C sichern, benötigen Sie mehrere Werte für die [eingehende Richtlinie](../api-management/api-management-howto-policies.md), die Sie in APIM erstellen. Notieren Sie sich zuerst die Anwendungs-ID einer Anwendung, die Sie zuvor in Ihrem Azure AD B2C-Mandanten registriert haben. Wenn Sie die Anwendung verwenden, die Sie in den Voraussetzungen erstellt haben, verwenden Sie die Anwendungs-ID für *webbapp1*.

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche für **App-Registrierungen** oder unsere alte Benutzeroberfläche für **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Klicken Sie erst auf **App-Registrierungen** und dann auf die Registerkarte **Anwendungen mit Besitzer**.
1. Notieren Sie den Wert in der Spalte **Anwendungs-ID (Client)** für *webapp1* oder eine andere Anwendung, die Sie zuvor erstellt haben.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Klicken Sie unter **Verwalten** auf die Option **Applications (Legacy)** (Anwendungen (Legacy)).
1. Notieren Sie den Wert in der Spalte **ANWENDUNGS-ID** für *webapp1* oder eine andere Anwendung, die Sie zuvor erstellt haben.

* * *

## <a name="get-token-issuer-endpoint"></a>Abrufen des Tokenaussteller-Endpunkts

Rufen Sie als Nächstes die bekannte Konfigurations-URL für einen Ihrer Azure AD B2C-Benutzerflows ab. Sie benötigen außerdem den Tokenaussteller-Endpunkt-URI, der in Azure API Management unterstützt werden soll.

1. Navigieren Sie zu Ihrem Azure AD B2C-Mandanten im [Azure-Portal](https://portal.azure.com).
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie eine vorhandene Richtlinie (etwa *B2C_1_signupsignin1*) und dann **Benutzerflow ausführen** aus.
1. Notieren Sie sich die URL des Hyperlinks, der unter der Überschrift **Benutzerflow ausführen** oben auf der Seite angezeigt wird. Diese URL ist der bekannte OpenID Connect-Ermittlungsendpunkt für den Benutzerflow. Verwenden Sie ihn im nächsten Abschnitt beim Konfigurieren der eingehenden Richtlinie in Azure API Management.

    ![Bekannter URI-Link auf der Seite „Jetzt ausführen“ im Azure-Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Wählen Sie den Hyperlink aus, um zur bekannten OpenID Connect-Konfigurationsseite zu navigieren.
1. Notieren Sie sich den Wert `issuer`, der auf der im Browser geöffneten Seite angezeigt wird, z. B.:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Verwenden Sie diesen Wert im nächsten Abschnitt, wenn Sie Ihre API in Azure API Management konfigurieren.

Sie haben sich nun zwei URLs für die Verwendung im nächsten Abschnitt notiert: die bekannte OpenID Connect-Konfigurationsendpunkt-URL und den Aussteller-URI. Beispiel:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurieren einer eingehenden Richtlinie in Azure API Management

Nun können Sie die eingehende Richtlinie, mit der API-Aufrufe überprüft werden, in Azure API Management hinzufügen. Durch Hinzufügen einer Richtlinie für die [JWT-Überprüfung](../api-management/api-management-access-restriction-policies.md#ValidateJWT), mit der die Zielgruppe und der Aussteller in einem Zugriffstoken überprüft werden, können Sie sicherstellen, dass nur API-Aufrufe mit einem gültigen Token akzeptiert werden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure API Management-Instanz.
1. Klicken Sie auf **APIs**.
1. Wählen Sie die API aus, die Sie mit Azure AD B2C sichern möchten.
1. Klicken Sie auf die Registerkarte **Entwurf**.
1. Wählen Sie unter **Inbound processing** (Eingehende Verarbeitung) **\</\>** aus, um den Richtliniencode-Editor zu öffnen.
1. Platzieren Sie das folgende `<validate-jwt>`-Tag in der `<inbound>`-Richtlinie.

    1. Aktualisieren Sie den `url`-Wert im `<openid-config>`-Element durch die bekannte Konfigurations-URL Ihrer Richtlinie.
    1. Aktualisieren Sie das `<audience>`-Element durch die Anwendungs-ID der Anwendung, die Sie zuvor in Ihrem B2C-Mandanten erstellt haben (z. B. *webapp1*).
    1. Aktualisieren Sie das `<issuer>`-Element durch den zuvor aufgezeichneten Tokenaussteller-Endpunkt.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Überprüfen des sicheren API-Zugriffs

Um sicherzustellen, dass nur authentifizierte Aufrufer auf Ihre API zugreifen können, können Sie Ihre Azure API Management-Konfiguration überprüfen, indem Sie die API mit [Postman](https://www.getpostman.com/) aufrufen.

Zum Aufrufen der API benötigen Sie sowohl ein von Azure AD B2C ausgestelltes Zugriffstoken als auch einen APIM-Abonnementschlüssel.

### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Sie benötigen zunächst ein Token, das von Azure AD B2C zur Verwendung im `Authorization`-Header in Postman ausgestellt wird. Dieses können Sie mithilfe der Funktion **Jetzt ausführen** Ihres Benutzerflows für die Registrierung bzw. Anmeldung, den Sie als eine der Voraussetzungen erstellt haben, abrufen.

1. Navigieren Sie zu Ihrem Azure AD B2C-Mandanten im [Azure-Portal](https://portal.azure.com).
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie eine vorhandene Richtlinie für die Registrierung bzw. Anmeldung aus, wie z. B. *B2C_1_signupsignin1*.
1. Wählen Sie für **Anwendung** die Option *webapp1* aus.
1. Wählen Sie für **Antwort-URL**`https://jwt.ms` aus.
1. Wählen Sie **Benutzerflow ausführen** aus.

    ![Ausführen der Benutzerflowseite für die Registrierung bzw. Anmeldung beim Benutzerflow im Azure-Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Schließen Sie den Anmeldevorgang ab. Sie werden zu `https://jwt.ms` weitergeleitet.
1. Notieren Sie sich das verschlüsselte Token, das im Browser angezeigt wird. Sie verwenden dieses Token für den Autorisierungsheader in Postman.

    ![In jwt.ms angezeigter verschlüsselter Tokenwert](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Abrufen des API-Abonnementschlüssels

Eine Clientanwendung (in diesem Fall Postman), die eine veröffentlichte API aufruft, muss in Ihren HTTP-Anforderungen an die API einen gültigen API Management-Abonnementschlüssel enthalten. So rufen Sie einen Abonnementschlüssel ab, den Sie in Ihre Postman-HTTP-Anforderung einbeziehen können:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure API Management-Dienstinstanz.
1. Wählen Sie **Abonnements**.
1. Wählen Sie die Auslassungspunkte für **Produkt: Unbegrenzt** und anschließend die Option **Geheimnisse anzeigen/ausblenden** aus.
1. Notieren Sie sich den **PRIMÄRSCHLÜSSEL** für das Produkt. Verwenden Sie diesen Schlüssel für den `Ocp-Apim-Subscription-Key`-Header in Ihrer HTTP-Anforderung in Postman.

![Abonnementschlüsselseite mit der Auswahl „Geheimnisse anzeigen/ausblenden“ im Azure-Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testen eines sicheren API-Aufrufs

Nachdem Sie sich das Zugriffstoken und den APIM-Abonnementschlüssel notiert haben, können Sie nun testen, ob Sie den sicheren Zugriff auf die API ordnungsgemäß konfiguriert haben.

1. Erstellen Sie in [Postman](https://www.getpostman.com/) eine neue `GET`-Anforderung. Geben Sie für die Anforderungs-URL den Sprecherliste-Endpunkt der API an, den Sie als eine der Voraussetzungen veröffentlicht haben. Beispiel:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Fügen Sie als Nächstes die folgenden Header hinzu:

    | Schlüssel | Wert |
    | --- | ----- |
    | `Authorization` | Der verschlüsselte Tokenwert, den Sie zuvor notiert haben, mit dem Präfix `Bearer ` (mit einem Leerzeichen nach „Bearer“) |
    | `Ocp-Apim-Subscription-Key` | Der APIM-Abonnementschlüssel, den Sie zuvor notiert haben |

    Die Anforderungs-URL **GET** und **Headers** (Header) sollten in etwa wie folgt angezeigt werden:

    ![Postman-Benutzeroberfläche mit der Anforderungs-URL „GET“ und den Headern](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Wählen Sie in Postman die Schaltfläche **Send** (Senden) aus, um die Anforderung auszuführen. Wenn Sie alles korrekt konfiguriert haben, wird Ihnen eine JSON-Antwort mit einer Sammlung von Konferenzsprechern angezeigt werden (hier abgeschnitten):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testen eines unsicheren API-Aufrufs

Nachdem Sie nun eine erfolgreiche Anforderung durchgeführt haben, testen Sie den Fehlerfall, um sicherzustellen, dass Aufrufe Ihrer API mit einem *ungültigen* Token wie erwartet abgelehnt werden. Eine Möglichkeit zum Durchführen des Tests besteht darin, im Tokenwert einige Zeichen hinzuzufügen oder zu ändern, und anschließend die gleiche `GET`-Anforderung wie zuvor auszuführen.

1. Fügen Sie dem Tokenwert mehrere Zeichen hinzu, um ein ungültiges Token zu simulieren. Fügen Sie zum Tokenwert beispielsweise „INVALID“ (ungültig) hinzu:

    ![Abschnitt „Headers“ (Header) der Postman-Benutzeroberfläche, in dem INVALID zum Token hinzugefügt wurde](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Wählen Sie die Schaltfläche **Send** (Senden) aus, um die Anforderung auszuführen. Bei einem ungültigen Token ist das erwartete Ergebnis der nicht autorisierte Statuscode `401`:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Wenn Ihnen der Statuscode `401` angezeigt wird, haben Sie überprüft, dass nur Aufrufer mit einem gültigen von Azure AD B2C ausgestellten Zugriffstoken erfolgreiche Anforderungen an Ihre Azure API Management-API senden können.

## <a name="support-multiple-applications-and-issuers"></a>Unterstützen mehrerer Anwendungen und Aussteller

Mehrere Anwendungen interagieren in der Regel mit einer einzigen REST-API. Damit Ihre API Tokens für mehrere Apps akzeptieren kann, fügen Sie die entsprechenden App-Kennungen dem Element `<audiences>` in der APIM-Eingangsrichtlinie zu.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Wenn Sie mehrere Tokenaussteller unterstützen möchten, fügen Sie deren Endpunkt-URIs zum `<issuers>`-Element in der eingehenden APIM-Richtlinie hinzu.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrieren zu b2clogin.com

Wenn Sie eine APIM-API haben, die Token überprüft, welche vom `login.microsoftonline.com`-Legacyendpunkt ausgestellt wurden, müssen Sie die API und die Anwendungen, die sie aufrufen, migrieren, damit von [b2clogin.com](b2clogin.md) ausgestellte Token verwendet werden.

Gehen Sie wie folgt vor, um eine gestaffelte Migration durchzuführen:

1. Fügen Sie in Ihrer eingehenden APIM-Richtlinie Unterstützung für Token hinzu, die sowohl von b2clogin.com als auch von login.microsoftonline.com ausgestellt wurden.
1. Aktualisieren Sie Ihre Anwendungen nacheinander, um Token vom b2clogin.com-Endpunkt abzurufen.
1. Sobald alle Anwendungen korrekt Token von b2clogin.com abrufen, entfernen Sie die Unterstützung für von login.microsoftonline.com ausgestellte Token aus der API.

Die folgende eingehende APIM-Beispielrichtlinie zeigt, wie Token akzeptiert werden, die sowohl von b2clogin.com als auch von login.microsoftonline.com ausgestellt wurden. Außerdem unterstützt sie API-Anforderungen von zwei Anwendungen.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure API Management-Richtlinien finden Sie unter [Richtlinien für die API-Verwaltung](../api-management/api-management-policies.md).

Informationen zur Migration von OWIN-basierten Web-APIs und deren Anwendungen zu b2clogin.com finden Sie unter [Migrieren einer OWIN-basierten Web-API zu b2clogin.com](multiple-token-endpoints.md).