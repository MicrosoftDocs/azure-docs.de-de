---
title: Vorgehensweise beim Arbeiten mit dem .NET Back-End Server SDK für Mobile Apps | Microsoft Docs
description: Informationen zum Arbeiten mit der .NET-Back-End-Server-SDK für Azure App Service Mobile Apps.
keywords: App Service, Azure App Service, mobile App, mobiler Service, skalieren, skalierbar, App-Bereitstellung, Azure-App-Bereitstellung
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 195a2dd88f443120f337ba441358389f0dc290f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119509"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

In diesem Thema wird das Verwenden des .NET-Back-End-Server-SDKs in Azure App Service Mobile Apps-Szenarien veranschaulicht. Das Azure-SDK für Mobile Apps erleichtert Ihnen die Arbeit mit mobilen Clients aus der ASP.NET-Anwendung.

> [!TIP]
> Das [.NET-Server-SDK für Azure Mobile Apps][2] ist als Open Source auf GitHub verfügbar. Das Repository enthält sämtlichen Quellcode, z.B. die gesamte SDK-Komponententest-Suite und einige Beispielprojekte.
>
>

## <a name="reference-documentation"></a>Referenzdokumentation
Die Referenzdokumentation für das Server-SDK finden Sie hier: [Azure Mobile Apps – .NET-Referenz][1]

## <a name="create-app"></a>Vorgehensweise: Erstellen eines .NET Mobile App-Back-Ends
Wenn Sie ein neues Projekt beginnen, können Sie entweder über das [Azure-Portal] oder mit Visual Studio eine App Service-Anwendung erstellen. Sie können die App Service-Anwendung lokal ausführen oder das Projekt in der cloudbasierten mobilen App Service-App veröffentlichen.

Wenn Sie einem vorhandenen Projekt mobile Funktionen hinzufügen, helfen Ihnen die Informationen im Abschnitt [Herunterladen und Initialisieren des SDK](#install-sdk) weiter.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Erstellen eines .NET-Back-Ends mithilfe des Azure-Portals
Folgen Sie zum Erstellen eines mobilen App Service-Back-Ends entweder der Anleitung im [Schnellstart-Tutorial][3], oder führen Sie diese Schritte aus:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wählen Sie auf dem Blatt *Erste Schritte* unter **Erstellen einer Tabellen-API** die Option **C#** als **Back-End-Sprache**. Klicken Sie auf **Herunterladen**, extrahieren Sie die komprimierten Projektdateien auf Ihrem lokalen Computer, und öffnen Sie die Projektmappe in Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Erstellen eines .NET-Back-Ends mithilfe von Visual Studio 2017

Installieren Sie die Azure-Workload über den Visual Studio-Installer, um im Azure Mobile Apps-Projekt aus Visual Studio zu veröffentlichen. Erstellen Sie nach dem Installieren des SDK mit den folgenden Schritten eine ASP.NET-Anwendung:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über **Datei** > **Neu** > **Projekt...** ).
2. Erweitern Sie **Visual C#** , und wählen Sie **Web** aus.
3. Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** aus.
4. Geben Sie den Projektnamen ein. Klicken Sie dann auf **OK**.
5. Wählen Sie aus der Vorlagenliste **Azure Mobile App** aus.
6. Klicken Sie auf **OK**, um die Projektmappe zu erstellen.
7. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Veröffentlichen...** . Wählen Sie dann **App Service** als Ziel für die Veröffentlichung.
8. Folgen Sie den Anweisungen zur Authentifizierung, und wählen Sie einen neuen oder bestehenden Azure App Service zur Veröffentlichung aus.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Erstellen eines .NET-Back-Ends mithilfe von Visual Studio 2015

