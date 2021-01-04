---
title: Mehrinstanzenfähige Apps mit Sicherheit auf Zeilenebene und Tools für elastische Datenbanken
description: Verwenden Sie Tools für elastische Datenbanken mit Sicherheit auf Zeilenebene zum Erstellen einer Anwendung mit einer hochgradig skalierbaren Datenschicht.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793210"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Mehrinstanzenfähige Anwendungen mit elastischen Datenbanktools und zeilenbasierter Sicherheit
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Tools für elastische Datenbanken](elastic-scale-get-started.md) und die [zeilenbasierte Sicherheit (RLS)][rls] ermöglichen die Skalierung der Datenschicht einer mehrinstanzenfähigen Anwendung mit Azure SQL-Datenbank. Durch die Kombination dieser Technologien können Sie eine Anwendung erstellen, die über eine hochgradig skalierbare Datenschicht verfügt. Die Datenschicht unterstützt mehrinstanzenfähige Shards und verwendet **ADO.NET SqlClient** oder **Entity Framework** . Weitere Informationen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](./saas-tenancy-app-design-patterns.md).

- **Elastische Datenbanktools** ermöglichen Entwicklern eine Aufskalierung der Datenschicht über Sharding-Standardmethoden, indem .NET-Bibliotheken und Azure-Dienstvorlagen verwendet werden. Die Verwaltung von Shards mit der [Clientbibliothek für elastische Datenbanken][s-d-elastic-database-client-library] hilft bei der Automatisierung und Optimierung zahlreicher infrastruktureller Aufgaben, die i. d. R. Sharding zugeordnet werden.
- Mithilfe der **Sicherheit auf Zeilenebene** können Entwickler Daten für mehrere Mandanten in derselben Datenbank sicher speichern. Sicherheitsrichtlinien für die Sicherheit auf Zeilenebene filtern Zeilen heraus, die nicht dem Mandanten gehören, der eine Abfrage ausführt. Die Zentralisierung der Filterlogik in der Datenbank vereinfacht die Wartung und reduziert das Risiko eines Sicherheitsfehlers. Die Alternative, sich auf den gesamten Clientcode zu verlassen, um Sicherheit zu erzwingen, ist riskant.

Durch die gemeinsame Nutzung dieser Features kann eine Anwendung Daten für mehrere Mandanten in derselben Sharddatenbank speichern. Es ist kostengünstiger pro Mandant, wenn sich die Mandanten eine Datenbank teilen. Die gleiche Anwendung kann aber auch seinen Premium-Mandanten die Möglichkeit bieten, für ihren eigenen, dedizierten Shard für einen einzelnen Mandanten zu bezahlen. Ein Vorteil der Einzelmandantenisolierung ist, dass die Leistungsgarantien strenger sind. In einer Datenbank mit nur einem Mandanten gibt es keinen anderen Mandanten, der um Ressourcen wetteifert.

Das Ziel ist die Verwendung der APIs der elastischen Datenbank-Clientbibliothek zum [datenabhängigen Routen](elastic-scale-data-dependent-routing.md), um jeden bestimmten Mandanten automatisch mit der richtigen Sharddatenbank zu verbinden. Nur ein Shard enthält einen bestimmten TenantId-Wert für den bestimmten Mandanten. Die „TenantId“ ist der *Shardingschlüssel* . Nachdem die Verbindung hergestellt wurde, sorgt eine Sicherheitsrichtlinie für die Sicherheit auf Zeilenebene innerhalb der Datenbank dafür, dass der jeweilige Mandant nur auf die Datenzeilen zugreifen kann, die seine „TenantId“ enthalten.

> [!NOTE]
> Der Mandantenbezeichner kann aus mehreren Spalten bestehen. Der Einfachheit halber nehmen wir informell eine einspaltige „TenantId“ an.

![Blogging-App-Architektur][1]

## <a name="download-the-sample-project"></a>Herunterladen des Beispielprojekts

### <a name="prerequisites"></a>Voraussetzungen

