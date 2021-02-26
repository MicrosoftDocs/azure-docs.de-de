---
title: 'Tutorial: Erstellen eines mehrinstanzenfähigen Daemons mit Zugriff auf Microsoft Graph-Geschäftsdaten | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erfahren Sie, wie Sie von einer Windows-Desktopanwendung (WPF) aus eine durch Azure Active Directory geschützte ASP.NET-Web-API aufrufen. Der WPF-Client authentifiziert einen Benutzer, fordert ein Zugriffstoken an und ruft die Web-API auf.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4fccff70fd267aef84550b4e2f5d6f5f9422a341
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103021"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Tutorial: Erstellen eines mehrinstanzenfähigen Daemons, der Microsoft Identity Platform verwendet

In diesem Tutorial laden Sie eine ASP.NET-Daemon-Web-App herunter, die die Verwendung der Genehmigung von OAuth 2.0-Clientanmeldeinformationen zum Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API veranschaulicht. Diese Web-App führen Sie dann auch aus.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Integrieren einer Daemon-App in Microsoft Identity Platform
> * Erteilen von Anwendungsberechtigungen direkt für die App durch einen Administrator
> * Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API
> * Rufen Sie die Microsoft Graph-API auf.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2017 oder 2019](https://visualstudio.microsoft.com/downloads/)
- Einen Azure AD-Mandanten. Weitere Informationen finden Sie unter [Schnellstart: Einrichten eines Mandanten](quickstart-create-new-tenant.md).
- Mindestens ein Benutzerkonto in Ihrem Azure AD-Mandanten. Dieses Beispiel funktioniert nicht mit einem Microsoft-Konto. Falls Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Microsoft-Konto angemeldet und bislang noch kein Benutzerkonto in Ihrem Verzeichnis erstellt haben, holen Sie dies nun nach.

## <a name="scenario"></a>Szenario

Die App wird als ASP.NET-MVC-Anwendung erstellt. Sie verwendet die OWIN OpenID Connect-Middleware für die Benutzeranmeldung.

Bei der Daemon-Komponente in diesem Beispiel handelt es sich um einen API-Controller (`SyncController.cs`). Wenn der Controller aufgerufen wird, pullt er von Microsoft Graph eine Liste mit Benutzern im Azure AD-Mandanten (Azure Active Directory) des Kunden. `SyncController.cs` wird durch einen AJAX-Aufruf in der Webanwendung ausgelöst. Er verwendet die [Microsoft-Authentifizierungsbibliothek (MSAL) für .NET](msal-overview.md), um ein Zugriffstoken für Microsoft Graph abzurufen.

Da es sich bei der App um eine mehrinstanzenfähige App für Microsoft-Geschäftskunden handelt, müssen Kunden die Möglichkeit haben, sich zu registrieren oder die Anwendung mit ihren Unternehmensdaten zu verknüpfen. Im Zuge der Verknüpfung weist ein globaler Administrator der App zunächst direkt *Anwendungsberechtigungen* zu, damit sie auf nicht interaktive Weise (ohne Beteiligung eines angemeldeten Benutzers) auf Unternehmensdaten zugreifen kann. Der Großteil der Logik in diesem Beispiel zeigt, wie diese Verknüpfung mithilfe des Identity Platform-Endpunkts [Administratoreinwilligung](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) implementiert wird.

![Diagramm: UserSync-App mit drei lokalen Elementen, die eine Verbindung mit Azure herstellen. Dabei erwirbt Start.Auth interaktiv ein Token für die Verbindungsherstellung mit Azure AD, AccountController holt die Administratoreinwilligung für die Verbindungsherstellung mit Azure AD ein, und SyncController liest die Benutzer für die Verbindungsherstellung mit Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Weitere Informationen zu den Konzepten in diesem Beispiel finden Sie unter [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md).

## <a name="clone-or-download-this-repository"></a>Klonen oder Herunterladen des Repositorys

Geben Sie in Ihrer Shell oder Befehlszeile den folgenden Befehl ein:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Oder [laden Sie das Beispiel in einer ZIP-Datei herunter](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Anwendung registrieren

Dieses Beispiel enthält ein einzelnes Projekt. Zum Registrieren der Anwendung bei Ihrem Azure AD-Mandanten haben Sie folgende Möglichkeiten:

- Mithilfe der Schritte [Registrieren der Beispielanwendung bei Ihrem Azure AD-Mandanten](#register-the-client-app-dotnet-web-daemon-v2) und [Auswählen des Azure AD-Mandanten für die Anwendungen](#choose-the-azure-ad-tenant)
- Mithilfe von PowerShell-Skripts, die:
  - *Automatisch* die Azure AD-Anwendungen und die zugehörigen Objekte (Kennwörter, Berechtigungen, Abhängigkeiten) für Sie erstellen.
  - Die Konfigurationsdateien der Visual Studio-Projekte ändern.

Wenn Sie die Automatisierung verwenden möchten, gehen Sie wie folgt vor:

1. Führen Sie unter Windows PowerShell aus, und navigieren Sie zum Stamm des geklonten Verzeichnisses.
1. Führen Sie den folgenden Befehl aus:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Führen Sie das Skript aus, um Ihre Azure AD-Anwendung zu erstellen und den Code der Beispielanwendung entsprechend zu konfigurieren:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Weitere Skriptausführungsmethoden werden [hier](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md) beschrieben.

1. Öffnen Sie die Visual Studio-Projektmappe, und wählen Sie **Starten** aus, um den Code auszuführen.

Falls Sie die Automatisierung nicht verwenden möchten, führen Sie die Schritte in den folgenden Abschnitten aus.

### <a name="choose-the-azure-ad-tenant"></a>Erstellen des Azure AD-Mandanten

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrieren der Client-App (dotnet-web-daemon-v2)

1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `dotnet-web-daemon-v2`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
1. Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie `https://localhost:44316/` und `https://localhost:44316/Account/GrantPermissions` als Umleitungs-URIs ein.

    Sind mehr als zwei Umleitungs-URIs vorhanden, müssen diese nach erfolgreicher Erstellung der App auf der Registerkarte **Authentifizierung** hinzugefügt werden.
1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungs-ID (Client)** , und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Visual Studio-Konfigurationsdatei für dieses Projekt zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Legen Sie **URL für Front-Channel-Abmeldung** auf `https://localhost:44316/Account/EndSession` fest.
1. Wählen Sie im Abschnitt **Implizite Gewährung und Hybridflows** die Optionen **Zugriffstoken** und **ID-Token** aus. Für dieses Beispiel muss der [Flow zur impliziten Gewährung](v2-oauth2-implicit-grant-flow.md) aktiviert werden, um den Benutzer anzumelden und eine API aufzurufen.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus. 
1. Geben Sie eine Schlüsselbeschreibung ein (z. B. **App-Geheimnis**).
1. Wählen Sie eine Schlüsseldauer aus: **In 1 Jahr**, **In 2 Jahren** oder **Läuft nie ab**.
1. Wählen Sie **Hinzufügen**. Notieren Sie den Schlüsselwert, und verwahren Sie ihn an einem sicheren Ort. Dieser Schlüssel wird später benötigt, um das Projekt in Visual Studio zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus.
1. Wählen Sie im Abschnitt **Häufig verwendete Microsoft-APIs** die Option **Microsoft Graph** aus.
1. Vergewissern Sie sich im Abschnitt **Anwendungsberechtigungen**, dass die richtigen Berechtigungen ausgewählt sind: **User.Read.All**.
1. Wählen Sie **Berechtigungen hinzufügen** aus.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurieren des Beispiels für die Verwendung Ihres Azure-Mandanten

In den folgenden Schritten ist **ClientID** mit „Anwendungs-ID“ oder **AppId** identisch.

Öffnen Sie die Projektmappe in Visual Studio, um die Projekte zu konfigurieren.

### <a name="configure-the-client-project"></a>Konfigurieren des Clientprojekts

Wenn Sie die Setupskripts verwendet haben, wurden die folgenden Änderungen für Sie angewendet.

1. Öffnen Sie die Datei **UserSync\Web.Config**.
1. Suchen Sie den App-Schlüssel **ida:ClientId**. Ersetzen Sie den vorhandenen Wert durch die aus dem Azure-Portal kopierte Anwendungs-ID der Anwendung **dotnet-web-daemon-v2**.
1. Suchen Sie den App-Schlüssel **ida:ClientSecret**. Ersetzen Sie den vorhandenen Wert durch den Schlüssel, den Sie beim Erstellen der App **dotnet-web-daemon-v2** im Azure-Portal gespeichert haben.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Bereinigen Sie die Projektmappe, erstellen Sie die Projektmappe neu, führen Sie die UserSync-Anwendung aus, und melden Sie sich anschließend als Administrator bei Ihrem Azure AD-Mandanten an. Sollten Sie noch keinen Azure AD-Mandanten zum Testen haben, gehen Sie wie [hier](quickstart-create-new-tenant.md) beschrieben vor, um einen zu erhalten.

Wenn Sie sich anmelden, fordert die App von Ihnen zunächst Berechtigungen zum Anmelden sowie zum Lesen Ihres Benutzerprofils an. Mithilfe dieser Einwilligung kann die App überprüfen, ob es sich bei Ihnen um einen Geschäftsbenutzer handelt.

![Benutzerzustimmung](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Anschließend versucht die App, über Microsoft Graph eine Liste mit Benutzern aus Ihrem Azure AD-Mandanten zu synchronisieren. Sollte das nicht möglich ist, werden Sie (der Mandantenadministrator) aufgefordert, Ihren Mandanten mit der App zu verbinden.

Die App fordert dann eine Leseberechtigung für die Liste der Benutzer in Ihrem Mandanten an.

![Administratorzustimmung](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Nach Erteilung der Berechtigung werden Sie von der App abgemeldet. Durch diese Abmeldung wird sichergestellt, dass alle vorhandenen Zugriffstoken für Microsoft Graph aus dem Tokencache entfernt werden. Wenn Sie sich erneut anmelden, verfügt das neue Token über die erforderlichen Berechtigungen für Microsoft Graph-Aufrufe.


Nach Erteilung der Berechtigung kann die App jederzeit Benutzer abfragen. Dies können Sie überprüfen, indem Sie die Schaltfläche **Sync Users** (Benutzer synchronisieren) auswählen und die Benutzerliste aktualisieren. Fügen Sie einen Benutzer hinzu, oder entfernen Sie einen Benutzer, und synchronisieren Sie die Liste anschließend erneut. (Beachten Sie jedoch, dass nur die erste Seite mit Benutzern synchronisiert wird.)

## <a name="about-the-code"></a>Informationen zum Code

Der relevante Code für dieses Beispiel befindet sich in den folgenden Dateien:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Erste Anmeldung. Die Aktionen für den Controller verfügen über ein Autorisierungsattribut (**Authorize**), das die Anmeldung des Benutzers erzwingt. Die Anwendung verwendet den [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md), um den Benutzer anzumelden.
- **Controllers\SyncController.cs**: Synchronisierung der Benutzerliste mit dem lokalen In-Memory-Speicher.
- **Controllers\UserController.cs**: Anzeige der Benutzerliste aus dem lokalen In-Memory-Speicher.
- **Controllers\AccountController.cs**: Anforderung von Berechtigungen vom Mandantenadministrator mithilfe des Endpunkts für die Administratoreinwilligung.

## <a name="re-create-the-sample-app"></a>Erneutes Erstellen der Beispiel-App

1. Erstellen Sie in Visual Studio ein neues **Visual C#-Projekt** vom Typ **ASP.NET-Webanwendung (.NET Framework)** .
1. Wählen Sie im nächsten Bildschirm die Projektvorlage **MVC** aus. Fügen Sie außerdem Ordner- und Kernverweise für **Web-API** hinzu, da Sie später noch einen Web-API-Controller hinzufügen. Behalten Sie den standardmäßig ausgewählten Authentifizierungsmodus bei: **Keine Authentifizierung**.
1. Wählen Sie im Fenster **Projektmappen-Explorer** das Projekt aus, und drücken Sie **F4**.
1. Legen Sie in den Projekteigenschaften die Eigenschaft **SSL aktiviert** auf **True** fest. Notieren Sie sich die Information unter **SSL-URL**. Sie wird beim Konfigurieren der Registrierung dieser Anwendung im Azure-Portal benötigt.
1. Fügen Sie die folgenden NuGet-Pakete für die ASP.NET-OWIN-Middleware hinzu:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. Führen Sie im Ordner **App_Start** die folgenden Schritte aus:
   1. Erstellen Sie eine Klasse mit dem Namen **Startup.Auth.cs**.
   1. Entfernen Sie **.App_Start** aus dem Namespacenamen.
   1. Ersetzen Sie den Code für die Klasse **Startup** durch den Code aus der gleichen Datei der Beispiel-App.
   Achten Sie darauf, die gesamte Klassendefinition zu verwenden. Die Definition ändert sich von **public class Startup** in **public partial class Startup**.
1. Beheben Sie die fehlenden Verweise in **Startup.Auth.cs** durch Hinzufügen von **using**-Anweisungen (gemäß den Empfehlungen von Visual Studio IntelliSense).
1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Klasse** aus.
1. Geben Sie **OWIN** in das Suchfeld ein. **OWIN-Startup-Klasse** wird als Option angezeigt. Wählen Sie diese Option aus, und nennen Sie die Klasse **Startup.cs**.
1. Ersetzen Sie in **Startup.cs** den Code für die Klasse **Startup** durch den Code aus der gleichen Datei der Beispiel-App. Auch hier ändert sich die Definition von **public class Startup** in **public partial class Startup**.
1. Fügen Sie im Ordner **Models** eine neue Klasse namens **MsGraphUser.cs** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **MVC 5-Controller - leer** namens **AccountController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **MVC 5-Controller - leer** namens **UserController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **Web API 2-Controller – Leer** namens **SyncController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie für die Benutzeroberfläche im Ordner **Views\Account** drei **leere Ansichten (ohne Modell)** namens **GrantPermissions**, **Index** und **UserMismatch** hinzu. Fügen Sie außerdem im Ordner **Views\User** eine Instanz namens **Index** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Aktualisieren Sie **Shared\_Layout.cshtml** und **Home\Index.cshtml**, um die verschiedenen Ansichten ordnungsgemäß miteinander zu verknüpfen.

## <a name="deploy-the-sample-to-azure"></a>Bereitstellen des Beispiels in Azure

Dieses Projekt enthält Web-App- und Web-API-Projekte. Führen Sie jeweils die folgenden Schritte aus, um sie für Azure-Websites bereitzustellen:

1. Erstellen Sie eine Azure-Website.
1. Veröffentlichen Sie die Web-App und die Web-APIs für die Website.
1. Aktualisieren Sie die Clients, um die Website aufzurufen (anstelle von IIS Express).

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Erstellen und Veröffentlichen von „dotnet-web-daemon-v2“ für eine Azure-Website

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Wählen Sie **Web** > **Web-App** aus, und benennen Sie Ihre Website. Nennen Sie sie beispielsweise **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Wählen Sie die Informationen für **Abonnement** und **Ressourcengruppe** sowie für **App Service-Plan und Standort** aus. Legen Sie **Betriebssystem** auf **Windows** und **Veröffentlichen** auf **Code** fest.
1. Wählen Sie **Erstellen** aus, und warten Sie, bis der App-Dienst erstellt wurde.
1. Wählen Sie nach Erhalt der Benachrichtigung **Bereitstellung erfolgreich** die Option **Zu Ressource wechseln** aus, um zum neu erstellten App-Dienst zu navigieren.
1. Suchen Sie die erstellte Website auf dem **Dashboard**, und wählen Sie sie aus, um den Bildschirm **Übersicht** für den App-Dienst zu öffnen.
1. Laden Sie auf der Registerkarte **Übersicht** des App-Diensts das Veröffentlichungsprofil herunter, indem Sie den Link **Veröffentlichungsprofil abrufen** auswählen, und speichern Sie das Profil. Es können auch andere Bereitstellungsmechanismen wie etwa die Bereitstellung per Quellcodeverwaltung verwendet werden.
1. Wechseln Sie zu Visual Studio, und führen Sie die folgenden Schritte aus:
   1. Navigieren Sie zum Projekt **dotnet-web-daemon-v2**.
   1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
   1. Wählen Sie auf der unteren Leiste die Option **Profil importieren** aus, und importieren Sie das zuvor heruntergeladene Veröffentlichungsprofil.
1. Wählen Sie **Konfigurieren** aus.
1. Aktualisieren Sie auf der Registerkarte **Verbindung** die Ziel-URL, sodass sie HTTPS verwendet. Verwenden Sie z. B. `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Wählen Sie **Weiter** aus.
1. Vergewissern Sie sich auf der Registerkarte **Einstellungen**, dass die Option **Organisationsauthentifizierung aktivieren** deaktiviert ist.
1. Wählen Sie **Speichern** aus. Wählen Sie im Hauptbildschirm **Veröffentlichen** aus.

Visual Studio veröffentlicht das Projekt und öffnet die Projekt-URL automatisch in einem Browser. Wenn die Standardwebseite des Projekts angezeigt wird, war die Veröffentlichung erfolgreich.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualisieren der Azure AD-Mandantenanwendungsregistrierung für „dotnet-web-daemon-v2“

1. Wechseln Sie zurück zum <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a>.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** aus.
1. Wählen Sie die Anwendung **dotnet-web-daemon-v2** aus.
1. Aktualisieren Sie auf der Seite **Authentifizierung** für Ihre Anwendung die Felder vom Typ **URL für Front-Channel-Abmeldung** mit der Adresse Ihres Diensts. Verwenden Sie z. B. `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession`.
1. Aktualisieren Sie im Menü **Branding** die **URL der Startseite** auf die Adresse Ihres Diensts. Verwenden Sie z. B. `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Speichern Sie die Konfiguration.
1. Fügen Sie die gleiche URL der Werteliste unter **Authentifizierung** > **Umleitungs-URIs** hinzu. Falls Sie über mehrere Umleitungs-URLs verfügen, muss für jede ein neuer Eintrag mit dem URI des App-Diensts vorhanden sein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie das im Schritt [Registrieren Ihrer Anwendung](#register-your-application) erstellte App-Objekt, wenn Sie es nicht mehr benötigen.  Befolgen Sie zum Entfernen der Anwendung die Anweisungen unter [Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Verwenden Sie [Microsoft Q&A](https://docs.microsoft.com/answers/products/), um Unterstützung von der Community zu erhalten.
Stellen Sie Ihre Fragen zuerst auf [Microsoft Q&A](https://docs.microsoft.com/answers/products/), und durchsuchen Sie die vorhandenen Probleme, um zu prüfen, ob vielleicht schon jemand anders die gleiche Frage hatte.
Markieren Sie Ihre Fragen und Kommentare mit „azure-ad-adal-deprecation“, „azure-ad-msal“ und „dotnet-standard“.

Sollten Sie einen Fehler im Beispiel finden, erstellen Sie ein Problem unter [GitHub-Probleme](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Sollten Sie einen Fehler in MSAL.NET finden, erstellen Sie ein Problem unter [GitHub-Probleme für MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Wenn Sie eine Empfehlung abgeben möchten, besuchen Sie die [Benutzerfeedback-Seite](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr über das Erstellen von Daemon-Apps, die mithilfe von Microsoft Identity Platform auf geschützte Web-APIs zugreifen:

> [!div class="nextstepaction"]
> [Szenario: Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md)