Installieren Sie das [Azure SDK für .NET][4] (Version 2.9.0 oder höher), um in Visual Studio ein Azure Mobile Apps-Projekt zu erstellen. Erstellen Sie nach dem Installieren des SDK mit den folgenden Schritten eine ASP.NET-Anwendung:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über **Datei** > **Neu** > **Projekt...** ).
2. Erweitern Sie **Vorlagen** > **Visual C#** , und wählen Sie **Web** aus.
3. Wählen Sie **ASP.NET-Webanwendung**aus.
4. Geben Sie den Projektnamen ein. Klicken Sie dann auf **OK**.
5. Wählen Sie unter *ASP.NET 4.5.2-Vorlagen* die Option **Azure Mobile App** aus. Aktivieren Sie **In der Cloud hosten** , um ein mobiles Back-End in der Cloud zu erstellen, in der Sie dieses Projekt veröffentlichen können.
6. Klicken Sie auf **OK**.

## <a name="install-sdk"></a>Vorgehensweise: Herunterladen und Initialisieren des SDK
Das SDK steht unter [NuGet.org]zur Verfügung. Dieses Paket enthält die Basisfunktionalität, die für den Einstieg in das SDK erforderlich ist. Um das SDK zu initialisieren, müssen Sie Aktionen auf dem **HttpConfiguration** -Objekt ausführen.

### <a name="install-the-sdk"></a>Installieren des SDKs
Um das SDK zu installieren, klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, wählen **NuGet-Pakete verwalten**, suchen nach dem [Microsoft.Azure.Mobile.Server]-Paket und klicken dann auf **Installieren**.

### <a name="server-project-setup"></a> Initialisieren des Serverprojekts
Ein .NET-Back-End-Server-Projekt wird wie andere ASP.NET-Projekte durch das Einschließen einer OWIN-Startklasse initialisiert. Stellen Sie sicher, dass Sie auf das NuGet-Paket `Microsoft.Owin.Host.SystemWeb`verwiesen haben. Um diese Klasse in Visual Studio hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Serverprojekt und wählen **Hinzufügen** >
**Neues Element** und dann **Web** > **Allgemein** > **OWIN-Startklasse** aus.  Eine Klasse mit dem folgenden Attribut wird generiert:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Verwenden Sie in der `Configuration()` -Methode Ihrer OWIN-Startklasse ein **HttpConfiguration** -Objekt, um die Azure Mobile Apps-Umgebung zu konfigurieren.
Im folgenden Beispiel wird das Serverprojekt ohne zusätzliche Funktionen initialisiert:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Um einzelne Funktionen zu aktivieren, müssen Sie die Erweiterungsmethoden im **MobileAppConfiguration**-Objekt vor dem Aufruf von **ApplyTo** aufrufen. Mit dem folgenden Code werden beispielsweise während der Initialisierung die Standardrouten für alle API-Controller hinzugefügt, die über das `[MobileAppController]` -Attribut verfügen:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Beim Serverschnellstart aus dem Azure-Portal wird **UseDefaultConfiguration()** aufgerufen. Dies entspricht der folgenden Konfiguration:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Die verwendeten Erweiterungsmethoden lauten:

* Mit `AddMobileAppHomeController()` wird die Azure Mobile Apps-Standardstartseite bereitgestellt.
* Mit `MapApiControllers()` werden benutzerdefinierte API-Funktionen für WebAPI-Controller bereitgestellt, die mit dem Attribut `[MobileAppController]` versehen sind.
* Mit `AddTables()` wird eine Zuordnung der `/tables`-Endpunkte für die Tabellencontroller bereitgestellt.
* `AddTablesWithEntityFramework()` ist eine Kurznotation zum Zuordnen der `/tables`-Endpunkte mit Entity Framework-basierten Controllern.
* Mit `AddPushNotifications()` wird eine einfache Methode zum Registrieren von Geräten für Notification Hubs bereitgestellt.
* `MapLegacyCrossDomainController()` werden CORS-Standardheader für die lokale Entwicklung bereitgestellt.

### <a name="sdk-extensions"></a>SDK-Erweiterungen
Die folgenden NuGet-basierten Erweiterungspakete bieten verschiedene mobile Funktionen, die von der Anwendung verwendet werden können. Sie aktivieren mithilfe des **MobileAppConfiguration** -Objekts Erweiterungen während der Initialisierung.

