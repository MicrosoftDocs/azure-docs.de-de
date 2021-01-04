---
title: Azure Cosmos DB-Tabellen-API mit .NET Standard SDK
description: Hier erfahren Sie, wie Sie die strukturierten Daten im Azure Cosmos DB-Tabellen-API-Konto speichern und abfragen.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f43a890185fcd69967fd2a035c27e1cf92798f04
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548153"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Erste Schritte mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage mit .NET SDK
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Sie können die Azure Cosmos DB-Tabellen-API oder Azure Table Storage verwenden, um strukturierte NoSQL-Daten in der Cloud zu speichern. Dazu wird ein Schlüssel--Attribut-Speicher mit einem schemalosen Design angeboten. Aufgrund der Schemalosigkeit der Azure Cosmos DB-Tabellen-API und von Table Storage ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Mit der Azure Cosmos DB-Tabellen-API oder Table Storage können Sie flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und andere Arten von Metadaten speichern, die Ihr Dienst benötigt. 

Dieses Tutorial beschreibt ein Beispiel dafür, wie Sie die [Microsoft Azure Cosmos DB-Tabellenbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) in Szenarien mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage verwenden. Sie müssen die spezifische Verbindung für den Azure-Dienst verwenden. Diese Szenarien werden anhand von C#-Beispielen erläutert, die veranschaulichen, wie Sie Tabellen erstellen, Daten einfügen/aktualisieren, Daten abfragen und die Tabellen löschen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure Cosmos DB-Tabellenbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): Diese Bibliothek ist derzeit für .NET Standard und das .NET Framwork verfügbar. 

