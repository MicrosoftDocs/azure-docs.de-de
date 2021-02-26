---
title: 'Schnellstart: Anmelden von Benutzern und Aufrufen von Microsoft Graph in einer App für Universelle Windows-Plattform | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine Anwendung für die Universelle Windows-Plattform (UWP-Anwendung) ein Zugriffstoken abrufen und eine API aufrufen kann, die durch Microsoft Identity Platform geschützt ist.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 1552dfedc4510ba22e58d166d12a356ba546c092
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103463"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Schnellstart: Aufrufen der Microsoft Graph-API über eine UWP-Anwendung (UWP = Universelle Windows-Plattform)

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine UWP-Anwendung (Universelle Windows-Plattform) Benutzer anmelden und ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen. 

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Voraussetzungen
>
> * Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> [!div renderon="docs" class="sxs-lookup"]
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden:
> * [Express] [Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuell] [Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe die Registrierungsinformationen Ihrer App hinzuzufügen:
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `UWP-App-calling-MsGraph`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z.B. Skype, Xbox, Outlook.com)** aus.
> 1. Wählen Sie die Option **Registrieren** aus, um die Anwendung zu erstellen, und notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.
> 1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Wählen Sie **Plattform hinzufügen** > **Mobilgerät- und Desktopanwendungen** aus.
> 1. Wählen Sie unter **Umleitungs-URIs** die Option `https://login.microsoftonline.com/common/oauth2/nativeclient` aus.
> 1. Wählen Sie **Konfigurieren** aus.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Schritt 1: Konfigurieren der Anwendung
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie den **Umleitungs-URI** `https://login.microsoftonline.com/common/oauth2/nativeclient` hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-uwp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-visual-studio-project"></a>Schritt 2: Laden Sie das Visual Studio-Projekt herunter

