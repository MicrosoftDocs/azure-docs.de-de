---
title: 'Entwicklerhandbuch für dauerhafte Entitäten in .NET: Azure Functions'
description: Es wird beschrieben, wie Sie dauerhafte Entitäten in .NET nutzen, indem Sie die Durable Functions-Erweiterung für Azure Functions verwenden.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740020"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Entwicklerhandbuch für dauerhafte Entitäten in .NET

In diesem Artikel werden ausführlich die verfügbaren Schnittstellen für die Entwicklung dauerhafter Entitäten mit .NET beschrieben, z. B. mit Beispielen und in Form von allgemeinen Ratschlägen. 

Entitätsfunktionen sind für Entwickler von serverlosen Anwendungen eine gute Möglichkeit, um den Anwendungszustand als Sammlung mit differenzierten Entitäten zu organisieren. Ausführlichere Informationen zu den zugrunde liegenden Konzepten finden Sie im Artikel zum Thema [Dauerhafte Entitäten: Konzepte](durable-functions-entities.md).

Zum Definieren von Entitäten stehen derzeit zwei APIs zur Verfügung:

- Bei der **klassenbasierten Syntax** werden Entitäten und Vorgänge als Klassen und Methoden dargestellt. Mit dieser Syntax wird leicht lesbarer Code erstellt, und Vorgänge können mit Typüberprüfung über Schnittstellen aufgerufen werden. 

- Die **funktionsbasierte Syntax** ist eine Schnittstelle auf niedrigerer Ebene, auf der Entitäten als Funktionen dargestellt werden. Sie ermöglicht eine genaue Steuerung, wie die Entitätsvorgänge übermittelt werden und der Entitätszustand verwaltet wird.  

