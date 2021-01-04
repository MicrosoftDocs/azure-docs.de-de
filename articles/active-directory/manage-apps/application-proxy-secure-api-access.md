---
title: Zugreifen auf lokale APIs mit dem Azure AD-Anwendungsproxy
description: Mit dem Anwendungsproxy von Azure Active Directory können native Anwendungen sicher auf APIs und Geschäftslogik zugreifen, die Sie lokal oder in Cloud-VMs hosten.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: eaa4a8f92809dcf44969958573231ad37d908daa
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835696"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Sicherer Zugriff auf lokale APIs mit dem Azure AD-Anwendungsproxy

Möglicherweise verfügen Sie über Geschäftslogik-APIs, die lokal ausgeführt oder auf virtuellen Computern in der Cloud gehostet werden. Ihre nativen Android-, iOS-, Mac- oder Windows-Apps müssen mit den API-Endpunkten interagieren, um Daten zu verwenden oder Benutzerinteraktionen bereitzustellen. Der Azure AD-Anwendungsproxy und die [Microsoft-Authentifizierungsbibliothek (MSAL, Microsoft Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) ermöglichen Ihren nativen Apps den sicheren Zugriff auf Ihre lokalen APIs. Der Azure Active Directory-Anwendungsproxy ist eine schnellere und sicherere Lösung als das Öffnen von Firewallports und das Steuern von Authentifizierung und Autorisierung auf Anwendungsebene.

Dieser Artikel führt Sie durch schrittweise die Einrichtung einer Azure AD-Anwendungsproxylösung für das Hosting eines Web-API-Diensts, auf den native Apps zugreifen können.

## <a name="overview"></a>Übersicht

Die folgende Abbildung zeigt eine herkömmliche Methode zum Veröffentlichen lokaler APIs. Dieser Ansatz erfordert das Öffnen der Ports 80 und 443 für eingehenden Datenverkehr.

![Herkömmliche API-Zugriff](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Die folgende Abbildung zeigt, wie Sie mit dem Azure AD-Anwendungsproxy APIs sicher veröffentlichen können, ohne Ports für eingehenden Datenverkehr zu öffnen:

![API-Zugriff über den Azure AD-Anwendungsproxy](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Der Azure AD-Anwendungsproxy bildet das Rückgrat der Lösung und fungiert dabei als öffentlicher Endpunkt für den API-Zugriff. Er stellt Authentifizierung und Autorisierung bereit. Sie können über eine Vielzahl von Plattformen auf Ihre APIs zugreifen, indem Sie die [MSAL](../azuread-dev/active-directory-authentication-libraries.md)-Bibliotheken (Microsoft Authentication Library, MSAL) verwenden.

Da Authentifizierung und Autorisierung des Azure AD-Anwendungsproxys auf Azure AD aufbauen, können Sie den bedingten Zugriff von Azure AD verwenden, um sicherzustellen, dass nur vertrauenswürdige Geräte auf die über den Anwendungsproxy veröffentlichten APIs zugreifen können. Verwenden Sie Azure AD Join oder Azure AD Hybrid Joined für Desktopcomputer und Intune Managed für Geräte. Sie können auch die Vorteile von Azure Active Directory Premium-Features wie Azure AD Multi-Factor Authentication und die durch maschinelles Lernen unterstützte Sicherheit von [Azure Identity Protection](../identity-protection/overview-identity-protection.md) nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese exemplarische Vorgehensweise benötigen Sie Folgendes:

- Administratorzugriff auf ein Azure-Verzeichnis mit einem Konto, das Apps erstellen und registrieren kann
- Die Beispiel-Web-API und die nativen Client-Apps von [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Veröffentlichen der API über den Anwendungsproxy

Um eine API außerhalb Ihres Intranets über den Anwendungsproxy zu veröffentlichen, folgen Sie dem gleichen Muster wie bei der Veröffentlichung von Web-Apps. Weitere Informationen finden Sie im [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md).

Um die SecretAPI-Web-API über den Anwendungsproxy zu veröffentlichen:

1. Erstellen und veröffentlichen Sie das SecretAPI-Projekt als ASP.NET-Web-App auf Ihrem lokalen Computer oder im Intranet. Stellen Sie sicher, dass Sie auf die Web-App lokal zugreifen können.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus. Wählen Sie dann **Unternehmensanwendungen** aus.

1. Wählen Sie oben auf der Seite **Unternehmensanwendungen – Alle Anwendungen** die Option **Neue Anwendung** aus.

1. Wählen Sie auf der Seite **Anwendung hinzufügen** die Option **Lokale Anwendungen** aus. Die Seite **Fügen Sie Ihre eigene lokale Anwendung hinzu** wird angezeigt.

1. Wenn kein Anwendungsproxyconnector installiert ist, werden Sie zur Installation aufgefordert. Wählen Sie **Anwendungsproxyconnector herunterladen** aus, um den Connector herunterzuladen und zu installieren.

1. Nachdem Sie den Anwendungsproxyconnector installiert haben, gehen Sie auf der Seite **Fügen Sie Ihre eigene lokale Anwendung hinzu** folgendermaßen vor:

   1. Geben Sie neben **Name** *SecretAPI* ein.

   1. Geben Sie die URL neben **Interne URL** ein, die Sie verwenden, um auf die API über Ihr Intranet zuzugreifen.

   1. Stellen Sie sicher, dass **Vorauthentifizierung** auf **Azure Active Directory** festgelegt ist.

   1. Wählen Sie **Hinzufügen** oben auf der Seite aus, und warten Sie, während die App erstellt wird.

   ![Hinzufügen der API-App](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Wählen Sie auf der Seite **Unternehmensanwendungen – Alle Anwendungen** die App **SecretAPI** aus.

1. Wählen Sie auf der Seite **SecretAPI – Übersicht** die Option **Eigenschaften** im linken Navigationsbereich aus.

1. Sie möchten nicht, dass APIs für Endbenutzer im Bereich **MyApps** verfügbar sein sollen. Legen Sie daher **Für Benutzer sichtbar** unten auf der Seite **Eigenschaften** auf **Nein** fest, und wählen Sie dann **Speichern** aus.

   ![Nicht für Benutzer sichtbar](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Sie haben Ihre Web-API über den Azure AD-Anwendungsproxy veröffentlicht. Fügen Sie nun Benutzer hinzu, die auf die Anwendung zugreifen können.

1. Wählen Sie auf der Seite **SecretAPI – Übersicht** die Option **Benutzer und Gruppen** im linken Navigationsbereich aus.

1. Wählen Sie auf der Seite **Benutzer und Gruppen** die Option **Benutzer hinzufügen** aus.

1. Wählen Sie auf der Seite **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

1. Suchen Sie auf der Seite **Benutzer und Gruppen** nach Benutzern, und wählen Sie die Benutzer aus, die auf die App zugreifen können (wählen Sie mindestens sich selbst aus). Nachdem Sie alle Benutzer ausgewählt haben, wählen Sie **Auswählen** aus.

   ![Auswählen und Zuweisen von Benutzern](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. Wählen Sie auf der Seite **Zuweisung hinzufügen** die Option **Zuweisen** aus.

> [!NOTE]
> Für APIs, die integrierte Windows-Authentifizierung verwenden, sind möglicherweise [zusätzliche Schritte](./application-proxy-configure-single-sign-on-with-kcd.md) erforderlich.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrieren der nativen App und Gewähren des Zugriffs auf die API

Native Apps sind Programme, die für die Verwendung auf einer bestimmten Plattform oder auf einem bestimmten Gerät entwickelt wurden. Bevor Ihre native App eine Verbindung herstellen und auf eine API zugreifen kann, müssen Sie sie in Azure AD registrieren. Die folgenden Schritte zeigen, wie Sie eine native App registrieren und ihr Zugriff auf die Web-API gewähren können, die Sie über den Anwendungsproxy veröffentlicht haben.

So registrieren Sie die native AppProxyNativeAppSample-App:

1. Wählen aus der Seite **Übersicht** von Azure Active Directory **App-Registrierungen** aus, und klicken Sie dann oben im Bereich **App-Registrierungen** auf **Neue Registrierung**.

1. Gehen Sie auf der Seite **Registrieren einer Anwendung** folgendermaßen vor:

   1. Geben Sie *AppProxyNativeAppSample* unter **Name** ein.

   1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.

   1. Wählen Sie in der Dropdownliste **Umleitungs-URL** die Option **Öffentlicher Client (Mobilgerät und Desktop)** aus, und geben Sie dann *https://login.microsoftonline.com/common/oauth2/nativeclient* ein.

   1. Wählen Sie **Registrieren** aus, und warten Sie, bis die App erfolgreich registriert wurde.

      ![Registrierung einer neuen Anwendung](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Sie haben die AppProxyNativeAppSample-App jetzt in Azure Active Directory registriert. So gewähren Sie der nativen App Zugriff auf die SecretAPI-Web-API:

1. Wählen Sie auf der Seite **Übersicht** > **App-Registrierungen** von Azure Active Directory die App **AppProxyNativeAppSample** aus.

1. Wählen Sie auf der Seite **AppProxyNativeAppSample** im linken Navigationsbereich **API-Berechtigungen** aus.

1. Wählen Sie auf der Seite **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus.

1. Wählen Sie auf der ersten Seite **API-Berechtigungen anfordern**  die Registerkarte **Von meiner Organisation verwendete APIs** aus, und suchen Sie dann nach **SecretAPI**. Wählen Sie diese Option aus.

1. Aktivieren Sie auf der nächsten Seite **API-Berechtigungen anfordern** das Kontrollkästchen neben **user_impersonation**, und wählen Sie dann **Berechtigungen hinzufügen** aus.

    ![Auswählen einer API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. Auf der Seite **API-Berechtigungen** können Sie **Administratorzustimmung für Contoso erteilen** auswählen, um zu verhindern, dass Benutzer der App einzeln zustimmen müssen.

## <a name="configure-the-native-app-code"></a>Konfigurieren des Codes der nativen App

Der letzte Schritt besteht im Konfigurieren der nativen App. Der folgende Codeausschnitt aus der Datei *Form1.cs* in der NativeClient-Beispiel-App bewirkt, dass die MSAL-Bibliothek das Token für die Anforderung des API-Aufrufs abruft und als Bearer an den App-Header anfügt.

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

Um die native App so zu konfigurieren, dass sie eine Verbindung mit Azure Active Directory herstellt und den API-App-Proxy aufruft, aktualisieren Sie die Platzhalterwerte in der Datei *App.config* der NativeClient-Beispiel-App mit Werten aus Azure AD:

- Fügen Sie die **Verzeichnis-ID (Mandant)** in das `<add key="ida:Tenant" value="" />`-Feld ein. Sie können diesen Wert (eine GUID) auf der Seite **Übersicht** einer Ihrer Apps finden und kopieren.

- Fügen Sie die **Anwendungs-ID (Client)** von AppProxyNativeAppSample in das `<add key="ida:ClientId" value="" />`-Feld ein. Sie können diesen Wert (eine GUID) auf der Seite **Übersicht** von AppProxyNativeAppSample finden und kopieren.

- Fügen Sie den **Umleitungs-URI** von AppProxyNativeAppSample in das `<add key="ida:RedirectUri" value="" />`-Feld ein. Sie können diesen Wert (einen URI) auf der Seite **Authentifizierung** von AppProxyNativeAppSample finden und kopieren.

- Fügen Sie den **Anwendungs-ID-URI** von SecretAPI in das `<add key="todo:TodoListResourceId" value="" />`-Feld ein. Sie können diesen Wert (einen URI) auf der Seite **Eine API verfügbar machen** von SecretAPI finden und kopieren.

- Fügen Sie die **Homepage-URL** von SecretAPI in das `<add key="todo:TodoListBaseAddress" value="" />`-Feld ein. Sie können diesen Wert (eine URL) auf der Seite **Branding** von SecretAPI finden und kopieren.

Nachdem Sie die Parameter konfiguriert haben, erstellen Sie die native App, und führen Sie sie aus. Wenn Sie die Schaltfläche **Anmelden** auswählen, können Sie sich anmelden. Die App zeigt dann einen Erfolgsbildschirm an, um zu bestätigen, dass Sie erfolgreich eine Verbindung mit SecretAPI hergestellt haben.

![Der Screenshot zeigt die Meldung „SecretAPI erfolgreich“ und die Schaltfläche „OK“.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](../develop/quickstart-configure-app-access-web-apis.md)
- [Aktivieren von nativen Clientanwendungen für die Interaktion mit Proxyanwendungen](application-proxy-configure-native-client-application.md)