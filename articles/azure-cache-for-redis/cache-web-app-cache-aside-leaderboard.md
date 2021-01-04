---
title: 'Tutorial: Erstellen einer Web-App (cachefremd) – Azure Cache for Redis'
description: Hier erfahren Sie, wie Sie eine Web-App mit Azure Cache for Redis erstellen, die ein cachefremdes Muster nutzt.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536843"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Tutorial: Erstellen eines cachefremden Leaderboards in ASP.NET

In diesem Tutorial aktualisieren Sie die in [ASP.NET quickstart for Azure Cache for Redis](cache-web-app-howto.md) (ASP.NET-Schnellstart für Azure Cache for Redis) erstellte ASP.NET-Web-App *ContosoTeamStats* , indem Sie ein Leaderboard hinzufügen, das das [cachefremde Muster](/azure/architecture/patterns/cache-aside) mit Azure Cache for Redis verwendet. Die Beispielanwendung zeigt eine Liste von Teamstatistiken aus einer Datenbank und veranschaulicht verschiedene Möglichkeiten, wie Azure Cache for Redis zum Speichern und Abrufen von Daten aus dem Cache verwendet werden kann, um die Leistung zu verbessern. Nach Abschluss des Tutorials verfügen Sie über eine funktionsfähige Web-App, die Lese- und Schreibvorgänge in einer Datenbank durchführt, mit Azure Cache for Redis optimiert ist und in Azure gehostet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Optimieren des Datendurchsatzes und Verringern der Datenbanklast durch Speichern und Abrufen von Daten mithilfe von Azure Cache for Redis
> * Abrufen der fünf besten Teams mithilfe eines sortierten Redis-Satzes
> * Bereitstellen der Azure-Ressourcen für die Anwendung mithilfe einer Resource Manager-Vorlage
> * Veröffentlichen der Anwendung in Azure mithilfe von Visual Studio

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Dieses Tutorial wird an der Stelle fortgesetzt, an der Sie in [ASP.NET quickstart for Azure Cache for Redis](cache-web-app-howto.md) (ASP.NET-Schnellstart für Azure Cache for Redis) aufgehört haben. Sollten Sie den Schnellstart noch nicht absolviert haben, holen Sie dies jetzt nach.
* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    * ASP.NET und Webentwicklung
    * Azure-Entwicklung
    * .NET Desktopentwicklung mit SQL Server Express LocalDB oder [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express)

## <a name="add-a-leaderboard-to-the-project"></a>Hinzufügen einer Bestenliste zum Projekt

In diesem Abschnitt des Tutorials konfigurieren Sie das Projekt *ContosoTeamStats* mit einer Bestenliste, die die Statistiken für Siege, Niederlagen und Unentschieden für eine Liste fiktiver Teams ausweist.

### <a name="add-the-entity-framework-to-the-project"></a>Hinzufügen von Entity Framework zum Projekt

1. Öffnen Sie in Visual Studio die Projektmappe *ContosoTeamStats* , die Sie in [ASP.NET quickstart for Azure Cache for Redis](cache-web-app-howto.md) (ASP.NET-Schnellstart für Azure Cache for Redis) erstellt haben.
2. Klicken Sie auf **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** .
3. Führen Sie den folgenden Befehl im Fenster **Paket-Manager-Konsole** aus, um Entity Framework zu installieren:

    ```powershell
    Install-Package EntityFramework
    ```

