---
title: Authentifizierung und Autorisierung
description: Erfahren Sie etwas über die integrierte Authentifizierungs- und Autorisierungsunterstützung in Azure App Service und Azure Functions sowie darüber, wie sie zum Schutz Ihrer App vor unbefugtem Zugriff beitragen kann.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 4f1aceed7e6a41a8ad4f3a062f23a796e31e34ad
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601678"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Authentifizierung und Autorisierung in Azure App Service und Azure Functions

Azure App Service bietet integrierte Authentifizierungs- und Autorisierungsunterstützung, sodass Sie für das Anmelden von Benutzern und den Zugriff auf Daten nur wenig oder keinen Code in Ihrer Web-App, RESTful-API und dem mobilen Back-End sowie in [Azure Functions](../azure-functions/functions-overview.md) schreiben müssen. In diesem Artikel wird beschrieben, wie App Service zur Vereinfachung der Authentifizierung und Autorisierung für Ihre App beiträgt.

Eine sichere Authentifizierung und Autorisierung erfordert umfassende Sicherheitskenntnisse, u.a. zu Verbund, Verschlüsselung, Verwaltung von [JSON-Webtoken (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) und [Gewährungstypen](https://oauth.net/2/grant-types/). App Service bietet diese Hilfsprogramme, damit Sie mehr Zeit und Energie für das Bereitstellen von geschäftlichem Nutzen für Ihre Kunden aufwenden können.

> [!IMPORTANT]
> Die Nutzung dieser Funktion für die Authentifizierung und Autorisierung ist nicht verpflichtend. Sie können die gebündelten Sicherheitsfeatures im Webframework Ihrer Wahl verwenden oder eigene Hilfsprogramme schreiben. Beachten Sie jedoch, dass [Chrome 80 Breaking Changes an der SameSite-Implementierung für Cookies vornimmt](https://www.chromestatus.com/feature/5088147346030592), die voraussichtlich im März 2020 veröffentlicht werden, und die benutzerdefinierte Remoteauthentifizierung sowie andere Szenarien mit websiteübergreifender Cookiebereitstellung nach der Aktualisierung von Chrome-Clientbrowsern möglicherweise nicht mehr funktionieren. Die Problemumgehung ist komplex, da unterschiedliche SameSite-Verhaltensweisen für verschiedene Browser unterstützt werden müssen. 
>
> Die von App Service gehosteten ASP.NET Core-Versionen ab 2.1 wurden bereits für diesen Breaking Change gepatcht und behandeln Chrome 80 sowie ältere Browser entsprechend. Im Januar 2020 wurde der gleiche Patch für ASP.NET Framework 4.7.2 außerdem in den App Service-Instanzen bereitgestellt. Weitere Informationen finden Sie unter [Azure App Service: Update für SameSite-Cookies](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

> [!NOTE]
> Das Authentifizierungs-/Autorisierungsfeature wird manchmal auch als „Einfache Autorisierung“ bezeichnet.

> [!NOTE]
> Durch Aktivieren dieser Funktion werden **alle** nicht sicheren HTTP-Anforderungen an Ihre Anwendung automatisch an HTTPS umgeleitet, unabhängig von der App Service-Konfigurationseinstellung zum [Erzwingen von HTTPS](configure-ssl-bindings.md#enforce-https). Bei Bedarf können Sie dies über die Einstellung `requireHttps` in der [Konfigurationsdatei für die Authentifizierungseinstellungen](app-service-authentication-how-to.md#configuration-file-reference) deaktivieren, Sie müssen dann jedoch sicherstellen, dass niemals Sicherheitstoken über unsichere HTTP-Verbindungen übertragen werden.

Spezielle Informationen zu nativen mobilen Apps finden Sie unter [Authentifizierung und Autorisierung in Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-auth).

## <a name="how-it-works"></a>Funktionsweise

### <a name="on-windows"></a>Unter Windows

Das Modul für Authentifizierung und Autorisierung wird in der gleichen Sandbox wie Ihr Anwendungscode ausgeführt. Wenn es aktiviert ist, wird es von allen eingehenden HTTP-Anforderungen durchlaufen, bevor diese von Ihrem Anwendungscode behandelt werden.

![Ein Architekturdiagramm, das zeigt, wie Anforderungen durch einen Prozess in der Website-Sandbox abgefangen werden, der mit Identitätsanbietern interagiert, bevor Datenverkehr an die bereitgestellte Website zugelassen wird](media/app-service-authentication-overview/architecture.png)

Dieses Modul erledigt einige Dinge für Ihre App:

- Authentifizierung von Benutzern mit dem angegebenen Anbieter
- Überprüfung, Speicherung und Aktualisierung von Token
- Verwaltung der authentifizierten Sitzung
- Einfügen von Identitätsinformationen in Anforderungsheader

Das Modul wird getrennt vom Anwendungscode ausgeführt und mithilfe von App-Einstellungen konfiguriert. Weder SDKs noch bestimmte Sprachen oder Änderungen am Anwendungscode sind erforderlich. 

### <a name="on-containers"></a>Informationen zu Containern

Das Modul für Authentifizierung und Autorisierung wird in einem separaten Container isoliert von Ihrem Anwendungscode ausgeführt. Über das sogenannte [Botschaftermuster](/azure/architecture/patterns/ambassador) interagiert es mit dem eingehenden Datenverkehr, um ähnliche Funktionen wie unter Windows auszuführen. Da es nicht in einem Prozess ausgeführt wird, ist keine direkte Integration in bestimmte Sprachframeworks möglich. Die relevanten Informationen für Ihre App werden jedoch wie unten erläutert mithilfe von Anforderungsheadern weitergeleitet.

### <a name="userapplication-claims"></a>Benutzer-/Anwendungsansprüche

App Service stellt Ihrem Code für alle Sprachenframeworks die Ansprüche im eingehenden Token (ob dieses von einem authentifizierten Endbenutzer oder einer Clientanwendung stammt) zur Verfügung, indem sie in die Anforderungsheader eingefügt werden. Für ASP.NET 4.6-Apps füllt App Service die [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)-Eigenschaft mit Ansprüchen des authentifizierten Benutzers, sodass Sie dem standardmäßigen .NET Codemuster einschließlich des `[Authorize]`-Attributs folgen können. Auf ähnliche Weise füllt App Service die `_SERVER['REMOTE_USER']`-Variable für PHP-Apps. Für Java-Apps sind die Ansprüche [über das Tomcat-Servlet zugänglich](configure-language-java.md#authenticate-users-easy-auth).

Für [Azure Functions](../azure-functions/functions-overview.md) wird `ClaimsPrincipal.Current` nicht für .NET Code aufgefüllt, Sie können die Benutzeransprüche jedoch weiterhin in den Anforderungsheadern finden oder das `ClaimsPrincipal`-Objekt aus dem Anforderungskontext oder selbst durch einen Bindungsparameter abrufen. Weitere Informationen finden Sie unter [Arbeiten mit Clientidentitäten](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

Weitere Informationen finden Sie unter [Zugriff auf Benutzeransprüche](app-service-authentication-how-to.md#access-user-claims).

> [!NOTE]
> Zu diesem Zeitpunkt unterstützt ASP.NET Core aktuell nicht das Auffüllen des aktuellen Benutzers mit der Authentifizierungs-/Autorisierungsfunktion. Allerdings gibt es [Open-Source-Middlewarekomponenten von Drittanbietern](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth), die diese Lücke füllen.
>

### <a name="token-store"></a>Tokenspeicher

App Service bietet einen integrierten Tokenspeicher. Dabei handelt es sich um ein Repository mit Token, die den Benutzern Ihrer Web-Apps, APIs oder nativen mobilen Apps zugeordnet sind. Wenn Sie die Authentifizierung für jeden Anbieter aktivieren, ist dieser Tokenspeicher sofort für Ihre App verfügbar. Wenn Ihr Anwendungscode im Auftrag des Benutzers auf Daten dieser Anbieter zugreifen muss, z.B um: 

- Beiträge in der Facebook-Chronik des authentifizierten Benutzers zu veröffentlichen
- Lesen der Unternehmensdaten des Benutzers mit der Microsoft Graph-API

In der Regel müssen Sie Code schreiben, um diese Token in Ihrer Anwendung zu erfassen, speichern und aktualisieren. Mit dem Tokenspeicher [rufen Sie die Token bei Bedarf einfach ab](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) und [weisen App Service an, sie zu aktualisieren](app-service-authentication-how-to.md#refresh-identity-provider-tokens), wenn sie ungültig werden. 

Die ID-, Zugriffs- und Aktualisierungstoken werden für die authentifizierte Sitzung zwischengespeichert und sind nur für den zugehörigen Benutzer zugänglich.  

Wenn Sie in Ihrer App nicht mit Token arbeiten müssen, können Sie auf der Seite **Authentifizierung/Autorisierung** Ihrer App den Tokenspeicher deaktivieren.

### <a name="logging-and-tracing"></a>Protokollierung und Nachverfolgung

Wenn Sie die [Anwendungsprotokollierung](troubleshoot-diagnostic-logs.md) aktivieren, werden Ablaufverfolgungen für Authentifizierung und Autorisierung direkt in den Protokolldateien angezeigt. Wenn ein unerwarteter Authentifizierungsfehler angezeigt wird, können Sie alle Details dazu problemlos in den vorhandenen Anwendungsprotokollen finden. Wenn Sie die [Ablaufverfolgung für Anforderungsfehler](troubleshoot-diagnostic-logs.md) aktivieren, sehen Sie genau, welche Rolle das Modul für Authentifizierung und Autorisierung möglicherweise bei einer fehlgeschlagenen Anforderung gespielt hat. Suchen Sie in den Ablaufverfolgungsprotokollen nach Verweisen auf ein Modul namens `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Identitätsanbieter

App Service nutzt die [Verbundidentität](https://en.wikipedia.org/wiki/Federated_identity). Dabei werden die Benutzeridentitäten und der Authentifizierungsablauf von einem externen Identitätsanbieter für Sie verwaltet. Fünf Identitätsanbieter sind standardmäßig verfügbar: 

| Anbieter | Anmeldungsendpunkt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-Konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |
| Jeder [OpenID Connect](https://openid.net/connect/)-Anbieter (Vorschau) | `/.auth/login/<providerName>` |

Wenn Sie die Authentifizierung und Autorisierung mit einem dieser Anbieter aktivieren, ist der entsprechende Anmeldungsendpunkt für die Benutzerauthentifizierung und die Überprüfung von Authentifizierungstoken vom Anbieter verfügbar. Sie können Ihren Benutzern problemlos eine beliebige Anzahl von diesen Anmeldeoptionen bereitstellen.

Zur Integration in andere Identitätsanbieter oder eine benutzerdefinierte Authentifizierungslösung ist ein [Pfad für die Legacyerweiterbarkeit][custom-auth] vorhanden, dieser wird aber nicht empfohlen. Nutzen Sie stattdessen die OpenID Connect-Unterstützung.

## <a name="authentication-flow"></a>Authentifizierungsfluss

Der Authentifizierungsablauf gilt für alle Anbieter, unterscheidet sich jedoch abhängig davon, ob die Anmeldung mit dem SDK des Anbieters erfolgen soll:

- Ohne Anbieter-SDK: Die Anwendung delegiert die Verbundanmeldung an App Service. Dies ist normalerweise bei Browser-Apps der Fall, die dem Benutzer die Anmeldeseite des Anbieters anzeigen können. Der Servercode verwaltet den Anmeldevorgang, daher wird er auch als _servergesteuerter Datenfluss_ oder _Serverfluss_ bezeichnet. Dieser Fall gilt für Browser-Apps. Es gilt auch für native Apps, die Benutzer mithilfe des Client SDK von Mobile Apps anmelden, da das SDK eine Webansicht zum Anmelden von Benutzern mit der App Service-Authentifizierung öffnet. 
- Mit Anbieter-SDK: Die Anwendung meldet den Benutzer manuell beim Anbieter an und sendet dann das Authentifizierungstoken zur Überprüfung an App Service. Dies ist normalerweise bei Apps ohne Browser der Fall, die dem Benutzer die Anmeldeseite des Anbieters nicht anzeigen können. Der Anwendungscode verwaltet den Anmeldevorgang, daher wird er auch als _clientgesteuerter Datenfluss_ oder _Clientfluss_ bezeichnet. Dieser Fall gilt für REST-APIs [Azure Functions](../azure-functions/functions-overview.md) und JavaScript-Browserclients sowie Browser-Apps, die eine höhere Flexibilität beim Anmeldevorgang erfordern. Er gilt auch für native mobile Apps, die Benutzer mithilfe des Anbieter-SDKs anmelden.

> [!NOTE]
> Bei Aufrufen aus einer vertrauenswürdigen Browser-App in App Service an eine weitere REST-API in App Service oder [Azure Functions](../azure-functions/functions-overview.md) kann über den servergesteuerten Datenfluss authentifiziert werden. Weitere Informationen finden Sie unter [Anpassen der Authentifizierung und Autorisierung in App Service](app-service-authentication-how-to.md).
>

Die folgende Tabelle zeigt die Schritte des Authentifizierungsablaufs.

| Schritt | Ohne Anbieter-SDK | Mit Anbieter-SDK |
| - | - | - |
| 1. Anmeldung des Benutzers | Client wird zu `/.auth/login/<provider>` umgeleitet. | Clientcode meldet Benutzer direkt mit dem Anbieter-SDK an und erhält ein Authentifizierungstoken. Informationen finden Sie in der Dokumentation des Anbieters. |
| 2. Nachauthentifizierung | Anbieter leitet Client zu `/.auth/login/<provider>/callback` um. | Clientcode [sendet Token des Anbieters](app-service-authentication-how-to.md#validate-tokens-from-providers) zur Überprüfung an `/.auth/login/<provider>`. |
| 3. Einrichten der authentifizierten Sitzung | App Service fügt der Antwort ein authentifiziertes Cookie hinzu. | App Service gibt das eigene Authentifizierungstoken an den Clientcode zurück. |
| 4. Bereitstellen von authentifiziertem Inhalt | Client schließt Authentifizierungscookie in nachfolgenden Anforderungen (die automatisch vom Browser verarbeitet werden) ein. | Clientcode stellt Authentifizierungstoken im `X-ZUMO-AUTH`-Header (der automatisch von Mobile Apps-Client-SDKs verarbeitet wird) bereit. |

Für Clientbrowser kann App Service alle nicht authentifizierten Benutzer automatisch an `/.auth/login/<provider>` weiterleiten. Sie können Benutzern auch einen oder mehrere `/.auth/login/<provider>`-Links zur Anmeldung bei Ihrer App mit dem Anbieter ihrer Wahl bereitstellen.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Autorisierungsverhalten

Im [Azure-Portal](https://portal.azure.com) können Sie die App Service-Autorisierung mit einer Reihe von Verhaltensweisen konfigurieren, wenn eine eingehende Anforderung nicht authentifiziert ist.

![Screenshot des Dropdownmenüs „Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist“](media/app-service-authentication-overview/authorization-flow.png)

Die folgenden Überschriften beschreiben die Optionen.

### <a name="allow-anonymous-requests-no-action"></a>Anonyme Anforderungen zulassen (keine Aktion)

Diese Option verweist die Autorisierung von nicht authentifiziertem Datenverkehr an Ihren Anwendungscode. Für authentifizierte Anforderungen übergibt App Service auch Authentifizierungsinformationen in den HTTP-Headern. 

Diese Option bietet mehr Flexibilität bei der Verarbeitung anonymer Anforderungen. Beispielsweise können Sie für Ihre Benutzer [mehrere Anmeldungsanbieter bereitstellen](app-service-authentication-how-to.md#use-multiple-sign-in-providers). Sie müssen jedoch Code schreiben. 

### <a name="allow-only-authenticated-requests"></a>Nur authentifizierte Anforderungen zulassen

Die Option lautet **Mit \<provider> anmelden**. App Service leitet alle anonymen Anforderungen an `/.auth/login/<provider>` für den ausgewählten Anbieter um. Wenn die anonyme Anforderung von einer nativen mobilen App stammt, wird `HTTP 401 Unauthorized` als Antwort zurückgegeben.

Mit dieser Option müssen Sie in Ihrer App keinen Authentifizierungscode schreiben. Eine genauere Autorisierung, z.B. rollenspezifische Autorisierung, kann durch das Untersuchen der Ansprüche des Benutzers durchgeführt werden (siehe [Zugriff auf Benutzeransprüche](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Das Einschränken des Zugriffs auf diese Weise gilt für alle Aufrufe Ihrer App, was für Apps, die eine öffentlich verfügbare Startseite wünschen, eventuell nicht wünschenswert ist, wie bei vielen Single-Page-Anwendungen.

> [!NOTE]
> Standardmäßig kann jeder Benutzer in Ihrem Azure AD-Mandanten ein Token für Ihre Anwendung aus Azure AD anfordern. Sie können die [Anwendung in Azure AD konfigurieren](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md), wenn Sie den Zugriff auf die Anwendung auf eine bestimmte Gruppe von Benutzern beschränken möchten.

## <a name="more-resources"></a>Weitere Ressourcen

* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service (Windows)](tutorial-auth-aad.md)  
* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service für Linux](./tutorial-auth-aad.md?pivots=platform-linux%3fpivots%3dplatform-linux)  
* [Anpassen der Authentifizierung und Autorisierung in App Service](app-service-authentication-how-to.md)
* [.NET Core-Integration von Azure AppService EasyAuth (Drittanbieter)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
* [Azure App Service-Authentifizierung mit .NET Core (Drittanbieter)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Anbieterspezifische Anleitungen:

* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung][AAD]
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung][Facebook]
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung][Google]
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung][MSA]
* [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung][Twitter]
* [Konfigurieren Ihrer App für die Verwendung eines OpenID Connect-Anbieters für die Anmeldung (Vorschau)][OIDC]
* [Konfigurieren Ihrer App für die Verwendung einer Anmeldung mit Apple (Vorschau)][Apple]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md
[OIDC]: configure-authentication-provider-openid-connect.md
[Apple]: configure-authentication-provider-apple.md

[custom-auth]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#custom-auth

[ADAL-Android]: /previous-versions/azure/app-service-mobile/app-service-mobile-android-how-to-use-client-library#adal
[ADAL-iOS]: /previous-versions/azure/app-service-mobile/app-service-mobile-ios-how-to-use-client-library#adal
[ADAL-dotnet]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#adal