* [Konto für die Azure Cosmos DB-Tabellen-API](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Erstellen eines Kontos für die Azure Cosmos DB-Tabellen-API

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Erstellen eines .NET-Konsolenprojekts

Erstellen Sie in Visual Studio eine neue .NET-Konsolenanwendung. In den folgenden Schritten wird veranschaulicht, wie Sie eine Konsolenanwendung in Visual Studio 2019 erstellen. Sie können die Azure Cosmos DB-Tabellenbibliothek in jeder Art von .NET-Anwendung nutzen, z.B. einem Azure-Clouddienst, einer Azure-Web-App, einer Desktopanwendung oder einer mobilen Anwendung. In diesem Leitfaden verwenden wir der Einfachheit halber eine Konsolenanwendung.

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

1. Wählen Sie **Konsolen-App (.NET Core)** und dann **Weiter** aus.

1. Geben Sie im Feld **Projektname** einen Namen für Ihre Anwendung ein, z. B. **CosmosTableSamples**. (Sie können bei Bedarf einen anderen Namen angeben.)

1. Klicken Sie auf **Erstellen**.

Alle Codebeispiele in diesem Beispiel können in der Datei **Program.cs** Ihrer Konsolenanwendung der „Main()“-Methode hinzugefügt werden.

## <a name="install-the-required-nuget-package"></a>Installieren der erforderlichen NuGet-Pakete

Führen Sie die folgenden Schritte aus, um die NuGet-Pakete abzurufen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Suchen Sie online nach [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) und [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder), und wählen Sie **Installieren** aus, um die Microsoft Azure Cosmos DB-Tabellenbibliothek zu installieren.

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Azure Cosmos-Konto oder Ihrem Table Storage-Konto. 

1. Öffnen Sie einen der Bereiche **Verbindungszeichenfolge** oder **Zugriffsschlüssel**. Kopieren Sie mithilfe der Kopierschaltfläche im rechten Fensterbereich die **PRIMÄRE VERBINDUNGSZEICHENFOLGE**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Anzeigen und Kopieren der primären Verbindungszeichenfolge im Bereich „Verbindungszeichenfolge“":::
   
1. Um Ihre Verbindungszeichenfolge zu konfigurieren, klicken Sie in Visual Studio mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**.

1. Wählen Sie **Hinzufügen** > **Neues Element** aus. Erstellen Sie eine neue Datei **Settings.json** mit dem Dateityp als **TypeScript-JSON-Konfiguration**. 

1. Ersetzen Sie den Code in der Datei „Settings.json“ durch den folgenden Code, und weisen Sie Ihre primäre Verbindungszeichenfolge zu:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **AppSettings.cs** hinzu.

1. Fügen Sie AppSettings.cs den folgenden Code hinzu: Diese Datei liest die Verbindungszeichenfolge aus der Datei „Settings.json“ und weist sie dem Konfigurationsparameter zu:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Analysieren und Überprüfen der Verbindungsdetails

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **Common.cs** hinzu. Sie schreiben Code, um die Verbindungsdetails zu überprüfen und eine Tabelle innerhalb dieser Klasse zu erstellen.

1. Definieren Sie eine Methode `CreateStorageAccountFromConnectionString`, wie unten dargestellt. Diese Methode analysiert die Details der Verbindungszeichenfolge und überprüft, ob der Kontoname und die Schlüsseldaten des Kontos, die in der Datei „Settings.json“ angegeben sind, gültig sind.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Erstellen einer Tabelle 

Mit der [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) -Klasse können Sie im Tabellenspeicher gespeicherte Tabellen und Entitäten abrufen. Da wir keine Tabellen im Konto der Cosmos DB-Tabellen-API haben, fügen wir die Methode `CreateTableAsync` zur Klasse **Common.cs** hinzu, um eine Tabelle zu erstellen:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Sollte der Fehler „503 Dienst nicht verfügbar“ angezeigt werden, werden die erforderlichen Ports für den Konnektivitätsmodus unter Umständen durch eine Firewall blockiert. Öffnen Sie zum Beheben des Problems entweder die erforderlichen Ports, oder verwenden Sie die Gatewaymoduskonnektivität wie im folgenden Code gezeigt:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definieren der Entität 

Entitäten werden C#-Objekten zugeordnet, indem eine benutzerdefinierte Klasse verwendet wird, die aus [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity)abgeleitet wurde. Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen.

Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen**, **Neuer Ordner**,und nennen Sie ihn **Modell**. Fügen Sie im Ordner „Modell“ eine Klasse namens **CustomerEntity.cs** hinzu, und fügen Sie den folgenden Code hinzu.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Mit dem Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. Eine Entität in der Tabelle wird durch die Kombination aus Partitions- und Zeilenschlüssel eindeutig identifiziert. Entitäten mit dem gleichen Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere Skalierbarkeit paralleler Vorgänge. Bei Entitäten, die in Tabellen gespeichert werden sollen, muss es sich um einen unterstützten Typ handeln (beispielsweise abgeleitet von der [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity)-Klasse). Bei Entitätseigenschaften, die Sie in einer Tabelle speichern möchten, muss es sich um öffentliche Eigenschaften des Typs handeln, und sie müssen sowohl das Abrufen als auch das Festlegen von Werten unterstützen. Darüber hinaus muss der Entitätstyp einen parameterlosen Konstruktor verfügbar machen.

## <a name="insert-or-merge-an-entity"></a>Einfügen oder Zusammenführen einer Entität

Das folgende Codebeispiel erstellt ein Entitätsobjekt und fügt es der Tabelle hinzu. Die Methode „InsertOrMerge“ innerhalb der Klasse [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) wird verwendet, um eine Entität einzufügen oder zusammenzuführen. Danach wird die Methode [CloudTable.ExecuteAsyn](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?preserve-view=true&view=azure-dotnet) aufgerufen, um den Vorgang auszuführen. 

Klicken Sie mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element**, und fügen Sie eine Klasse mit dem Namen **SamplesUtils.cs** hinzu. Diese Klasse speichert den gesamten Code, der für die Durchführung von CRUD-Operationen an den Entitäten erforderlich ist. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Abrufen einer Entität aus einer Partition

Sie können eine Entität von einer Partition abrufen, indem Sie die Retrieve-Methode der Klasse [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) verwenden. Das folgende Codebeispiel ruft den Zeilenschlüssel des Partitionsschlüssels, die E-Mail-Adresse und die Telefonnummer einer Kundenentität ab. Dieses Beispiel druckt auch die Anforderungseinheiten aus, die für die Abfrage nach der Entität benötigt werden. Um eine Entität abzufragen, fügen Sie den folgenden Code an die Datei **SamplesUtils.cs** an:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Löschen einer Entität

Eine Entität kann nach dem Abrufen problemlos gelöscht werden. Verwenden Sie dazu das gleiche Muster wie beim Aktualisieren einer Entität. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht. Um eine Entität zu löschen, fügen Sie den folgenden Code an die Datei **SamplesUtils.cs** an: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Ausführen der CRUD-Vorgänge für Beispieldaten

Nachdem Sie die Methoden zum Erstellen von Tabellen, Einfügen oder Zusammenführen von Entitäten definiert haben, führen Sie diese Methoden für Beispieldaten aus. Klicken Sie dafür mit der rechten Maustaste auf Ihr Projekt **CosmosTableSamples**. Wählen Sie **Hinzufügen** > **Neues Element** aus, fügen Sie eine Klasse mit dem Namen **BasicSamples.cs** hinzu, und fügen Sie der Klasse den folgenden Code hinzu. Dieser Code erstellt eine Tabelle und fügt Entitäten hinzu.

Wenn Sie die Entität und Tabelle am Ende des Projekts nicht löschen möchten, kommentieren Sie die Methoden `await table.DeleteIfExistsAsync()` und `SamplesUtils.DeleteEntityAsync(table, customer)` im folgenden Code aus. Es empfiehlt sich, diese Methoden auszukommentieren und die Daten zu überprüfen, bevor Sie die Tabelle löschen.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Der vorherige Code erstellt eine Tabelle, die mit „demo“ beginnt, und die generierte GUID wird an den Tabellennamen angehängt. Anschließend wird eine Kundenentität mit Vor- und Nachnamen als „Walter Harp“ hinzugefügt und später die Telefonnummer dieses Benutzers aktualisiert. 

In diesem Tutorial haben Sie Code erstellt, um grundlegende CRUD-Vorgänge an den im Tabellen-API-Konto gespeicherten Daten durchzuführen. Sie können auch erweiterte Operationen durchführen. Beispiele hierfür sind das Einfügen von Daten per Batchvorgang, das Abfragen aller Daten oder eines Datenbereichs innerhalb einer Partition oder das Auflisten der im Konto vorhandenen Tabellen, deren Namen mit dem angegebenen Präfix beginnen. Sie können das komplette Beispiel aus dem [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)-GitHub-Repositoy herunterladen. Die [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) -Klasse verfügt über mehr Vorgänge, die Sie für die Daten ausführen können.  

## <a name="run-the-project"></a>Ausführen des Projekts

Aus Ihrem Projekt **CosmosTableSamples**. Öffnen Sie die Klasse **Program.cs**, und fügen Sie den folgenden Code hinzu, um „BasicSamples“ beim Ausführen des Projekts aufzurufen.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Nun erstellen Sie die Projektmappe, und drücken F5, um das Projekt auszuführen. Wenn das Projekt ausgeführt wird, sehen Sie die folgende Ausgabe in der Eingabeaufforderung:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Ausgabe in der Eingabeaufforderung":::

Wenn der Fehler mit dem Hinweis angezeigt wird, dass die Datei „Settings.json“ beim Ausführen des Projekts nicht gefunden werden kann, können Sie das Problem lösen, indem Sie den Projekteinstellungen den folgenden XML-Eintrag hinzufügen. Klicken Sie mit der rechten Maustaste auf „CosmosTableSamples“, wählen Sie „CosmosTableSamples.csproj bearbeiten“ aus, und fügen Sie die folgende „itemGroup“ hinzu: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Jetzt können Sie sich beim Azure-Portal anmelden und überprüfen, ob die Daten in der Tabelle vorhanden sind. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Ergebnisse im Portal":::

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem nächsten Tutorial fortfahren, in dem Sie erfahren, wie Sie Daten in das Azure Cosmos DB-Tabellen-API-Konto migrieren können. 

> [!div class="nextstepaction"]
>[Migrieren von Daten zur Azure Cosmos DB-Tabellen-API](../cosmos-db/table-import.md)