In diesem Artikel geht es vorrangig um die klassenbasierte Syntax, weil wir erwarten, dass sie für die meisten Anwendungen besser geeignet ist. Die [funktionsbasierte Syntax](#function-based-syntax) ist aber ggf. eine gute Wahl für Anwendungen, bei denen eigene Abstraktionen für den Entitätszustand und die Vorgänge definiert bzw. verwaltet werden sollen. Darüber hinaus kann sie auch gut für die Implementierung von Bibliotheken geeignet sein, für die eine Generizität benötigt wird, die von der klassenbasierten Syntax derzeit nicht unterstützt wird. 

> [!NOTE]
> Da es sich bei der klassenbasierten Syntax lediglich um eine Schicht oberhalb der funktionsbasierten Syntax handelt, können beide Varianten in derselben Anwendung verwendet werden. 
 
## <a name="defining-entity-classes"></a>Definieren von Entitätsklassen

Das folgende Beispiel ist eine Implementierung einer `Counter`-Entität, mit der ein einzelner Wert vom Typ „integer“ gespeichert wird und die über die vier Vorgänge `Add`, `Reset`, `Get` und `Delete` verfügt.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Die Funktion `Run` enthält den Baustein, der für die Nutzung der klassenbasierten Syntax erforderlich ist. Hierbei muss es sich um eine *statische* Azure-Funktion handeln. Sie wird einmal pro Vorgangsmeldung ausgeführt, die von der Entität verarbeitet wird. Wenn `DispatchAsync<T>` aufgerufen wird und sich die Entität nicht bereits im Arbeitsspeicher befindet, wird ein Objekt vom Typ `T` erstellt, und die Felder werden mit den Daten aus dem letzten gespeicherten JSON-Code im Speicher gefüllt (falls vorhanden). Anschließend wird die Methode mit dem übereinstimmenden Namen aufgerufen.

> [!NOTE]
> Der Zustand einer klassenbasierten Entität wird **implizit erstellt** , bevor die Entität einen Vorgang verarbeitet, und kann per Vorgang **explizit gelöscht** werden, indem `Entity.Current.DeleteState()` aufgerufen wird.

### <a name="class-requirements"></a>Klassenanforderungen
 
Entitätsklassen sind POCOs (Plain Old CLR Objects), für die keine speziellen übergeordneten Klassen, Schnittstellen oder Attribute erforderlich sind. Allerdings:

- Die Klasse muss erstellbar sein (siehe [Entitätserstellung](#entity-construction)).
- Die Klasse muss JSON-serialisierbar sein (siehe [Entitätsserialisierung](#entity-serialization)).

Außerdem muss jede Methode, die als Vorgang aufgerufen werden soll, zusätzliche Anforderungen erfüllen:

- Ein Vorgang darf maximal über ein Argument verfügen und darf keine Überladungen oder generischen Typargumente aufweisen.
- Ein Vorgang, der von einer Orchestrierung mit einer Schnittstelle aufgerufen werden soll, muss `Task` oder `Task<T>` zurückgeben.
- Argumente und Rückgabewerte müssen serialisierbare Werte oder Objekte sein.

### <a name="what-can-operations-do"></a>Was gibt es mit Vorgängen für Möglichkeiten?

Alle Entitätsvorgänge können den Entitätszustand lesen und aktualisieren, und Änderungen des Zustands werden automatisch dauerhaft gespeichert. Darüber hinaus können mit Vorgängen externe E/A-Berechnungen oder andere Berechnungen durchgeführt werden, und zwar innerhalb der allgemeinen Grenzwerte, die für alle Azure-Funktionen gelten.

Für Vorgänge ist auch der Zugriff auf Funktionalität möglich, die über den `Entity.Current`-Kontext bereitgestellt wird:

* `EntityName`: Der Name der Entität, die derzeit ausgeführt wird.
* `EntityKey`: Der Schlüssel der Entität, die derzeit ausgeführt wird.
* `EntityId`: Die ID der Entität, die derzeit ausgeführt wird (enthält Name und Schlüssel).
* `SignalEntity`: Sendet eine unidirektionale Nachricht an eine Entität.
* `CreateNewOrchestration`: Startet eine neue Orchestrierung.
* `DeleteState`: Löscht den Zustand dieser Entität.

Wir können die Counter-Entität beispielsweise so modifizieren, dass eine Orchestrierung gestartet wird, wenn der Zähler den Wert 100 erreicht und die Entitäts-ID als Eingabeargument übergibt:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Direktes Zugreifen auf Entitäten

Auf klassenbasierte Entitäten kann direkt zugegriffen werden, indem explizite Zeichenfolgennamen für die Entität und die zugehörigen Vorgänge verwendet werden. Unten sind einige Beispiele angegeben. Eine ausführlichere Beschreibung der zugrunde liegenden Konzepte (z. B. Signale und Aufrufe) finden Sie unter [Zugreifen auf Entitäten](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Aufgrund der besseren Typüberprüfung empfehlen wir Ihnen, nach Möglichkeit [über Schnittstellen auf Entitäten zuzugreifen](#accessing-entities-through-interfaces).

### <a name="example-client-signals-entity"></a>Beispiel: Client sendet Signal an Entität

Mit der folgenden Azure-HTTP-Funktion wird ein DELETE-Vorgang mit REST-Konventionen implementiert. Sie sendet ein Löschsignal an die Counter-Entität, deren Schlüssel im URL-Pfad übergeben wird.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Beispiel: Client liest Entitätszustand

Mit der folgenden Azure-HTTP-Funktion wird ein GET-Vorgang mit REST-Konventionen implementiert. Sie liest den aktuellen Zustand der Counter-Entität, deren Schlüssel im URL-Pfad übergeben wird.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Das von `ReadEntityStateAsync` zurückgegebene Objekt ist nur eine lokale Kopie, also eine Momentaufnahme des Entitätszustands zu einem früheren Zeitpunkt. Es kann auch veraltet sein, und eine Änderung dieses Objekts hat keinerlei Auswirkung auf die eigentliche Entität. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Beispiel: Orchestrierung sendet zunächst Signal und ruft anschließend Entität auf

Bei der folgenden Orchestrierung wird ein Signal an eine Counter-Entität gesendet, um diese zu inkrementieren, und anschließend wird dieselbe Entität aufgerufen, um den letzten Wert zu lesen.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Zugreifen auf Entitäten über Schnittstellen

Schnittstellen können zum Zugreifen auf Entitäten über generierte Proxyobjekte verwendet werden. Mit diesem Ansatz wird sichergestellt, dass der Name und der Argumenttyp eines Vorgangs mit den implementierten Komponenten übereinstimmen. Wir empfehlen Ihnen, nach Möglichkeit Schnittstellen für den Zugriff auf Entitäten zu verwenden.

Wir können das Counter-Beispiel beispielsweise wie folgt modifizieren:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Entitätsklassen und -schnittstellen ähneln den „Grains“ und Grainschnittstellen von [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Weitere Informationen zu Ähnlichkeiten und Unterschieden zwischen dauerhaften Entitäten und Orleans finden Sie unter [Vergleich mit virtuellen Akteuren](durable-functions-entities.md#comparison-with-virtual-actors).

Schnittstellen ermöglichen nicht nur die Typüberprüfung, sondern sind auch nützlich für eine bessere Trennung von Zuständigkeiten innerhalb der Anwendung. Beispiel: Da von einer Entität ggf. mehrere Schnittstellen implementiert werden, kann eine einzelne Entität mehrere Rollen übernehmen. Weil eine Schnittstelle zudem von mehreren Entitäten implementiert werden kann, können allgemeine Kommunikationsmuster als wiederverwendbare Bibliotheken implementiert werden.

### <a name="example-client-signals-entity-through-interface"></a>Beispiel: Client sendet Signal per Schnittstelle an Entität

Für den Clientcode kann `SignalEntityAsync<TEntityInterface>` verwendet werden, um Signale an Entitäten zu senden, die `TEntityInterface` implementieren. Beispiel:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

In diesem Beispiel ist der Parameter `proxy` eine dynamisch generierte Instanz von `ICounter`, die den Aufruf von `Delete` intern in ein Signal übersetzt.

> [!NOTE]
> Die `SignalEntityAsync`-APIs können nur für unidirektionale Vorgänge verwendet werden. Auch wenn für einen Vorgang `Task<T>` zurückgegeben wird, ist der Wert des Parameters `T` immer null oder `default` (nicht das tatsächliche Ergebnis).
Es ergibt beispielsweise keinen Sinn, ein Signal an den `Get`-Vorgang zu senden, weil kein Wert zurückgegeben wird. Stattdessen können Clients entweder `ReadStateAsync` verwenden, um direkt auf den Zählerzustand zuzugreifen, oder eine Orchestratorfunktion starten, mit der der `Get`-Vorgang aufgerufen wird. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Beispiel: Orchestrierung sendet zunächst Signal und ruft anschließend per Proxy die Entität auf

Um eine Entität aus einer Orchestrierung aufzurufen oder ihr ein Signal zu senden, kann `CreateEntityProxy` zusammen mit dem Schnittstellentyp verwendet werden, um einen Proxy für die Entität zu generieren. Dieser Proxy kann dann genutzt werden, um Vorgänge aufzurufen oder Signale dafür zu senden:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implizit wird an alle Vorgänge, die `void` zurückgeben, ein Signal gesendet, und alle Vorgänge, die `Task` oder `Task<T>` zurückgeben, werden aufgerufen. Sie können dieses Standardverhalten ändern und auch dann Signale an Vorgänge senden, wenn diese „Task“ zurückgeben, indem Sie die `SignalEntity<IInterfaceType>`-Methode explizit verwenden.

### <a name="shorter-option-for-specifying-the-target"></a>Kürzere Option zum Angeben des Ziels

Wenn Sie eine Entität mit einer Schnittstelle aufrufen oder ihr darüber ein Signal senden, muss im ersten Argument die Zielentität angegeben werden. Sie können das Ziel angeben, indem Sie die Entitäts-ID bereitstellen. In Fällen, in denen die Entität nur mit einer Klasse implementiert wird, geben Sie nur den Entitätsschlüssel an:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Wenn nur der Entitätsschlüssel angegeben wird und zur Laufzeit keine eindeutige Implementierung gefunden werden kann, wird `InvalidOperationException` ausgelöst. 

### <a name="restrictions-on-entity-interfaces"></a>Einschränkungen für Entitätsschnittstellen

Alle Parameter und Rückgabetypen müssen JSON-serialisierbar sein. Andernfalls werden zur Laufzeit Serialisierungsausnahmen ausgelöst.

Außerdem werden einige zusätzliche Regeln erzwungen:
* Entitätsschnittstellen dürfen nur Methoden definieren.
* Entitätsschnittstellen dürfen keine generischen Parameter enthalten.
* Entitätsschnittstellenmethoden dürfen nicht mehr als einen Parameter enthalten.
* Entitätsschnittstellenmethoden müssen `void`, `Task` oder `Task<T>` zurückgeben. 

Falls eine dieser Regeln verletzt wird, wird zur Laufzeit eine `InvalidOperationException` ausgelöst, wenn die Schnittstelle als Typargument für `SignalEntity` oder `CreateProxy` verwendet wird. In der Ausnahmemeldung ist beschrieben, welche Regel verletzt wurde.

> [!NOTE]
> Schnittstellenmethoden, die `void` zurückgeben, können nur per Signal erreicht (unidirektional) und nicht aufgerufen (bidirektional) werden. Schnittstellenmethoden, die `Task` oder `Task<T>` zurückgeben, können entweder aufgerufen oder per Signal erreicht werden. Bei einem Aufruf wird das Ergebnis des Vorgangs zurückgegeben, oder vom Vorgang ausgelöste Ausnahmen werden erneut ausgelöst. Bei einer Signalisierung wird nicht das tatsächliche Ergebnis oder die Ausnahme des Vorgangs zurückgegeben, sondern nur der Standardwert.

## <a name="entity-serialization"></a>Entitätsserialisierung

Da der Zustand einer Entität dauerhaft gespeichert wird, muss die Entitätsklasse serialisierbar sein. Für die Durable Functions-Laufzeit wird zu diesem Zweck die [JSON.NET](https://www.newtonsoft.com/json)-Bibliothek verwendet. Sie unterstützt verschiedene Richtlinien und Attribute, um den Serialisierungs- und Deserialisierungsprozess zu steuern. Die am häufigsten verwendeten C#-Datentypen (z. B. Arrays und Sammlungstypen) sind bereits serialisierbar und können problemlos verwendet werden, um den Zustand von dauerhaften Entitäten zu definieren.

Mit JSON.NET kann die folgende Klasse leicht serialisiert und deserialisiert werden:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Serialisierungsattribute

Im obigen Beispiel haben wir mehrere Attribute eingefügt, um die zugrunde liegende Serialisierung besser sichtbar zu machen:
- Wir kommentieren die Klasse mit `[JsonObject(MemberSerialization.OptIn)]`, um daran zu erinnern, dass die Klasse serialisierbar sein muss und nur Member gespeichert werden sollen, die explizit als JSON-Eigenschaften gekennzeichnet sind.
-  Wir kommentieren die zu speichernden Felder mit `[JsonProperty("name")]`, um daran zu erinnern, dass ein Feld Teil des gespeicherten Entitätszustands ist, und um den Eigenschaftennamen anzugeben, der in der JSON-Darstellung verwendet werden soll.

Diese Attribute sind aber nicht obligatorisch. Andere Konventionen oder Attribute sind zulässig, solange sie mit JSON.NET funktionieren. Es besteht beispielsweise die Möglichkeit, `[DataContract]`-Attribute oder gar keine Attribute zu verwenden:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Standardmäßig wird der Name der Klasse *nicht* als Teil der JSON-Darstellung gespeichert. Dies bedeutet, dass wir `TypeNameHandling.None` als Standardeinstellung verwenden. Dieses Standardverhalten kann außer Kraft gesetzt werden, indem `JsonObject`- oder `JsonProperty`-Attribute verwendet werden.

### <a name="making-changes-to-class-definitions"></a>Vornehmen von Änderungen an Klassendefinitionen

Gehen Sie mit Bedacht vor, wenn Sie Änderungen an einer Klassendefinition vornehmen, nachdem eine Anwendung ausgeführt wurde. Es kann sein, dass das gespeicherte JSON-Objekt nicht mehr mit der neuen Klassendefinition übereinstimmt. Trotzdem ist es häufig möglich, auf korrekte Weise mit sich ändernden Datenformaten zu arbeiten, solange Sie mit dem von `JsonConvert.PopulateObject` verwendeten Deserialisierungsprozess vertraut sind.

Hier sind einige Beispiele für Änderungen und ihre Auswirkungen angegeben:

1. Wenn eine neue Eigenschaft hinzugefügt wird, die im gespeicherten JSON-Code nicht enthalten ist, wird ihr Standardwert verwendet.
1. Wenn eine Eigenschaft entfernt wird, die im gespeicherten JSON-Code enthalten ist, geht der vorherige Inhalt verloren.
1. Wenn eine Eigenschaft umbenannt wird, wirkt sich dies so aus, als ob die alte Eigenschaft entfernt und eine neue hinzugefügt wird.
1. Wenn der Typ einer Eigenschaft geändert wird, sodass sie nicht mehr aus dem gespeicherten JSON-Code deserialisiert werden kann, wird eine Ausnahme ausgelöst.
1. Wenn der Typ einer Eigenschaft geändert wird, die Deserialisierung aus dem gespeicherten JSON-Code aber trotzdem noch möglich ist, wird dies durchgeführt.

Es sind viele Optionen verfügbar, mit denen Sie das Verhalten von JSON.NET anpassen können. Wenn Sie beispielsweise eine Ausnahme für den Fall erzwingen möchten, in dem der gespeicherte JSON-Code über ein Feld verfügt, das in der Klasse nicht enthalten ist, geben Sie das Attribut `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` an. Sie können auch benutzerdefinierten Code für die Deserialisierung schreiben, mit dem JSON-Code, der in beliebigen Formaten gespeichert ist, gelesen werden kann.

## <a name="entity-construction"></a>Entitätserstellung

Es kann beispielsweise sein, dass besser gesteuert werden soll, wie Entitätsobjekte erstellt werden. Wir beschreiben nun verschiedene Optionen zum Ändern des Standardverhaltens beim Erstellen von Entitätsobjekten. 

### <a name="custom-initialization-on-first-access"></a>Benutzerdefinierte Initialisierung beim ersten Zugriff

Gelegentlich müssen wir eine spezielle Initialisierung durchführen, bevor ein Vorgang an eine Entität übermittelt wird, auf die nie zugegriffen oder die gelöscht wurde. Zum Angeben dieses Verhaltens kann vor `DispatchAsync` eine Bedingung eingefügt werden:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Bindungen in Entitätsklassen

Im Gegensatz zu regulären Funktionen haben Entitätsklassenmethoden keinen direkten Zugriff auf Eingabe- und Ausgabebindungen. Stattdessen müssen Bindungsdaten in der Einstiegspunkt-Funktionsdeklaration erfasst und anschließend an die Methode `DispatchAsync<T>` übergeben werden. Alle an `DispatchAsync<T>` übergebenen Objekte werden automatisch als Argument an den Entitätsklassenkonstruktor übergeben.

Im folgenden Beispiel wird gezeigt, wie ein Verweis vom Typ `CloudBlobContainer` aus der [Blobeingabebindung](../functions-bindings-storage-blob-input.md) für eine klassenbasierte Entität verfügbar gemacht werden kann.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Weitere Informationen zu Bindungen in Azure Functions finden Sie in der Dokumentation [Konzepte für Azure Functions-Trigger und -Bindungen](../functions-triggers-bindings.md).

### <a name="dependency-injection-in-entity-classes"></a>Abhängigkeitsinjektion in Entitätsklassen

Entitätsklassen unterstützen die [Abhängigkeitsinjektion in Azure Functions](../functions-dotnet-dependency-injection.md). Das folgende Beispiel zeigt die Registrierung eines Diensts vom Typ `IHttpClientFactory` bei einer klassenbasierten Entität:

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Der folgende Codeausschnitt zeigt die Integration des eingefügten Diensts in Ihre Entitätsklasse:

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Um Probleme mit der Serialisierung zu vermeiden, sollten Sie Felder ausschließen, die zum Speichern von injizierten Werten aus der Serialisierung dienen.

> [!NOTE]
> Anders als bei Verwendung der Konstruktorinjektion in regulären .NET-Azure-Funktionen *muss* die Funktionseinstiegspunkt-Methode für klassenbasierte Entitäten als `static` deklariert werden. Wenn Sie einen nicht statischen Funktionseinstiegspunkt deklarieren, kommt es unter Umständen zu Konflikten zwischen dem normalen Azure Functions-Objektinitialisierer und dem Durable Entities-Objektinitialisierer.

## <a name="function-based-syntax"></a>Funktionsbasierte Syntax

Bisher ging es um die klassenbasierte Syntax, weil wir glauben, dass sie für die meisten Anwendungen besser geeignet ist. Die funktionsbasierte Syntax kann aber für Anwendungen geeignet sein, bei denen eigene Abstraktionen für den Entitätszustand und die Vorgänge definiert bzw. verwaltet werden sollen. Darüber hinaus kann sie auch gut für die Implementierung von Bibliotheken geeignet sein, für die eine Generizität benötigt wird, die von der klassenbasierten Syntax derzeit nicht unterstützt wird. 

Mit der funktionsbasierten Syntax wird die Vorgangsübermittlung explizit von der Entitätsfunktion verarbeitet und der Zustand der Entität explizit verwaltet. Im folgenden Code ist die *Counter* -Entität dargestellt, die mit der funktionsbasierten Syntax implementiert wird.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Entitätskontextobjekt

Auf die entitätsspezifische Funktionalität kann über ein Kontextobjekt vom Typ `IDurableEntityContext` zugegriffen werden. Dieses Kontextobjekt ist auch als Parameter für die Entitätsfunktion und über die asynchrone lokale `Entity.Current`-Eigenschaft verfügbar.

Die folgenden Member liefern Informationen zum aktuellen Vorgang und ermöglichen es uns, einen Rückgabewert anzugeben. 

* `EntityName`: Der Name der Entität, die derzeit ausgeführt wird.
* `EntityKey`: Der Schlüssel der Entität, die derzeit ausgeführt wird.
* `EntityId`: Die ID der Entität, die derzeit ausgeführt wird (enthält Name und Schlüssel).
* `OperationName`: Der Name des aktuellen Vorgangs.
* `GetInput<TInput>()`: Ruft die Eingabe für den aktuellen Vorgang ab.
* `Return(arg)`: Gibt einen Wert an die Orchestrierung zurück, die den Vorgang aufgerufen hat.

Mit den folgenden Membern wird der Zustand der Entität (Erstellen, Lesen, Aktualisieren, Löschen) verwaltet. 

* `HasState`: Gibt an, ob die Entität vorhanden ist – also über einen Zustand verfügt. 
* `GetState<TState>()`: Ruft den aktuellen Zustand der Entität ab. Dieses Element wird erstellt, falls es noch nicht vorhanden ist.
* `SetState(arg)`: Erstellt oder aktualisiert den Zustand der Entität.
* `DeleteState()`: Löscht den Zustand der Entität, falls er vorhanden ist. 

Wenn der von `GetState` zurückgegebene Zustand ein Objekt ist, ist eine direkte Änderung per Anwendungscode möglich. Es ist nicht erforderlich, am Ende `SetState` aufzurufen (aber es schadet auch nicht). Wenn `GetState<TState>` mehrere Male aufgerufen wird, muss der gleiche Typ verwendet werden.

Abschließend werden die folgenden Member verwendet, um Signale an andere Entitäten zu senden oder neue Orchestrierungen zu starten:

* `SignalEntity(EntityId, operation, input)`: Sendet eine unidirektionale Nachricht an eine Entität.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: Startet eine neue Orchestrierung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Entitätskonzepten](durable-functions-entities.md)