- Visual Studio (2012 oder neuere Version)
- Erstellen von drei Datenbanken in Azure SQL-Datenbank
- Laden Sie das Beispielprojekt herunter: [Elastische DB-Tools für Azure SQL: Mehrinstanzenfähige Shards](https://go.microsoft.com/?linkid=9888163)
  - Geben Sie die Informationen für Ihre Datenbanken am Anfang der Datei **Program.cs**

Dieses Projekt stellt eine Erweiterung des unter [Elastische DB-Tools für Azure SQL – Entity Framework-Integration](elastic-scale-use-entity-framework-applications-visual-studio.md) beschriebenen Projekts dar, indem Unterstützung für Shard-Datenbanken mit mehreren Mandanten hinzugefügt wird. Das Projekt erstellt eine einfache Konsolenanwendung zum Erstellen von Blogs und Beiträgen. Das Projekt umfasst vier Mandanten sowie zwei mehrinstanzenfähige Sharddatenbanken. Diese Konfiguration ist im vorhergehenden Diagramm dargestellt.

Erstellen Sie die Anwendung, und führen Sie sie aus. Durch diese Ausführung werden ein Bootstrap für den Shard-Zuordnungs-Manager der elastischen Datenbanktools und die folgenden Tests ausgeführt:

1. Erstellen Sie mithilfe von Entity Framework und LINQ einen neuen Blog und zeigen Sie dann alle Blogs für jeden Mandanten an.
2. Zeigen Sie mithilfe von ADO.NET SqlClient alle Blogs für einen Mandanten an.
3. Versuchen Sie, einen Blog für den falschen Mandanten einzufügen, um zu überprüfen, ob ein Fehler ausgelöst wird.

Beachten Sie, dass diese gesamten Tests ein Problem aufzeigen, da RLS noch nicht in den Shard-Datenbanken aktiviert wurde: Mandanten können Blogs anzeigen, die Ihnen nicht gehören, und die Anwendung, wird nicht daran gehindert, einen Blog für den falschen Mandanten einzufügen. Im restlichen Artikel wird beschrieben, wie Sie diese Probleme beheben, indem Sie die Mandantenisolierung mit RLS erzwingen. Dafür sind zwei Schritte nötig:

1. **Anwendungsschicht** : Ändern Sie den Anwendungscode so, dass die aktuelle Mandanten-ID im SESSION\_CONTEXT immer nach dem Öffnen einer Verbindung festgelegt wird. Das Beispielprojekt legt die „TenantId“ bereits auf diese Weise fest.
2. **Datenschicht** : Erstellen Sie auf der Grundlage der in SESSION\_CONTEXT gespeicherten Mandanten-ID in jeder Sharddatenbank eine RLS-Sicherheitsrichtlinie zum Filtern von Zeilen. Erstellen Sie für jede Sharddatenbank eine Richtlinie. Andernfalls werden die Zeilen in Shards mit mehreren Mandanten nicht gefiltert.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Anwendungsschicht: Festlegen von TenantId in SESSION\_CONTEXT

Zuerst stellen Sie eine Verbindung mit einer Sharddatenbank her, indem Sie die APIs der elastischen Datenbank-Clientbibliothek zum datenabhängigen Routen verwenden. Die Anwendung muss der Datenbank noch mitteilen, welche „TenantId“ die Verbindung nutzt. Die „TenantId“ teilt der RLS-Sicherheitsrichtlinie mit, welche Zeilen als zu anderen Mandanten gehörend herausgefiltert werden müssen. Speichern Sie die aktuelle „TenantId“ im [SESSION\_CONTEXT](/sql/t-sql/functions/session-context-transact-sql) der Verbindung.

Ein Alternative für SESSION\_CONTEXT ist [CONTEXT\_INFO](/sql/t-sql/functions/context-info-transact-sql). Aber SESSION\_CONTEXT ist eine bessere Option. SESSION\_CONTEXT ist einfacher zu verwenden, gibt standardmäßig NULL zurück und unterstützt Schlüssel-Wert-Paare.

### <a name="entity-framework"></a>Entity Framework

Für Anwendungen, die Entity Framework verwenden, ist die einfachste Herangehensweise, SESSION\_CONTEXT in der ElasticScaleContext-Überschreibung festzulegen, wie unter [Datenabhängiges Routing mit EF DbContext](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext) beschrieben. Erstellen Sie einen SQL-Befehl, der „TenantId“ für SESSION\_CONTEXT auf den „Shardingschlüssel“ festlegt, der für die Verbindung angegeben wurde. Anschließend geben Sie die über datenabhängiges Routing vermittelte Verbindung zurück. Auf diese Weise müssen Sie nur einmal Code zum Festlegen von SESSION\_CONTEXT schreiben.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Der SESSION\_CONTEXT wird jetzt automatisch auf die angegebene „TenantId“ festgelegt, wenn „ElasticScaleContext“ aufgerufen wird:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Für Anwendungen, die ADO.NET SqlClient verwenden, erstellen Sie um die Methode „ShardMap.OpenConnectionForKey“ herum eine Wrapperfunktion. Lassen Sie den Wrapper für „TenantId“ in SESSION\_CONTEXT automatisch die aktuelle „TenantId“ festlegen, bevor Sie eine Verbindung zurückgeben. Um sicherzustellen, dass SESSION\_CONTEXT immer richtig konfiguriert ist, sollten Sie Verbindungen nur mit dieser Wrapperfunktion öffnen.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datenebene: Erstellen Sie eine zeilenbasierte Sicherheitsrichtlinie

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Erstellen einer Sicherheitsrichtlinie, um die Zeilen zu filtern, auf die jeder Mandant zugreifen kann

Da die Anwendung nun SESSION\_CONTEXT vor dem Abfragen auf die aktuelle „TenantId“ festlegt, kann eine RLS-Sicherheitsrichtlinie Abfragen filtern und Zeilen ausschließen, die eine andere „TenantId“ aufweisen.

RLS ist in Transact-SQL implementiert. Eine benutzerdefinierte Funktion definiert die Zugriffslogik, und eine Sicherheitsrichtlinie bindet diese Funktion an eine beliebige Anzahl von Tabellen. Für dieses Projekt:

1. Die Funktion überprüft, dass die Anwendung mit der Datenbank verbunden wird, und die im SESSION\_CONTEXT gespeicherte „TenantId“ mit der „TenantId“ einer bestimmten Zeile übereinstimmt.
    - Die Anwendung ist verbunden und nicht ein anderer SQL-Benutzer.

2. Mit einem FILTER-Prädikat können Zeilen, die dem Filter „TenantId“ entsprechen, für SELECT-, UPDATE- und DELETE-Abfragen durchgeleitet werden.
    - Ein BLOCK-Prädikat verhindert, dass für Zeilen, die dem Filter nicht entsprechen, INSERT oder UPDATE ausgeführt werden kann.
    - Wenn SESSION\_CONTEXT nicht festgelegt wurde, gibt die Funktion NULL zurück. Darüber hinaus sind Zeilen nicht sichtbar und können nicht eingefügt werden.

Um RLS für alle Shards zu aktivieren, führen Sie das folgende T-SQL mithilfe von Visual Studio (SSDT) oder SSMS bzw. das im Projekt enthaltene PowerShell-Skript aus. Wenn Sie [elastische Datenbankaufträge](./elastic-jobs-overview.md) verwenden, können Sie die T-SQL-Ausführung für alle Shards automatisieren.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> In einem komplexen Projekt kann es erforderlich sein, das Prädikat zu Hunderten von Tabellen hinzuzufügen, was mühsam sein kann. Es gibt eine gespeicherte Prozedur als Hilfsprogramm, die automatisch eine Sicherheitsrichtlinie generiert und ein Prädikat zu allen Tabellen in einem Schema hinzufügt. Weitere Informationen finden Sie im Blogbeitrag unter [Apply Row-Level Security to all tables – helper script](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360) (Anwenden von zeilenbasierter Sicherheit auf alle Tabellen – Hilfsprogrammskript).

Wenn Sie die Beispielanwendung jetzt erneut ausführen, können Mandanten nur Zeilen anzeigen, die zu ihnen gehören. Darüber hinaus kann die Anwendung keine Zeilen einfügen, die Mandanten gehören, die nicht derzeit mit der Sharddatenbank verbunden sind. Außerdem kann die Anwendung die „TenantId“ nicht in allen Zeilen aktualisieren, die sie sehen kann. Wenn die Anwendung versucht, eine dieser Aktionen auszuführen, wird eine „DbUpdateException“ ausgelöst.

Wenn Sie später eine neue Tabelle hinzufügen, führen Sie einfach einen ALTER-Vorgang für die Sicherheitsrichtlinie aus und fügen der neuen Tabelle FILTER- und BLOCK-Prädikate hinzu.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Hinzufügen von Standardeinschränkungen zum automatischen Auffüllen der Mandanten-ID für INSERTs.

Sie können eine Standardeinschränkung für jede Tabelle festlegen, um die „TenantId“ beim Einfügen von Zeilen automatisch mit dem derzeit in SESSION\_CONTEXT gespeicherten Wert aufzufüllen. Ein Beispiel folgt.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Jetzt muss die Anwendung beim Einfügen von Zeilen keine Mandanten-ID angeben:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Bei der Verwendung von Standardeinschränkungen für ein Entity Framework-Projekt wird empfohlen, dass Sie *NICHT* die Mandanten-ID-Spalte in Ihr EF-Datenmodell einbeziehen. Dies liegt daran, dass Entity Framework-Abfragen automatisch Standardwerte bereitstellen, die die in T-SQL erstellten Standardeinschränkungen mit SESSION\_CONTEXT überschreiben.
> Um Standardeinschränkungen im Beispielprojekt zu verwenden, sollten Sie z. B. "TenantId" (die Mandanten-ID) aus "DataClasses.cs" entfernen (und "Add-Migration" in der Paket-Manager-Konsole ausführen) sowie T-SQL verwenden, um sicherzustellen, dass das Feld nur in den Datenbanktabellen vorhanden ist. Auf diese Weise stellt EF beim Einfügen von Daten automatisch falsche Standardwerte bereit.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Optional) Aktivieren Sie einen *Benutzer mit Administratorrechten* (Superuser), der auf alle Zeilen zugreifen kann.

