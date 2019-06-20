---
title: Erste Schritte in Azure AD Xamarin | Microsoft-Dokumentation
description: Erstellen Sie Xamarin-Anwendungen, die für die Anmeldung in Azure AD integriert sind und über OAuth durch Azure AD geschützte APIs aufrufen.
services: active-directory
documentationcenter: xamarin
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3470d9220ed471a05792ed5b3bb259e0dcbe0a6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121914"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Schnellstart: Erstellen einer Xamarin-App, die die Microsoft-Anmeldung integriert

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Mithilfe von Xamarin können Sie mobile Apps in C# schreiben, die unter iOS, Android und Windows (sowohl auf mobilen Geräten als auch PCs) ausgeführt werden können. Bei der Entwicklung einer App mit Xamarin können Sie Benutzer mit Azure Active Directory (Azure AD) einfach und problemlos über deren Azure AD-Konten authentifizieren. Die App kann auf sichere Weise alle Web-APIs nutzen, die per Azure AD geschützt sind, z.B. die Office 365-APIs oder die Azure-API.

Für Xamarin-Apps, die auf geschützte Ressourcen zugreifen müssen, bietet Azure AD die Active Directory Authentication Library (ADAL). Die einzige Aufgabe der ADAL besteht darin, Apps das Abrufen von Zugriffstoken zu erleichtern. Um zu zeigen, wie einfach das geht, wird in diesem Artikel erläutert, wie die App „Directory Searcher“ mit folgenden Funktionen erstellt wird:

* Ausführung unter iOS, Android, Windows Desktop, Windows Phone und Windows Store
* Verwendung einer einzigen portablen Klassenbibliothek (PCL) zur Authentifizierung der Benutzer und zum Abrufen der Token für die Azure AD Graph-API
* Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten UPN

## <a name="prerequisites"></a>Voraussetzungen

* Laden Sie das [Gerüstprojekt](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) oder [vollständige Beispiel](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) herunter. Bei beiden Downloads handelt es sich um eine Visual Studio 2013-Projektmappe.
* Außerdem benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und bei dem Sie die App registrieren. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](quickstart-create-new-tenant.md).

Wenn Sie bereit sind, führen Sie die Verfahren in den folgenden vier Abschnitten aus.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Schritt 1: Einrichten der Xamarin-Entwicklungsumgebung

Da in diesem Tutorial Projekte für iOS, Android und Windows erstellt werden, benötigen Sie sowohl Visual Studio als auch Xamarin. Befolgen Sie zum Erstellen der erforderlichen Umgebung alle Schritte in der Anleitung [Setup und Installation von Visual Studio und Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) auf MSDN. In dieser Anleitung finden Sie auch Materialien mit weiteren Informationen zu Xamarin, die Sie während der Installation lesen können.

Öffnen Sie nach Abschluss des Setups die Projektmappe in Visual Studio. Sie enthält sechs Projekte: fünf plattformspezifische Projekte und die PLC „DirectorySearcher.cs“, die auf allen Plattformen verwendet werden kann.

## <a name="step-2-register-the-directorysearcher-app"></a>Schritt 2: Registrieren der App DirectorySearcher

Damit Ihre Anwendung Token abrufen kann, müssen Sie sie zunächst bei Ihrem Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen. Das geht so:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf der oberen Leiste auf Ihr Konto. Wählen Sie dann in der Liste **Verzeichnis** den Active Directory-Mandanten aus, bei dem Sie die App registrieren möchten.
3. Klicken Sie im linken Bereich auf **Alle Dienste**, und wählen Sie **Azure Active Directory** aus.
4. Klicken Sie auf **App-Registrierungen**, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
5. Folgen Sie den Eingabeaufforderungen, um eine neue Clientanwendung zu erstellen.
   * **Name** enthält eine Beschreibung der App für Benutzer.
   * Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
   * **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen Wert ein (beispielsweise `http://DirectorySearcher`).
6. Nach Abschluss der Registrierung weist Azure AD der App eine eindeutige Anwendungs-ID zu. Kopieren Sie den Wert auf der Registerkarte **Anwendung**, da Sie ihn später benötigen.
7. Wählen Sie auf der Seite **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus. Wählen Sie in **Hiermit wählen Sie eine API aus** die Option ***Microsoft Graph*** aus.
8. Wählen Sie unter **Delegierte Berechtigungen** die Berechtigung **User.Read** aus, und klicken Sie dann auf **Hinzufügen**, um den Vorgang zu speichern. Mit dieser Berechtigung kann die Anwendung die Azure AD Graph-API nach Benutzern abfragen.

## <a name="step-3-install-and-configure-adal"></a>Schritt 3: Installieren und Konfigurieren der ADAL

Nachdem Sie nun eine App in Azure AD erstellt haben, können Sie die ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit die ADAL mit Azure AD kommunizieren kann, müssen Sie ihr einige Informationen zur App-Registrierung bereitstellen.