* [Microsoft.Azure.Mobile.Server.Quickstart] Unterstützt die grundlegende Einrichtung von Mobile Apps. Wird der Konfiguration durch das Aufrufen der **UseDefaultConfiguration**-Erweiterungsmethode während der Initialisierung hinzugefügt. Diese Erweiterung umfasst die folgenden Erweiterungstypen: Notifications-, Authentication-, Entity-, Tables-, CrossDomain- und Home-Pakete. Dieses Paket wird vom Mobile Apps-Schnellstart verwendet, der im Azure-Portal verfügbar ist.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) : Implementiert die Standardseite *Diese mobile App ist betriebsbereit* für den Websitestamm. Wird durch Aufrufen der **AddMobileAppHomeController** -Erweiterungsmethode zur Konfiguration hinzugefügt.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) : Enthält Klassen zum Arbeiten mit Daten und richtet die Datenpipeline ein. Wird durch Aufrufen der **AddTables** -Erweiterungsmethode zur Konfiguration hinzugefügt.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) : Ermöglicht dem Entity Framework, auf Daten in der SQL-Datenbank zuzugreifen. Wird durch Aufrufen der **AddTablesWithEntityFramework** -Erweiterungsmethode zur Konfiguration hinzugefügt.
* [Microsoft.Azure.Mobile.Server.Authentication] : Ermöglicht die Authentifizierung und richtet die OWIN-Middleware zum Überprüfen von Token ein. Wird der Konfiguration durch das Aufrufen der Erweiterungsmethoden **AddAppServiceAuthentication** und **IAppBuilder**.**UseAppServiceAuthentication** hinzugefügt.
* [Microsoft.Azure.Mobile.Server.Notifications] : Aktiviert Pushbenachrichtigungen und definiert einen Endpunkt für die Pushregistrierung. Wird durch Aufrufen der **AddPushNotifications** -Erweiterungsmethode zur Konfiguration hinzugefügt.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) : Erstellt einen Controller, der Daten aus Ihrer mobilen App auf älteren Webbrowsern bereitstellt. Wird der Konfiguration durch Aufrufen der **MapLegacyCrossDomainController**-Erweiterungsmethode hinzugefügt.
* Mit [Microsoft.Azure.Mobile.Server.Login] wird die AppServiceLoginHandler.CreateToken()-Methode bereitgestellt. Dies ist eine statische Methode, die für Szenarien mit benutzerdefinierter Authentifizierung verwendet wird.

## <a name="publish-server-project"></a>Vorgehensweise: Veröffentlichen des Serverprojekts
In diesem Abschnitt erfahren Sie, wie Sie Ihr .NET-Back-End-Projekt über Visual Studio veröffentlichen. Sie können Ihr Back-End-Projekt auch über [Git](../app-service/deploy-local-git.md) oder eine der anderen dort verfügbaren Methoden bereitstellen.

1. Erstellen Sie das Projekt in Visual Studio neu, um die NuGet-Pakete wiederherzustellen.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**. Bei der erstmaligen Veröffentlichung müssen Sie ein Veröffentlichungsprofil definieren. Wenn Sie bereits ein Profil definiert haben, können Sie es auswählen und auf **Veröffentlichen** klicken.
3. Wenn Sie zum Auswählen eines Veröffentlichungsziels aufgefordert werden, klicken Sie auf **Microsoft Azure App Service** > **Weiter**, und melden Sie sich dann bei Bedarf mit Ihren Azure-Anmeldeinformationen an.
   Visual Studio lädt die Veröffentlichungseinstellungen direkt von Azure herunter und speichert sie sicher.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Wählen Sie Ihr **Abonnement** und unter **Ansicht** die Option **Ressourcentyp** aus, erweitern Sie **Mobile App**, klicken Sie auf das Back-End Ihrer mobilen App, und klicken Sie anschließend auf **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Überprüfen Sie die Angaben des Veröffentlichungsprofils, und klicken Sie auf **Veröffentlichen**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Wenn das mobile App-Back-End erfolgreich veröffentlicht wurde, wird eine Startseite angezeigt.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Vorgehensweise: Definieren eines Tabellencontrollers
Definieren Sie einen Tabellencontroller, um eine SQL-Tabelle für mobile Clients verfügbar zu machen.  Zum Konfigurieren eines Tabellencontrollers sind drei Schritte erforderlich:

