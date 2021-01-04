---
title: Verwenden von Azure Cosmos DB-Ressourcentoken mit dem Gremlin SDK
description: Hier erfahren Sie, wie Sie Ressourcentoken erstellen und damit auf die Graphdatenbank zugreifen.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361692"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Verwenden von Azure Cosmos DB-Ressourcentoken mit dem Gremlin SDK
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel wird erläutert, wie Sie mithilfe von [Azure Cosmos DB-Ressourcentoken](secure-access-to-data.md) über das Gremlin SDK auf die Graphdatenbank zugreifen.

## <a name="create-a-resource-token"></a>Erstellen eines Ressourcentoken

Das Apache TinkerPop Gremlin SDK enthält keine API zum Erstellen von Ressourcentoken. Der Begriff *Ressourcentoken* ist ein Azure Cosmos DB-Konzept. Laden Sie zum Erstellen von Ressourcentoken das [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) herunter. Wenn Ihre Anwendung Ressourcentoken erstellen und damit auf die Graphdatenbank zugreifen muss, werden zwei separate SDKs benötigt.

Die Objektmodellhierarchie über Ressourcentoken ist in der folgenden Gliederung dargestellt:

- **Azure Cosmos DB-Konto** : Entität der höchsten Ebene, der ein DNS zugeordnet ist (z. B. `contoso.gremlin.cosmos.azure.com`).
  - **Azure Cosmos DB-Datenbank**
    - **Benutzer**
      - **Berechtigung**
        - **Token** : Eine Eigenschaft des Permission-Objekts, die angibt, welche Aktionen zugelassen oder verweigert werden

Ein Ressourcentoken verwendet das folgende Format: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Diese Zeichenfolge ist für die Clients nicht transparent und muss unverändert verwendet werden.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Verwenden eines Ressourcentoken
Beim Erstellen der GremlinServer-Klasse können Sie Ressourcentoken direkt als password-Eigenschaft verwenden.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Der gleiche Ansatz funktioniert in allen TinkerPop Gremlin SDKs.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Begrenzung

Mit einem einzelnen Gremlin-Konto können Sie eine unbegrenzte Anzahl von Token ausgeben. Sie können jedoch innerhalb von 1 Stunde nur bis zu 100 Token gleichzeitig verwenden. Wenn eine Anwendung das stündliche Tokenlimit überschreitet, wird eine Authentifizierungsanforderung verweigert, und Sie erhalten die folgende Fehlermeldung: „Exceeded allowed resource token limit of 100 that can be used concurrently.“ (Das zulässige Ressourcentokenlimit von 100 für die gleichzeitige Verwendung wurde überschritten.) Das Schließen aktiver Verbindungen mit bestimmten Token zum Freigeben von Slots für neue Token funktioniert nicht. Die Cosmos DB-Gremlin-Datenbank-Engine erfasst eindeutige Token in der Stunde vor der Authentifizierungsanforderung.

## <a name="permission"></a>Berechtigung

Ein häufiger Fehler, der in Anwendungen bei der Verwendung von Ressourcentoken auftritt, ist „Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header.“ (Unzureichende Berechtigungen im Autorisierungsheader für die entsprechende Anforderung. Versuchen Sie es mit einem anderen Autorisierungsheader erneut.). Dieser Fehler wird zurückgegeben, wenn bei einem Gremlin-Durchlauf versucht wird, eine Kante oder einen Scheitelpunkt zu schreiben, das Ressourcentoken jedoch nur *Leseberechtigungen* erteilt. Überprüfen Sie, ob der Durchlauf einen der folgenden Schritte enthält: *.addV()* , *.addE()* , *.drop()* oder *.property()* .

## <a name="next-steps"></a>Nächste Schritte
* [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](role-based-access-control.md) in Azure Cosmos DB
* [Sicherer Zugriff auf Daten in Azure Cosmos DB](secure-access-to-data.md)
