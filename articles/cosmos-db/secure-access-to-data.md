---
title: Informationen zum sicheren Zugriff auf Daten in Azure Cosmos DB
description: Informationen zu Zugriffssteuerungskonzepten in Azure Cosmos DB, darunter Primärschlüssel, Schlüssel mit Leseberechtigung, Benutzer und Berechtigungen.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 6dd95fc8fd0ab0099ac7404d4ca4e4b1851f650f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359607"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Sicherer Zugriff auf Daten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel bietet eine Übersicht über den sicheren Zugriff auf in [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) gespeicherte Daten.

Azure Cosmos DB verwendet zwei Arten von Schlüsseln, um Benutzer zu authentifizieren und den Zugriff auf Daten und Ressourcen zu ermöglichen. 

|Schlüsseltyp|Ressourcen|
|---|---|
|[Primärschlüssel](#primary-keys) |Wird für Verwaltungsressourcen verwendet: Datenbankkonten, Datenbanken, Benutzer und Berechtigungen|
|[Ressourcentoken](#resource-tokens)|Wird für Anwendungsressourcen verwendet: Container, Dokumente, Anlagen, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Primärschlüssel

Primärschlüssel ermöglichen den Zugriff auf alle Verwaltungsressourcen für das Datenbankkonto. Jedes Konto umfasst zwei Primärschlüssel: einen primären und einen sekundären Schlüssel. Dank der Verwendung von zwei Schlüsseln können Sie Schlüssel neu generieren oder ersetzen und trotzdem ohne Unterbrechung auf Ihr Konto und Ihre Daten zugreifen. Weitere Informationen zu Primärschlüsseln finden Sie im Artikel zur [Datenbanksicherheit](database-security.md#primary-keys).

### <a name="key-rotation"></a>Schlüsselrotation<a id="key-rotation"></a>

Der Primärschlüssel kann ganz einfach gewechselt werden. 

1. Navigieren Sie zum Azure-Portal, um Ihren sekundären Schlüssel abzurufen.
2. Ersetzen Sie den Primärschlüssel in Ihrer Anwendung durch Ihren sekundären Schlüssel. Stellen Sie sicher, dass alle Cosmos DB-Clients in sämtlichen Bereitstellungen umgehend neu gestartet werden und mit der Verwendung des aktualisierten Schlüssels beginnen.
3. Rotieren Sie den Primärschlüssel im Azure-Portal.
4. Überprüfen Sie, ob der neue Primärschlüssel mit allen Ressourcen funktioniert. Der Schlüsselrotationsvorgang kann je nach Größe des Cosmos DB-Kontos unterschiedlich lange dauern – von weniger als einer Minute bis hin zu mehreren Stunden.
5. Ersetzen Sie den sekundären Schlüssel durch den neuen Primärschlüssel.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Rotation des Primärschlüssels im Azure-Portal: Veranschaulichung der NoSQL-Datenbanksicherheit" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Codebeispiel für die Verwendung eines Primärschlüssels

Das folgende Codebeispiel veranschaulicht, wie mit einem Cosmos DB-Kontoendpunkt und einem Primärschlüssel eine DocumentClient-Instanz instanziiert und eine Datenbank erstellt wird:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

Das folgende Codebeispiel veranschaulicht, wie mit dem Azure Cosmos DB-Kontoendpunkt und dem Primärschlüssel ein `CosmosClient`-Objekt instanziiert wird:

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Ressourcentoken <a id="resource-tokens"></a>

Ressourcentoken ermöglichen den Zugriff auf die Anwendungsressourcen in einer Datenbank. Ressourcentoken:

- Ermöglichen den Zugriff auf bestimmte Container, Partitionsschlüssel, Dokumente, Anhänge, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen.
- Werden erstellt, wenn einem [Benutzer](#users)[Berechtigungen](#permissions) für eine bestimmte Ressource gewährt werden.
- Werden neu erstellt, wenn durch einen POST-, GET- oder PUT-Aufruf eine Aktion für eine Berechtigungsressource ausgeführt wird.
- Verwenden ein Hashressourcentoken, das speziell für den Benutzer, die Ressource und die Berechtigung erstellt wird.
- Verfügen über einen anpassbaren Gültigkeitszeitraum. Die Gültigkeitsdauer beträgt standardmäßig eine Stunde. Die Tokengültigkeitsdauer kann aber explizit auf bis zu fünf Stunden festgelegt werden.
- Stellen eine sichere Alternative zur Weitergabe des Primärschlüssels dar.
- Ermöglichen Clients das Lesen, Schreiben und Löschen von Ressourcen im Cosmos DB-Konto gemäß den gewährten Berechtigungen.

Mit einem Ressourcentoken (durch Erstellung von Cosmos DB-Benutzern und -Berechtigungen) können Sie einem Client, dem Sie den Primärschlüssel nicht anvertrauen können, Zugriff auf Ressourcen in Ihrem Cosmos DB-Konto gewähren.  

Cosmos DB-Ressourcentoken stellen eine sichere Alternative dar, um Clients das Lesen, Schreiben und Löschen von Ressourcen in einem Cosmos DB-Konto gemäß den gewährten Berechtigungen und ohne Primärschlüssel oder schreibgeschützten Schlüssel zu ermöglichen.

Hier sehen Sie ein typisches Design, bei dem Ressourcentoken angefordert, generiert und an Clients übermittelt werden können:

1. Ein Mid-Tier-Dienst dient zum Freigeben von Benutzerfotos für eine mobile Anwendung.
2. Der Mid-Tier-Dienst verfügt über den Primärschlüssel des Cosmos DB-Kontos.
3. Die Foto-App ist auf mobilen Endbenutzergeräten installiert.
4. Bei der Anmeldung richtet die Fotoapp die Identität des Benutzers mit dem Mid-Tier-Dienst ein. Dieser Mechanismus der Identitätseinrichtung hängt nur von der Anwendung ab.
5. Nachdem die Identität eingerichtet wurde, fordert der Mid-Tier-Dienst Berechtigungen auf Grundlage der Identität an.
6. Der Mid-Tier-Dienst sendet einen Ressourcentoken an die Phoneapp zurück.
7. Die Phoneapp kann weiterhin den Ressourcentoken für direkten Zugriff auf Cosmos DB-Ressourcen mit den Berechtigungen verwenden, die durch das Ressourcentoken für einen bestimmten Zeitraum definiert sind.
8. Wenn das Ressourcentoken abläuft, tritt bei anschließenden Anforderungen die Ausnahme 401 (nicht autorisierter Zugriff) auf.  An dieser Stelle richtet die Phoneapp die Identität erneut ein und fordert ein neues Ressourcentoken an.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Workflow der Azure Cosmos DB-Ressourcentoken" border="false":::

Die Generierung und Verwaltung von Ressourcentoken wird von den nativen Cosmos DB-Clientbibliotheken übernommen. Bei Verwendung von REST müssen Sie allerdings die Anforderungs-/Authentifizierungsheader erstellen. Weitere Informationen zum Erstellen von Authentifizierungsheadern für REST finden Sie unter [Zugriffssteuerung in der SQL-API von Azure Cosmos DB](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) oder im Quellcode für unser [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) oder [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Ein Beispiel für einen Dienst der mittleren Ebene, der zum Generieren oder Vermitteln von Ressourcentoken dient, finden Sie unter der [ResourceTokenBroker-App](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Benutzer<a id="users"></a>

Azure Cosmos DB-Benutzer werden einer Cosmos-Datenbank zugeordnet.  Jede Datenbank kann null oder mehr Cosmos DB-Benutzer enthalten. Das folgende Codebeispiel veranschaulicht das Erstellen eines Cosmos DB-Benutzers mit dem [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Jeder Cosmos DB-Benutzer verfügt über eine ReadAsync()-Methode, mit deren Hilfe die Liste mit den [Berechtigungen](#permissions) abgerufen werden kann, die dem Benutzer zugeordnet sind.

## <a name="permissions"></a>Berechtigungen<a id="permissions"></a>

Eine Berechtigungsressource ist einem Benutzer zugeordnet und sowohl auf der Container- als auch auf der Partitionsschlüsselebene zugewiesen. Jeder Benutzer kann null oder mehr Berechtigungen enthalten. Eine Berechtigungsressource ermöglicht den Zugriff auf ein Sicherheitstoken, das der Benutzer beim Zugriff auf einen bestimmten Container oder bestimmte Daten in einem speziellen Partitionsschlüssel benötigt. Von einer Berechtigungsressource können zwei Zugriffsebenen bereitgestellt werden:

- Alle: Der Benutzer verfügt über Vollzugriff auf die Ressource.
- Lesen: Der Benutzer kann die Inhalte der Ressource nur lesen und keine Schreib-, Aktualisierungs- oder Löschvorgänge für die Ressource vornehmen.

> [!NOTE]
> Zum Ausführen von gespeicherten Prozeduren muss der Benutzer über uneingeschränkte Berechtigung für den Container verfügen, in dem die gespeicherte Prozedur ausgeführt wird.

Wenn Sie die [Diagnoseprotokolle für Anforderungen auf Datenebene](cosmosdb-monitor-resource-logs.md) aktivieren, werden die beiden folgenden Eigenschaften entsprechend der Berechtigung protokolliert:

* **resourceTokenPermissionId**: Diese Eigenschaft gibt die von Ihnen festgelegten Ressourcentokenberechtigungs-ID an. 

* **resourceTokenPermissionMode**: Diese Eigenschaft gibt den Berechtigungsmodus an, den Sie beim Erstellen des Ressourcentokens festgelegt haben. Der Berechtigungsmodus kann Werte wie „Alle“ oder „Lesen“ aufweisen.

### <a name="code-sample-to-create-permission"></a>Codebeispiel für die Berechtigungserstellung

Das folgende Codebeispiel veranschaulicht das Erstellen einer Berechtigungsressource, das Lesen des Ressourcentokens der Berechtigungsressource sowie das Zuordnen der Berechtigungen zum oben erstellten [Benutzer](#users).

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Codebeispiel für das Lesen von Berechtigungen für einen Benutzer

Der folgende Codeausschnitt zeigt, wie die dem oben erstellten Benutzer zugeordnete Berechtigung abgerufen und ein neues CosmosClient-Element für den Benutzer instanziiert werden kann, das auf einen einzelnen Partitionsschlüssel festgelegt ist.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Hinzufügen von Benutzern und Zuweisen von Rollen

Um Ihrem Benutzerkonto Azure Cosmos DB-Kontoleserzugriff hinzuzufügen, lassen Sie den Besitzer eines Abonnements die folgenden Schritte im Azure-Portal ausführen.

1. Öffnen Sie das Azure-Portal, und wählen Sie Ihr Azure Cosmos DB-Konto aus.
2. Klicken Sie auf die Registerkarte **Zugriffssteuerung (IAM)** aus, und klicken Sie dann auf **+ Rollenzuweisung hinzufügen**.
3. Wählen Sie im Bereich **Rollenzuweisung hinzufügen** im Feld **Rolle** die Option **Cosmos DB-Rolle „Kontoleser“** aus.
4. Wählen Sie im Feld **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Anwendung** aus.
5. Wählen Sie den Benutzer, die Gruppe oder die Anwendung aus, dem oder der Sie Zugriff gewähren möchten.  Sie können das Verzeichnis nach Anzeigename, E-Mail-Adresse oder Objektbezeichner durchsuchen.
    Der ausgewählten Benutzer, die Gruppe oder die Anwendung wird in der Liste der ausgewählten Elemente angezeigt.
6. Klicken Sie auf **Speichern**.

Die Entität kann jetzt Azure Cosmos DB-Ressourcen lesen.

## <a name="delete-or-export-user-data"></a>Löschen oder Exportieren von Benutzerdaten

Mithilfe des Datenbankdiensts Azure Cosmos DB können Sie alle Daten, die sich in einer Datenbank oder Containern befinden, durchsuchen, auswählen, ändern und löschen. Es liegt jedoch in Ihrer Verantwortung, mithilfe der bereitgestellten APIs die Logik zu definieren, die erforderlich ist, um personenbezogene Daten zu finden und ggf. zu löschen. Jede API für mehrere Modelle (SQL, MongoDB, Gremlin, Cassandra, Tabelle) stellt SDKs für verschiedene Sprachen bereit, die Methoden zum Durchsuchen und Löschen von Daten basierend auf benutzerdefinierten Prädikaten bieten. Sie können darüber hinaus die [TTL-Funktion (time-to-live)](time-to-live.md) verwenden, um Daten nach einem festgelegten Zeitraum automatisch zu löschen, wodurch keine weiteren Kosten anfallen.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Cosmos-Datenbanksicherheit finden Sie unter [Sicherheit bei Azure Cosmos DB – Übersicht](database-security.md).
- Informationen zum Erstellen von Azure Cosmos DB-Autorisierungstoken finden Sie unter [Access Control on Azure Cosmos DB Resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) (Zugriffssteuerung für Azure Cosmos DB-Ressourcen).
- Beispiele für Benutzerverwaltung mit Benutzern und Berechtigungen finden Sie unter [.NET SDK v3: Beispiele für die Benutzerverwaltung](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