1. Erstellen Sie eine DTO-Klasse (Data Transfer Object, Datenübertragungsobjekt).
2. Konfigurieren Sie einen Tabellenverweis in der Mobile-DbContext-Klasse.
3. Erstellen Sie einen Tabellencontroller.

Ein Datenübertragungsobjekt (DTO) ist ein einfaches C#-Objekt, das von `EntityData`erbt.  Beispiel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Das DTO wird zum Definieren der Tabelle in der SQL-Datenbank verwendet.  Fügen Sie dem verwendeten DbContext-Element zum Erstellen eine `DbSet<>` -Eigenschaft hinzu.  In der Standardprojektvorlage für Azure Mobile Apps hat DbContext die Bezeichnung `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Wenn Sie das Azure SDK installiert haben, können Sie jetzt wie folgt einen Tabellencontroller als Vorlage erstellen:

1. Klicken Sie mit der rechten Maustaste auf den Ordner „Controllers“, und wählen Sie **Hinzufügen** > **Controller...** .
2. Wählen Sie die Option **Azure Mobile Apps-Tabellencontroller**, und klicken Sie dann auf **Hinzufügen**.
3. Im Dialogfeld **Controller hinzufügen** :
   * Wählen Sie in der Dropdownliste **Modellklasse** Ihr neues DTO-Element aus.
   * Wählen Sie in der Dropdownliste **DbContext** die Mobile Service-Klasse „DbContext“ aus.
   * Der Controllername wird für Sie erstellt.
4. Klicken Sie auf **Hinzufügen**.

Das Schnellstart-Serverprojekt enthält ein Beispiel für einen einfachen **TodoItemController**.

### <a name="adjust-pagesize"></a>Vorgehensweise: Anpassen der Größe der Tabellenauslagerungsdatei
Azure Mobile Apps gibt standardmäßig 50 Datensätze pro Anforderung zurück.  Mit der Auslagerung wird sichergestellt, dass der Client seinen UI-Thread und den Server nicht zu lange auslastet, um eine gute Benutzerfunktionalität zu gewährleisten. Erhöhen Sie zum Ändern der Tabellenauslagerungsgröße die „zulässige Abfragegröße“ auf der Serverseite und die Auslagerungsgröße auf der Clientseite. Die „zulässige Abfragegröße“ auf der Serverseite wird mit dem Attribut `EnableQuery` angepasst:

    [EnableQuery(PageSize = 500)]

Stellen Sie sicher, dass der PageSize-Wert mindestens der Größe entspricht, die vom Client angefordert wird.  Weitere Informationen zum Ändern der Größe der Clientauslagerungsdatei finden Sie in der Clientdokumentation.

## <a name="how-to-define-a-custom-api-controller"></a>Gewusst wie: Definieren eines benutzerdefinierten API-Controllers
Der benutzerdefinierte API-Controller bietet Grundfunktionen für Ihr Mobile App-Back-End, indem ein Endpunkt verfügbar gemacht wird. Mit dem [MobileAppController]-Attribut können Sie einen mobilspezifischen API-Controller registrieren. Mit dem Attribut `MobileAppController` wird die Route registriert, das JSON-Serialisierungsprogramm für Mobile Apps eingerichtet und die [Clientversionsüberprüfung](app-service-mobile-client-and-server-versioning.md)aktiviert.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner „Controller“, und klicken Sie dann auf **Hinzufügen**  > **Controller**. Wählen Sie **Web-API 2-Controller&mdash;Empty**, und klicken Sie auf **Hinzufügen**.
2. Geben Sie einen **Controllernamen** an, z.B. `CustomController`, und klicken Sie auf **Hinzufügen**.
3. Fügen Sie in der neuen Controller-Klassendatei die folgende using-Anweisung hinzu:

        using Microsoft.Azure.Mobile.Server.Config;
