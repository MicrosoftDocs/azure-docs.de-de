---
title: Node.js-Beispiele zum Verwalten von Daten in einer Azure Cosmos-Datenbank
description: Node.js-Beispiele für häufige Aufgaben in Azure Cosmos DB, einschließlich CRUD-Vorgänge, finden Sie auf GitHub.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 261ffc97d12202b588e519502895d338453c4380
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097242"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js-Beispiele zum Verwalten von Daten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-Beispiele](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-Beispiele](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-Beispiele](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-Beispiele](sql-api-spring-data-sdk-samples.md)
> * [Node.js-Beispiele](sql-api-nodejs-samples.md)
> * [Python-Beispiele](sql-api-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Beispiellösungen, in denen CRUD-Vorgänge und andere gängige Vorgänge für Azure Cosmos DB-Ressourcen ausgeführt werden, finden Sie im GitHub-Repository [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen Node.js-Beispielprojektdateien.
* Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Darüber hinaus benötigen Sie das [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Jedes Beispiel ist eigenständig mit eigener Einrichtung und Bereinigung. In den Beispielen werden mehrere Aufrufe von [Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest) ausgegeben. Bei jeder Ausführung dieses Vorgangs wird Ihrem Abonnement eine Stunde Nutzung gemäß Leistungsstufe des erstellten Containers in Rechnung gestellt.
   > 
   > 

## <a name="database-examples"></a>Datenbankbeispiele

Die Datei [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) veranschaulicht, wie die CRUD-Vorgänge für die Datenbank ausgeführt werden. Um mehr über die Azure Cosmos-Datenbanken zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank, sofern noch nicht vorhanden](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#createifnotexists-databaserequest--requestoptions-) |
| [Auflisten von Datenbanken für ein Konto](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Lesen einer Datenbank nach ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Löschen einer Datenbank](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="container-examples"></a>Containerbeispiele

Die Datei [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) veranschaulicht, wie die CRUD-Vorgänge für den Container ausgeführt werden. Um mehr über die Azure Cosmos-Sammlungen zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Containers, sofern noch nicht vorhanden](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#createifnotexists-containerrequest--requestoptions-) |
| [Auflisten von Containern für ein Konto](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Lesen einer Containerdefinition](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Löschen eines Containers](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="item-examples"></a>Elementbeispiele

Die Datei [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) veranschaulicht, wie die CRUD-Vorgänge für das Element ausgeführt werden. Um mehr über die Azure Cosmos-Dokumente zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen von Elementen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#create-t--requestoptions-) |
| [Lesen aller Elemente in einem Container](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Lesen eines Elements nach ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Festlegen, dass ein Element nur gelesen wird, wenn es geändert wurde](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Abfragen von Dokumenten](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Ersetzen eines Elements](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |
| [Ersetzen eines Elements mit bedingter ETag-Überprüfung](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Löschen eines Elements](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |

## <a name="indexing-examples"></a>Indizierungsbeispiele

Die Datei [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) veranschaulicht die Verwaltung der Indizierung. Um mehr über die Indizierung in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele die Konzeptartikel [Indizierungsrichtlinien](index-policy.md), [Indizierungstypen](index-overview.md#index-kinds) und [Indizierungspfade](index-policy.md#include-exclude-paths). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Manuelles Indizieren eines bestimmten Elements](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Manuelles Ausschließen eines bestimmten Elements aus dem Index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Ausschließen eines Pfads aus dem Index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest#excludedpaths) |
| [Erstellen eines Bereichsindexes in einem Zeichenfolgenpfad](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind?preserve-view=true&view=azure-node-latest), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest), [Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Erstellen eines Containers mit indexPolicy-Standardwert und anschließende Onlineaktualisierung](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Serverseitige Programmierbeispiele

Die Datei [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) im Projekt [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die serverseitige Programmierung in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures?preserve-view=true&view=azure-node-latest) |
| [Ausführen einer gespeicherten Prozedur](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure?preserve-view=true&view=azure-node-latest) |

Weitere Informationen zur serverseitigen Programmierung finden Sie unter [Serverseitige Azure Cosmos DB-Programmierung: gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md).