Weitere Informationen zu diesem Paket finden Sie auf der [EntityFramework](https://www.nuget.org/packages/EntityFramework/)-NuGet-Seite.

### <a name="add-the-team-model"></a>Hinzufügen des Teammodells

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Modelle** , und wählen Sie **Hinzufügen** > **Klasse** aus.

1. Geben Sie als Klassenname die Zeichenfolge `Team` ein, und klicken Sie anschließend auf **Hinzufügen** .

    ![Modellklasse hinzufügen](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Ersetzen Sie die Anweisungen des Typs `using` (am Anfang der Datei *Team.cs* ) durch folgende `using`-Anweisungen:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Ersetzen Sie die Definition der `Team`-Klasse durch den folgenden Codeausschnitt. Dieser enthält eine aktualisierte Definition für die `Team`-Klasse sowie einige weitere Entity Framework-Hilfsklassen. In diesem Tutorial wird der Code First-Ansatz mit Entity Framework verwendet. Dieser Ansatz ermöglicht Entity Framework das Erstellen der Datenbank aus Ihrem Code. Weitere Informationen zum in diesem Tutorial verwendeten Code First-Ansatz für Entity Framework finden Sie unter [Code First für eine neue Datenbank](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **Web.config** , um sie zu öffnen.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Fügen Sie den folgenden `connectionStrings`-Abschnitt im `configuration`-Abschnitt hinzu. Der Name der Verbindungszeichenfolge muss dem Namen der Entity Framework-Datenbank-Kontextklasse (`TeamContext`) entsprechen.

    Diese Verbindungszeichenfolge setzt voraus, dass Sie die [Voraussetzungen](#prerequisites) erfüllt und SQL Server Express LocalDB installiert haben, das Teil des mit Visual Studio 2019 installierten Workloads *.NET-Desktopentwicklung* ist.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Im folgenden Beispiel wird der neue `connectionStrings`-Abschnitt nach `configSections` im `configuration`-Abschnitt angezeigt:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Hinzufügen von „TeamsController“ und Ansichten

1. Erstellen Sie in Visual Studio das Projekt. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie anschließend **Hinzufügen** > **Controller** aus.

1. Wählen Sie **MVC 5-Controller mit Ansichten per Entity Framework** aus, und klicken Sie auf **Hinzufügen** . Sollte nach dem Klicken auf **Hinzufügen** eine Fehlermeldung angezeigt werden, vergewissern Sie sich, dass das Projekt erstellt wurde.

    ![Controllerklasse hinzufügen](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Wählen Sie in der Dropdownliste **Modellklasse** die Option **Team (ContosoTeamStats.Models)** aus. Wählen Sie in der Dropdownliste **Datenkontextklasse** die Option **TeamContext (ContosoTeamStats.Models)** aus. Geben Sie im Textfeld **Controllername** die Zeichenfolge `TeamsController` ein (sofern sie nicht automatisch eingefügt wird). Klicken Sie auf **Hinzufügen** , um die Controllerklasse zu erstellen und die Standardansichten hinzuzufügen.

    ![Controller konfigurieren](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. Erweitern Sie im **Projektmappen-Explorer** den Knoten **Global.asax** , und doppelklicken Sie anschließend auf **Global.asax.cs** , um die Datei zu öffnen.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Fügen Sie den anderen `using`-Anweisungen am Anfang der Datei die beiden folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Fügen Sie am Ende der `Application_Start` -Methode folgenden Code hinzu:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Knoten `App_Start`, und doppelklicken Sie auf `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. Ersetzen Sie, wie im folgenden Beispiel gezeigt, in der `RegisterRoutes`-Methode `controller = "Home"` in der Route `Default` durch `controller = "Teams"`:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Konfigurieren der Layoutansicht

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** , erweitern Sie den Ordner **Freigegeben** , und doppelklicken Sie auf **_Layout.cshtml** . 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Ändern Sie den Inhalt des `title`-Elements, und ersetzen Sie `My ASP.NET Application` durch `Contoso Team Stats`, wie im folgenden Beispiel gezeigt:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. Fügen Sie im Abschnitt `body` die folgende neue `Html.ActionLink`-Anweisung für *Contoso Team Stats* unmittelbar unterhalb des Links für *Azure Cache for Redis Test* hinzu.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Änderungen am Code](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Drücken Sie **STRG+F5** , um die Anwendung zu erstellen und auszuführen. Diese Version der Anwendung liest die Ergebnisse direkt aus der Datenbank. Die Aktionen **Neu erstellen** , **Bearbeiten** , **Details** und **Löschen** wurden der Anwendung aufgrund des Gerüsts **MVC 5-Controller mit Ansichten per Entity Framework** automatisch hinzugefügt. Im nächsten Abschnitt des Tutorials fügen Sie Azure Cache for Redis hinzu, um den Datenzugriff zu optimieren und zusätzliche Features für die Anwendung bereitzustellen.

    ![Starter-Anwendung](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Konfigurieren der App für Azure Cache for Redis

In diesem Abschnitt des Tutorials konfigurieren Sie die Beispielanwendung zum Speichern und Abrufen von Contoso-Teamstatistiken aus einer Azure Cache for Redis-Instanz unter Verwendung des Cacheclients [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Hinzufügen einer Cacheverbindung zum TeamsController

Sie haben das Clientbibliothekspaket *StackExchange.Redis* bereits im Schnellstart installiert. Sie haben auch bereits die App-Einstellung *CacheConnection* so konfiguriert, dass sie lokal und mit dem veröffentlichten App Service verwendet wird. Verwenden Sie dieselben Clientbibliotheks- und *CacheConnection* -Informationen in *TeamsController* .

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Controller** , und doppelklicken Sie auf **TeamsController.cs** , um die Datei zu öffnen.

    ![Teamcontroller](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Fügen Sie **TeamsController.cs** die beiden folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Fügen Sie der `TeamsController`-Klasse die beiden folgenden Eigenschaften hinzu:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Aktualisieren von TeamsController zum Lesen von Daten aus dem Cache oder der Datenbank

In diesem Beispiel können Teamstatistikdaten aus der Datenbank oder aus dem Cache abgerufen werden. Teamstatistikdaten werden im Cache als serialisierte Daten vom Typ `List<Team>`sowie als sortierter Satz mit Redis-Datentypen gespeichert. Beim Abrufen von Elementen aus einem sortierten Satz können Sie einige oder alle Elemente abrufen oder bestimmte Elemente abfragen. Im vorliegenden Beispiel werden die fünf besten Teams aus dem sortierten Satz abgefragt und nach der Anzahl der Siege sortiert.

Die Teamstatistiken müssen zur Verwendung von Azure Cache for Redis nicht in mehreren Formaten im Cache gespeichert werden. Dieses Tutorial verwendet mehrere Formate, um verschiedene Möglichkeiten und Datentypen für die Zwischenspeicherung von Daten zu veranschaulichen.

1. Fügen Sie den `using`-Anweisungen am Anfang der Datei `TeamsController.cs` folgende weitere `using`-Anweisungen hinzu:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Ersetzen Sie die aktuelle Implementierung der `public ActionResult Index()`-Methode durch die folgende Implementierung:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Fügen Sie der `TeamsController`-Klasse die drei folgenden Methoden hinzu, um die Aktionstypen `playGames`, `clearCache` und `rebuildDB` aus der im vorherigen Codeausschnitt hinzugefügten switch-Anweisung zu implementieren.

    Die `PlayGames` -Methode aktualisiert die Teamstatistik durch eine simulierte Spielesaison, speichert die Ergebnisse in der Datenbank und löscht die inzwischen veralteten Daten aus dem Cache.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Die `RebuildDB` -Methode initialisiert die Datenbank mit dem Standardteamsatz neu, generiert die Statistik für die Teams und löscht die nun veralteten Daten aus dem Cache.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Die `ClearCachedTeams` -Methode entfernt alle zwischengespeicherten Teamstatistikdaten aus dem Cache.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Fügen Sie der `TeamsController` -Klasse die vier folgenden Methoden hinzu, um die verschiedenen Methoden zum Abrufen der Teamstatistik aus dem Cache und der Datenbank zu implementieren. Jede dieser Methoden gibt ein Element des Typs `List<Team>` zurück, das dann in der Ansicht gezeigt wird.

    Die `GetFromDB` -Methode liest die Teamstatistik aus der Datenbank.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    Die `GetFromList`-Methode liest die Teamstatistik aus dem Cache als serialisierte Daten vom Typ `List<Team>`. Wenn die Statistiken nicht im Cache vorhanden sind, tritt ein Cachefehler auf. Bei einem Cachefehler wird die Teamstatistik aus der Datenbank gelesen und dann für die nächste Anforderung im Cache gespeichert. In diesem Beispiel wird die JSON.NET-Serialisierung verwendet, um die .NET-Objekte in und aus dem Cache zu serialisieren. Weitere Informationen finden Sie unter [How to work with .NET objects in Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) (Arbeiten mit .NET-Objekten in Azure Cache for Redis).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    Die `GetFromSortedSet` -Methode liest die Teamstatistik aus einem zwischengespeicherten sortierten Satz. Bei einem Cachefehler wird die Teamstatistik aus der Datenbank gelesen und im Cache als sortierter Satz gespeichert.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    Die `GetFromSortedSetTop5`-Methode liest die fünf besten Teams aus dem zwischengespeicherten sortierten Satz. Dabei wird zunächst geprüft, ob der `teamsSortedSet` -Schlüssel im Cache vorhanden ist. Ist der Schlüssel nicht vorhanden, wird die `GetFromSortedSet` -Methode aufgerufen, um die Teamstatistik zu lesen und im Cache zu speichern. Als Nächstes werden aus dem zwischengespeicherten sortierten Satz die fünf besten Teams abgefragt und zurückgegeben.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Aktualisieren der Create-, Edit- und Delete-Methoden für den Cache

Der im Rahmen dieses Beispiels generierte Gerüstcode enthält Methoden zum Hinzufügen, Bearbeiten und Löschen. Sobald ein Team hinzugefügt, bearbeitet oder entfernt wurde, sind die Daten im Cache veraltet. In diesem Abschnitt werden diese drei Methoden geändert, um die zwischengespeicherten Teams zu löschen, damit der Cache aktualisiert wird.

1. Navigieren Sie in der `TeamsController`-Klasse zur `Create(Team team)`-Methode. Fügen Sie der `ClearCachedTeams`-Methode einen Aufruf hinzu, wie im folgenden Beispiel zu sehen:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Navigieren Sie in der `TeamsController`-Klasse zur `Edit(Team team)`-Methode. Fügen Sie der `ClearCachedTeams`-Methode einen Aufruf hinzu, wie im folgenden Beispiel zu sehen:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Navigieren Sie in der `TeamsController`-Klasse zur `DeleteConfirmed(int id)`-Methode. Fügen Sie der `ClearCachedTeams`-Methode einen Aufruf hinzu, wie im folgenden Beispiel zu sehen:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Hinzufügen von Zwischenspeichermethoden zur Ansicht „Teams Index“

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten** , erweitern Sie den Ordner **Teams** , und doppelklicken Sie auf **Index.cshtml** .

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Suchen Sie im oberen Bereich der Datei nach dem folgenden Absatzelement:

    ![Aktionentabelle](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Dieser Link erstellt ein neues Team. Ersetzen Sie das Absatzelement durch die folgende Tabelle. Die Tabelle enthält Aktionslinks zum Erstellen eines neuen Teams, zum Spielen einer neuen Saison, zum Löschen des Cache, zum Abrufen der Teams aus dem Cache in unterschiedlichen Formaten, zum Abrufen der Teams aus der Datenbank sowie zum Neuerstellen der Datenbank mit frischen Beispieldaten.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Führen Sie einen Bildlauf zum unteren Rand der Datei **Index.cshtml** durch, und fügen Sie das folgende `tr`-Element als letzte Zeile der letzten Tabelle in der Datei hinzu:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    In dieser Zeile wird der Wert `ViewBag.Msg` angezeigt, der einen Statusbericht zum aktuellen Vorgang enthält. `ViewBag.Msg` wird festgelegt, wenn Sie auf einen der Aktionslinks aus dem vorherigen Schritt klicken.

    ![Statusmeldung](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Drücken Sie **F6** , um das Projekt zu erstellen.

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

Führen Sie die Anwendung lokal auf Ihrem Computer aus, um die Funktionalität zu überprüfen, die zur Unterstützung der Teams hinzugefügt wurde.

In diesem Test werden Anwendung und Datenbank lokal ausgeführt. Azure Cache for Redis wird jedoch remote in Azure gehostet. Daher bleibt die Leistung des Caches geringfügig hinter der der Datenbank zurück. Die beste Leistung wird erzielt, wenn sich die Clientanwendung und die Azure Cache for Redis-Instanz am gleichen Ort befinden. Im nächsten Abschnitt stellen Sie alle Ressourcen in Azure bereit, um zu prüfen, wie sich die Leistung mithilfe des Caches verbessert.

So führen Sie die App lokal aus

1. Drücken Sie **STRG+F5** , um die Anwendung auszuführen.

    ![Lokal ausgeführte App](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Testen Sie die neuen Methoden, die der Ansicht hinzugefügt wurden. Da der Cache bei diesen Tests remote ist, ist die Leistung der Datenbank geringfügig besser als die des Caches.

## <a name="publish-and-run-in-azure"></a>Veröffentlichen und Ausführen in Azure

### <a name="provision-a-database-for-the-app"></a>Bereitstellen einer Datenbank für die App

In diesem Abschnitt stellen Sie eine neue Datenbank in SQL-Datenbank für die App bereit, die in Azure gehostet wird.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) links oben auf **Ressource erstellen** .

1. Klicken Sie auf der Seite **Neu** auf **Datenbanken** > **SQL-Datenbank** .

1. Verwenden Sie die folgenden Einstellungen für die neue SQL-Datenbank:

   | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Datenbankname** | *ContosoTeamsDatabase* | Gültige Datenbanknamen finden Sie unter [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Datenbankbezeichner). |
   | **Abonnement** | *Ihr Abonnement*  | Wählen Sie das gleiche Abonnement, mit dem Sie den Cache erstellt und den App Service gehostet haben. |
   | **Ressourcengruppe**  | *TestResourceGroup* | Klicken Sie auf **Vorhandene verwenden** , und verwenden Sie die gleichen Ressourcengruppe, der Sie Ihren Cache und App Service hinzugefügt haben. |
   | **Quelle auswählen** | **Leere Datenbank** | Beginnen Sie mit einer leeren Datenbank. |

1. Klicken Sie unter **Server** auf **Erforderliche Einstellungen konfigurieren** > **Neuen Server erstellen** , und geben Sie die folgenden Informationen ein. Klicken Sie dann auf die Schaltfläche **Auswählen** :

   | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servername** | Ein global eindeutiger Name | Gültige Servernamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
   | **Serveradministratoranmeldung** | Ein gültiger Name | Gültige Anmeldenamen finden Sie unter [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Datenbankbezeichner). |
   | **Kennwort** | Ein gültiges Kennwort | Ihr Kennwort muss mindestens acht Zeichen umfassen und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen. |
   | **Location** | *USA, Osten* | Wählen Sie die gleiche Region, in der Sie den Cache und App Service erstellt haben. |

1. Klicken Sie auf **An Dashboard anheften** und dann auf **Erstellen** , um die neue Datenbank und den Server zu erstellen.

1. Klicken Sie nach Erstellen der neuen Datenbank auf **Datenbank-Verbindungszeichenfolgen anzeigen** , und kopieren Sie die Verbindungszeichenfolge **ADO.NET** .

    ![Verbindungszeichenfolgen anzeigen](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Navigieren Sie im Azure-Portal zu Ihrem App Service, und klicken Sie unter den Abschnitt „Verbindungszeichenfolgen“ erst auf **Anwendungseinstellungen** und dann auf **Neue Verbindungszeichenfolge hinzufügen** .

1. Fügen Sie eine neue Verbindungszeichenfolge namens *TeamContext* hinzu, die der Entity Framework-Datenbank-Kontextklasse entspricht. Fügen Sie die Verbindungszeichenfolge für die neue Datenbank als Wert ein. Ersetzen Sie unbedingt die folgenden Platzhalter in der Verbindungszeichenfolge, und klicken Sie auf **Speichern** :

    | Platzhalter | Vorgeschlagener Wert |
    | --- | --- |
    | *{Ihr_Benutzername}* | Verwenden Sie die **Serveradministratoranmeldung** für den Server, den Sie gerade erstellt haben. |
    | *{Ihr_Kennwort}* | Verwenden Sie das Kennwort für den Server, den Sie gerade erstellt haben. |

    Wenn Sie den Benutzernamen und das Kennwort als Anwendungseinstellung hinzufügen, sind Ihr Benutzername und Ihr Kennwort nicht in Ihrem Code enthalten. Diese Vorgehensweise hilft, diese Anmeldeinformationen zu schützen.

### <a name="publish-the-application-updates-to-azure"></a>Veröffentlichen von Anwendungsaktualisierungen in Azure

In diesem Schritt des Tutorials veröffentlichen Sie die Anwendungsaktualisierungen in Azure für die Ausführung in der Cloud.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **ContosoTeamStats** , und wählen Sie **Veröffentlichen** .

    ![Veröffentlichen](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Klicken Sie auf **Veröffentlichen** , um das gleiche Veröffentlichungsprofil zu verwenden, das Sie im Schnellstart erstellt haben.

3. Nach der Veröffentlichung startet Visual Studio die App in Ihrem standardmäßigen Webbrowser.

    ![Cache hinzugefügt](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    In der folgenden Tabelle werden die einzelnen Aktionslinks aus der Beispielanwendung beschrieben:

    | Aktion | BESCHREIBUNG |
    | --- | --- |
    | Create New |Erstellt ein neues Team. |
    | Play Season |Spielt eine Spielesaison durch, aktualisiert die Teamstatistik und entfernt veraltete Teamdaten aus dem Cache. |
    | Löschen des Cache |Löscht die Teamstatistik aus dem Cache. |
    | List from Cache |Ruft die Teamstatistik aus dem Cache ab. Bei einem Cachefehler wird die Teamstatistik aus der Datenbank geladen und für den nächsten Zugriff im Cache gespeichert. |
    | Sorted Set from Cache |Ruft die Teamstatistik unter Verwendung eines sortierten Satzes aus dem Cache ab. Bei einem Cachefehler wird die Teamstatistik aus der Datenbank geladen und unter Verwendung eines sortierten Satzes im Cache gespeichert. |
    | Top 5 Teams from Cache |Ruft die fünf besten Teams unter Verwendung eines sortierten Satzes aus dem Cache ab. Bei einem Cachefehler wird die Teamstatistik aus der Datenbank geladen und unter Verwendung eines sortierten Satzes im Cache gespeichert. |
    | Load from DB |Ruft die Teamstatistik aus der Datenbank ab. |
    | Rebuild DB |Erstellt die Datenbank neu und lädt sie mit Beispielteamdaten. |
    | Edit/Details/Delete |Dient zum Bearbeiten eines Teams, zum Anzeigen von Teamdetails bzw. zum Löschen eines Teams. |

Klicken Sie auf einige der Aktionen, und experimentieren Sie mit dem Abrufen der Daten aus unterschiedlichen Quellen. Beachten Sie die jeweils unterschiedliche Vorgangsdauer bei den verschiedenen Methoden zum Abrufen von Daten aus der Datenbank und dem Cache.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Beispielanwendung des Tutorials nicht mehr benötigen, können Sie die Azure-Ressourcen löschen, um Kosten und Ressourcen zu sparen. Alle Ihre Ressourcen müssen sich in der gleichen Ressourcengruppe befinden, sodass Sie sie durch Löschen der Ressourcengruppe alle in einem Vorgang löschen können. In den Anweisungen zu diesem Thema wurde eine Ressourcengruppe namens *TestResources* verwendet.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln über das jeweilige Blatt löschen.
>

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen** .
2. Geben Sie im Textfeld **Filterelemente...** den Namen Ihrer Ressourcengruppe ein.
3. Klicken Sie rechts neben Ihrer Ressourcengruppe auf **...** und dann auf **Ressourcengruppe löschen** .

    ![Löschen](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen der entsprechenden Ressourcengruppe ein, und klicken Sie auf **Löschen** .

    Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [How to Scale Azure Cache for Redis](./cache-how-to-scale.md) (Skalieren von Azure Cache for Redis)