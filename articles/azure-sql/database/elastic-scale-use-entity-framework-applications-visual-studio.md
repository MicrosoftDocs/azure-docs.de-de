---
title: Verwenden der Clientbibliothek für elastische Datenbanken mit Entity Framework
description: Verwenden der Clientbibliothek für elastische Datenbanken und von Entity Framework für die Programmierung von Datenbanken
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793397"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Clientbibliothek für elastische Datenbanken mit Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieses Dokument zeigt, welche Änderungen in einer Entity Framework-Anwendung erforderlich sind, damit diese die Funktionen der [Tools für elastische Datenbanken](elastic-scale-introduction.md)nutzen kann. Der Schwerpunkt liegt auf der Erstellung der [Shardzuordnungsverwaltung](elastic-scale-shard-map-management.md) und des [datenabhängigen Routings](elastic-scale-data-dependent-routing.md) mit dem **Code First** -Ansatz von Entity Framework. Das Tutorial [Code First für eine neue Datenbank](/ef/ef6/modeling/code-first/workflows/new-database) für EF wird im gesamten Dokument als Beispiel verwendet. Der zu diesem Dokument gehörige Beispielcode ist Teil der Beispielserie der Tools für elastische Datenbanken in den Visual Studio-Codebeispielen.

## <a name="downloading-and-running-the-sample-code"></a>Herunterladen und Ausführen des Beispielcodes

So laden Sie den Code für diesen Artikel herunter:

