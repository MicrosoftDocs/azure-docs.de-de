---
title: 'Schnellstart: Aufrufen einer durch Microsoft Identity Platform geschützten ASP.NET-Web-API | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie aus einer Windows (WPF)-Desktopanwendung eine ASP.NET-Web-API aufrufen, die durch Microsoft Identity Platform geschützt ist.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: fe82b03c4a8c71f84de02245b075ff30da31b45b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031096"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Schnellstart: Aufrufen einer durch Microsoft Identity Platform geschützten ASP.NET-Web-API

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie Sie eine ASP.NET-Web-API schützen, indem Sie den Zugriff auf die Ressourcen ausschließlich auf autorisierte Konten beschränken. Das Beispiel unterstützt die Autorisierung von persönlichen Microsoft-Konten sowie Konten in einer beliebigen Azure AD-Organisation (Azure Active Directory).

Der Artikel verwendet auch eine Windows Presentation Foundation (WPF)-App, um zu veranschaulichen, wie Sie ein Zugriffstoken für den Zugriff auf eine Web-API anfordern können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 oder 2019. [Laden Sie Visual Studio kostenlos herunter](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Klonen oder Herunterladen des Beispiels

Zum Abrufen des Beispiels gibt es zwei Möglichkeiten:

* Clonen von der Shell oder der Befehlszeile:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Herunterladen als ZIP-Datei](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Registrieren Ihrer Web-API

In diesem Abschnitt registrieren Sie Ihre Web-API im Azure-Portal in **App-Registrierungen**.

### <a name="choose-your-azure-ad-tenant"></a>Auswählen Ihres Azure AD-Mandanten

Wenn Sie Ihre Apps manuell registrieren möchten, wählen Sie den Azure Active Directory (Azure AD)-Mandanten aus, für den Sie Ihre Apps erstellen möchten.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn sich Ihr Konto in mehreren Azure AD-Mandanten befindet, wählen Sie oben rechts Ihr Profil und anschließend **Verzeichnis wechseln** aus.
1. Ändern Sie Ihre Portalsitzung für den gewünschten Azure AD-Mandanten.

### <a name="register-the-todolistservice-app"></a>Registrieren der TodoListService-App

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `AppModelv2-NativeClient-DotNet-TodoListService`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** für die App den Wert von **Anwendungs-ID (Client)** , und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Visual Studio-Konfigurationsdatei für dieses Projekt (d. h. `ClientId` in der Datei *TodoListService\Web.config*) zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Optionen **Eine API verfügbar machen** > **Bereich hinzufügen** aus. Übernehmen Sie den vorgeschlagenen Anwendungs-ID-URI (`api://{clientId}`), indem Sie **Speichern und fortfahren** auswählen. Geben Sie dann die folgenden Informationen ein:

    1. Geben Sie **access_as_user** als `access_as_user` ein.
    1. Stellen Sie sicher, dass unter **Zum Einwilligen berechtigte Personen** die Option **Administratoren und Benutzer** ausgewählt ist.
    1. Geben Sie `Access TodoListService as a user` im Feld **Anzeigename der Administratorzustimmung** ein.
    1. Geben Sie `Accesses the TodoListService web API as a user` im Feld **Beschreibung der Administratorzustimmung** ein.
    1. Geben Sie `Access TodoListService as a user` im Feld **Anzeigename der Benutzereinwilligung** ein.
    1. Geben Sie `Accesses the TodoListService web API as a user` im Feld **Beschreibung der Benutzereinwilligung** ein.
    1. Übernehmen Sie **Aktiviert** als **Status**.
1. Wählen Sie **Bereich hinzufügen** aus.

### <a name="configure-the-service-project"></a>Konfigurieren des Dienstprojekts

Konfigurieren Sie das Dienstprojekt, damit es der registrierten Web-API entspricht. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie die Projektmappe in Visual Studio, und öffnen Sie dann im Stammverzeichnis des TodoListService-Projekts die Datei *Web.config*.

1. Ersetzen Sie den Wert des `ida:ClientId`-Parameters durch den Wert der Client-ID (Anwendungs-ID) der Anwendung, die Sie gerade im Portal **App-Registrierungen** registriert haben.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Hinzufügen des neuen Bereichs zur Datei „app.config“

Gehen Sie wie folgt vor, um den neuen Bereich zur Datei *app.config* des TodoListClient-Projekts hinzuzufügen:

1. Öffnen Sie im Stammordner des TodoListClient-Projekts die Datei *app.config*.

1. Fügen Sie die Anwendungs-ID aus der Anwendung ein, die Sie soeben im `TodoListServiceScope`-Parameter für das TodoListService-Projekt registriert haben, und ersetzen Sie dabei die Zeichenfolge `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Stellen Sie sicher, das für die Anwendungs-ID das folgende Format verwendet wird: `api://{TodoListService-Application-ID}/access_as_user` (hierbei ist `{TodoListService-Application-ID}` die GUID, die für die Anwendungs-ID der TodoListService-App steht).

## <a name="register-the-todolistclient-client-app"></a>Registrieren der TodoListClient-Client-App

In diesem Abschnitt registrieren Sie im Azure-Portal unter **App-Registrierungen** die TodoListClient-App und konfigurieren dann den Code im TodoListClient-Projekt. Wenn Client und Server als *ein und dieselbe Anwendung* betrachtet werden, können Sie die Anwendung wiederverwenden, die Sie in Schritt 2 registriert haben. Verwenden Sie dieselbe Anwendung, wenn sich Ihre Benutzer mit einem persönlichen Microsoft-Konto anmelden sollen.

### <a name="register-the-app"></a>Registrieren der App

Gehen Sie zum Registrieren der TodoListClient-App wie folgt vor:

1. Navigieren Sie in Microsoft Identity Platform für Entwickler zum Portal [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908).
1. Wählen Sie **Neue Registrierung** aus.
1. Wenn die Seite **Anwendung registrieren** geöffnet wird, geben Sie die Registrierungsinformationen Ihrer Anwendung ein:

    1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (z. B. **NativeClient-DotNet-TodoListClient**).
    1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
    1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.

   > [!NOTE]
   > In der Datei *app.config* des TodoListClient-Projekts ist der Standardwert für `ida:Tenant` auf `common` festgelegt. Mögliche Werte sind:
   > - `common`: Sie können sich mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Microsoft-Konto anmelden (aufgrund der Auswahl von **Konten in einem beliebigen Organisationsverzeichnis** in Schritt 3b).
   > - `organizations`: Sie können sich mit einem Geschäfts-, Schul- oder Unikonto anmelden.
   > - `consumers`: Sie können sich nur mit einem persönlichen Microsoft-Konto anmelden.

1. Wählen Sie auf der Seite **Übersicht** der App die Option **Authentifizierung** aus, und gehen Sie dann wie folgt vor:

    1. Wählen Sie unter **Plattformkonfigurationen** die Schaltfläche **Plattform hinzufügen** aus.
    1. Wählen Sie unter **Mobilgerät- und Desktopanwendungen** die Option **Mobilgerät- und Desktopanwendungen** aus.
    1. Aktivieren Sie unter **Umleitungs-URIs** das Kontrollkästchen **https://login.microsoftonline.com/common/oauth2/nativeclient** .
    1. Wählen Sie **Konfigurieren** aus.

1. Wählen Sie **API-Berechtigungen** aus, und gehen Sie dann wie folgt vor:

    1. Wählen Sie die Schaltfläche **Berechtigung hinzufügen**.
    1. Wählen Sie die Registerkarte **Meine APIs** aus.
    1. Wählen Sie in der API-Liste den Namen **AppModelv2-NativeClient-DotNet-TodoListService API** bzw. den Namen aus, den Sie für die Web-API eingegeben haben.
    1. Aktivieren Sie das Kontrollkästchen für die Berechtigung **access_as_user**, falls es nicht bereits aktiviert ist. Verwenden Sie bei Bedarf das Suchfeld.
    1. Wählen Sie die Schaltfläche **Berechtigungen hinzufügen** aus.

### <a name="configure-your-project"></a>Konfigurieren des Projekts

Gehen Sie zum Konfigurieren des TodoListClient-Projekts wie folgt vor:

1. Kopieren Sie im Portal **App-Registrierungen** auf der Seite **Übersicht** den Wert von **Anwendungs-ID (Client)** .

1. Öffnen Sie die Datei *app.config*, die sich im Stammordner des TodoListClient-Projekts befindet, und fügen Sie dann den Wert der Anwendungs-ID in den `ida:ClientId`-Parameter ein.

## <a name="run-your-todolistclient-project"></a>Ausführen des TodoListClient-Projekts

Gehen Sie zum Ausführen des TodoListClient-Projekts wie folgt vor:

1. Drücken Sie F5, um das TodoListClient-Projekt zu öffnen. Die Projektseite sollte geöffnet werden.

1. Wählen Sie oben rechts die Option **Anmelden** aus, und melden Sie sich mit den gleichen Anmeldeinformationen an, die Sie zum Registrieren der Anwendung verwendet haben. Sie können sich aber auch als Benutzer im selben Verzeichnis anmelden.

   Wenn Sie sich zum ersten Mal anmelden, werden Sie möglicherweise aufgefordert, der TodoListServiceWeb-API zuzustimmen.

   Mit der Anmeldung wird auch ein Zugriffstoken für den Bereich *access_as_user* angefordert, damit Sie auf die TodoListService-Web-API zugreifen und die *To-Do-Liste* bearbeiten können.

## <a name="pre-authorize-your-client-application"></a>Vorautorisieren der Clientanwendung

Eine Möglichkeit, Benutzern aus anderen Verzeichnissen den Zugriff auf Ihre Web-API zu gestatten, besteht darin, die Clientanwendung vorab für den Zugriff auf Ihre Web-API zu autorisieren. Dazu fügen Sie die Anwendungs-ID aus der Client-App zur Liste der vorautorisierten Anwendungen für Ihre Web-API hinzu. Durch das Hinzufügen eines vorautorisierten Clients gestatten Sie Benutzern den Zugriff auf Ihre Web-API, ohne ihre Zustimmung erteilen zu müssen. Gehen Sie zum Vorautorisieren Ihrer Client-App wie folgt vor:

1. Öffnen Sie im Portal **App-Registrierungen** die Eigenschaften Ihrer TodoListService-App.
1. Wählen Sie im Abschnitt **Eine API verfügbar machen** unter **Autorisierte Clientanwendungen** die Option **Clientanwendung hinzufügen** aus.
1. Fügen Sie im Feld **Client-ID** die Anwendungs-ID der TodoListClient-App ein.
1. Wählen Sie im Abschnitt **Autorisierte Bereiche** den Bereich `api://<Application ID>/access_as_user` für die Web-API aus.
1. Wählen Sie **Anwendung hinzufügen** aus.

### <a name="run-your-project"></a>Ausführen des Projekts

1. Drücken Sie F5, um das Projekt auszuführen. Ihre TodoListClient-App sollte geöffnet werden.
1. Wählen Sie oben rechts die Option **Anmelden** aus, und melden Sie sich mit einem persönlichen Microsoft-Konto (z. B. live.com oder hotmail.com) oder einem Geschäfts-, Schul- oder Unikonto an.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Optional: Beschränken des Anmeldezugriffs auf bestimmte Benutzer

Wenn Sie die obigen Schritte ausgeführt haben, können standardmäßig alle persönlichen Konten (z. B. outlook.com oder live.com) oder Geschäfts-, Schul- oder Unikonten von in Azure AD integrierten Organisationen Token anfordern und auf Ihre Web-API zugreifen.

Verwenden Sie eine der folgenden Optionen, um anzugeben, wer sich bei Ihrer Anwendung anmelden kann:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Option 1: Beschränken des Zugriffs auf eine einzelne Organisation (einzelner Mandant)

Sie können den Anmeldezugriff für Ihre Anwendung auf bestimmte Benutzerkonten beschränken, die in einem einzelnen Azure AD-Mandanten vorhanden sind, einschließlich *Gastkonten* dieses Mandanten. Dieses Szenario wird häufig für *Branchenanwendungen* eingesetzt.

1. Öffnen Sie die Datei *App_Start\Startup.Auth*, und ändern Sie dann den Wert des Metadatenendpunkts, der an den `OpenIdConnectSecurityTokenProvider` übergeben wird, in `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`. Sie können auch den Mandantennamen verwenden, beispielsweise `contoso.onmicrosoft.com`.
2. Legen Sie in der gleichen Datei die Eigenschaft `ValidIssuer` für `TokenValidationParameters` auf `"https://sts.windows.net/{Tenant ID}/"` und das Argument `ValidateIssuer` auf `true` fest.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Option 2: Überprüfen von Ausstellern mithilfe einer benutzerdefinierten Methode

Sie können eine benutzerdefinierte Methode implementieren, um Aussteller mit dem Parameter `IssuerValidator` zu überprüfen. Weitere Informationen zur Verwendung dieses Parameters finden Sie unter [TokenValidationParameters-Klasse](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu dem Szenario mit geschützten Web-APIs, das von der Microsoft Identity Platform unterstützt wird, finden Sie hier:
> [!div class="nextstepaction"]
> [Szenario: Geschützte Web-API](scenario-protected-web-api-overview.md)
