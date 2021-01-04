---
title: Registrieren einer Web-App, die Benutzer anmeldet – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Web-App zum Anmelden von Benutzern registrieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 29f5d2960a678204387b2bd1dfd6d4acdc4f9c3d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442513"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web-App für Benutzeranmeldungen: App-Registrierung

In diesem Artikel werden die Besonderheiten der App-Registrierung für eine Web-App erläutert, mit der Benutzer angemeldet werden.

Für das Registrieren Ihrer Anwendung können Sie Folgendes verwenden:

- [Web-App-Schnellstarts](#register-an-app-by-using-the-quickstarts). Schnellstarts im Azure-Portal vermitteln nicht nur erste Erfahrungen beim Erstellen einer Anwendung, sondern umfassen auch die Schaltfläche **Diese Änderung für mich vornehmen**. Sie können diese Schaltfläche verwenden, um die benötigten Eigenschaften festzulegen. Dies gilt auch für eine bereits vorhandene App. Sie müssen die Werte dieser Eigenschaften an Ihren konkreten Fall anpassen. Insbesondere unterscheidet sich die Web-API-URL für Ihre App wahrscheinlich vom vorgeschlagenen Standardwert, was sich auch auf den Abmelde-URI auswirkt.
- Das Azure-Portal für die [manuelle Registrierung Ihrer Anwendung](#register-an-app-by-using-the-azure-portal)
- PowerShell und Befehlszeilentools

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrieren einer App mithilfe der Schnellstarts

Sie können diese Links für den Einstieg in die Erstellung Ihrer Webanwendung verwenden:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrieren einer App über das Azure-Portal

> [!NOTE]
> Welches Portal Sie verwenden müssen, hängt davon ab, ob Ihre Anwendung in der öffentlichen Cloud von Microsoft Azure oder in einer nationalen oder Sovereign Cloud ausgeführt wird. Weitere Informationen finden Sie unter [Nationale Clouds](./authentication-national-cloud.md#app-registration-endpoints).


1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an. Melden Sie sich alternativ beim [Azure-Portal](./authentication-national-cloud.md#app-registration-endpoints) der nationalen Cloud Ihrer Wahl an.
2. Wenn Sie unter Ihrem Konto Zugriff auf mehrere Mandanten haben, können Sie Ihr Konto rechts oben auswählen. Legen Sie dann die Portalsitzung auf den gewünschten Azure AD-Mandanten (Azure Active Directory) fest.
3. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** > **Neue Registrierung** aus.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   1. Wählen Sie die unterstützten Kontotypen für Ihre Anwendung aus. (Weitere Informationen finden Sie unter [Unterstützte Kontotypen](./v2-supported-account-types.md).)
   1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird. Geben Sie beispielsweise **AspNetCore-WebApp** ein.
   1. Fügen Sie in **Umleitungs-URI** den Anwendungstyp und das URI-Ziel hinzu, das nach einer erfolgreichen Authentifizierung zurückgegebene Tokenantworten akzeptiert. Geben Sie z.B. **https://localhost:44321** ein. Wählen Sie dann **Registrieren**.
   ![Screenshot der Seite „Anwendung registrieren“, auf der Sie „Registrieren“ auswählen können.](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. Wählen Sie das Menü **Authentifizierung** aus, und fügen Sie dann die folgenden Informationen hinzu:
   1. Fügen Sie in **Antwort-URL** die Zeichenfolge **https://localhost:44321/signin-oidc** vom Typ **Web** hinzu.
   1. Legen Sie im Abschnitt **Erweiterte Einstellungen** die Option **Abmelde-URL** auf **https://localhost:44321/signout-oidc** fest.
   1. Wählen Sie unter **Implizite Genehmigung** die Option **ID-Token** aus.
   1. Klicken Sie auf **Speichern**.
  ![Screenshot der Authentifizierungsoptionen, in denen Sie die beschriebenen Änderungen vornehmen können.](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:
   1. Wählen Sie die unterstützten Kontotypen für Ihre Anwendung aus. (Weitere Informationen finden Sie unter [Unterstützte Kontotypen](./v2-supported-account-types.md).)
   1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird. Geben Sie z. B. **MailApp-openidconnect-v2** ein.
   1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie den folgenden Umleitungs-URI ein: **https://localhost:44326/** .
1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Wählen Sie das Menü **Authentifizierung** aus.
1. Wählen Sie im Abschnitt **Erweiterte Einstellungen** | **Implizite Gewährung** die Option **ID-Token** aus. Für dieses Beispiel muss der [Flow zur impliziten Gewährung](v2-oauth2-implicit-grant-flow.md) aktiviert werden, damit Benutzer angemeldet werden.
1. Wählen Sie **Speichern** aus.

# <a name="java"></a>[Java](#tab/java)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Anzeigenamen für die Anwendung ein. Geben Sie z. B. **java-webapp** ein.
1. Wählen Sie **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** und dann für **Anwendungstyp** die Option **Web-App/API** aus.
1. Wählen Sie **Registrieren** aus, um die Anwendung zu registrieren.
1. Wählen Sie im linken Menü die Option **Authentifizierung** aus. Wählen Sie unter **Umleitungs-URIs** die Option **Web** aus.

1. Geben Sie zwei Umleitungs-URIs ein: einen für die Anmeldeseite und einen für die Graph-Seite. Verwenden Sie für beide denselben Host und dieselbe Portnummer, gefolgt von **/msal4jsample/secure/aad** für die Anmeldeseite und **msal4jsample/graph/me** für die Benutzerinformationsseite.

   Im Beispiel wird standardmäßig Folgendes verwendet:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Wählen Sie anschließend **Speichern** aus.

1. Wählen Sie im Menü die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, und führen Sie dann folgende Schritte aus:

   1. Geben Sie eine Beschreibung für den Schlüssel ein.
   1. Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
   1. Wählen Sie **Hinzufügen**.
   1. Wenn der Schlüsselwert angezeigt wird, kopieren Sie ihn für später. Dieser Wert wird nicht noch einmal angezeigt, und er kann nicht auf andere Weise abgerufen werden.

# <a name="python"></a>[Python](#tab/python)

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:
   1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird. Geben Sie z. B. **python-webapp** ein.
   1. Ändern Sie **Unterstützte Kontotypen** in **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** .
   1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie den folgenden Umleitungs-URI ein: **http://localhost:5000/getAToken** .
1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID** , und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Visual Studio-Konfigurationsdatei für dieses Projekt zu konfigurieren.
1. Wählen Sie im linken Menü die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, und führen Sie dann folgende Schritte aus:

   1. Geben Sie eine Beschreibung für den Schlüssel ein.
   1. Wählen Sie als Schlüsseldauer die Option **In 1 Jahr** aus.
   1. Wählen Sie **Hinzufügen**.
   1. Wenn der Schlüsselwert angezeigt wird, kopieren Sie ihn für die spätere Verwendung. Sie benötigen die Information später.
---

## <a name="register-an-app-by-using-powershell"></a>Registrieren einer App mit PowerShell

> [!NOTE]
> Derzeit erstellt Azure AD PowerShell nur Anwendungen mit den folgenden unterstützten Kontotypen:
>
> - MyOrg (nur Konten in diesem Organisationsverzeichnis)
> - AnyOrg (Konten in einem beliebigen Organisationsverzeichnis)
>
> Sie können eine Anwendung erstellen, die Benutzer mit ihren persönlichen Microsoft-Konten (z. B. Skype, Xbox oder Outlook.com) anmeldet. Erstellen Sie zunächst eine mehrinstanzenfähige Anwendung. Zu den unterstützten Kontotypen gehören Konten in einem beliebigen Organisationsverzeichnis. Ändern Sie dann über das Azure-Portal im [Anwendungsmanifest](./reference-app-manifest.md) die [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute)-Eigenschaft in **2** und die [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute)-Eigenschaft in `AzureADandPersonalMicrosoftAccount`. Weitere Informationen finden Sie im ASP.NET Core-Tutorial unter [Schritt 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant). Sie können diesen Schritt in einer beliebigen Sprache für Web-Apps generalisieren.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Codekonfiguration der App](scenario-web-app-sign-user-app-configuration.md).