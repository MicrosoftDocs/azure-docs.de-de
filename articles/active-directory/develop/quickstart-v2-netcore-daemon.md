---
title: 'Schnellstart: Abrufen eines Tokens und Aufrufen von Microsoft Graph in einer Konsolen-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine .NET Core-Beispiel-App den Clientanmeldeinformations-Flow zum Abrufen eines Tokens und zum Aufrufen von Microsoft Graph verwenden kann.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: d732d2fd8b97ca61222accc21c9930ed8c5c5d3a
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95993889"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über die Identität einer Konsolen-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine .NET Core-Konsolenanwendung ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht auch, wie ein Auftrag oder ein Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann. 

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core) erforderlich.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App

> [!div renderon="docs" class="sxs-lookup"]
>
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden: „Express“ (Option 1 unten) und „Manuell“ (Option 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zum neuen Bereich [Azure-Portal – App-Registrierungen](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs).
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
> 1. Navigieren Sie in Microsoft Identity Platform für Entwickler zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908), indem Sie in der Suchleiste des Azure-Portals nach **App-Registrierungen** suchen.
> 1. Wählen Sie **Neue Registrierung** aus.
> 1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein.
> 1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird, z.B. `Daemon-console`. Wählen Sie anschließend **Registrieren**, um die Anwendung zu erstellen.
> 1. Wählen Sie nach Abschluss der Registrierung das Menü **Zertifikate & Geheimnisse**.
> 1. Wählen Sie unter **Geheime Clientschlüssel** die Option **+ Neuer geheimer Clientschlüssel**. Geben Sie ihm einen Namen, und wählen Sie **Hinzufügen**. Kopieren Sie das Geheimnis an einen sicheren Speicherort. Sie benötigen das Geheimnis zur Verwendung in Ihrem Code, und es wird nicht erneut im Portal angezeigt.
> 1. Wählen Sie nun das Menü **API-Berechtigungen**, die Schaltfläche **+ Berechtigung hinzufügen** und dann die Option **Microsoft Graph**.
> 1. Wählen Sie **Anwendungsberechtigungen**.
> 1. Wählen Sie unter dem Knoten **Benutzer** die Option **User.Read.All** und dann **Berechtigungen hinzufügen**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Herunterladen und Konfigurieren Ihrer Schnellstart-App
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-your-visual-studio-project"></a>Schritt 2: Herunterladen des Visual Studio-Projekts

> [!div renderon="docs"]
> [Laden Sie das Visual Studio-Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Sie können das bereitgestellte Projekt entweder in Visual Studio oder Visual Studio für Mac ausführen.


> [!div class="sxs-lookup" renderon="portal"]
> Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Schritt 3: Konfigurieren des Visual Studio-Projekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise **C:\Azure-Samples**).
> 1. Öffnen Sie die Projektmappe in Visual Studio: **1-Call-MSGraph\daemon-console.sln** (optional).
> 1. Bearbeiten Sie **appsettings.json**, und ersetzen Sie die Felder `ClientId`, `Tenant` und `ClientSecret` durch Folgendes:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Hierbei gilt:
>   - `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
>   - `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>   - `Enter_the_Client_Secret_Here`: Ersetzen Sie diesen Wert durch den geheimen Clientschlüssel, den Sie in Schritt 1 erstellt haben.

> [!div renderon="docs"]
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** finden Sie im Azure-Portal auf der Seite **Übersicht** der App. Navigieren Sie zum Generieren eines neuen Schlüssels zur Seite **Zertifikate & Geheimnisse**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Schritt 4: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, wird der Fehler *HTTP 403 – Verboten* angezeigt: `Insufficient privileges to complete the operation`. Der Grund dafür ist, dass für eine *nur für die App geltende Berechtigung* eine Administratoreinwilligung erforderlich ist. Dies bedeutet, dass ein globaler Administrator Ihres Verzeichnisses die Einwilligung zu Ihrer Anwendung erteilen muss. Wählen Sie je nach Ihrer Rolle eine der unten angegebenen Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