4. Wenden Sie das **[MobileAppController]** -Attribut wie im folgenden Beispiel auf die Definition der API-Controllerklasse an:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. Fügen Sie in der Datei „App_Start/Startup.MobileApp.cs“ einen Aufruf der **MapApiControllers**-Erweiterungsmethode wie im folgenden Beispiel hinzu:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Sie können anstelle von `MapApiControllers()` auch die `UseDefaultConfiguration()`-Erweiterungsmethode verwenden. Clients haben noch immer Zugriff auf alle Controller ohne angewendetes **MobileAppControllerAttribute**. Die Controller werden aber unter Umständen nicht korrekt von Clients genutzt, die ein Client-SDK für mobile Apps verwenden.

## <a name="how-to-work-with-authentication"></a>Gewusst wie: Verwenden der Authentifizierung
Azure Mobile Apps verwendet die App Service-Authentifizierung/-Autorisierung, um das mobile Back-End zu schützen.  In diesem Abschnitt erfahren Sie, wie Sie die folgenden authentifizierungsbezogenen Aufgaben in Ihrem .NET-Back-End-Serverprojekt ausführen:

* [Vorgehensweise: Hinzufügen der Authentifizierung zu einem Serverprojekt](#add-auth)
* [Vorgehensweise: Verwenden einer benutzerdefinierten Authentifizierung für Ihre Anwendung](#custom-auth)
* [Vorgehensweise: Abrufen von Informationen zu authentifizierten Benutzern](#user-info)
* [Vorgehensweise: Einschränken des Datenzugriffs für autorisierte Benutzer](#authorize)

### <a name="add-auth"></a>Vorgehensweise: Hinzufügen der Authentifizierung zu einem Serverprojekt
Sie können dem Serverprojekt eine Authentifizierung hinzufügen, indem Sie das **MobileAppConfiguration** -Objekt erweitern und OWIN-Middleware konfigurieren. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart] -Paket installieren und die **UseDefaultConfiguration** -Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Installieren Sie in Visual Studio das [Microsoft.Azure.Mobile.Server.Authentication] -Paket.
2. Fügen Sie in der Projektdatei "Startup.cs" die folgende Codezeile am Anfang der **Configuration** -Methode hinzu:

        app.UseAppServiceAuthentication(config);

    Mit dieser OWIN-Middleware-Komponente werden Token überprüft, die vom zugeordneten App Service-Gateway ausgestellt werden.
3. Fügen Sie jedem Controller oder jeder Methode das `[Authorize]` -Attribut hinzu, falls dafür eine Authentifizierung erforderlich ist.

Informationen zum Authentifizieren von Clients bei Ihrem Mobile Apps-Back-End finden Sie unter [Hinzufügen von Authentifizierung zur App](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Vorgehensweise: Verwenden einer benutzerdefinierten Authentifizierung für Ihre Anwendung
> [!IMPORTANT]
> Um eine benutzerdefinierte Authentifizierung zu ermöglichen, müssen Sie zuerst die App Service-Authentifizierung aktivieren, ohne einen Anbieter für Ihren App Service im Azure-Portal auszuwählen. Dadurch wird die Umgebungsvariable „WEBSITE_AUTH_SIGNING_KEY“ aktiviert, wenn sie gehostet wird.
> 
> 
> Sie können auch eigene Anmeldesysteme implementieren, wenn Sie keinen der Authentifizierungs-/Autorisierungsanbieter von App Service nutzen möchten. Installieren Sie das [Microsoft.Azure.Mobile.Server.Login]-Paket als Unterstützung bei der Generierung des Authentifizierungstokens.  Geben Sie eigenen Code zum Überprüfen der Benutzeranmeldeinformationen an. Sie können beispielsweise eine Datenbank auf Kennwörter mit Salt und Hash überprüfen. Im folgenden Beispiel übernimmt die `isValidAssertion()`-Methode diese Überprüfungen. Sie wird an anderer Stelle definiert.

Die benutzerdefinierte Authentifizierung wird verfügbar gemacht, indem ein ApiController-Element erstellt und die Aktionen `register` und `login` verfügbar gemacht werden. Der Client sollte eine benutzerdefinierte Benutzeroberfläche verwenden, um die Informationen vom Benutzer zu sammeln.  Die Informationen werden dann per HTTP POST-Standardaufruf an die API übermittelt. Wenn der Server die Assertion überprüft, wird mit der `AppServiceLoginHandler.CreateToken()` -Methode ein Token ausgegeben.  Für das ApiController-Element sollte das Attribut `[MobileAppController]` **nicht** verwendet werden.

`login` -Beispielaktion:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

Im vorherigen Beispiel sind LoginResult und LoginResultUser serialisierbare Objekte, mit denen die erforderlichen Eigenschaften verfügbar gemacht werden. Der Client erwartet, dass Anmeldeantworten als JSON-Objekte im folgenden Format zurückgegeben werden:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Die `AppServiceLoginHandler.CreateToken()`-Methode enthält die Parameter *audience* und *issuer*. Beide Parameter werden mithilfe des HTTPS-Schemas auf die URL des Anwendungsstammverzeichnisses festgelegt. Für *secretKey* muss der Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden. Verteilen Sie den Signaturschlüssel nicht an einen Client, da er zum Erstellen von Schlüsseln und Annehmen der Identität von Benutzern verwendet werden kann. Sie können den in App Service gehosteten Signaturschlüssel abrufen, indem Sie auf die Umgebungsvariable *WEBSITE\_AUTH\_SIGNING\_KEY* verweisen. Falls er in einem lokalen Debuggingkontext erforderlich ist, befolgen Sie die Anweisungen im Abschnitt [Lokales Debuggen mit Authentifizierung](#local-debug) , um den Schlüssel abzurufen und als Anwendungseinstellung zu speichern.

Das ausgestellte Token kann auch andere Ansprüche und ein Ablaufdatum enthalten.  Das ausgestellte Token muss mindestens den Anspruch „Antragsteller“ (**sub**) enthalten.

Sie können die `loginAsync()` -Standardclientmethode unterstützen, indem Sie für die Authentifizierungsroute eine Überladung durchführen.  Wenn der Client `client.loginAsync('custom');` für die Anmeldung aufruft, muss Ihre Route `/.auth/login/custom` lauten.  Sie können die Route für den benutzerdefinierten Authentifizierungscontroller mit `MapHttpRoute()`festlegen:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Mit dem `loginAsync()`-Ansatz wird sichergestellt, dass das Authentifizierungstoken an jeden nachfolgenden Aufruf des Diensts angefügt wird.
>
>

### <a name="user-info"></a>Vorgehensweise: Abrufen von Informationen zu authentifizierten Benutzern
Wenn ein Benutzer von App Service authentifiziert wird, können Sie in Ihrem .NET-Back-End-Code auf die zugewiesene Benutzer-ID sowie auf andere Informationen zugreifen. Die Benutzerinformationen können zum Treffen von Autorisierungsentscheidungen auf dem Back-End verwendet werden. Mit dem folgenden Code wird die Benutzer-ID abgerufen, die einer Anforderung zugeordnet ist:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Die SID leitet sich von der anbieterspezifischen Benutzer-ID ab und ist für einen bestimmten Benutzer und Anmeldeanbieter statisch.  Die SID für ungültige Authentifizierungstoken ist 0.

App Service ermöglicht auch die Anforderung bestimmter Ansprüche von Ihrem Anmeldeanbieter. Jeder Identitätsanbieter kann mit dem Identitätsanbieter-SDK weitere Informationen bereitstellen.  Beispielsweise können Sie die Facebook-Graph-API für Informationen zu Freunden verwenden.  Sie können Ansprüche angeben, die auf dem Anbieterblatt im Azure-Portal angefordert werden. Einige Ansprüche erfordern zusätzliche Konfigurationsschritte für den Identitätsanbieter.

Der folgende Code ruft die Erweiterungsmethode **GetAppServiceIdentityAsync** auf, um die Anmeldeinformationen abzurufen (einschließlich des Zugriffstokens, das für Anforderungen an die Facebook-Graph-API erforderlich ist):

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Fügen Sie eine using-Anweisung für `System.Security.Principal` hinzu, um die **GetAppServiceIdentityAsync** -Erweiterungsmethode bereitzustellen.

### <a name="authorize"></a>Vorgehensweise: Einschränken des Datenzugriffs für autorisierte Benutzer
Im vorherigen Abschnitt wurde gezeigt, wie die Benutzer-ID eines authentifizierten Benutzers abgerufen wird. Sie können den Zugriff auf Daten und andere Ressourcen auf der Grundlage dieses Werts einschränken. Beispielsweise ist das Hinzufügen einer userId-Spalte zu Tabellen und das Filtern der Abfrageergebnisse nach der Benutzer-ID eine einfache Möglichkeit, die zurückgegebenen Daten nur auf autorisierte Benutzer zu beschränken. Der folgende Code gibt nur Datenzeilen zurück, wenn die SID mit dem Wert in der Spalte „UserId“ für die Tabelle „TodoItem“ übereinstimmt:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Mit der `Query()`-Methode wird ein `IQueryable`-Element zurückgegeben, das per LINQ für die Behandlung der Filterung geändert werden kann.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Gewusst wie: Hinzufügen von Pushbenachrichtigungen zu einem Serverprojekt
Sie können dem Serverprojekt Pushbenachrichtigungen hinzufügen, indem Sie das **MobileAppConfiguration** -Objekt erweitern und einen Notification Hubs-Client erstellen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, klicken Sie auf **NuGet-Pakete verwalten**, und suchen Sie nach `Microsoft.Azure.Mobile.Server.Notifications`. Klicken Sie anschließend auf **Installieren**.
2. Wiederholen Sie diesen Schritt zum Installieren des `Microsoft.Azure.NotificationHubs` -Pakets, das die Notification Hubs-Clientbibliothek enthält.
3. Fügen Sie in „App_Start/Startup.MobileApp.cs“ einen Aufruf der **AddPushNotifications()** -Erweiterungsmethode während der Initialisierung hinzu:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Fügen Sie folgenden Code hinzu, der einen Notification Hubs-Client erstellt:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Sie können den Notification Hubs-Client nun zum Senden von Pushbenachrichtigungen an registrierte Geräte verwenden. Weitere Informationen finden Sie unter [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-ios-get-started-push.md). Weitere Informationen zu Notification Hubs finden Sie unter [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Vorgehensweise: Ermöglichen von zielgerichteten Pushvorgängen mit Tags
Mit Notification Hubs können Sie zielgerichtete Benachrichtigungen an spezielle Registrierungen senden, indem Sie Tags verwenden. Mehrere Tags werden automatisch erstellt:

* Mit der Installations-ID wird ein bestimmtes Gerät identifiziert.
* Mit der Benutzer-ID, die auf der authentifizierten SID basiert, wird ein bestimmter Benutzer identifiziert.

Der Zugriff auf die Installations-ID ist über die **installationId**-Eigenschaft von **MobileServiceClient** möglich.  Im folgenden Beispiel wird veranschaulicht, wie Sie eine Installations-ID verwenden, um ein Tag einer bestimmten Installation in Notification Hubs hinzuzufügen:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Alle Tags, die vom Client während der Registrierung von Pushbenachrichtigungen bereitgestellt werden, werden vom Back-End beim Erstellen der Installation ignoriert. Damit ein Client der Installation Tags hinzufügen kann, müssen Sie eine benutzerdefinierte API erstellen, die Tags nach dem obigen Muster hinzufügt.

Eine Beispiel hierfür finden Sie unter [Client-added push notification tags][5] (Vom Client hinzugefügte Tags für Pushbenachrichtigungen) im abgeschlossenen Schnellstartbeispiel für Mobile App Service-Apps.

## <a name="push-user"></a>Vorgehensweise: Senden von Pushbenachrichtigungen an einen authentifizierten Benutzer
Wenn ein authentifizierter Benutzer für Pushbenachrichtigungen registriert wird, wird der Registrierung automatisch ein Tag mit der Benutzer-ID hinzugefügt. Mit diesem Tag können Sie Pushbenachrichtigungen an alle Geräte senden, die von dieser Person registriert wurden. Mit dem folgenden Code wird die SID des Benutzers abgerufen, der die Anforderung stellt, und an jede Geräteregistrierung für diese Person wird eine Pushbenachrichtigungsvorlage gesendet:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Stellen Sie vor der Registrierung für Pushbenachrichtigungen von einem authentifizierten Client sicher, dass die Authentifizierung abgeschlossen ist. Weitere Informationen finden Sie unter „App Service Mobile Apps completed quickstart for .NET backend“ (Abgeschlossenes Schnellstartbeispiel für Mobile App Service-Apps für .NET-Back-Ends) im Abschnitt [Push to users][6] (Push an Benutzer).

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Gewusst wie: Debuggen und Problembehandlung für das .NET Server SDK
Azure App Service stellt mehrere Debug- und Problembehandlungsverfahren für ASP.NET-Anwendungen bereit.

* [Überwachen eines Azure App Service](../app-service/web-sites-monitor.md)
* [Aktivieren der Diagnoseprotokollierung in Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Problembehandlung für einen Azure App Service in Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Protokollierung
Zum Schreiben in App Service-Diagnoseprotokolle kann das standardmäßige Schreiben von ASP.NET-Ablaufverfolgungen verwendet werden. Um in die Protokolle schreiben zu können, müssen Sie in Ihrem Mobile App-Back-End die Diagnose aktivieren.

Gehen Sie wie folgt vor, um die Diagnose zu aktivieren und in die Protokolle zu schreiben:

1. Führen Sie die unter [Aktivieren der Diagnose](../app-service/troubleshoot-diagnostic-logs.md#enablediag)angegebenen Schritte aus.
2. Fügen Sie Ihrer Codedatei folgende using-Anweisung hinzu:

        using System.Web.Http.Tracing;
3. Erstellen Sie wie folgt einen Ablaufverfolgungs-Writer für Schreibvorgänge aus dem .NET Back-End in die Diagnoseprotokolle:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Veröffentlichen Sie Ihr Serverprojekt neu, und greifen Sie auf das Mobile App-Back-End zu, um den Codepfad mit der Protokollierung auszuführen.
5. Gehen Sie zum Herunterladen und Auswerten der Protokolle wie unter [Vorgehensweise: Herunterladen von Protokollen](../app-service/troubleshoot-diagnostic-logs.md#download) beschrieben vor.

### <a name="local-debug"></a>Lokales Debuggen mit Authentifizierung
Sie können zum Testen von Änderungen Ihre Anwendung vor der Veröffentlichung in der Cloud lokal ausführen. Für die meisten Azure Mobile Apps-Back-Ends drücken Sie in Visual Studio die Taste *F5* . Bei der Verwendung der Authentifizierung gibt es jedoch einige zusätzliche Aspekte zu berücksichtigen.

Sie benötigen eine cloudbasierte mobile App, für die App Service-Authentifizierung/-Autorisierung konfiguriert ist. Für den Client muss als alternativer Anmeldehost zudem der Cloudendpunkt angegeben sein. In der Dokumentation für Ihre Clientplattform sind die genauen Schritte angegeben.

Stellen Sie sicher, dass für Ihr mobiles Back-End [Microsoft.Azure.Mobile.Server.Authentication] installiert ist. Fügen Sie dann in der OWIN-Startklasse Ihrer Anwendung Folgendes hinzu (nach der Anwendung von `MobileAppConfiguration` auf `HttpConfiguration`):

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Im obigen Beispiel müssen die Anwendungseinstellungen *authAudience* und *authIssuer* in der Datei „Web.config“ so konfiguriert werden, dass beide die URL des Anwendungsstammverzeichnisses darstellen. Verwenden Sie hierzu das HTTPS-Schema. Für *authSigningKey* muss der Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden.
Gehen Sie wie folgt vor, um den Signaturschlüssel zu erhalten:

1. Navigieren Sie im [Azure-Portal]
2. Klicken Sie auf **Tools**, **Kudu**, **Los**.
3. Klicken Sie auf der Kudu-Verwaltungswebsite auf **Umgebung**.
4. Suchen Sie nach dem Wert für *WEBSITE\_AUTH\_SIGNING\_KEY*.

Verwenden Sie den Signaturschlüssel für den Parameter *authSigningKey* in Ihrer lokalen Anwendungskonfiguration.  Das mobile Back-End ist jetzt zum Überprüfen von Token während der lokalen Ausführung bereit, die der Client vom cloudbasierten Endpunkt erhält.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