Einige Anwendungen möchten vielleicht einen *Benutzer mit Administratorrechten* erstellen, der auf alle Zeilen zugreifen kann. Ein Benutzer mit Administratorrechten könnte die Berichterstellung mandantenübergreifend für alle Shards aktivieren. Ein Benutzer mit Administratorrechten könnte Split-Merge-Vorgänge für Shards durchführen, die das Verschieben von Mandantenzeilen zwischen Datenbanken einbeziehen.

Um einen Benutzer mit Administratorrechten zu aktivieren, erstellen Sie einen neuen SQL-Benutzer (in diesem Beispiel `superuser`) in den einzelnen Sharddatenbanken. Ändern Sie dann die Sicherheitsrichtlinie mit einer neuen Prädikatfunktion, durch die dieser Benutzer Zugriff auf alle Zeilen erhält. Eine solche Funktion wird als nächstes dargestellt.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Wartung

- **Hinzufügen neuer Shards** : Führen Sie das T-SQL-Skript zum Aktivieren von RLS auf allen neuen Shards aus. Andernfalls werden Abfragen für diese Shards nicht gefiltert.
- **Hinzufügen neuer Tabellen** : Fügen Sie den Sicherheitsrichtlinien aller Shards ein FILTER- und BLOCK-Prädikat hinzu, wenn eine neue Tabelle erstellt wird. Andernfalls werden Abfragen für die neue Tabelle nicht gefiltert. Dies kann mithilfe eines DDL-Triggers automatisiert werden, wie im Blogbeitrag [Apply Row-Level Security automatically to newly created tables](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393) (Automatisches Anwenden der Sicherheit auf Zeilenebene auf neu erstellte Tabellen) beschrieben.