> [!div renderon="docs"]
> [Laden Sie das Visual Studio-Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Schritt 3: Konfigurieren des Visual Studio-Projekts
>
> 1. Extrahieren Sie das ZIP-Archiv in einem lokalen Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Beispiel: **C:\Azure-Samples**.
> 1. Öffnen Sie das Projekt in Visual Studio. Installieren Sie die Workload **Entwicklung für die universelle Windows-Plattform** und alle individuellen SDK-Komponenten, wenn Sie dazu aufgefordert werden.
> 1. Ändern Sie in *MainPage.Xaml.cs* den Wert der Variablen `ClientId` in die **Anwendungs-ID (Client)** der Anwendung, die Sie weiter oben registriert haben.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    Sie finden die **Anwendungs-ID (Client)** im Azure-Portal im Bereich **Übersicht** der App (**Azure Active Directory** > **App-Registrierungen** >  *{Ihre App-Registrierung}* ).
> 1. Erstellen Sie ein neues selbstsigniertes Testzertifikat für das Paket, und wählen Sie es aus:
>     1. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei *Package.appxmanifest*.
>     1. Wählen Sie **Verpackung** > **Zertifikat auswählen...**  > **Erstellen...** aus.
>     1. Geben Sie ein Kennwort ein, und wählen Sie anschließend **OK** aus.
>     1. Wählen Sie die Option **Aus Datei auswählen...** und dann die soeben erstellte Datei *Native_UWP_V2_TemporaryKey.pfx* aus. Wählen Sie anschließend **OK** aus.
>     1. Schließen Sie die Datei *Package.appxmanifest* (Auswahl von **OK** bei Aufforderung zum Speichern der Datei).
>     1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **Native_UWP_V2**, und wählen Sie **Eigenschaften** aus.
>     1. Wählen Sie **Signieren** und dann die von Ihnen erstellte PFX-Datei in der Dropdownliste **Schlüsseldatei mit starkem Namen auswählen** aus.

#### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

Führen Sie die Beispielanwendung auf Ihrem lokalen Computer wie folgt aus:

1. Wählen Sie auf der Symbolleiste von Visual Studio die richtige Plattform aus (voraussichtlich **x64** oder **x86**, nicht ARM). Das Zielgerät sollte sich von *Gerät* in *Lokaler Computer* ändern.
1. Wählen Sie **Debuggen** > **Ohne Debuggen starten** aus.
    
    Bei entsprechender Aufforderung müssen Sie unter Umständen zuerst den **Entwicklermodus** aktivieren und dann **Ohne Debuggen starten** erneut verwenden, um die App zu starten.

Wenn das Fenster der App angezeigt wird, können Sie die Schaltfläche **Microsoft Graph-API aufrufen** auswählen, Ihre Anmeldeinformationen eingeben und Ihre Einwilligung zu den Berechtigungen erteilen, die von der Anwendung angefordert werden. Wenn der Vorgang erfolgreich ist, zeigt die Anwendung einige Tokeninformationen und Daten an, die aus dem Aufruf der Microsoft Graph-API stammen.

## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) ist die Bibliothek, mit der Benutzer angemeldet und Sicherheitstoken angefordert werden können. Mithilfe der Sicherheitstoken kann auf eine API zugegriffen werden, die durch Microsoft Identity Platform geschützt ist. Sie können MSAL installieren, indem Sie den folgenden Befehl in der *Paket-Manager-Konsole* von Visual Studio ausführen:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```csharp
using Microsoft.Identity.Client;
```

Anschließend wird MSAL mit dem folgenden Code initialisiert:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

Der Wert der `ClientId` ist die **Anwendungs-ID (Client)** der App, die Sie im Azure-Portal registriert haben. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App.

### <a name="requesting-tokens"></a>Anfordern von Token

MSAL verfügt über zwei Methoden zum Abrufen von Token in einer UWP-App: `AcquireTokenInteractive` und `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

In bestimmten Situationen müssen Benutzer über ein Popupfenster mit Microsoft Identity Platform interagieren, um entweder ihre Anmeldeinformationen zu bestätigen oder ihre Zustimmung zu geben. Beispiele hierfür sind:

- Erstmaliges Anmelden von Benutzern bei der Anwendung.
- Benutzer müssen ihre Anmeldeinformationen erneut eingeben, weil das Kennwort abgelaufen ist.
- Ihre Anwendung fordert Zugriff auf eine Ressource an, dem der Benutzer zustimmen muss.
- Zweistufige Authentifizierung ist erforderlich.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

Der Parameter `scopes` enthält die angeforderten Bereiche, z. B. `{ "user.read" }` für Microsoft Graph oder `{ "api://<Application ID>/access_as_user" }` für benutzerdefinierte Web-APIs.

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Verwenden Sie die `AcquireTokenSilent`-Methode, um Token für den Zugriff auf geschützte Ressourcen nach der anfänglichen `AcquireTokenInteractive`-Methode abzurufen. Sie möchten nicht, dass der Benutzer seine Anmeldeinformationen jedes Mal überprüfen muss, wenn er auf eine Ressource zugreifen muss. Meistens wünschen Sie sich den Abruf und die Erneuerung von Token ohne jegliche Benutzerinteraktion.

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` enthält die angeforderten Bereiche, z. B. `{ "user.read" }` für Microsoft Graph oder `{ "api://<Application ID>/access_as_user" }` für benutzerdefinierte Web-APIs.
* `firstAccount` gibt das erste Benutzerkonto im Cache an (MSAL unterstützt mehrere Benutzer in einer App).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie das Windows Desktop-Tutorial aus, um eine vollständige Schritt-für-Schritt-Anleitung zum Erstellen von Anwendungen und neuen Features zu erhalten, einschließlich einer vollständigen Erläuterung dieses Schnellstarts.

> [!div class="nextstepaction"]
> [Tutorial: UWP – Aufrufen der Microsoft Graph-API](tutorial-v2-windows-uwp.md)
