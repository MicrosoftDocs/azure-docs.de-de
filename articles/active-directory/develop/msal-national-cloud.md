---
title: Verwenden der MSAL in einer nationalen Cloud-App | Azure
titleSuffix: Microsoft identity platform
description: Die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) ermöglicht Anwendungsentwicklern das Abrufen von Token für den Aufruf von geschützten Web-APIs. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft-APIs, Partner-Web-APIs oder um Ihre eigene Web-API handeln. Die MSAL unterstützt mehrere Anwendungsarchitekturen und -plattformen.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 09c4dadd7a6560bd5163d623dd8a7f247b57860e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102494"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Verwenden der MSAL in einer nationalen Cloudumgebung

[Nationale Clouds](authentication-national-cloud.md) (auch als Sovereign Clouds bezeichnet) sind physisch isolierte Azure-Instanzen. Diese Regionen von Azure tragen dazu bei, dass Anforderungen in puncto Datenresidenz, Datenhoheit und Compliance innerhalb geografischer Grenzen erfüllt werden.

Neben der weltweiten Microsoft-Cloud ermöglicht die Microsoft Authentication Library (MSAL) den Anwendungsentwicklern in nationalen Clouds das Abrufen von Token für die Authentifizierung und das Aufrufen geschützter Web-APIs. Bei diesen Web-APIs kann es sich um Microsoft Graph oder um andere Microsoft-APIs handeln.

Neben der globalen Cloud wird Azure Active Directory (Azure AD) in den folgenden nationalen Clouds bereitgestellt:  

- Azure Government
- Azure China 21Vianet
- Azure Deutschland

