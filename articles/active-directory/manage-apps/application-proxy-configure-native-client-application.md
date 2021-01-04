---
title: Veröffentlichen nativer Client-Apps – Azure AD | Microsoft-Dokumentation
description: Erläutert, wie Sie es systemeigenen Client-Apps ermöglichen, mit einem Azure AD-Anwendungsproxyconnector zu kommunizieren und so einen sicheren Remotezugriff auf lokale Anwendungen bereitzustellen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09e813e0dff09885f104e43099b10d6f6f8a7f9b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657430"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Aktivieren von nativen Clientanwendungen für die Interaktion mit Proxyanwendungen

Sie können mit dem Azure Active Directory-Anwendungsproxy (Azure AD) Web-Apps veröffentlichen, er kann aber auch zum Veröffentlichen nativer Clientanwendungen verwendet werden, die mit der Microsoft-Authentifizierungsbibliothek (MSAL) konfiguriert werden. Native Clientanwendungen unterscheiden sich von Web-Apps dadurch, dass sie auf einem Gerät installiert werden, während auf Web-Apps über einen Browser zugegriffen wird.

Um native Clientanwendungen zu unterstützen, akzeptiert der Anwendungsproxy von Azure AD ausgestellte Token, die im Header gesendet werden. Der Anwendungsproxydienst führt die Authentifizierung für die Benutzer durch. Diese Lösung verwendet keine Anwendungstoken für die Authentifizierung.

![Beziehung zwischen Endbenutzern, Azure AD und veröffentlichten Anwendungen](./media/application-proxy-configure-native-client-application/richclientflow.png)

Verwenden Sie zum Veröffentlichen nativer Anwendungen die Microsoft-Authentifizierungsbibliothek, die die Authentifizierung übernimmt und viele Clientumgebungen unterstützt. Der Anwendungsproxy passt in das Szenario [Desktop-App, die eine Web-API im Namen eines angemeldeten Benutzers aufruft](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user).

Dieser Artikel begleitet Sie durch die vier Schritte zum Veröffentlichen einer nativen Anwendung mit dem Anwendungsproxy und der Active Directory Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Schritt 1: Veröffentlichen Ihrer Proxyanwendung

Veröffentlichen Sie Ihre Proxyanwendung wie jede andere Anwendung, und weisen Sie Benutzern den Zugriff auf Ihre Anwendung zu. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Schritt 2: Registrieren Ihrer nativen Anwendung

Jetzt müssen Sie Ihre Anwendung wie folgt in Azure AD registrieren:

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) an. Das **Dashboard** für **Azure Active Directory Admin Center** wird angezeigt.
1. Wählen Sie auf der Seitenleiste **Azure Active Directory** aus. Die Übersichtsseite **Azure Active Directory** wird angezeigt.
1. Wählen Sie auf der Seitenleiste der Azure AD-Übersicht die Option **App-Registrierungen** aus. Die Liste aller App-Registrierungen wird angezeigt.
1. Wählen Sie **Neue Registrierung** aus. Die Seite **Anwendung registrieren** wird angezeigt.

   ![Erstellen einer neuen App-Registrierung im Azure-Portal](./media/application-proxy-configure-native-client-application/create.png)

1. Geben Sie unter der Überschrift **Name** einen benutzerseitigen Anzeigenamen für Ihre Anwendung an.
1. Wählen Sie unter der Überschrift **Unterstützte Kontotypen** eine Zugriffsebene anhand der folgenden Richtlinien aus:

   - Um nur Konten einzuschließen, die in Ihrer Organisation intern sind, wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
   - Um nur Kunden aus dem Geschäfts- oder Bildungsbereich einzuschließen, wählen Sie **Konten in einem beliebigen Organisationsverzeichnis** aus.
   - Um die breiteste Gruppe von Microsoft-Identitäten einzuschließen, wählen Sie **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
