---
title: Verwenden integrierter Notebookbefehle und -features in C#-Notebooks in Azure Cosmos DB (Vorschau)
description: Erfahren Sie, wie Sie integrierte Befehle und Features verwenden, um allgemeine Vorgänge unter Verwendung der in Azure Cosmos DB integrierten C#-Notebooks durchzuführen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340476"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Verwenden integrierter Notebookbefehle und -features in C#-Notebooks in Azure Cosmos DB (Vorschau)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Integrierte Jupyter Notebooks in Azure Cosmos DB ermöglichen es Ihnen, Ihre Daten über das Azure-Portal zu analysieren und zu visualisieren. In diesem Artikel wird beschrieben, wie Sie integrierte Notebookbefehle und -features verwenden, um allgemeine Vorgänge in C#-Notebooks durchzuführen.

## <a name="install-a-new-nuget-package"></a>Installieren eines neuen NuGet-Pakets
Nachdem Sie die Notebookunterstützung für Ihre Azure Cosmos-Konten aktiviert haben, können Sie ein neues Notebook öffnen und ein Paket installieren.

Fügen Sie in einer neuen Codezelle den folgenden Code ein, und führen Sie ihn aus. Ersetzen Sie dabei ``PackageToBeInstalled`` durch das gewünschte NuGet-Paket und ``optionalVersion`` bei Bedarf durch eine bestimmte Version des Pakets. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Sie können mehrere NuGet-Pakete in derselben Zelle installieren. Die Pakete können von einem beliebigen Notebook im Arbeitsbereich des Azure Cosmos-Kontos verwendet werden. 

Der Arbeitsbereich der C#-Notebooks unterstützt derzeit keine rekursive Auflösung von NuGet-Paketen. Wenn ein NuGet-Paket Abhängigkeiten von anderen NuGet-Paketen aufweist, die derzeit nicht installiert sind, müssen Sie auf diese zusammen mit dem übergeordneten Paket explizit verweisen.

