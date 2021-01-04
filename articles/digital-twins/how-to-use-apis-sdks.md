---
title: Verwenden der Azure Digital Twins-APIs und SDKs
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie mit den Azure Digital Twins-APIs arbeiten können, auch über SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: cac0d8cb8a910b735454c9270060364cab2db5fb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187237"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Verwenden der Azure Digital Twins-APIs und SDKs

Azure Digital Twins ist sowohl mit **Steuerungsebenen-APIs** als auch mit **Datenebenen-APIs** zur Verwaltung Ihrer Instanz und ihrer Elemente ausgestattet. 
* Die Steuerungsebenen-APIs sind [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md)-APIs, die Vorgänge zur Ressourcenverwaltung wie das Erstellen und Löschen von Instanzen abdecken. 
* Bei den Datenebenen-APIs handelt es sich um Azure Digital Twins-APIs, die für Vorgänge zur Datenverwaltung wie die Verwaltung von Modellen, Zwillingen und des Graphen verwendet werden.

Dieser Artikel bietet eine Übersicht über die verfügbaren APIs und die Methoden zur Interaktion mit ihnen. Sie können die REST-APIs entweder direkt mit den zugehörigen Swagger-Dateien (über ein Tool wie [Postman](how-to-use-postman.md)) oder über ein SDK verwenden.

## <a name="overview-control-plane-apis"></a>Übersicht: Steuerungsebenen-APIs

Bei den Steuerungsebenen-APIs handelt es sich um [ARM](../azure-resource-manager/management/overview.md)-APIs, die verwendet werden, um Ihre Azure Digital Twins-Instanz als Ganzes zu verwalten, sodass sie Vorgänge wie das Erstellen oder Löschen Ihrer gesamten Instanz abdecken. Sie werden diese auch zum Erstellen und Löschen von Endpunkten verwenden.

Die aktuellste Steuerungsebenen-API-Version ist _**2020-10-31**_.

