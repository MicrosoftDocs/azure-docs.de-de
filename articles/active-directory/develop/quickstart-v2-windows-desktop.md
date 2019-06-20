---
title: 'Schnellstart: Microsoft Identity Platform – Windows Desktop | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie eine Windows Desktop .NET-Anwendung (XAML) ein Zugriffstoken abrufen und eine API aufrufen kann, die durch einen Microsoft Identity Platform-Endpunkt geschützt ist.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa400d678fb1e0793ea1a0cfd53bdfb715a5bc77
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785021"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer Windows Desktop-App aus

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Windows Desktop .NET-Anwendung (WPF-Anwendung) schreiben, die persönliche Konten oder Geschäfts-, Schul- oder Unikonten anmelden, ein Zugriffstoken abrufen und die Microsoft Graph-API aufrufen kann.

![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden:
> * [Express] [Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuell] [Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zum neuen Bereich [Azure-Portal – App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
> 1. Navigieren Sie zur Seite [App-Registrierungen](https://aka.ms/MobileAppReg) von Microsoft Identity Platform für Entwickler.
> 1. Wählen Sie **Neue Registrierung** aus.
>      - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `Win-App-calling-MsGraph`).
>      - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
>      - Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
> 1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus.
> 1. Erweitern Sie den Abschnitt **Desktop + devices** (Desktop + Geräte).  (Wird **Desktop + devices** (Desktop + Geräte) nicht angezeigt, klicken Sie zuerst auf das obere Banner, um die Vorschauversion der Authentifizierungsumgebung anzuzeigen.)
> 1. Wählen Sie im Abschnitt **Umleitungs-URI** die Option **URI hinzufügen** aus.  Geben Sie **urn:ietf:wg:oauth:2.0:oob** ein.
> 1. Wählen Sie **Speichern** aus.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel für diesen Schnellstart funktioniert, müssen Sie eine Antwort-URL als **urn:ietf:wg:oauth:2.0:oob** hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-windows-desktop/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-your-visual-studio-project"></a>Schritt 2: Herunterladen des Visual Studio-Projekts

[Laden Sie das Visual Studio-Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip). ([Projekt auf GitHub anzeigen](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/))

#### <a name="step-3-configure-your-visual-studio-project"></a>Schritt 3: Konfigurieren des Visual Studio-Projekts

1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise **C:\Azure-Samples**).
1. Öffnen Sie das Projekt in Visual Studio.
1. Bearbeiten Sie **App.Xaml.cs**. Ersetzen Sie dabei die Werte der Felder `ClientId` und `Tenant` durch den folgenden Code:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```

> [!div renderon="docs"]
> Hinweis:
> - `Enter_the_Application_Id_here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
> - `Enter_the_Tenant_Info_Here` wird auf eine der folgenden Optionen festgelegt:
>   - Unterstützt Ihre Anwendung **Nur Konten in diesem Organisationsverzeichnis**, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (etwa „contoso.microsoft.com“).
>   - Unterstützt Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis**, ersetzen Sie diesen Wert durch `organizations`.
>   - Unterstützt Ihre Anwendung **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten**, ersetzen Sie diesen Wert durch `common`.
>
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** , **Verzeichnis-ID (Mandant)** und **Unterstützte Kontotypen** finden Sie im Azure-Portal auf der Seite **Übersicht** der App.

## <a name="more-information"></a>Weitere Informationen

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. Sie können MSAL installieren, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole** von Visual Studio ausführen:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```csharp
using Microsoft.Identity.Client;
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Hinweis: ||
> |---------|---------|
> | `ClientId` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |

### <a name="requesting-tokens"></a>Anfordern von Token

MSAL verfügt über zwei Methoden zum Abrufen von Token: `AcquireTokenInteractive` und `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

In bestimmten Situationen müssen Benutzer über ein Popupfenster mit dem Microsoft Identity Platform-Endpunkt interagieren, um entweder ihre Anmeldeinformationen zu bestätigen oder ihre Zustimmung zu geben. Beispiele hierfür sind:

- Erstmaliges Anmelden von Benutzern bei der Anwendung.
- Benutzer müssen ihre Anmeldeinformationen erneut eingeben, weil das Kennwort abgelaufen ist.
- Ihre Anwendung fordert Zugriff auf eine Ressource an, dem der Benutzer zustimmen muss.
- Zweistufige Authentifizierung ist erforderlich.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Hinweis:||
> |---------|---------|
> | `_scopes` | Enthält die angeforderten Bereiche (etwa `{ "user.read" }` für Microsoft Graph oder `{ "api://<Application ID>/access_as_user" }` für benutzerdefinierte Web-APIs). |

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Sie möchten nicht, dass der Benutzer seine Anmeldeinformationen jedes Mal überprüfen muss, wenn er auf eine Ressource zugreifen muss. Meistens wünschen Sie sich den Abruf und die Erneuerung von Token ohne jegliche Benutzerinteraktion. Sie können die `AcquireTokenSilentAsync`-Methode verwenden, um Token für den Zugriff auf geschützte Ressourcen nach der anfänglichen `AcquireTokenInteractive`-Methode abzurufen:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Hinweis: ||
> |---------|---------|
> | `scopes` | Enthält die angeforderten Bereiche (etwa `{ "user.read" }` für Microsoft Graph oder `{ "api://<Application ID>/access_as_user" }` für benutzerdefinierte Web-APIs). |
> | `firstAccount` | Gibt den ersten Benutzer im Cache an. (MSAL unterstützt mehrere Benutzer in einer App.) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie das Windows Desktop-Tutorial aus, um eine vollständige Schritt-für-Schritt-Anleitung zum Erstellen von Anwendungen und neuen Features zu erhalten, einschließlich einer vollständigen Erläuterung dieses Schnellstarts.

> [!div class="nextstepaction"]
> [Tutorial: Aufrufen der Microsoft Graph-API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