1. Fügen Sie die ADAL mithilfe der Paket-Manager-Konsole dem Projekt „DirectorySearcher“ hinzu.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    ```

    Beachten Sie, dass jedem Projekt zwei Bibliotheksverweise hinzugefügt werden: der PCL-Teil von ADAL und ein plattformspezifischer Teil.
2. Öffnen Sie im Projekt „DirectorySearcherLib“ die Datei „DirectorySearcher.cs“.
3. Ersetzen Sie die Klassenmemberwerte durch die Werte, die Sie im Azure-Portal eingegeben haben. Sobald Ihr Code die ADAL verwendet, verweist er auf diese Werte.

   * *tenant* ist die Domäne Ihres Azure AD-Mandanten, z.B. „contoso.onmicrosoft.com“.
   * *clientId* ist die Client-ID der App, die Sie aus dem Portal kopiert haben.
   * *returnUri* ist der Umleitungs-URI, den Sie im Portal eingegeben haben (z. B. `http://DirectorySearcher`).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Schritt 4: Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Nahezu die gesamte Authentifizierungslogik der App befindet sich in `DirectorySearcher.SearchByAlias(...)`. In den plattformspezifischen Projekten müssen Sie an die PCL von `DirectorySearcher` daher nur noch einen kontextbezogenen Parameter übergeben.

1. Öffnen Sie „DirectorySearcher.cs“, und fügen Sie dann der `SearchByAlias(...)`-Methode einen neuen Parameter hinzu. `IPlatformParameters` ist der kontextbezogene Parameter, der die plattformspezifischen Objekte kapselt, die die ADAL benötigt, um die Authentifizierung auszuführen.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Initialisieren Sie den `AuthenticationContext`, bei dem es sich um die primäre ADAL-Klasse handelt. Hierdurch übergeben Sie der ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten.
3. Rufen Sie `AcquireTokenAsync(...)` auf, das das Objekt `IPlatformParameters` verwendet und die Authentifizierungsroutine aufruft, die erforderlich ist, damit die Anwendung ein Token erhält.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` versucht zunächst, ein Token für die angeforderte Ressource (in diesem Fall die Graph-API) zurückzugeben, ohne Benutzer zur Eingabe von Anmeldeinformationen aufzufordern (die Authentifizierung erfolgt dann über den Cache oder durch Aktualisierung alter Token). Bei Bedarf wird Benutzern die Azure AD-Anmeldeseite angezeigt, ehe das angeforderte Token bezogen wird.
4. Fügen Sie das Zugriffstoken an die Graph-API-Anforderung im **Authorization**-Header an:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Mehr ist für die PCL von `DirectorySearcher` und den identitätsbezogenen Code der App nicht zu tun. Sie müssen nun in den Ansichten der einzelnen Plattformen nur noch die `SearchByAlias(...)`-Methode aufrufen und ggf. Code für die ordnungsgemäße Handhabung des Lebenszyklus der Benutzeroberfläche hinzufügen.

### <a name="android"></a>Android
1. Fügen Sie in „MainActivity.cs“ im unteren Klickhandler einen Aufruf von `SearchByAlias(...)` hinzu:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Überschreiben Sie die Lebenszyklusmethode `OnActivityResult`, wenn Authentifizierungsumleitungen zurück an die betreffende Methode geleitet werden sollen. In Android stellt ADAL hierfür eine Hilfsmethode bereit:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows Desktop

Richten Sie in „MainWindow.xaml.cs“ einen Aufruf an `SearchByAlias(...)`, indem Sie `WindowInteropHelper` an das Objekt `PlatformParameters` des Desktops übergeben:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
In „DirSearchClient_iOSViewController.cs“ verwendet das iOS-Objekt `PlatformParameters` einen Verweis auf den View Controller:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
Öffnen Sie in Windows Universal „MainPage.xaml.cs“, und implementieren Sie die `Search`-Methode. Diese Methode verwendet eine Hilfsmethode in einem freigegebenen Projekt, um die Benutzeroberfläche bei Bedarf zu aktualisieren.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Sie verfügen nun über eine funktionsfähige Xamarin-App, die Benutzer auf fünf verschiedenen Plattformen authentifizieren und Web-APIs über OAuth 2.0 sicher aufrufen kann.

## <a name="step-5-populate-your-tenant"></a>Schritt 5: Auffüllen Ihres Mandanten mit Daten 

Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen.

1. Führen Sie danach Ihre App „DirectorySearcher“ aus, und melden Sie sich als einer der Benutzer an.
2. Suchen Sie anhand des UPN nach anderen Benutzern.

## <a name="next-steps"></a>Nächste Schritte

Die ADAL erleichtert Ihnen die Integration allgemeiner Identitätsfunktionen in die App. Sie übernimmt die unangenehmen Verwaltungsarbeiten für Sie, wie z.B. die Cacheverwaltung, Unterstützung des OAuth-Protokolls, Anzeige einer Anmeldeschnittstelle für den Benutzer und Aktualisierung abgelaufener Token. Sie müssen lediglich einen einzigen API-Aufruf kennen: `authContext.AcquireToken*(…)`.

* Sie können das [vollständige Beispiel](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (ohne Ihre Konfigurationswerte) herunterladen.
* Informieren Sie sich über das [Schützen einer .NET Web-API mit Azure AD](quickstart-v1-dotnet-webapi.md).