So verwenden Sie die Steuerungsebenen-APIs
* Sie können die APIs direkt aufrufen, indem Sie auf den neuesten Swagger im [Swagger-Ordner der Steuerungsebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins) verweisen. Dieses Repository enthält auch einen Ordner mit Beispielen, die die Verwendung veranschaulichen.
* Sie können derzeit auf SDKs für Steuerungs-APIs in Folgendem zugreifen...
  - [ **.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([Referenz [automatisch generiert]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([Quelle](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_10_31/azure-mgmt-digitaltwins/1.0.0/jar) ([Referenz [automatisch generiert]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([Quelle](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/mgmt-v2020_10_31))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Quelle](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([Quelle](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([Quelle](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

Sie können auch Steuerungsebenen-APIs anwenden, indem Sie mit Azure Digital Twins über das [Azure-Portal](https://portal.azure.com) und die [CLI](how-to-use-cli.md) interagieren.

## <a name="overview-data-plane-apis"></a>Übersicht: Datenebenen-APIs

Bei den Datenebenen-APIs handelt es sich um die Azure Digital Twins-APIs, die verwendet werden, um die Elemente innerhalb Ihrer Azure Digital Twins-Instanz zu verwalten. Dazu gehören Vorgänge wie das Erstellen von Routen, das Hochladen von Modellen, das Erstellen von Beziehungen und die Verwaltung von Zwillingen. Sie lassen sich grob in die folgenden Kategorien einteilen:
* **DigitalTwinModels** – Die Kategorie „DigitalTwinModels“ enthält APIs zur Verwaltung der [Modelle](concepts-models.md) in einer Azure Digital Twins-Instanz. Zu den Verwaltungsaktivitäten gehören das Hochladen, Überprüfen, Abrufen und Löschen von Modellen, die in DTDL erstellt wurden.
* **DigitalTwins** – Die Kategorie „DigitalTwins“ enthält die APIs, mit denen Entwickler [digitale Zwillinge](concepts-twins-graph.md) und ihre Beziehungen in einer Azure Digital Twins-Instanz erstellen, ändern und löschen können.
* **Query** – Die Kategorie „Query“ ermöglicht es Entwicklern, [Gruppen von digitalen Zwillingen im Zwillingsgraphen](how-to-query-graph.md) beziehungsübergreifend zu finden.
* **Event Routes** – Die Kategorie „Event Routes“ enthält APIs zur [Weiterleitung von Daten](concepts-route-events.md), durch das System und zu Downstreamdiensten.

Die aktuellste Datenebenen-API-Version ist _**2020-10-31**_.

So verwenden Sie die Datenebenen-APIs
* Sie können die APIs direkt aufrufen, indem Sie wie folgt vorgehen:
   - Verweisen auf den aktuellen Swagger im [Swagger-Ordner der Datenebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Dieses Repository enthält auch einen Ordner mit Beispielen, die die Verwendung veranschaulichen. 
   - Zeigen Sie die [API-Referenzdokumentation](/rest/api/azure-digitaltwins/) an.
* Sie können das **.NET (C#) SDK** verwenden. So verwenden Sie das .NET SDK
   - Sie können das Paket über NuGet anzeigen und hinzufügen: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Sie können die [SDK-Referenzdokumentation](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) anzeigen.
   - Sie suchen die SDK-Quelle, einschließlich eines Ordner mit Beispielen, in GitHub: [Azure IoT Digital Twins-Clientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Sie können ausführliche Informationen und Anwendungsbeispiele anzeigen, indem Sie mit dem Abschnitt [ *.NET (C#) SDK (Datenebene)*](#net-c-sdk-data-plane) dieses Artikels fortfahren.
* Sie können das **Java SDK** verwenden. Zum Verwenden des Java-SDK...
   - Sie können das Paket über Maven anzeigen und installieren: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - Sie können die [SDK-Referenzdokumentation](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable) anzeigen.
   - Sie können die SDK-Quelle in GitHub finden: [Azure IoT Digital Twins-Clientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Sie können das **JavaScript SDK** verwenden. So verwenden Sie das JavaScript SDK
   - Sie können das Paket über npm anzeigen und installieren: [Azure Digital Twins-Hauptclientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Sie können die [SDK-Referenzdokumentation](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true) anzeigen.
   - Sie können die SDK-Quelle in GitHub finden: [Azure Digital Twins-Hauptclientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Sie können das **Python SDK** verwenden. Zum Verwenden des Python SDK...
   - Sie können das Paket über PyPi anzeigen und installieren: [Azure Digital Twins-Hauptclientbibliothek für Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Sie können die [SDK-Referenzdokumentation](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true) anzeigen.
   - Sie können die SDK-Quelle in GitHub finden: [Azure Digital Twins-Hauptclientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Mit AutoRest können Sie ein SDK für eine andere Sprache generieren. Führen Sie die Anweisungen unter [ *Erstellen von benutzerdefinierten SDKs für Azure Digital Twins mit AutoRest*](how-to-create-custom-sdks.md).

Sie können auch Datenebenen-APIs anwenden, indem Sie mit Azure Digital Twins über die [CLI](how-to-use-cli.md) interagieren.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (Datenebene)

Das Azure Digital Twins .NET (C#) SDK ist Teil des Azure SDK für .NET. Es ist Open Source und basiert auf den APIs der Azure Digital Twins-Datenebene.

> [!NOTE]
> Weitere Informationen zum SDK-Entwurf finden Sie in den allgemeinen [Entwurfsprinzipien für Azure SDKs](https://azure.github.io/azure-sdk/general_introduction.html) und in den spezifischen [.NET-Entwurfsrichtlinien](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Um das SDK zu verwenden, fügen Sie Ihrem Projekt das NuGet-Paket **Azure.DigitalTwins.Core** hinzu. Sie benötigen auch die aktuelle Version des Pakets **Azure.Identity**.

* In Visual Studio können Sie Pakete mit dem NuGet-Paket-Manager hinzufügen (Zugriff über *Extras > NuGet-Paket-Manager > NuGet-Pakete für Lösung verwalten*). 
* Sie können das .NET-Befehlszeilentool verwenden:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Eine ausführliche exemplarische Vorgehensweise zur Verwendung der APIs in der Praxis finden Sie im [*Tutorial: Programmieren einer Client-App*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Beispiele für die Verwendung des .NET SDKs

Hier finden Sie einige Codebeispiele, die die Verwendung des .NET SDKs veranschaulichen.

Authentifizierung beim Dienst:

```csharp
// Authenticate against the service and create a client
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
```

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Modell hochladen und Modelle auflisten:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Zwillinge erstellen und abfragen:

```csharp
// Initialize twin metadata
BasicDigitalTwin updateTwinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.Contents.Add("data", "Hello World!");
try {
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("firstTwin", updateTwinData);
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Eine exemplarische Vorgehensweise zu diesem Beispiel-App-Code finden Sie im [*Tutorial: Programmieren einer Client-App*](tutorial-code.md). 

Weitere Beispiele finden Sie auch im [GitHub-Repository für das .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Hilfsprogramme für die Serialisierung

Serialisierungshilfen sind im SDK verfügbare Hilfsfunktionen zum schnellen Erstellen oder Deserialisieren von Zwillingsdaten für den Zugriff auf grundlegende Informationen. Da die Kernmethoden des SDK Zwillingsdaten standardmäßig als JSON zurückgeben, kann es hilfreich sein, die Zwillingsdaten mithilfe dieser Hilfsklassen weiter zu unterteilen.

Die folgenden Hilfsklassen sind verfügbar:
* `BasicDigitalTwin`: Stellt die Kerndaten eines digitalen Zwillings dar.
* `BasicRelationship`: Stellt die Kerndaten einer Beziehung dar.
* `UpdateOperationUtility`: Stellt JSON-Patchinformationen dar, die in Aktualisierungsaufrufen verwendet werden.
* `WriteableProperty`: Stellt Eigenschaftsmetadaten dar.

##### <a name="deserialize-a-digital-twin"></a>Deserialisieren eines digitalen Zwillings

Sie können jederzeit Zwillingsdaten deserialisieren, indem Sie die JSON-Bibliothek Ihrer Wahl wie `System.Test.Json` oder `Newtonsoft.Json` verwenden. Für den grundlegenden Zugriff auf einen Zwilling gestalten die Helferklassen dies etwas komfortabler.

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

Die Hilfsklasse `BasicDigitalTwin` bietet Ihnen auch Zugriff auf Eigenschaften, die auf dem Zwilling definiert sind, über ein `Dictionary<string, object>`. Sie können Folgendes verwenden, um die Eigenschaften eines Zwillings aufzulisten:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Erstellen eines digitalen Zwillings

Mit der Klasse `BasicDigitalTwin` können Sie Daten für die Erstellung einer Zwillingsinstanz vorbereiten:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

Der obige Code entspricht der folgenden „manuellen“ Variante:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

##### <a name="deserialize-a-relationship"></a>Deserialisieren einer Beziehung

Beziehungsdaten können jederzeit in einen Typ Ihrer Wahl deserialisiert werden. Verwenden Sie für grundlegenden Zugriff auf eine Beziehung den Typ `BasicRelationship`.

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

Die Hilfsklasse `BasicRelationship` bietet Ihnen über ein `IDictionary<string, object>`-Element auch Zugriff auf Eigenschaften, die für die Beziehung definiert sind. Zum Auflisten von Eigenschaften können Sie Folgendes verwenden:

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Erstellen einer Beziehung

Mit der Klasse `BasicRelationship` können Sie auch Daten für die Erstellung von Beziehungen auf einer Zwillingsinstanz vorbereiten:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.Properties = props;
client.CreateOrReplaceRelationshipAsync("mySourceTwin", "rel001", rel);
```

##### <a name="create-a-patch-for-twin-update"></a>Erstellen eines Patches für die Zwillingsaktualisierung

Aktualisierungsaufrufe für Zwillinge und Beziehungen verwenden die [JSON-Patchstruktur](http://jsonpatch.com/). Zum Erstellen von Listen mit JSON-Patchvorgängen können Sie `JsonPatchDocument` wie unten gezeigt verwenden.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", 25.0);
updateTwinData.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
updateTwinData.AppendRemoveOp("/Humidity");

client.UpdateDigitalTwin("myTwin", updateTwinData);
```

## <a name="general-apisdk-usage-notes"></a>Allgemeine Hinweise zur API/SDK-Verwendung

> [!NOTE]
> Beachten Sie, dass **Cross-Origin Resource Sharing (CORS)** in Azure Digital Twins derzeit nicht unterstützt wird. Weitere Informationen zu den Auswirkungen und Lösungsstrategien finden Sie im Abschnitt [*Cross-Origin Resource Sharing (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) unter *Konzepte: Sicherheit für Azure Digital Twins-Lösungen*.

Die folgende Liste enthält zusätzliche Details und allgemeine Richtlinien für die Verwendung der APIs und SDKs.

* Sie können ein HTTP REST-Testtool wie Postman verwenden, um direkte Aufrufe an die APIs von Azure Digital Twins durchzuführen. Weitere Informationen zu diesem Vorgang finden Sie unter [ *Senden von Anforderungen mit Postman*](how-to-use-postman.md).
* Instanziieren Sie die Klasse `DigitalTwinsClient`, um das SDK zu verwenden. Der Konstruktor erfordert Anmeldeinformationen, die mit einer Vielzahl von Authentifizierungsmethoden im `Azure.Identity`-Paket abgerufen werden können. Weitere Informationen zu `Azure.Identity` finden Sie in der [Dokumentation zu Namespaces](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet). 
* Möglicherweise finden Sie das `InteractiveBrowserCredential` zu Beginn nützlich, aber es gibt noch verschiedene andere Optionen, einschließlich Anmeldeinformationen für die [verwaltete Identität](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), die Sie wahrscheinlich zur Authentifizierung von [mit MSI eingerichteten Azure-Funktionen](../app-service/overview-managed-identity.md?tabs=dotnet) für Azure Digital Twins verwenden werden. Weitere Informationen über `InteractiveBrowserCredential` finden Sie in dessen [Klassendokumentation](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet).
* Alle Aufrufe von Dienst-APIs werden als Memberfunktionen für die `DigitalTwinsClient`-Klasse verfügbar gemacht.
* Alle Dienstfunktionen sind in synchroner und asynchroner Version vorhanden.
* Alle Dienstfunktionen lösen eine Ausnahme für einen beliebigen Rückgabestatus von 400 oder höher aus. Stellen Sie sicher, dass Sie Aufrufe in einem `try`-Abschnitt umschließen und mindestens `RequestFailedExceptions` erfassen. Weitere Informationen zu dieser Art von Ausnahmen finden Sie [hier](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet).
* Die meisten Dienstmethoden geben `Response<T>` (oder `Task<Response<T>>` für die asynchronen Aufrufe) zurück, wobei `T` die Klasse des Rückgabeobjekts für den Dienstaufruf darstellt. Die Klasse [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet) kapselt die Dienstrückgabe und präsentiert Rückgabewerte in ihrem `Value`-Feld.  
* Dienstmethoden mit ausgelagerten Ergebnissen geben `Pageable<T>` oder `AsyncPageable<T>` als Ergebnis zurück. Weitere Informationen zur Klasse `Pageable<T>` finden Sie [hier](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview). Weitere Informationen zu `AsyncPageable<T>` finden Sie [hier](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview).
* Mithilfe einer `await foreach`-Schleife können Sie ausgelagerte Ergebnisse durchlaufen. Mehr zu diesem Prozess finden Sie [hier](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Das zugrunde liegende SDK ist `Azure.Core`. In der [Dokumentation zu Azure-Namespaces](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview) finden Sie Verweise auf die SDK-Infrastruktur und -Typen.

Dienstmethoden geben, wo immer möglich, stark typisierte Objekte zurück. Da Azure Digital Twins jedoch auf Modellen basiert, die vom Benutzer zur Laufzeit individuell konfiguriert werden (über DTDL-Modelle, die in den Dienst hochgeladen werden), verwenden viele Dienst-APIs das JSON-Format, um Zwillingsdaten zu übernehmen und zurückzugeben.

## <a name="monitor-api-metrics"></a>Überwachen von API-Metriken

API-Metriken wie Anforderungen, Wartezeit und Ausfallrate können im [Azure-Portal](https://portal.azure.com/) angezeigt werden. 

Suchen Sie auf der Startseite des Portals nach Ihrer Azure Digital Twins-Instanz, um die Details abzurufen. Wählen Sie die Option **Metrics** aus dem Menü der Azure Digital Twins-Instanz aus, um die Seite *Metriken* aufzurufen.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot der Seite „Metriken“ für Azure Digital Twins":::

Von hier aus können Sie die Metriken für Ihre Instanz anzeigen und benutzerdefinierte Ansichten erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Ausführen direkter Anforderungen an die APIs mithilfe von Postman:
* [*Vorgehensweise: Senden von Anforderungen mit Postman*](how-to-use-postman.md)

Verwenden Sie alternativ das .NET SDK, indem Sie eine Client-App mithilfe dieses Tutorials erstellen:
* [*Tutorial: Codieren einer Client-App*](tutorial-code.md)