## <a name="summary"></a>Zusammenfassung

Elastische Datenbanktools und zeilenbasierte Sicherheit können zusammen zum Aufskalieren der Datenebene einer Anwendung mit Unterstützung für Shards mit sowohl mehreren als einzelnen Mandanten verwendet werden. Mehrinstanzenfähige Shards können genutzt werden, um Daten effizienter zu speichern. Diese Effizienz ist besonders ausgeprägt, wenn eine große Anzahl von Mandanten nur über wenige Datenzeilen verfügt. Shards mit einem einzelnen Mandanten können Premium-Mandanten unterstützen, die strengere Leistungs- und Isolationsanforderungen aufweisen. Weitere Informationen finden Sie unter [Sicherheit auf Zeilenebene][rls].

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Was ist ein Pool für elastische Azure-Datenbanken?](elastic-pool-overview.md)
- [Erweitern mit Azure SQL-Datenbank](elastic-scale-introduction.md)
- [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](./saas-tenancy-app-design-patterns.md)
- [Authentifizierung in mehrinstanzenfähigen Apps mithilfe von Azure AD und OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Tailspin-Anwendung „Surveys“](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Fragen und Funktionswünsche

Wenn Sie Fragen haben, kontaktieren Sie uns auf der [Microsoft-Seite mit Fragen und Antworten zu SQL-Datenbank](/answers/topics/azure-sql-database.html). Featureanforderungen können im [SQL-Datenbank-Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/) hinzugefügt werden.

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md