---
title: 'Erweiterte Verwendung der Authentifizierung und Autorisierung: Azure App Service | Microsoft-Dokumentation'
description: Veranschaulicht die Anpassung der Authentifizierung und Autorisierung in App Service und das Abrufen von Benutzeransprüchen und verschiedenen Token.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b628f88e3d0d644cf5a9471be1d7a766c2b9575b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143940"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Erweiterte Verwendung der Authentifizierung und Autorisierung in Azure App Service

Dieser Artikel zeigt, wie Sie die integrierte [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) anpassen und Identitäten über Ihre Anwendung verwalten. 

Sehen Sie sich eines der folgenden Tutorials an, um sofort loszulegen:

* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service für Linux](containers/tutorial-auth-aad.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](configure-authentication-provider-aad.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung](configure-authentication-provider-facebook.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung](configure-authentication-provider-google.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung](configure-authentication-provider-microsoft.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Verwenden mehrerer Anmeldungsanbieter

Die Portalkonfiguration bietet keine einfache Möglichkeit, Ihren Benutzern mehrere Anmeldungsanbieter (z.B. sowohl Facebook als auch Twitter) bereitzustellen. Allerdings lässt sich diese Funktionalität Ihrer App problemlos hinzufügen. Dazu sind folgende Schritte erforderlich:

Zunächst konfigurieren Sie im Azure-Portal auf der Seite **Authentifizierung/Autorisierung** alle Identitätsanbieter, die Sie aktivieren möchten.

Wählen Sie für **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Anonyme Anforderungen zulassen (keine Aktion)** aus.

Fügen Sie auf der Anmeldeseite, der Navigationsleiste oder an einer anderen Stelle in Ihrer App einen Anmeldelink für alle Anbieter hinzu, die Sie aktiviert haben (`/.auth/login/<provider>`). Beispiel:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Wenn der Benutzer auf einen der Links klickt, wird die entsprechende Anmeldeseite geöffnet, um den Benutzer anzumelden.

Um den Benutzer nach der Anmeldung auf eine benutzerdefinierte URL umzuleiten, verwenden Sie den `post_login_redirect_url`-Abfragezeichenfolgen-Parameter (nicht zu verwechseln mit der Umleitungs-URI in der Konfiguration Ihres Identitätsanbieters). Zur Umleitung des Benutzers auf `/Home/Index` nach der Anmeldung verwenden Sie zum Beispiel den folgenden HTML-Code:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Überprüfen von Token von einem Anbieter

In einer clientgeführten Anmeldung meldet die Anwendung den Benutzer manuell beim Anbieter an und sendet dann das Authentifizierungstoken zur Überprüfung an App Service. (Informationen hierzu finden Sie unter [Authentifizierungsflow](overview-authentication-authorization.md#authentication-flow).) Diese Überprüfung allein gewährt Ihnen noch keinen Zugriff auf die gewünschten App-Ressourcen. Bei erfolgreicher Überprüfung erhalten Sie jedoch ein Sitzungstoken, das Sie für den Zugriff auf App-Ressourcen verwenden können. 

Um das Anbietertoken zu überprüfen, muss die App Service-App zunächst mit dem gewünschten Anbieter konfiguriert werden. Zur Laufzeit, nachdem Sie das Authentifizierungstoken von Ihrem Anbieter abgerufen haben, posten Sie das Token zur Überprüfung unter `/.auth/login/<provider>`. Beispiel: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Das Tokenformat kann je nach Anbieter leicht variieren. Details finden Sie in der folgenden Tabelle:

| Anbieterwert | Im Anforderungstext erforderlich | Kommentare |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Die `expires_in`-Eigenschaft ist optional. <br/>Fordern Sie beim Anfordern des Tokens von Livediensten immer den Bereich `wl.basic` an. |
| `google` | `{"id_token":"<id_token>"}` | Die `authorization_code`-Eigenschaft ist optional. Wenn sie angegeben wird, kann sie optional auch zusammen mit der Eigenschaft `redirect_uri` verwendet werden. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Verwenden Sie ein gültiges [Benutzerzugriffstoken](https://developers.facebook.com/docs/facebook-login/access-tokens) aus Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Wenn das Anbietertoken erfolgreich überprüft wird, gibt die API im Antworttext ein `authenticationToken` zurück. Dies ist Ihr Sitzungstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Sobald Sie dieses Sitzungstoken erhalten haben, können Sie auf geschützte App-Ressourcen zugreifen, indem Sie Ihren HTTP-Anforderungen den Header `X-ZUMO-AUTH` hinzufügen. Beispiel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Abmelden von einer Sitzung

Benutzer können eine Abmeldung initiieren, indem Sie eine `GET`-Anforderung an den Endpunkt `/.auth/logout` der App senden. Die `GET`-Anforderung bewirkt Folgendes:

- Löscht Authentifizierungscookies aus der aktuellen Sitzung
- Löscht die Token des aktuellen Benutzers aus dem Tokenspeicher
- Führt für Azure Active Directory und Google eine serverseitige Abmeldung für den Identitätsanbieter aus

Hier ist ein einfacher Abmeldungslink auf einer Webseite:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Standardmäßig wird bei einer erfolgreichen Abmeldung der Client an die URL `/.auth/logout/done` weitergeleitet. Sie können die Weiterleitungsseite nach der Abmeldung ändern, indem Sie den Abfrageparameter `post_logout_redirect_uri` hinzufügen. Beispiel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Es wird empfohlen, den Wert von `post_logout_redirect_uri` zu [codieren](https://wikipedia.org/wiki/Percent-encoding).

Wenn Sie vollqualifizierte URLs verwenden, muss die URL in derselben Domäne gehostet oder als zulässige externe Weiterleitungs-URL für Ihre App konfiguriert werden. Im folgenden Beispiel erfolgt einer Weiterleitung an die URL `https://myexternalurl.com`, die nicht in derselben Domäne gehostet wird:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Sie müssen den nachstehenden Befehl in der [Azure Cloud Shell](../cloud-shell/quickstart.md) ausführen:

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Beibehalten von URL-Fragmenten

Nachdem sich Benutzer bei Ihrer App anmelden, möchten sie in der Regel zum selben Abschnitt aus derselben Seite weitergeleitet werden, z.B. `/wiki/Main_Page#SectionZ`. Da [URL-Fragmente](https://wikipedia.org/wiki/Fragment_identifier) (etwa `#SectionZ`) jedoch nie an den Server gesendet werden, werden sie standardmäßig nach Abschluss der OAuth-Anmeldung und der Weiterleitung zurück zu Ihrer App nicht beibehalten. Benutzer erhalten dadurch ein suboptimales Erlebnis, wenn sie wieder zum gewünschten Anker navigieren müssen. Diese Einschränkung betrifft alle serverseitigen Authentifizierungslösungen.

Bei der App Service-Authentifizierung können Sie die URL-Fragmente während der OAuth-Anmeldung beibehalten. Legen Sie dazu die App-Einstellung `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` auf `true` fest. Sie können dies im [Azure-Portal](https://portal.azure.com) erledigen oder einfach den folgenden Befehl in der [Azure Cloud Shell](../cloud-shell/quickstart.md) ausführen:

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Zugriff auf Benutzeransprüche

App Service übergibt Benutzeransprüche mithilfe spezieller Header an Ihre Anwendung. Externe Anforderungen sind nicht zum Festlegen dieser Header berechtigt, sie sind also nur vorhanden, wenn sie von App Service festgelegt wurden. Beispiele für solche Header wären etwa:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Anhand dieser Header kann jeder Code unabhängig von Programmiersprache oder Framework die benötigten Informationen ermitteln. Bei ASP.NET 4.6-Apps wird **ClaimsPrincipal** automatisch mit entsprechenden Werten festgelegt.

Ihre Anwendung kann durch Aufrufen von `/.auth/me` auch zusätzliche Details zum authentifizierten Benutzer abrufen. Die Mobile Apps-Server-SDKs enthalten Hilfsmethoden für die Verwendung dieser Daten. Weitere Informationen finden Sie unter [Verwenden des Azure Mobile Apps SDK für Node.js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) sowie unter [Arbeiten mit dem .NET-Back-End-Server-SDK für Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Abrufen von Token in App-Code

Die anbieterspezifischen Token werden aus dem Servercode in den Anforderungsheader eingefügt, sodass Sie problemlos darauf zugreifen können. Die folgende Tabelle zeigt mögliche Namen von Tokenheadern:

| Anbieter | Headernamen |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-Konto | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Senden Sie von Ihrem Clientcode (z.B. einer mobilen App oder JavaScript im Browser) eine HTTP-`GET`-Anforderung an `/.auth/me`. Der zurückgegebene JSON-Code enthält die anbieterspezifischen-Token.

> [!NOTE]
> Zugriffstoken sind für den Zugriff auf Anbieterressourcen vorgesehen, daher sind sie nur vorhanden, wenn Sie den Anbieter mit einem geheimen Clientschlüssel konfigurieren. Informationen zum Abrufen von Aktualisierungstoken finden Sie unter „Zugriffstoken für die Aktualisierung“.

## <a name="refresh-identity-provider-tokens"></a>Aktualisieren von Identitätsanbieter-Token

Wenn das Zugriffstoken Ihres Anbieters (nicht das [Sitzungstoken](#extend-session-token-expiration-grace-period)) abgelaufen ist, müssen Sie den Benutzer erneut authentifizieren, bevor Sie dieses Token erneut verwenden. Sie können den Tokenablauf vermeiden, indem Sie einen `GET`-Aufruf an den `/.auth/refresh`-Endpunkt Ihrer Anwendung durchführen. Bei einem Aufruf aktualisiert App Service automatisch die Zugriffstoken im Tokenspeicher für den authentifizierten Benutzer. Bei nachfolgenden Anforderungen von Token durch Ihren App-Code werden die aktualisierten Token abgerufen. Damit die Tokenaktualisierung funktioniert, muss der Tokenspeicher jedoch [Aktualisierungstoken](https://auth0.com/learn/refresh-tokens/) für Ihren Anbieter enthalten. Die jeweilige Methode zum Abrufen von Aktualisierungstoken ist von den einzelnen Anbietern dokumentiert, die folgende Liste stellt jedoch eine kurze Zusammenfassung dar:

- **Google**: Fügen Sie einen Abfragezeichenfolgen-Parameter vom Typ `access_type=offline` an Ihren `/.auth/login/google`-API-Aufruf an. Bei Verwendung des Mobile Apps SDK können Sie den Parameter einer der `LogicAsync`-Überladungen hinzufügen (siehe [Google-Aktualisierungstoken](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Stellt keine Aktualisierungstoken bereit. Langlebige Token laufen nach 60 ab (siehe [Verlängern von Zugriffsschlüsseln für Seiten](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Zugriffstoken laufen nicht ab (siehe [Häufig gestellte Fragen zu OAuth für Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-Konto**: Wählen Sie beim [Konfigurieren der Authentifizierungseinstellungen für das Microsoft-Konto](configure-authentication-provider-microsoft.md) den Bereich `wl.offline_access` aus.
- **Azure Active Directory:** Führen Sie in [https://resources.azure.com](https://resources.azure.com) folgende Schritte aus:
    1. Wählen Sie am oberen Seitenrand die Option **Lesen/Schreiben** aus.
    2. Navigieren Sie im linken Browser zu **subscriptions** >  ** _\<Name des\_Abonnements_**  > **resourceGroups** >  _ **\<Name\_der\_Ressourcengruppe>** _ > **providers** > **Microsoft.Web** > **sites** >  _ **\<App\_Name>** _ > **config** > **authsettings**. 
    3. Klicken Sie auf **Edit**.
    4. Ändern Sie die folgende Eigenschaft. Ersetzen Sie _\<app\_id>_ mit der ID der Azure Active Directory-Anwendung des Diensts, auf den Sie zugreifen möchten.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicken Sie auf **Put**. 

Sobald Ihr Anbieter konfiguriert ist, können Sie im Tokenspeicher [das Aktualisierungstoken und die Ablaufzeit für das Zugriffstoken suchen](#retrieve-tokens-in-app-code). 

Um das Zugriffstoken jederzeit zu aktualisieren, rufen Sie einfach `/.auth/refresh` in einer beliebigen Sprache auf. Im folgenden Codeausschnitt wird jQuery verwendet, um Ihre Zugriffstoken aus einem JavaScript-Client zu aktualisieren.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Wenn ein Benutzer die Ihrer App gewährten Berechtigungen widerruft, schlägt Ihr Aufruf von `/.auth/me` möglicherweise mit der Antwort `403 Forbidden` fehl. Überprüfen Sie Ihre Anwendungsprotokolle auf Details, um Fehler zu diagnostizieren.

## <a name="extend-session-token-expiration-grace-period"></a>Verlängern der Toleranzperiode für das Sitzungstoken

Die authentifizierte Sitzung läuft nach acht Stunden ab. Nachdem eine authentifizierte Sitzung abgelaufen ist, gilt standardmäßig eine Toleranzperiode von 72 Stunden. Innerhalb dieser Toleranzperiode sind Sie berechtigt, das Sitzungstoken mit App Service zu aktualisieren, ohne den Benutzer erneut zu authentifizieren. Rufen Sie einfach `/.auth/refresh` auf, wenn Ihr Sitzungstoken ungültig wird. Sie müssen den Tokenablauf nicht selbst nachverfolgen. Nach Ablauf der 72-stündigen Toleranzperiode muss sich der Benutzer neu anmelden, um ein gültiges Sitzungstoken zu erhalten.

Wenn 72 Stunden für Sie nicht ausreichend sind, können Sie dieses Ablauffenster erweitern. Die Erweiterung des Ablauffensters über einen langen Zeitraum kann sich erheblich auf die Sicherheit auswirken (z.B. wenn ein Authentifizierungstoken kompromittiert oder gestohlen wird). Daher wird empfohlen, den Standardwert von 72 Stunden beizubehalten oder den Ablaufzeitraum auf den kleinsten Wert festzulegen.

Zum Erweitern des standardmäßigen Ablauffensters führen Sie den folgenden Befehl in [Cloud Shell](../cloud-shell/overview.md) aus.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Die Toleranzperiode gilt nur für die authentifizierte App Service-Sitzung, nicht jedoch für die Token von den Identitätsanbietern. Es gibt keine Toleranzperiode für die abgelaufenen Anbietertoken. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Beschränken der Domäne von Anmeldekonten

Die Anmeldung über mehrere Domänen ist sowohl bei Microsoft-Konten als auch bei Azure Active Directory möglich. Microsoft-Konten lassen beispielsweise _outlook.com_-, _live.com_- und _hotmail.com_-Konten zu. Azure Active Directory lässt eine beliebige Anzahl von benutzerdefinierten Domänen für die Anmeldekonten zu. Dieses Verhalten ist für eine interne App möglicherweise nicht wünschenswert, wenn nicht jeder Benutzer mit einem _outlook.com_-Konto über Zugriff verfügen soll. Gehen Sie folgendermaßen vor, um den Domänennamen der Anmeldekonten zu beschränken.

Navigieren Sie in [https://resources.azure.com](https://resources.azure.com) zu **subscriptions** >  ** _\< Name des\_ Abonnements_**  > **resourceGroups** >  _ **\< Name\_ der\_ Ressourcengruppe>** _ > **providers** > **Microsoft.Web** > **sites** >  _ **\< App\_ Name>** _ > **config** > **authsettings**. 

Klicken Sie auf **Bearbeiten**, ändern Sie die folgende Eigenschaft, und klicken Sie dann auf **Put**. Achten Sie darauf, _\<Domänen\_Name>_ durch die gewünschte Domäne zu ersetzen.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern (Linux)](containers/tutorial-auth-aad.md)