* Visual Studio 2012 oder höher wird vorausgesetzt.
* Laden Sie das [Beispiel Elastic DB Tools for Azure SQL – Entity Framework Integration](https://github.com/Azure/elastic-db-tools/) herunter. Entzippen Sie das Beispiel in einem Speicherort Ihrer Wahl.
* Starten Sie Visual Studio.
* Wählen Sie in Visual Studio „Datei“ > „Projekt/Projektmappe öffnen“ aus.
* Navigieren Sie im Dialog **Projekt öffnen** zu dem Beispiel, das Sie heruntergeladen haben, und wählen Sie **EntityFrameworkCodeFirst.sln** aus, um das Beispiel zu öffnen.

Um das Beispiel ausführen zu können, müssen Sie drei leere Datenbanken in Azure SQL-Datenbank erstellen:

* Die Datenbank "Shard Map Manager"
* Die Datenbank "Shard 1"
* Die Datenbank "Shard 2"

Wenn Sie diese Datenbanken erstellt haben, ersetzen Sie die Platzhalter in der Datei **Program.cs** durch Ihren Servernamen, die Datenbanknamen und die Anmeldeinformationen für die Verbindung mit den Datenbanken. Erstellen Sie die Projektmappe in Visual Studio. Visual Studio lädt die erforderlichen NuGet-Pakete für die Clientbibliothek für elastische Datenbanken, Entity Framework und die Behandlung zeitweise auftretender Fehler im Rahmen des Buildprozesses herunter. Stellen Sie sicher, dass das Wiederherstellen von NuGet-Paketen für Ihre Lösung aktiviert ist. Sie können diese Einstellung aktivieren, indem Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf die Projektmappendatei klicken.

## <a name="entity-framework-workflows"></a>Entity Framework-Workflows

Entity Framework-Entwickler verwenden einen der folgenden vier Workflows, um Anwendungen zu erstellen und die Persistenz von Anwendungsobjekten sicherzustellen:

* **Code First (neue Datenbank):** Der EF-Entwickler erstellt das Modell im Anwendungscode, und EF generiert daraus die Datenbank.
* **Code First (vorhandene Datenbank):** Der Entwickler lässt EF den Anwendungscode für das Modell aus einer vorhandenen Datenbank generieren.
* **Model First:** Der EF-Entwickler erstellt das Modell im EF-Designer, und EF erstellt dann aus diesem Modell die Datenbank.
* **Database First:** Der Entwickler verwendet EF-Tools, um das Modell aus einer vorhandenen Datenbank abzuleiten.

Alle diese Ansätze basieren auf der DbContext-Klasse, die Datenbankverbindungen und das Datenbankschema für eine Anwendung transparent verwaltet. Verschiedene Konstruktoren der DbContext-Basisklasse lassen verschiedene Grade der Kontrolle über das Herstellen von Verbindungen, das Datenbank-Bootstrapping und die Schemaerstellung zu. Herausforderungen ergeben sich in erster Linie aus der Tatsache, dass sich die von EF bereitgestellte Verwaltung von Datenbankverbindungen mit der Verbindungsverwaltungsfunktionen der von der Clientbibliothek für elastische Datenbanken bereitgestellten Schnittstellen für das datenabhängige Routing überschneidet.

## <a name="elastic-database-tools-assumptions"></a>Tools für elastische Datenbanken – Annahmen

Begriffsdefinitionen finden Sie unter [Tools für elastische Datenbanken – Glossar](elastic-scale-glossary.md).

Mit der Clientbibliothek für elastische Datenbanken definieren Sie Partitionen für Ihre Anwendungsdaten, die als Shardlets bezeichnet werden. Shardlets werden durch einen Sharding-Schlüssel identifiziert und bestimmten Datenbanken zugeordnet. Eine Anwendung kann so viele Datenbanken wie erforderlich nutzen und die Shardlets verteilen, um genügend Kapazität oder Leistung für die aktuell gegebenen Geschäftsanforderungen bereitzustellen. Die Zuordnung der Sharding-Schlüsselwerte zu den Datenbanken wird in einer Shard-Zuordnung gespeichert, die durch die Client-APIs für elastische Datenbanken bereitgestellt wird. Diese Funktion wird als Shardzuordnungsverwaltung ( **Shard Map Management** , SMM) bezeichnet. Die Shard-Zuordnung fungiert auch als Broker von Datenbankverbindungen für Anforderungen, die einen Sharding-Schlüssel enthalten. Diese Funktion wird als **datenabhängiges Routing** bezeichnet.

Die Shard-Zuordnungsverwaltung schützt den Benutzer vor inkonsistenten Sichten in Shardlet-Daten, die auftreten können, wenn gleichzeitige Shardlet-Verwaltungsvorgänge (z. B. das Verschieben von Daten zwischen Shards) ausgeführt werden. Dazu fungieren die von der Clientbibliothek verwalteten Shard-Zuordnungen als Broker der Datenbankverbindungen für eine Anwendung. Dadurch können die Shard-Zuordnungsfunktionen automatisch eine Datenbankverbindung beenden, wenn sich Shard-Verwaltungsvorgänge auf das Shardlet auswirken können, für das die Verbindung erstellt wurde. Bei diesem Ansatz müssen einige EF-Funktionen berücksichtigt werden, wie z. B. das Erstellen neuer Verbindungen aus einer vorhandenen Datenbank, um zu prüfen, ob die Datenbank vorhanden ist. Unserer Erfahrung nach funktionieren die DbContext-Standardkonstruktoren im Allgemeinen nur mit geschlossenen Datenbankverbindungen zuverlässig, die für EF sicher geklont werden können. Dagegen setzt das Entwurfsmodell von elastischen Datenbanken voraus, dass nur offene Verbindungen vermittelt werden. Man könnte vermuten, dass sich dieses Problem lösen lässt, indem Verbindungen geschlossen werden, für die die Clientbibliothek als Broker fungiert, bevor Sie an DbContext von EF übergeben werden. Wenn die Verbindung jedoch geschlossen und somit das erneute Herstellen der Verbindung EF überlassen wird, finden die von der Bibliothek durchgeführten Gültigkeits- und Konsistenzprüfungen nicht statt. Die Migrationsfunktionalität in EF verwendet diese Verbindungen jedoch, um das zugrunde liegende Datenbankschema auf eine Weise zu verwalten, die für die Anwendung transparent ist. Im Idealfall behalten Sie alle diese Funktionen – sowohl die der Clientbibliothek für elastische Datenbanken als auch die von EF – in der gleichen Anwendung und kombinieren sie in dieser. Im folgenden Abschnitt werden diese Eigenschaften und Anforderungen detaillierter erläutert.

## <a name="requirements"></a>Requirements (Anforderungen)

Bei gleichzeitiger Verwendung der Clientbibliothek für elastische Datenbanken und der Entity Framework-APIs sollten die folgenden Eigenschaften beibehalten werden:

* **Horizontale Skalierung:** Datenbanken werden der Datenschicht der Anwendung entsprechend den Kapazitätsanforderungen der partitionierten Anwendung hinzugefügt oder aus dieser entfernt. Dies impliziert die Kontrolle über die Erstellung und Löschung von Datenbanken und den Einsatz der APIs für die Shardzuordnungsverwaltung von elastischen Datenbanken, um Datenbanken und die Zuordnung von Shardlets zu verwalten.
* **Konsistenz:** Die Anwendung nutzt Sharding und verwendet die datenabhängigen Routingfunktionen der Clientbibliothek. Um Beschädigung von Daten oder falsche Abfrageergebnisse zu vermeiden, werden Verbindungen über die Shard-Zuordnungsverwaltung vermittelt. Dadurch bleiben auch Gültigkeitsprüfung und Konsistenz gewahrt.
* **Code First:** Beibehalten des komfortablen Code-First-Paradigmas von EF. In Code First werden in der Anwendung vorhandene Klassen transparent den zugrunde liegenden Datenbankstrukturen zugeordnet. Der Anwendungscode arbeitet mit DbSets, wodurch die meisten Aspekte der zugrunde liegenden Datenbankverarbeitung maskiert werden.
* **Schema** : Entity Framework übernimmt die Schemaerstellung für die Ausgangsdatenbank und die nachfolgende Weiterentwicklung des Schemas über Migrationsvorgänge. Wenn diese Funktionen beibehalten werden, lassen sich Anwendung einfach an die Datenentwicklung anpassen.

Die folgende Anleitung beschreibt, wie diese Anforderungen für Code-First-Anwendungen unter Verwendung der Tools für elastische Datenbanken erfüllt werden.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Datenabhängiges Routing mit EF DbContext

Datenbankverbindungen mit Entity Framework werden in der Regel über Unterklassen von **DbContext** verwaltet. Erstellen Sie diese Unterklassen durch Ableiten von **DbContext** . Hierbei definieren Sie **DbSets** , die die in Datenbanken gesicherten Auflistungen von CLR-Objekten für Ihre Anwendung implementieren. Im Kontext des datenabhängigen Routing können Sie einige nützliche Eigenschaften identifizieren, die nicht unbedingt für andere EF Code First-Anwendungsszenarien geeignet sind:

* Die Datenbank ist bereits vorhanden und in der Shard-Zuordnung für elastische Datenbanken registriert.
* Das Schema der Anwendung wurde bereits in der Datenbank (siehe nachfolgende Erläuterung) bereitgestellt.
* Datenabhängige Routingverbindungen zur Datenbank werden durch die Shard-Zuordnung vermittelt.

So integrieren Sie **DbContexts** zum horizontalen Skalieren in datenabhängiges Routing:

1. Erstellen Sie über die Schnittstellen der Shard-Zuordnungsverwaltung für elastische Datenbanken physische Datenbankverbindungen.
2. Umschließen Sie die Verbindung mit der **DbContext** -Unterklasse.
3. Übergeben Sie die Verbindung weiter in die **DbContext** -Klassen, um sicherzustellen, dass auch alle erforderlichen Schritte auf der EF-Seite ausgeführt werden.

Das folgende Codebeispiel veranschaulicht diese Vorgehensweise. (Dieser Code ist auch im zugehörigen Visual Studio-Projekt enthalten)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Hauptpunkte

* Der Standardkonstruktor der DbContext-Unterklasse wird durch einen neuen Konstruktor ersetzt.
* Der neue Konstruktor akzeptiert die Argumente, die für das datenabhängige Routing über die Clientbibliothek für elastische Datenbanken erforderlich sind:
  
  * die Shard-Zuordnung für den Zugriff auf datenabhängige Routing-Schnittstellen,
  * den Sharding-Schlüssel zum Identifizieren des Shardlets,
  * eine Verbindungszeichenfolge mit den Anmeldeinformationen für die datenabhängige Routing-Verbindung mit der Shard.
* Der Aufruf des Konstruktors der Basisklasse macht einen Umweg über eine statische Methode, die alle für das datenabhängige Routing erforderlichen Schritte ausführt.
  
  * Dazu wird die OpenConnectionForKey-Methode der Schnittstellen für Datenbankclients für die Shard-Zuordnung aufgerufen, um eine offene Verbindung herzustellen.
  * Die Shard-Zuordnung erstellt die offene Verbindung mit der Shard, die das Shardlet für den gegebenen Sharding-Schlüssel enthält.
  * Diese offene Verbindung wird zurück an den Basisklassenkonstruktor von DbContext übergeben, um anzugeben, dass EF diese Verbindung verwenden und nicht automatisch eine neue Verbindung erstellen soll. Auf diese Weise wird die Verbindung von der Client-API für elastische Datenbanken markiert, damit die Konsistenz unter den Shard-Zuordnungsverwaltungs-Vorgängen gewährleistet werden kann.

Verwenden Sie in Ihrem Code den neuen Konstruktor für die DbContext-Unterklasse statt des Standardkonstruktors. Beispiel:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

Der neue Konstruktor öffnet die Verbindung mit der Shard, die die Daten für das durch den Wert von **tenantid1** bezeichnete Shardlet enthält. Der Code im **using** -Block wird für den Zugriff auf **DbSet** für Blogs unter Verwendung von EF für den Shard mit dem Shardlet **tenantid1** nicht verändert. Dadurch wird die Semantik für den Code im using-Code geändert, sodass alle Datenbankvorgänge jetzt auf den einen Shard beschränkt werden, in dem sich **tenantid1** befindet. Beispielsweise würde eine **DbSet** -Abfrage für die Blogs in DbSet nur die Blogs liefern, die im aktuellen Shard gespeichert sind, nicht jedoch die in anderen Shards gespeicherten Blogs.  

### <a name="transient-faults-handling"></a>Behandlung zeitweise auftretender Fehler

Das Microsoft Patterns & Practices-Team hat den [Transient Fault Handling Application Block](/previous-versions/msp-n-p/dn440719(v=pandp.60)) veröffentlicht. Die Bibliothek wird beim Einsatz der Clientbibliothek für elastische Datenbanken in Kombination mit EF verwendet. Achten Sie jedoch darauf, dass eine vorübergehende Ausnahme die Steuerung an eine Stelle zurückgibt, an der Sie sicherstellen können, dass nach einem vorübergehenden Fehler der neue Konstruktor verwendet wird, damit für alle neuen Verbindungsversuche die optimierten Konstruktoren verwendet werden. Andernfalls kann nicht sichergestellt werden, dass die Verbindung mit der richtigen Shard hergestellt und aufrechterhalten wird, wenn die Shard-Zuordnung verändert wird.

Im folgenden Codebeispiel wird veranschaulicht, wie eine SQL-Wiederholungsrichtlinie im Zusammenhang mit den neuen Konstruktoren für die **DbContext** -Unterklasse verwendet wird:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

Im Code oben ist **SqlDatabaseUtils.SqlRetryPolicy** als **SqlDatabaseTransientErrorDetectionStrategy** mit 10 Wiederholungen und einer Wartezeit von 5 Sekunden zwischen den Wiederholungen definiert. Dieser Ansatz ähnelt der Anleitung für EF und durch den Benutzer initiierte Transaktionen (siehe [Limitations with Retrying Execution Strategies (EF6 onwards)](/ef/ef6/fundamentals/connection-resiliency/retry-logic)(in englischer Sprache)). Beide Situationen erfordern, dass das Anwendungsprogramm den Bereich steuert, in dem die vorübergehende Ausnahme zurückgegeben wird: erneutes Öffnen der Transaktion oder (wie dargestellt) Neuerstellung des Kontexts von einem geeigneten Konstruktor, der die Clientbibliothek für elastische Datenbanken verwendet.

Die Notwendigkeit, den Rückgabeort für vorübergehende Ausnahmen zu steuern, schließt auch die Verwendung der integrierten **SqlAzureExecutionStrategy** ein, die im Lieferumfang von EF enthalten ist. **SqlAzureExecutionStrategy** würde eine Verbindung erneut öffnen, aber ohne **OpenConnectionForKey** zu verwenden. Dadurch würden alle Validierungen umgangen, die im Rahmen des **OpenConnectionForKey** -Aufrufs durchgeführt werden. Das Codebeispiel verwendet stattdessen die integrierte **DefaultExecutionStrategy** , die ebenfalls im Lieferumfang von EF enthalten ist. Im Gegensatz zu **SqlAzureExecutionStrategy** funktioniert sie jedoch ordnungsgemäß zusammen mit der Wiederholungsrichtlinie aus der Behandlung vorübergehender Fehler. Die Ausführungsrichtlinie wird in der **ElasticScaleDbConfiguration** -Klasse festgelegt. Beachten Sie, dass wir uns gegen die Verwendung von **DefaultSqlExecutionStrategy** entschieden haben, da sie im Fall vorübergehender Ausnahmen die Verwendung von **SqlAzureExecutionStrategy** nahelegt. Das wiederum würde zu dem bereits beschriebenen fehlerhaften Verhalten führen. Weitere Informationen über die verschiedenen Wiederholungsrichtlinien und EF finden Sie unter [Verbindungsstabilität in EF](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Neuschreiben von Konstruktoren

Die oben aufgeführten Codebeispiele veranschaulichen, welche Änderungen am Standardkonstruktor vorgenommen werden müssen, damit Ihre Anwendung das datenabhängige Routing mit Entity Framework verwenden kann. In der folgende Tabelle wird dieser Ansatz für die anderen Konstruktoren verallgemeinert.

| Aktuelle Konstruktor | Für Daten veränderter Konstruktor | Basiskonstruktor | Notizen |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext(DbConnection, bool) |Die Verbindung muss eine Funktion der Shard-Zuordnung und des datenabhängigen Routingschlüssels sein. Sie müssen die automatische Erstellung von Verbindungen in EEF umgehen und stattdessen die Shard-Zuordnung als Verbindungsbroker verwenden. |
| MyContext(string) |ElasticScaleContext (ShardMap, TKey) |DbContext(DbConnection, bool) |Die Verbindung ist eine Funktion der Shard Map und des datenabhängigen Routingschlüssels. Ein fester Datenbankname oder eine Verbindungszeichenfolge funktionieren nicht, da hiermit die Überprüfung der Shardzuordnung umgangen wird. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Die Verbindung wird für die angegebene Shardzuordnung und den Shardingschlüssel mit dem bereitgestellten Modell erstellt. Das kompilierte Modell wird an den Basiskonstruktor übergeben. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |Die Verbindung muss aus der Shard Map und dem Schlüssel abgeleitet werden. Sie kann nicht als Eingabe bereitgestellt werden (es sei denn, in der Eingabe wurden bereits Shard-Zuordnung und Schlüssel verwendet). Der boolesche Wert wird übergeben. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Die Verbindung muss aus der Shard Map und dem Schlüssel abgeleitet werden. Sie kann nicht als Eingabe bereitgestellt werden (es sei denn, in der Eingabe wurden bereits Shard-Zuordnung und Schlüssel verwendet). Das kompilierte Modell wird übergeben. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Der neue Konstruktor muss sicherstellen, dass alle als Eingabe in den ObjectContext übergebenen Verbindungen an eine von Elastic Scale verwaltete Verbindung umgeleitet werden. Eine detaillierte Erläuterung von ObjectContext würde den Rahmen dieses Dokuments sprengen. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |Die Verbindung muss aus der Shard Map und dem Schlüssel abgeleitet werden. Die Verbindung kann nicht als Eingabe bereitgestellt werden (es sei denn, in der Eingabe wurden bereits Shard-Zuordnung und Schlüssel verwendet). Modell und boolescher Wert werden an den Konstruktor der Basisklasse übergeben. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Shard-Schemabereitstellung durch EF-Migrationen

Die automatische Schemaverwaltung wird von Entity Framework bereitgestellt. Diese Möglichkeit sollte im Kontext von Anwendungen beibehalten werden, die Tools für elastische Datenbanken verwenden, um das Schema automatisch für neu erstellte Shards bereitzustellen, wenn der partitionierten Anwendung Datenbanken hinzugefügt werden. Der Hauptzweck besteht darin, für partitionierte Anwendungen die Kapazität auf Datenebene mithilfe von EF zu vergrößern. Durch die Nutzung der EF-Funktionen zur Schemaverwaltung lässt sich der Datenbankverwaltungsaufwand für die auf EF basierende partitionierte Anwendung verringern.

Die Schemabereitstellung über EF-Migrationen funktioniert am besten bei **nicht geöffneten Verbindungen** . Dies unterscheidet sich von dem Szenario für das datenabhängige Routing, das sich auf die von der API für elastische Datenbanken bereitgestellte geöffnete Verbindung stützt. Ein weiterer Unterschied ist die Konsistenzanforderung: Es ist zwar wünschenswert, die Konsistenz für alle datenabhängigen Routingverbindungen zum Schutz vor gleichzeitiger Manipulation der Shardzuordnung sicherzustellen, aber bei der anfänglichen Schemabereitstellung in einer neuen Datenbank, die noch nicht in der Shardzuordnung registriert wurde und folglich noch keine Shardlets enthält, ist dies nicht von Belang. Sie können daher für dieses Szenario anders als beim datenabhängigen Routing reguläre Datenbankverbindungen verwenden.  

Dies führt zu einem Vorgehen, bei dem die Schemabereitstellung über EF-Migrationen eng mit der Registrierung der neuen Datenbank als Shard in der Shard-Zuordnung verbunden ist. Dabei wird Folgendes vorausgesetzt:

* Die Datenbank wurde bereits erstellt.
* Die Datenbank ist leer. Sie enthält kein Benutzerschema und keine Benutzerdaten.
* Auf die Datenbank kann für das datenabhängige Routing noch nicht über die Client-APIs für elastische Datenbanken zugegriffen werden.

Wenn diese Voraussetzungen erfüllt sind, können Sie eine reguläre, nicht geöffnete **SqlConnection** -Verbindung erstellen, um die EF-Migrationen zur Schemabereitstellung zu starten. Das folgende Codebeispiel veranschaulicht diese Vorgehensweise.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Das Beispiel zeigt die **RegisterNewShard** -Methode, die den Shard in der Shard-Zuordnung registriert, das Schema über EF-Migrationen bereitstellt und eine Zuordnung des Sharding-Schlüssels im Shard speichert. Sie verwendet einen Konstruktor der **DbContext** -Unterklasse ( **ElasticScaleContext** im Beispiel), der eine SQL-Verbindungszeichenfolge als Eingabe akzeptiert. Der Code dieses Konstruktors ist einfach, wie im folgenden Beispiel gezeigt:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

Möglicherweise muss die Version des Konstruktors, die von der Basisklasse geerbt wird, verwendet werden. Jedoch muss der Code sicherstellen, dass der Standard-Initialisierer für EF zum Herstellen der Verbindung verwendet wird. Daher der kurze Umweg in die statische Methode, bevor der Basisklassen-Konstruktor mit der Verbindungszeichenfolge aufgerufen wird. Beachten Sie, dass die Registrierung von Shards in einer anderen Anwendungsdomäne bzw. einem anderen Prozess ausgeführt werden soll, damit kein Konflikt mit den Einstellungen der Initialisierer für EF entsteht.

## <a name="limitations"></a>Einschränkungen

Für die in diesem Dokument beschriebenen Vorgehensweisen gelten einige Einschränkungen:

* EF-Anwendungen, die **LocalDb** verwenden, müssen vor dem Einsatz der Clientbibliothek für elastische Datenbanken zuerst in eine reguläre SQL-Serverdatenbank migriert werden. Mit **LocalDb** ist es nicht möglich, eine Anwendung über Sharding mit Elastic Scale horizontal zu skalieren. Beachten Sie, dass in der Entwicklung **LocalDb** weiterhin verwendet werden kann.
* Alle Änderungen der Anwendung, die Änderungen am Datenbankschema zur Folge haben, müssen EF Migrationen in allen Shards durchlaufen. Dies wird im Beispielcode für dieses Dokument nicht veranschaulicht. Verwenden Sie Update-Database mit einem ConnectionString-Parameter zum Durchlaufen aller Shards. Oder extrahieren Sie das T-SQL-Skript für die anstehende Migration unter Verwendung von Update-Database mit der Option „-Script“, und wenden Sie das T-SQL-Skript auf die Shards an.  
* Wenn eine Anforderung gegeben ist, wird davon ausgegangen, dass die damit verbundene Datenbankverarbeitung innerhalb einer einzelnen Shard erfolgt, die durch den in der Anforderung bereitgestellten Sharding-Schlüssel bezeichnet wird. Allerdings ist diese Annahme nicht immer richtig. Beispielsweise dann, wenn es nicht möglich ist, einen Sharding-Schlüssel verfügbar zu machen. Um dieses Problem zu beheben, stellt die Clientbibliothek die **MultiShardQuery** -Klasse bereit, die eine Verbindungsabstraktion für Abfragen mit mehreren Shards implementiert. Eine detaillierte Erläuterung der Verwendung von **MultiShardQuery** in Kombination mit EF würde den Rahmen dieses Dokuments sprengen.

## <a name="conclusion"></a>Zusammenfassung

Durch die in diesem Dokument beschriebenen Schritte können EF-Anwendungen die Funktionen der Clientbibliothek für elastische Datenbanken für das datenabhängige Routing nutzen, indem die Konstruktoren der in der EF-Anwendung verwendeten **DbContext** -Unterklassen abgeändert werden. Dadurch werden die erforderlichen Änderungen auf die Stellen begrenzt, an denen bereits **DbContext** -Klassen vorhanden sind. Darüber hinaus können EF-Anwendungen weiterhin die automatische Schemabereitstellung nutzen, indem die Schritte, mit denen die erforderlichen EF-Migrationen aufgerufen werden, mit der Registrierung der neuen Shards und Zuordnungen in der Shard-Zuordnung kombiniert werden.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png