1. Wählen Sie unter **Umleitungs-URI** die Option **Public client (mobile & desktop)** (Öffentlicher Client (mobil und Desktop)) aus, und geben Sie dann den Umleitungs-URI `https://login.microsoftonline.com/common/oauth2/nativeclient` für Ihre Anwendung ein.
1. Wählen Sie die **Microsoft-Plattformrichtlinien** aus, und lesen Sie sie. Wählen Sie dann **Registrieren** aus. Eine Übersichtsseite für die neue Anwendungsregistrierung wird erstellt und angezeigt.

Ausführlichere Informationen zum Erstellen einer neuen Anwendungsregistrierung finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Schritt 3: Gewähren des Zugriffs auf Ihre Proxyanwendung

Nachdem Sie Ihre native Anwendung registriert haben, können Sie ihr Zugriff auf andere Anwendungen in Ihrem Verzeichnis gewähren, in diesem Fall auf die Proxyanwendung. So machen Sie die native Anwendung für die Proxyanwendung verfügbar

1. Wählen Sie auf der Seitenleiste der Seite für die neue Anwendungsregistrierung **API-Berechtigungen** aus. Die Seite **API-Berechtigungen** für die neue Anwendungsregistrierung wird angezeigt.
1. Wählen Sie **Berechtigung hinzufügen** aus. Die Seite **API-Berechtigungen anfordern** wird angezeigt.
1. Wählen Sie unter der Einstellung **Hiermit wählen Sie eine API aus** die Option **Von meiner Organisation verwendete APIs** aus. Eine Liste mit den Anwendungen in Ihrem Verzeichnis, die APIs verfügbar machen, wird angezeigt.
1. Geben Sie die Proxyanwendung, die Sie in [Schritt 1: Veröffentlichen Ihrer Proxyanwendung](#step-1-publish-your-proxy-application) veröffentlicht haben, im Suchfeld ein, oder scrollen Sie zu ihr, und wählen Sie dann die Proxyanwendung aus.
1. Wählen Sie unter der Überschrift **Welche Art von Berechtigungen sind für Ihre Anwendung erforderlich?** den Berechtigungstyp aus. Wenn Ihre native Anwendung als der angemeldete Benutzer auf die Proxy-Anwendung-API zugreifen muss, wählen Sie **Delegierte Berechtigungen** aus.
1. Wählen Sie unter der Überschrift **Berechtigungen auswählen** die gewünschte Berechtigung und dann **Berechtigungen hinzufügen** aus. Auf der Seite **API-Berechtigungen** für Ihre native Anwendung werden jetzt die hinzugefügte Proxyanwendung und die Berechtigungs-API angezeigt.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Schritt 4: Hinzufügen der Microsoft-Authentifizierungsbibliothek zu Ihrem Code (.NET C#-Beispiel)

Bearbeiten Sie den Code für native Anwendungen im Authentifizierungskontext der Microsoft-Authentifizierungsbibliothek (MSAL), sodass er folgenden Text enthält: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Die erforderlichen Informationen im Beispielcode sind wie folgt im Azure AD-Portal zu finden:

| Erforderliche Informationen | Suchpfad im Azure AD-Portal |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID** |
| \<App ID of the Native app> | **Anwendungs Registrierung** > *Ihre native Anwendung* > **Übersicht** > **Anwendungs-ID** |
| \<Scope> | **Anwendungsregistrierung** > *Ihre native Anwendung* > **API-Berechtigungen** > Klicken Sie auf die Berechtigungs-API (user_impersonation) > Ein Bereich mit der Überschrift **user_impersonation** wird auf der rechten Seite angezeigt. > Der Bereich ist die URL im Bearbeitungsfeld.
| \<Proxy App Url> | Die externe URL und der Pfad zur API.

Nachdem Sie den MSAL-Code mit diesen Parametern bearbeitet haben, können sich Ihre Benutzer bei nativen Client-Apps authentifizieren, auch wenn sie sich außerhalb des Unternehmensnetzwerks befinden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Flow bei nativen Anwendungen finden Sie unter [Native Apps in Azure Active Directory](../azuread-dev/native-app.md).

Erfahren Sie etwas über [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).