> [!TIP]
> Wenn für Ihr Notebook ein benutzerdefiniertes Paket erforderlich ist, wird empfohlen, zum Notebook eine Zelle hinzuzufügen, um das Paket zu installieren, und diese als erste Zelle festzulegen. Dadurch wird die Wahrscheinlichkeit von Konflikten mit anderen Paketen reduziert, die von Azure Cosmos DB standardmäßig geladen werden. Außerdem ist es einfach, die Pakete erneut zu installieren, wenn Sie [den Arbeitsbereich zurücksetzen](#reset-notebooks-workspace), wodurch alle Pakete entfernt werden. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Verwenden des integrierten Azure Cosmos DB .NET SDKs
In der Notebookumgebung für das Azure Cosmos-Konto ist Version 3 des [Azure Cosmos DB .NET SDKs für die SQL-API](https://github.com/Azure/azure-cosmos-dotnet-v3) installiert und enthalten.

Erstellen Sie eine Instanz von ``CosmosClient``, um SDK-Vorgänge auszuführen. 

Beispiel:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Weitere Informationen finden Sie in den [Beispielen zu Version 3 des .NET SDKs](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> Das integrierte Azure Cosmos DB .NET SDK wird nur für SQL-API-Konten (Core-API) unterstützt. Bei anderen APIs müssen Sie [den relevanten .NET-Treiber installieren](#install-a-new-nuget-package), der der API entspricht. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Festlegen von benutzerdefinierten Optionen mithilfe von ``CosmosClientOptions``
Für eine bessere Flexibilität können Sie die benutzerdefinierte ``CosmosClientOptions``-Eigenschaft festlegen und in Ihrer ``CosmosClient``-Instanz übergeben. Sie können diese Eigenschaft für Folgendes verwenden:

- Festlegen eines Anwendungsnamens im Suffix des Benutzer-Agents, um diesen in jeder Anforderung einzubeziehen
- Festlegen der bevorzugten zu verwendenden Region bei der Ausführung von Vorgängen im Dienst
- Festlegen einer benutzerdefinierten Wiederholungsrichtlinie zur Verarbeitung von begrenzten Anforderungen

Alle unterstützten Einstellungen finden Sie im Artikel [CosmosClientOptions-Klasse](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions). Im folgenden Beispiel wird gezeigt, wie die `cosmosClientOptions`-Eigenschaft festgelegt wird:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Zugreifen auf die Variablen für den Kontoendpunkt und den Primärschlüssel
Sie können auf den integrierten Endpunkt und den Schlüssel des Azure Cosmos-Kontos zugreifen, in dem Ihr Notebook vorhanden ist.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> Die Variablen ``Cosmos.Key`` und ``Cosmos.Endpoint`` gelten nur für die SQL-API. Bei anderen APIs finden Sie den Endpunkt und den Schlüssel auf dem Blatt **Verbindungszeichenfolgen** oder **Schlüssel** in Ihrem Azure Cosmos-Konto.  

## <a name="print-console-output-in-c-code"></a>Drucken der Konsolenausgabe im C#-Code
In Ihrem C#-Code können Sie die Display.AsMarkdown()-Syntax mit [Zeichenfolgeninterpolation](/dotnet/csharp/language-reference/tokens/interpolated) verwenden, um die Konsolenausgabe zu drucken, die beim Ausführen der Zelle angezeigt wird. 

Beispiel: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Die Console.WriteLine()-Syntax wird in C#-Notebooks derzeit nicht unterstützt. Verwenden Sie „Display.AsMarkdown“, um die Konsolenausgabe des Programms zu drucken. 

## <a name="use-built-in-nteract-data-explorer"></a>Verwenden des integrierten Daten-Explorers in nteract
Sie können den integrierten [Daten-Explorer in nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) verwenden, um eine Sammlung von Elementen zu filtern und zu visualisieren. Fügen Sie in einer Zelle die Variable, die Sie visualisieren möchten, in die letzte Zeile ein, die beim Ausführen der Zelle automatisch in nteract angezeigt wird.

Beispielsweise kann im Beispiel *GetingStarted_Csharp.ipynb* die Variable mit dem Ergebnis, ``telemetryEvents``, ausgedruckt werden. Das gesamte Beispiel finden Sie im [GettingStarted_Csharp.ipynb-Notebook](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb). 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="CSharp-Abfragezelle":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Daten-Explorer in nteract":::

## <a name="use-built-in-dictionary-viewer"></a>Verwenden der integrierten Wörterbuchanzeige
Sie können die integrierte Wörterbuchanzeige verwenden, um eine Variable anzuzeigen. Fügen Sie in einer Zelle die Variable, die Sie visualisieren möchten, in die letzte Zeile ein, die beim Ausführen der Zelle automatisch angezeigt wird.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Integrierte Wörterbuchanzeige":::

## <a name="upload-json-items-to-a-container"></a>Hochladen von JSON-Elementen in einen Container
Sie können den Magic-Befehl ``%%upload`` verwenden, um Daten aus einer JSON-Datei in einen angegebenen Azure Cosmos-Container hochzuladen. Laden Sie die Elemente mithilfe des folgenden Befehls hoch:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Ersetzen Sie ``{database_id}`` und ``{container_id}`` durch den Namen der Datenbank und des Containers in Ihrem Azure Cosmos-Konto. 
- Ersetzen Sie ``{url_location_of_file}`` durch den Speicherort Ihrer JSON-Datei. Die Datei muss ein Array aus gültigen JSON-Objekten sein, und sie sollte im Internet öffentlich zugänglich sein.

Beispiel:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Mit der Ausgabestatistik können Sie die effektive Rate an RU/s berechnen, die zum Hochladen der Elemente benötigt werden. Wenn beispielsweise 28.000 RUs in 38 Sekunden verarbeitet werden, liegt die effektive Rate an RU/s bei 25.000 RUs / 38 Sekunden = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Ausführen eines anderen Notebooks im aktuellen Notebook 
Sie können den Magic-Befehl ``%%run`` verwenden, um vom aktuellen Notebook in Ihrem Arbeitsbereich aus ein anderes Notebook auszuführen. Verwenden Sie die folgende Syntax:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Ersetzen Sie ``{notebookName}`` durch den Namen des Notebooks, das Sie ausführen möchten. Das Notebook muss sich in Ihrem aktuellen Arbeitsbereich „My Notebooks“ (Meine Notebooks) befinden. 

## <a name="reset-notebooks-workspace"></a>Zurücksetzen des Notebookarbeitsbereichs
Wählen Sie in der Befehlsleiste den Befehl zum **Zurücksetzen des Arbeitsbereichs** aus, um den Notebookarbeitsbereich auf die Standardeinstellungen zurückzusetzen. Dadurch werden alle installierten benutzerdefinierten Pakete entfernt und der Jupyter-Server neu gestartet. Ihre Notebooks, Dateien und Azure Cosmos-Ressourcen sind nicht betroffen.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Zurücksetzen des Notebookarbeitsbereichs":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Vorteilen von [Jupyter Notebooks in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Weitere Informationen zum [Azure Cosmos DB .NET SDK für die SQL-API](https://github.com/Azure/azure-cosmos-dotnet-v3)