Dieser Leitfaden veranschaulicht das Anmelden bei Geschäfts-, Schul- oder Unikonten, das Abrufen eines Zugriffstokens sowie das Aufrufen der Microsoft Graph-API in der [Azure Government](https://azure.microsoft.com/global-infrastructure/government/)-Cloudumgebung.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zu Beginn sicher, dass die folgenden Voraussetzungen erfüllt sind:

### <a name="choose-the-appropriate-identities"></a>Auswählen der geeigneten Identitäten

[Azure Government](../../azure-government/index.yml)-Anwendungen können zur Authentifizierung von Benutzern sowohl Azure AD Government-Identitäten als auch öffentliche Azure AD-Identitäten verwenden. Da Sie eine beliebige dieser Identitäten verwenden können, wählen Sie einen Autoritätsendpunkt für Ihr Szenario aus:

- Öffentliche Azure AD-Identität: Wird häufig verwendet, wenn Ihre Organisation bereits über einen öffentlichen Azure AD-Mandanten verfügt, um Microsoft 365 (öffentlich oder GCC) oder eine andere Anwendung zu unterstützen.
- Azure AD Government-Identität: Wird häufig verwendet, wenn Ihre Organisation bereits über einen Azure AD Government-Mandanten verfügt, um Office 365 (GCC High oder DoD) zu unterstützen, oder wenn ein neuer Mandant in Azure AD Government erstellt werden soll.

Nach dieser Entscheidung müssen Sie sich überlegen, wo die App-Registrierung erfolgen soll. Wenn Sie öffentliche Azure AD-Identitäten für Ihre Azure Government-Anwendung auswählen, müssen Sie die Anwendung in Ihrem öffentlichen Azure AD-Mandanten registrieren.

### <a name="get-an-azure-government-subscription"></a>Erhalten eines Azure Government-Abonnements

Informationen zum Beziehen eines Azure Government-Abonnements finden Sie unter [Verwalten und Herstellen einer Verbindung mit Ihrem Abonnement in Azure Government](../../azure-government/compare-azure-government-global-azure.md).

Wenn Sie kein Azure Government-Abonnement besitzen, können Sie zu Beginn ein [kostenloses Konto](https://azure.microsoft.com/global-infrastructure/government/request/) erstellen.

Weitere Informationen zur Verwendung einer nationalen Cloud mit einer bestimmten Programmiersprache erhalten Sie, indem Sie die Registerkarte entsprechend der gewünschten Sprache auswählen:

## <a name="net"></a>[.NET](#tab/dotnet)

Über MSAL.NET können Sie Benutzer anmelden, Token anfordern und die Microsoft Graph-API in nationalen Clouds aufrufen.

In den folgenden Tutorials erfahren Sie, wie Sie eine .NET Core 2.2-MVC-Web-App erstellen. Die App verwendet OpenID Connect, um Benutzer mit einem Geschäfts-, Schul- oder Unikonto in einer Organisation anzumelden, die zu einer nationalen Cloud gehört.

- Informationen zum Anmelden von Benutzern und Abrufen von Token finden Sie in diesem Tutorial: [Erstellen einer ASP.NET Core-Web-App, die Benutzer mit der Microsoft Identity Platform bei Sovereign Clouds anmeldet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Informationen zum Aufrufen der Microsoft Graph-API finden Sie in diesem Tutorial: [Verwenden der Microsoft Identity Platform zum Abrufen der Microsoft Graph-API aus einer ASP.NET Core 2.x-Web-App im Namen eines Benutzers, der sich mit seinem Geschäfts-, Schul- oder Unikonto bei einer nationalen Microsoft-Cloud anmeldet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

So aktivieren Sie Ihre MSAL.js-Anwendung für Sovereign Clouds

### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren

1. Melden Sie sich beim <a href="https://portal.azure.us/" target="_blank">Azure-Portal</a> an.

   Azure-Portalendpunkte für andere nationale Clouds finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).

1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
1. Wählen Sie im Abschnitt **Umleitungs-URI** die Plattform **Web** aus, und legen Sie den Wert auf die URL der Anwendung fest (basierend auf Ihrem Webserver). Eine Anleitung zum Festlegen und Abrufen der Umleitungs-URL in Visual Studio und Node finden Sie in den nächsten Abschnitten.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich für die spätere Verwendung auf der Seite **Übersicht** den Wert von **Anwendungs-ID (Client)** .
    In diesem Tutorial muss der [Flow zur impliziten Genehmigung](v2-oauth2-implicit-grant-flow.md) aktiviert werden. 
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie unter **Implizite Gewährung und Hybridflows** die Optionen **ID-Token** und **Zugriffstoken** aus. ID-Token und Zugriffstoken sind erforderlich, da diese App Benutzer anmelden und eine API aufrufen muss.
1. Wählen Sie **Speichern** aus.

### <a name="step-2--set-up-your-web-server-or-project"></a>Schritt 2:  Einrichten Ihres Webservers oder Projekts

- [Laden Sie die Projektdateien für einen lokalen Webserver herunter](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), z.B. Node.

  oder

- [Laden Sie das Visual Studio-Projekt herunter](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Fahren Sie anschließend mit dem Schritt [Konfigurieren Ihrer JavaScript-SPA](#step-4-configure-your-javascript-spa) fort, um das Codebeispiel vor der Ausführung zu konfigurieren.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Schritt 3: Verwenden der Microsoft-Authentifizierungsbibliothek für die Benutzeranmeldung

Gehen Sie wie im [Javascript-Tutorial](tutorial-v2-javascript-spa.md#create-your-project) beschrieben vor, um Ihr Projekt zu erstellen und in die Microsoft-Authentifizierungsbibliothek zu integrieren.

### <a name="step-4-configure-your-javascript-spa"></a>Schritt 4: Konfigurieren Ihrer JavaScript-SPA

Fügen Sie in der Datei `index.html`, die beim Einrichten des Projekts erstellt wurde, die Informationen für die Anwendungsregistrierung hinzu. Fügen Sie in den Tags `<script></script>` oben im Text der Datei `index.html` den folgenden Code hinzu:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Informationen zu diesem Code:

- `Enter_the_Application_Id_here` ist der Wert **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
- `Enter_the_Tenant_Info_Here` wird auf eine der folgenden Optionen festgelegt:
    - Falls Ihre Anwendung **nur Konten in diesem Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch die Mandanten-ID oder durch den Mandantennamen (beispielsweise „contoso.microsoft.com“).
    - Falls Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch `organizations`.

    Informationen zum Ermitteln von Authentifizierungsendpunkten für alle nationalen Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Persönliche Microsoft-Konten werden in nationalen Clouds nicht unterstützt.

- `graphEndpoint` ist der Microsoft Graph-Endpunkt für Microsoft Cloud for US Government.

   Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Microsoft Graph-Endpunkte in nationalen Clouds](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

So aktivieren Sie Ihre MSAL-Python-Anwendung für Sovereign Clouds

- Registrieren Sie Ihre Anwendung abhängig von der Cloud in einem bestimmten Portal. Weitere Informationen zum Auswählen des Portals finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).
- Verwenden Sie abhängig von der Cloud eines der [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) aus dem Repository mit einigen Änderungen, die im Folgenden genannt werden.
- Verwenden Sie abhängig von der Cloud, in der Sie die Anwendung registriert haben, eine bestimmte Autorisierung. Weitere Informationen zu Autorisierungen für verschiedene Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Hier ist ein Beispiel für eine Autorisierung:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Zum Aufrufen der Microsoft Graph-API ist eine Endpunkt-URL erforderlich, die für die von Ihnen verwendete Cloud spezifisch ist. Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Stammendpunkte für Microsoft Graph und Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Hier finden Sie ein Beispiel für einen Microsoft Graph-Endpunkt mit einem Bereich:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

So aktivieren Sie Ihre MSAL-Java-Anwendung für Sovereign Clouds

- Registrieren Sie Ihre Anwendung abhängig von der Cloud in einem bestimmten Portal. Weitere Informationen zum Auswählen des Portals finden Sie unter [App-Registrierungsendpunkte](authentication-national-cloud.md#app-registration-endpoints).
- Verwenden Sie abhängig von der Cloud eines der [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) aus dem Repository mit einigen Änderungen, die im Folgenden genannt werden.
- Verwenden Sie abhängig von der Cloud, in der Sie die Anwendung registriert haben, eine bestimmte Autorisierung. Weitere Informationen zu Autorisierungen für verschiedene Clouds finden Sie unter [Azure AD-Authentifizierungsendpunkte](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Hier ist ein Beispiel für eine Autorisierung:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Zum Aufrufen der Microsoft Graph-API ist eine Endpunkt-URL erforderlich, die für die von Ihnen verwendete Cloud spezifisch ist. Informationen zum Ermitteln von Microsoft Graph-Endpunkten für alle nationalen Clouds finden Sie unter [Stammendpunkte für Microsoft Graph und Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Hier ist ein Beispiel für einen Graph-Endpunkt mit einem Bereich:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL für iOS und macOS kann zum Abrufen von Token in nationalen Clouds verwendet werden. Für die Erstellung von `MSALPublicClientApplication` ist jedoch zusätzliche Konfiguration erforderlich.

Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL für iOS und macOS kann zum Abrufen von Token in nationalen Clouds verwendet werden. Für die Erstellung von `MSALPublicClientApplication` ist jedoch zusätzliche Konfiguration erforderlich.

Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Nächste Schritte

Unter [Authentifizierungsendpunkte der nationalen Cloud](authentication-national-cloud.md) finden Sie eine Liste der Azure-Portal-URLs und der Tokenendpunkte für jede Cloud.

Dokumentation zur nationalen Cloud:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Deutschland](../../germany/index.yml)