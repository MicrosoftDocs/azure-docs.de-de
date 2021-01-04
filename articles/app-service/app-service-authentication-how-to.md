---
title: Erweiterte Verwendung von AuthN/AuthZ
description: Erfahren Sie, wie Sie das Feature zur Authentifizierung und Autorisierung in App Service für unterschiedliche Szenarien verwenden und Benutzeransprüche und verschiedene Token abrufen.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 85fd7fdba4c62f4837a419af44c83f7e46cb9e39
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601780"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Erweiterte Verwendung der Authentifizierung und Autorisierung in Azure App Service

Dieser Artikel zeigt, wie Sie die integrierte [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) anpassen und Identitäten über Ihre Anwendung verwalten. 

Sehen Sie sich eines der folgenden Tutorials an, um sofort loszulegen:

* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](configure-authentication-provider-aad.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung](configure-authentication-provider-facebook.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung](configure-authentication-provider-google.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung](configure-authentication-provider-microsoft.md)
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung](configure-authentication-provider-twitter.md)
* [Konfigurieren Ihrer App für die Anmeldung über einen OpenID Connect-Anbieter (Vorschau)](configure-authentication-provider-openid-connect.md)
* [Konfigurieren Ihrer App für die Anmeldung mithilfe einer Apple-Anmeldung (Vorschau)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Verwenden mehrerer Anmeldungsanbieter

Die Portalkonfiguration bietet keine einfache Möglichkeit, Ihren Benutzern mehrere Anmeldungsanbieter (z.B. sowohl Facebook als auch Twitter) bereitzustellen. Allerdings lässt sich diese Funktionalität Ihrer App problemlos hinzufügen. Dazu sind folgende Schritte erforderlich:

Zunächst konfigurieren Sie im Azure-Portal auf der Seite **Authentifizierung/Autorisierung** alle Identitätsanbieter, die Sie aktivieren möchten.

Wählen Sie für **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Anonyme Anforderungen zulassen (keine Aktion)** aus.

Fügen Sie auf der Anmeldeseite, der Navigationsleiste oder an einer anderen Stelle in Ihrer App einen Anmeldelink für alle Anbieter hinzu, die Sie aktiviert haben (`/.auth/login/<provider>`). Beispiel:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Wenn der Benutzer auf einen der Links klickt, wird die entsprechende Anmeldeseite geöffnet, um den Benutzer anzumelden.

Um den Benutzer nach der Anmeldung auf eine benutzerdefinierte URL umzuleiten, verwenden Sie den `post_login_redirect_url`-Abfragezeichenfolgen-Parameter (nicht zu verwechseln mit der Umleitungs-URI in der Konfiguration Ihres Identitätsanbieters). Zur Umleitung des Benutzers auf `/Home/Index` nach der Anmeldung verwenden Sie zum Beispiel den folgenden HTML-Code:

```html
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

```html
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

Führen Sie den nachstehenden Befehl in der [Azure Cloud Shell](../cloud-shell/quickstart.md) aus:

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

Anhand dieser Header kann jeder Code unabhängig von Programmiersprache oder Framework die benötigten Informationen ermitteln. Bei ASP.NET 4.6-Apps wird **ClaimsPrincipal** automatisch mit entsprechenden Werten festgelegt. ASP.NET Core hingegen bietet keine Authentifizierungs-Middleware, die mit App Service-Benutzeransprüchen integriert ist. Eine Problemumgehung finden Sie unter [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Wenn der [Tokenspeicher](overview-authentication-authorization.md#token-store) für Ihre App aktiviert ist, können Sie auch zusätzliche Details zum authentifizierten Benutzer abrufen, indem Sie `/.auth/me` aufrufen. Die Mobile Apps-Server-SDKs enthalten Hilfsmethoden für die Verwendung dieser Daten. Weitere Informationen finden Sie unter [Verwenden des Azure Mobile Apps SDK für Node.js](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) sowie unter [Arbeiten mit dem .NET-Back-End-Server-SDK für Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

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

Senden Sie von Ihrem Clientcode (z. B. einer mobilen App oder JavaScript im Browser) eine HTTP-`GET`-Anforderung an `/.auth/me` (der [Tokenspeicher](overview-authentication-authorization.md#token-store) muss aktiviert sein). Der zurückgegebene JSON-Code enthält die anbieterspezifischen-Token.

> [!NOTE]
> Zugriffstoken sind für den Zugriff auf Anbieterressourcen vorgesehen, daher sind sie nur vorhanden, wenn Sie den Anbieter mit einem geheimen Clientschlüssel konfigurieren. Informationen zum Abrufen von Aktualisierungstoken finden Sie unter „Zugriffstoken für die Aktualisierung“.

## <a name="refresh-identity-provider-tokens"></a>Aktualisieren von Identitätsanbieter-Token

Wenn das Zugriffstoken Ihres Anbieters (nicht das [Sitzungstoken](#extend-session-token-expiration-grace-period)) abgelaufen ist, müssen Sie den Benutzer erneut authentifizieren, bevor Sie dieses Token erneut verwenden. Sie können den Tokenablauf vermeiden, indem Sie einen `GET`-Aufruf an den `/.auth/refresh`-Endpunkt Ihrer Anwendung durchführen. Bei einem Aufruf aktualisiert App Service automatisch die Zugriffstoken im [Tokenspeicher](overview-authentication-authorization.md#token-store) für den authentifizierten Benutzer. Bei nachfolgenden Anforderungen von Token durch Ihren App-Code werden die aktualisierten Token abgerufen. Damit die Tokenaktualisierung funktioniert, muss der Tokenspeicher jedoch [Aktualisierungstoken](https://auth0.com/learn/refresh-tokens/) für Ihren Anbieter enthalten. Die jeweilige Methode zum Abrufen von Aktualisierungstoken ist von den einzelnen Anbietern dokumentiert, die folgende Liste stellt jedoch eine kurze Zusammenfassung dar:

- **Google**: Fügen Sie einen Abfragezeichenfolgen-Parameter vom Typ `access_type=offline` an Ihren `/.auth/login/google`-API-Aufruf an. Bei Verwendung des Mobile Apps SDK können Sie den Parameter einer der `LogicAsync`-Überladungen hinzufügen (siehe [Google-Aktualisierungstoken](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Stellt keine Aktualisierungstoken bereit. Langlebige Token laufen nach 60 ab (siehe [Verlängern von Zugriffsschlüsseln für Seiten](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Zugriffstoken laufen nicht ab (siehe [Häufig gestellte Fragen zu OAuth für Twitter](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft-Konto**: Wählen Sie beim [Konfigurieren der Authentifizierungseinstellungen für das Microsoft-Konto](configure-authentication-provider-microsoft.md) den Bereich `wl.offline_access` aus.
- **Azure Active Directory:** Führen Sie in [https://resources.azure.com](https://resources.azure.com) folgende Schritte aus:
    1. Wählen Sie am oberen Seitenrand die Option **Lesen/Schreiben** aus.
    2. Navigieren Sie im linken Browser zu **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettings**. 
    3. Klicken Sie auf **Bearbeiten**.
    4. Ändern Sie die folgende Eigenschaft. Ersetzen Sie _\<app\_id>_ durch die ID der Azure Active Directory-Anwendung des Diensts, auf den Sie zugreifen möchten.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicken Sie auf **Put**. 

Sobald Ihr Anbieter konfiguriert ist, können Sie im Tokenspeicher [das Aktualisierungstoken und die Ablaufzeit für das Zugriffstoken suchen](#retrieve-tokens-in-app-code). 

Um das Zugriffstoken jederzeit zu aktualisieren, rufen Sie einfach `/.auth/refresh` in einer beliebigen Sprache auf. Im folgenden Codeausschnitt wird jQuery verwendet, um Ihre Zugriffstoken aus einem JavaScript-Client zu aktualisieren.

```javascript
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

Die Anmeldung über mehrere Domänen ist sowohl bei Microsoft-Konten als auch bei Azure Active Directory möglich. Microsoft-Konten lassen beispielsweise _outlook.com_-, _live.com_- und _hotmail.com_-Konten zu. Azure AD lässt eine beliebige Anzahl von benutzerdefinierten Domänen für die Anmeldekonten zu. Möglicherweise möchten Sie aber, dass Ihre Benutzer möglichst schnell direkt zu Ihrer eigenen Azure AD-Anmeldeseite (etwa `contoso.com`) gelangen. Gehen Sie folgendermaßen vor, um den Domänennamen der Anmeldekonten vorzuschlagen.

Navigieren Sie in [https://resources.azure.com](https://resources.azure.com) zu **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings**. 

Klicken Sie auf **Bearbeiten**, ändern Sie die folgende Eigenschaft, und klicken Sie dann auf **Put**. Achten Sie darauf, _\<domain\_name>_ durch die gewünschte Domäne zu ersetzen.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Diese Einstellung fügt den `domain_hint`-Abfragezeichenfolgen-Parameter an die Umleitungs-URL der Anmeldung an. 

> [!IMPORTANT]
> Der Client kann den `domain_hint`-Parameter nach dem Empfang der Umleitungs-URL entfernen und sich dann mit einer anderen Domäne anmelden. Diese Funktion ist zwar komfortabel, unter Sicherheitsaspekten aber bedenklich.
>

## <a name="authorize-or-deny-users"></a>Autorisieren oder Ablehnen von Benutzern

Während App Service den einfachsten Autorisierungsfall erledigt (also nicht authentifizierte Anforderungen ablehnt), erfordert Ihre App möglicherweise ein differenzierteres Autorisierungsverhalten, beispielsweise Einschränken des Zugriffs, sodass nur eine bestimmte Benutzergruppe Zugriff hat. Für bestimmte Fälle müssen Sie benutzerdefinierten Anwendungscode schreiben, um den Zugriff für einen angemeldeten Benutzer zuzulassen oder abzulehnen. In anderen Fällen kann App Service oder Ihr Identitätsanbieter möglicherweise unterstützen, ohne dass Codeänderungen erforderlich sind.

- [Serverebene](#server-level-windows-apps-only)
- [Identitätsanbieterebene](#identity-provider-level)
- [Anwendungsebene](#application-level)

### <a name="server-level-windows-apps-only"></a>Serverebene (nur Windows-Apps)

Für jede Windows-App können Sie das Autorisierungsverhalten des IIS-Webservers definieren, indem Sie die Datei *Web.config* bearbeiten. Linux-Apps verwenden IIS nicht und können nicht über *Web.config* konfiguriert werden.

1. Navigieren Sie zu `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. Navigieren Sie im Browser-Explorer Ihrer App Service-Dateien zu *site/wwwroot*. Wenn keine *Web.config*-Datei vorhanden ist, erstellen Sie diese, indem Sie **+**  > **Neue Datei** auswählen. 

1. Wählen Sie das Freihandwerkzeug für *Web.config.* aus, um die Datei zu bearbeiten. Fügen Sie den folgenden Konfigurationscode hinzu, und klicken Sie auf **Speichern**. Wenn *Web.config* bereits vorhanden ist, fügen Sie einfach das `<authorization>`-Element mit allen zugehörigen Werten hinzu. Fügen Sie die Konten, die Sie zulassen möchten, im `<allow>`-Element hinzu.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Identitätsanbieterebene

Der Identitätsanbieter kann eine bestimmte fertige Autorisierung bereitstellen. Beispiel:

- Für [Azure App Service](configure-authentication-provider-aad.md) können Sie das [Verwalten von Zugriff auf Unternehmensebene](../active-directory/manage-apps/what-is-access-management.md) direkt in Azure AD vornehmen. Anweisungen hierzu finden Sie unter [Aufheben des Zugriffs eines Benutzers auf eine Anwendung](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Für [Google](configure-authentication-provider-google.md) können Google-API-Projekte, die zu einer [Organisation](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) gehören, so konfiguriert werden, dass sie Zugriff nur für Benutzer aus Ihrer Organisation zulassen (weitere Informationen finden Sie auf der [Unterstützungsseite **Setting up OAuth 2.0** von Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Anwendungsschicht

Wenn keine der anderen Ebenen die erforderliche Autorisierung bereitstellt, oder wenn Ihre Plattform oder Ihr Identitätsanbieter nicht unterstützt wird, müssen Sie benutzerdefinierten Code schreiben, um Benutzer anhand der [Benutzeransprüche](#access-user-claims) zu autorisieren.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurieren mithilfe einer Datei (Vorschau)

Ihre Authentifizierungseinstellungen können optional über eine Datei konfiguriert werden, die in Ihrer Bereitstellung enthalten ist. Dies ist möglicherweise für bestimmte Vorschaufunktionen der Authentifizierung bzw. Autorisierung in App Service erforderlich.

> [!IMPORTANT]
> Denken Sie daran, dass die Nutzlast Ihrer App – und damit auch diese Datei – möglicherweise zwischen Umgebungen verschoben wird, z. B. bei [Slots](./deploy-staging-slots.md). Es ist sehr wahrscheinlich, dass an jeden Slot eine andere App-Registrierung angeheftet werden muss. In diesen Fällen sollten Sie weiterhin die Standardkonfigurationsmethode anstelle der Konfigurationsdatei verwenden.

### <a name="enabling-file-based-configuration"></a>Aktivieren der dateibasierten Konfiguration

> [!CAUTION]
> Während der Vorschau deaktiviert die Aktivierung der dateibasierten Konfiguration die Verwaltung des App Service-Authentifizierungs-/Autorisierungsfeatures für Ihre Anwendung durch einige Clients wie das Azure-Portal, die Azure CLI und Azure PowerShell.

1. Erstellen Sie im Stammverzeichnis Ihres Projekts (bereitgestellt in D:\home\site\wwwroot in Ihrer Web-App bzw. Funktions-App) eine neue JSON-Datei für Ihre Konfiguration. Geben Sie die gewünschten Konfigurationsdetails entsprechend der [Referenz zur dateibasierten Konfiguration](#configuration-file-reference) an. Wenn Sie eine vorhandene Azure Resource Manager-Konfiguration ändern, stellen Sie sicher, dass Sie die in der `authsettings`-Sammlung aufgezeichneten Eigenschaften in die Konfigurationsdatei übersetzen.

2. Ändern Sie die vorhandene Konfiguration, die in den [Azure Resource Manager](../azure-resource-manager/management/overview.md)-APIs unter `Microsoft.Web/sites/<siteName>/config/authsettings` aufgezeichnet ist. Hierfür können Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) oder ein Tool wie den [Azure-Ressourcen-Explorer](https://resources.azure.com/) verwenden. In der authsettings-Sammlung müssen Sie drei Eigenschaften festlegen (und möglicherweise andere Eigenschaften entfernen):

    1.  Legen Sie `enabled` auf TRUE fest.
    2.  Legen Sie `isAuthFromFile` auf TRUE fest.
    3.  Legen Sie `authFilePath` auf den Namen der Zertifikatdatei fest (z. B. „auth.json“).

> [!NOTE]
> Das Format für `authFilePath` variiert je nach Plattform. Unter Windows werden sowohl relative als auch absolute Pfade unterstützt. Relative Pfade werden empfohlen. Unter Linux werden derzeit nur absolute Pfade unterstützt. Daher sollte der Wert der Einstellung „/home/site/wwwroot/auth.json“ oder ähnlich lauten.

Nachdem Sie dieses Konfigurationsupdate vorgenommen haben, wird der Inhalt der Datei verwendet, um das Verhalten der App Service-Authentifizierung bzw. -Autorisierung für diesen Standort zu definieren. Wenn Sie zur Azure Resource Manager-Konfiguration zurückkehren möchten, legen Sie `isAuthFromFile` wieder auf FALSE fest.

### <a name="configuration-file-reference"></a>Verweis von der Konfigurationsdatei

Alle Geheimnisse, auf die von Ihrer Konfigurationsdatei verwiesen wird, müssen als [Anwendungseinstellungen](./configure-common.md#configure-app-settings) gespeichert werden. Sie können die Einstellungen ganz nach Wunsch benennen. Stellen Sie lediglich sicher, dass die Verweise von der Konfigurationsdatei dieselben Schlüssel verwenden.

Im Folgenden finden Sie mögliche Konfigurationsoptionen in der Datei:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Anheften Ihrer App an eine bestimmte Authentifizierungsruntimeversion

Wenn Sie die Authentifizierung/Autorisierung aktivieren, wird die Plattformmiddleware in Ihre HTTP-Anforderungspipeline eingefügt, wie in der [Featureübersicht](overview-authentication-authorization.md#how-it-works) beschrieben. Diese Plattformmiddleware wird im Rahmen der routinemäßigen Plattformupdates in regelmäßigen Abständen mit neuen Features und Verbesserungen aktualisiert. Standardmäßig wird Ihre Web- oder Funktions-App auf der neuesten Version dieser Plattformmiddleware ausgeführt. Diese automatischen Updates sind immer abwärtskompatibel. In dem seltenen Fall, dass dieses automatische Update ein Runtimeproblem bei Ihrer Web- oder Funktions-App verursacht, können Sie jedoch vorübergehend ein Rollback zur vorherigen Middlewareversion ausführen. In diesem Artikel wird erläutert, wie Sie eine App temporär einer bestimmten Version der Authentifizierungsmiddleware anheften.

### <a name="automatic-and-manual-version-updates"></a>Automatische und manuelle Versionsupdates 

Sie können Ihre App einer bestimmten Version der Plattformmiddleware anheften, indem Sie eine `runtimeVersion`-Einstellung für die App vornehmen. Ihre App wird immer auf der neuesten Version ausgeführt, es sei denn, Sie möchten sie explizit einer bestimmten Version anheften. Es werden mehrere Versionen gleichzeitig unterstützt. Wenn Sie sie einer ungültigen Version anheften, die nicht mehr unterstützt wird, verwendet Ihre App stattdessen die neueste Version. Um immer die neueste Version auszuführen, legen Sie `runtimeVersion` auf ~1 fest. 

### <a name="view-and-update-the-current-runtime-version"></a>Anzeigen und Aktualisieren der aktuellen Runtimeversion

Sie können die von der App verwendete Runtimeversion ändern. Die neue Runtimeversion sollte nach dem Neustart der App wirksam werden. 

#### <a name="view-the-current-runtime-version"></a>Anzeigen der aktuellen Runtimeversion

Sie können die aktuelle Version der Plattformauthentifizierungs-Middleware entweder mithilfe der Azure CLI oder über einen der HTTP-Endpunkte der integrierten Version in ihrer App anzeigen.

##### <a name="from-the-azure-cli"></a>Über die Azure CLI

Zeigen Sie mithilfe der Azure CLI die aktuelle Middlewareversion mit dem Befehl [az webapp auth show](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-show) an.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Ersetzen Sie in diesem Code `<my_app_name>` durch den Namen der App. Ersetzen Sie außerdem `<my_resource_group>` durch den Namen der Ressourcengruppe für Ihre App.

Das Feld `runtimeVersion` wird in der CLI-Ausgabe angezeigt. Es ähnelt der folgenden Beispielausgabe, die zur besseren Lesbarkeit beschnitten wurde: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Vom Versionsendpunkt

Sie können auch über den /.auth/version-Endpunkt einer App die aktuelle Middlewareversion anzeigen, auf der die App ausgeführt wird. Es ähnelt der folgenden Beispielausgabe:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aktualisieren der aktuellen Runtimeversion

Mithilfe der Azure CLI können Sie die `runtimeVersion`-Einstellung in der App mit dem Befehl [az webapp auth update](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-update) aktualisieren.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Ersetzen Sie `<my_app_name>` durch den Namen Ihrer App. Ersetzen Sie außerdem `<my_resource_group>` durch den Namen der Ressourcengruppe für Ihre App. Ersetzen Sie außerdem `<version>` durch eine gültige Version der Runtime 1.x oder durch `~1` für die aktuelle Version. Die Anmerkungen zu den verschiedenen Runtimeversionen finden Sie [hier] (https://github.com/Azure/app-service-announcements), um die Version zu ermitteln, der die App angeheftet werden soll.

Sie können diesen Befehl an der [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, indem Sie im vorangehenden Codebeispiel **Ausprobieren** auswählen. Sie können auch die [Azure-Befehlszeilenschnittstelle lokal](/cli/azure/install-azure-cli) zum Ausführen dieses Befehls verwenden, nachdem Sie sich mit [az login](/cli/azure/reference-index#az-login) angemeldet haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