> [!div renderon="docs"]
> Wenn Sie globaler Mandantenadministrator sind, navigieren Sie im Azure-Portal zu **Unternehmensanwendungen** > klicken Sie auf Ihre App-Registrierung > wählen Sie im linken Navigationsbereich im Abschnitt „Sicherheit“ die Option **Berechtigungen** aus. Klicken Sie auf die große Schaltfläche mit der Bezeichnung **Administratorzustimmung für "{Mandantenname}" erteilen** (wobei {Mandantenname} der Name Ihres Verzeichnisses ist).

> [!div renderon="portal" class="sxs-lookup"]
> Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für <Mandantennamen_hier_eingeben> erteilen**.
> > [!div id="apipermissionspage"]
> > [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, müssen Sie einen globalen Administrator bitten, die Administratoreinwilligung für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Hierbei gilt:
>> * `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>> * `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.

> [!NOTE]
> Unter Umständen wird der Fehler *„AADSTS50011: No reply address is registered for the application“* (AADSTS50011: Für die Anwendung ist keine Antwortadresse registriert.) angezeigt, nachdem über die obige URL die Einwilligung für die App erteilt wurde. Dieser Fehler tritt auf, da diese Anwendung und die URL nicht über einen Umleitungs-URI verfügen.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Schritt 5: Ausführen der Anwendung

Wenn Sie Visual Studio oder Visual Studio für Mac verwenden, drücken Sie **F5**, um die Anwendung auszuführen. Führen Sie die Anwendung andernfalls über eine Eingabeaufforderung oder die Konsole oder ein Terminal aus:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Hierbei gilt:
> * *{ProjectFolder}* ist der Ordner, in den Sie die ZIP-Datei extrahieren. Beispiel: **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Es sollte eine Liste mit Benutzern in Ihrem Azure AD-Verzeichnis angezeigt werden.

> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da das Clientgeheimnis Ihren Projektdateien als Nur-Text hinzugefügt wird, wird aus Sicherheitsgründen empfohlen, ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor die Anwendung als Produktionsanwendung eingestuft wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie im GitHub-Repository für dieses Beispiel in [diesen Anweisungen](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates).

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. In dieser Schnellstartanleitung werden wie beschrieben Token angefordert, indem keine delegierten Berechtigungen verwendet werden, sondern die eigene Identität der Anwendung. Der hier genutzte Authentifizierungsablauf wird als *[OAuth-Ablauf mit Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md)* bezeichnet. Weitere Informationen dazu, wie Sie MSAL.NET mit einem Clientanmeldeinformationen-Ablauf verwenden, finden Sie in [diesem Artikel](https://aka.ms/msal-net-client-credentials).

 Sie können MSAL.NET installieren, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole** von Visual Studio ausführen:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```csharp
using Microsoft.Identity.Client;
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Hierbei gilt: | BESCHREIBUNG |
> |---------|---------|
> | `config.ClientSecret` | Der geheime Clientschlüssel, der für die Anwendung im Azure-Portal erstellt wird. |
> | `config.ClientId` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
> | `config.Authority`    | (Optional) Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei „{tenant}“ der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|

Weitere Informationen finden Sie in der [Referenzdokumentation für `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Anfordern von Token

Verwenden Sie die `AcquireTokenForClient`-Methode, um ein Token mit der Identität einer App anzufordern:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `scopes` | Enthält die angeforderten Bereiche. Für vertrauliche Clients sollte ein Format wie `{Application ID URI}/.default` verwendet werden. Hiermit wird angegeben, dass die angeforderten Bereiche diejenigen sind, die im App-Objekt, das im Azure-Portal festgelegt ist, statisch definiert sind (für Microsoft Graph wird für `{Application ID URI}` auf `https://graph.microsoft.com` verwiesen). Für benutzerdefinierte Web-APIs wird `{Application ID URI}` in der Anwendungsregistrierung (Vorschauversion) im Azure-Portal unter dem Abschnitt **Eine API verfügbar machen** definiert. |

Weitere Informationen finden Sie in der [Referenzdokumentation für `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Daemon-Anwendungen finden Sie in der Szenarioübersicht:

> [!div class="nextstepaction"]
> [Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md)
